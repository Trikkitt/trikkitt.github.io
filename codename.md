---
title: Set Codename
permalink: /codename/
layout: page
---

From here you can set your codename and set or change your password.  If you haven't set a password yet then leave the password field blank.

Password: <input type="password" id="existingpw" name="existingpw">
<button id="getCodename">Retrieve Codename</button>
<p id="retrievestatus"> </p> <br/>

<br><br>

<p id="codenametext" >Codename: </p><input type="text" id="codenamebox" name="codenamebox" disabled=true> <button id="setCodename" disabled=true>Update Codename</button>
<p id="setcodenamestatus" > </p>
Codenames can be between 4 and 30 characters long consisting of only letters, numbers, spaces and symbols :;#@.  Please keep them appropriate for any age.<br><br>
If you haven't set a password it is highly recommended that you do.  Minimum 12 letters maximum 72.  Passwords are hashed using bcrypt.
<p>New Password:</p><input type="password" id="newpw1" name="newpw1" disabled=true><br>
<p>Repeat Password:</p><input type="password" id="newpw2" name="newpw2" disabled=true><br>
<button id="setPassword" disabled=true>Update Password</button>
<p id="setpwstatus"> </p><br><br>

<input type="hidden" id="salt" value="" />
<input type="hidden" id="hash" value="" />
<input type="hidden" id="newhash" value="" />

<script src="https://unpkg.com/mqtt/dist/mqtt.min.js"></script>
<script src="/assets/js/bcrypt.min.js"></script>

<script>
  const searchParams = new URLSearchParams(window.location.search);
  if (!searchParams.has("token_id")) {
    document.getElementById("codenametext").innerHTML="No token ID";
    tokenId="00000000000000000000";
  } else {
    var tokenId=searchParams.get("token_id");
  }
  var clientId="web_" + Math.random().toString(16).substr(2, 8);
  host="wss://scores.gen.polyb.io:8002/mqtt";
  options = {
    keepalive: 60,
    clientId: clientId,
    protocolId: "MQTT",
    protocolVersion: 4,
    clean: true,
    reconnectPeriod: 1000,
    connectTimeout: 30 * 1000
  };
  var mqttclient=mqtt.connect(host,options);
  mqttclient.on("error",(err) => {
    mqttclient.end();
    return;
  });
  mqttclient.on("connect", () => {
    mqttclient.subscribe(`/app/to/${clientId}/name`, {qos: 0});
    mqttclient.subscribe(`/app/to/${clientId}/newname`, {qos: 0});
    mqttclient.subscribe(`/app/to/${clientId}/salt`, {qos: 0});
    mqttclient.subscribe(`/app/to/${clientId}/passwordchanged`, {qos: 0});
    mqttclient.subscribe(`/app/to/${clientId}/error`, {qos: 0});
    mqttclient.publish(`/app/from/${clientId}/saltquery`,`${tokenId}`, {qos: 0, retain: false});
    return;
  });
  mqttclient.on("message", (topic, message, packet) => {
    console.log(`message topic: ${topic}`);
    console.log(`message content: ${message}`);
    topicsalt=`/app/to/${clientId}/salt`;
    topicname=`/app/to/${clientId}/name`;
    topicnewname=`/app/to/${clientId}/newname`;
    topicpasswordchanged=`/app/to/${clientId}/passwordchanged`;
    topicerror=`/app/to/${clientId}/error`;
    if (topic == topicsalt) {
      document.getElementById("salt").value=message;
    }
    if (topic == topicname) {
      console.log("Running mqtt name received");
      console.log(`topic: ${topic}  matched: ${topicname}`);
      document.getElementById("retrievestatus").innerHTML="Completed.";
      document.getElementById("codenamebox").value=message;
      document.getElementById("codenamebox").disabled=false;
      document.getElementById("existingpw").disabled=true;
      document.getElementById("existingpw").value="";
      document.getElementById("getCodename").disabled=true;
      document.getElementById("setCodename").disabled=false;
      document.getElementById("setPassword").disabled=false;
      document.getElementById("newpw1").disabled=false;
      document.getElementById("newpw2").disabled=false;
    }
    if (topic == topicnewname) {
      console.log("Running mqtt newname received");
      console.log(`topic: ${topic}  matched: ${topicnewname}`);
      document.getElementById("setcodenamestatus").innerHTML="Updated.";
    }
    if (topic == topicpasswordchanged) {
      console.log("Running mqtt passwordchanged received");
      console.log(`topic: ${topic}  matched: ${topicpasswordchanged}`);
      document.getElementById("setpwstatus").innerHTML="Updated.";
      document.getElementById("newpw1").value="";
      document.getElementById("newpw2").value="";
      document.getElementById("hash").value=document.getElementById("newhash").value
    }
    if (topic == topicerror) {
      console.log("Running mqtt error received");
      console.log(`topic: ${topic}  matched: ${topicerror}`);
      document.getElementById("retrievestatus").innerHTML=message;
    }
    return;
  });

  
getCodename.addEventListener("click", async () => {
  password=document.getElementById("existingpw").value;
  if (password) {
    if (password.length<12) {
      document.getElementById("retrievestatus").innerHTML="Password invalid";
      return;
    }
    if (password.length>72) {
      document.getElementById("retrievestatus").innerHTML="Password invalid";
      return;
    }    
  } else {
    password="PolyGenNewUser";
  }
  let bcrypt = dcodeIO.bcrypt;
  document.getElementById("retrievestatus").innerHTML="Hashing...";
  salt=document.getElementById("salt").value;
  hash=bcrypt.hashSync(password, salt);
  document.getElementById("hash").value=hash;
  password="";
  document.getElementById("retrievestatus").innerHTML="Checking...";
  mqttclient.publish(`/app/from/${clientId}/namequery`,`${tokenId},${hash}`, {qos: 0, retain: false});
  return;
});

setCodename.addEventListener("click", async () => {
  newCodename=document.getElementById("codenamebox").value;
  if (newCodename) {
    if (newCodename.length<4) {
      document.getElementById("setcodenamestatus").innerHTML="Invalid codename. Must be at least 4 characters long.";
      return;
    }
    if (newCodename.length>30) {
      document.getElementById("setcodenamestatus").innerHTML="Invalid codename. Must be no longer than 30 characters long.";
      return;
    }
  } else {
    document.getElementById("setcodenamestatus").innerHTML="Invalid codename. Must be at least 4 characters long.";
    return;
  }
    
  let regex = /[A-Za-z0-9 #:;()@]+$/i;
  if (!regex.test(newCodename)) {
    document.getElementById("setcodenamestatus").innerHTML="Invalid codename. Only numbers, letters, spaces and symbols #:;()@ are accepted.";
    return;
  }
  hash=document.getElementById("hash").value;
  mqttclient.publish(`/app/from/${clientId}/nameset`,`${tokenId},${hash},${newCodename}`, {qos: 0, retain: false});
  document.getElementById("setcodenamestatus").innerHTML="Updating...";
  return;
});

setPassword.addEventListener("click", async () => {
  password=document.getElementById("newpw1").value;
  if (password.length>0) {
    if (password.length<12) {
      document.getElementById("setpwstatus").innerHTML="Password too short.";
      return;
    }
    if (password.length>72) {
      document.getElementById("retrievestatus").innerHTML="Password too long.";
      return;
    }    
  } else {
    document.getElementById("setpwstatus").innerHTML="Password too short.";
    return;
  }
  if (password!=document.getElementById("newpw2").value) {
    document.getElementById("setpwstatus").innerHTML="Passwords do not match.";
    return;
  }
  document.getElementById("setpwstatus").innerHTML="Hashing...";
  let bcrypt = dcodeIO.bcrypt;
  let newsalt = bcrypt.genSaltSync(12);
  newhash = bcrypt.hashSync(password, newsalt); 
  document.getElementById("newhash").value=newhash;
  hash=document.getElementById("hash").value;
  password="";
  document.getElementById("setpwstatus").innerHTML="Updating...";
  mqttclient.publish(`/app/from/${clientId}/passwordset`,`${tokenId},${hash},${newhash}`, {qos: 0, retain: false});
  return;
});
  
codenamebox.addEventlistener("focus", async () => {
  document.getElementById("setcodenamestatus").innerHTML=" ";
});
newpw1.addEventlistener("focus", async () => {
  document.getElementById("setpwstatus").innerHTML=" ";
});
newpw2.addEventlistener("focus", async () => {
  document.getElementById("setpwstatus").innerHTML=" ";
});
  
</script>
