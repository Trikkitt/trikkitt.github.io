---
title: Set Codename
permalink: /codename/
layout: page
---

From here you can set your codename.

Password: <input type="password" id="existingpw" name="existingpw">
<p> </p> <button id="getCodename">Retrieve Codename</button><p id="retrievestatus" name="retrievestatus"></p> <br/>
If you haven't set a password yet then leave the password field blank.
<br><br>

<p id="codenametext" >Codename: </p><input type="text" id="codename" name="codename" disabled=true> <button id="setCodename">Update Codename</button><p id="setcodenamestatus" name="setcodenamestatus"></p><br>
Codenames can be between 4 and 20 characters long consisting of only letters, numbers and spaces.  Please keep them appropriate for any age.<br>
If you haven't set a password it is highly recommended that you do.  Minimum 12 letters maximum 72.  Passwords are hashed using bcrypt.
<p>New Password:</p><input type="password" id="newpw1" name="newpw1" disabled=true><br>
<p>Repeat Password:</p><input type="password" id="newpw2" name="newpw2" disabled=true><br>
<button id="setPassword" disabled=true>Update Password</button><p id="setpwstatus" name="setpwstatus"></p><br><br>

<script src="https://unpkg.com/mqtt/dist/mqtt.min.js"></script>
<script src="/assets/js/bcrypt.min.js"></script>

<script>
  const searchParams = new URLSearchParams(window.location.search);
  if (!searchParams.has('token_id')) {
    document.getElementById("codenametext").innerHTML='No token ID';
    tokenId='00000000000000000000';
  } else {
    var tokenId=searchParams.get('token_id');
  }

  clientId='web_' + Math.random().toString(16).substr(2, 8);
  host='wss://scores.gen.polyb.io:8002/mqtt';
  options = {
    keepalive: 60,
    clientId: clientId,
    protocolId: 'MQTT',
    protocolVersion: 4,
    clean: true,
    reconnectPeriod: 1000,
    connectTimeout: 30 * 1000
  };
  var mqttclient=mqtt.connect(host,options);
  mqttclient.on('error',(err) => {
    mqttclient.end();
  });
  mqttclient.on('connect', () => {
    mqttclient.subscribe(`/app/to/${clientId}/name`, {qos: 0});
    mqttclient.subscribe(`/app/to/${clientId}/newname`, {qos: 0});
    mqttclient.subscribe(`/app/to/${clientId}/salt`, {qos: 0});
    mqttclient.subscribe(`/app/to/${clientId}/passwordchanged`, {qos: 0});
    mqttclient.subscribe(`/app/to/${clientId}/error`, {qos: 0});
    mqttclient.publish(`/app/from/${clientId}/saltquery`,`${tokenId}`, {qos: 0, retain: false});
  });
  mqttclient.on('message', (topic, message, packet) => {
    if (topic = `/app/to/${clientId}/salt`) {
      var playersalt=message
    }
    if (topic = `/app/to/${clientId}/name`) {
      document.getElementById("retrievestatus").innerHTML='Completed.';
      document.getElementById("codename").value=message;
      document.getElementById("codename").disabled=false;
      document.getElementById("existingpw").disabled=true;
      document.getElementById("existingpw").value="";
      document.getElementById("getCodename").disabled=true;
      document.getElementById("setCodename").disabled=false;
      document.getElementById("setPassword").disabled=false;
      document.getElementById("newpw1").disabled=false;
      document.getElementById("newpw2").disabled=false;
    }
    if (topic = `/app/to/${clientId}/newname`) {
      document.getElementById("setcodenamestatus").innerHTML='Updated.';
    }
    if (topic = `/app/to/${clientId}/passwordchanged`) {
      document.getElementById("setpwstatus").innerHTML='Updated.';
      document.getElementById("newpw1").value="";
      document.getElementById("newpw2").value="";
      hash=newhash
    }
    if (topic = `/app/to/${clientId}/error`) {
      document.getElementById("retrievestatus").innerHTML=message;
    }
  });

  
getCodename.addEventListener("click", async () => {
  password=document.getElementById("existingpw").value;
  
  if (password.length>0) {
    if (password.length<12) {
      document.getElementById("retrievestatus").innerHTML='Password invalid';
      return;
    }
    if (password.length>72) {
      document.getElementById("retrievestatus").innerHTML='Password invalid';
      return;
    }    
  } else {
    password='PolyGenNewUser';
  }
  let bcrypt = dcodeIO.bcrypt;
  document.getElementById("retrievestatus").innerHTML='Hashing...';
  var hash=bcrypt.hashSync(password, playersalt);
  password='';
  document.getElementById("retrievestatus").innerHTML='Checking...';
  mqttclient.publish(`/app/from/${clientId}/namequery`,`${tokenId},${hash}`, {qos: 0, retain: false});
});

setCodename.addEventListener("click", async () => {
  newCodename=document.getElementById("codename").value
  if (len(newCodename)<4) {
    document.getElementById("setcodenamestatus").innerHTML="Invalid codename. Must be at least 4 characters long.";
    return
  }
  if (len(newCodename)>30) {
    document.getElementById("setcodenamestatus").innerHTML="Invalid codename. Must be no longer than 30 characters long.";
    return
  }
    
  let regex = /[A-Za-z0-9 #:;()@]+$/i;
  if (!regex.text(newCodename)) {
    document.getElementById("setcodenamestatus").innerHTML="Invalid codename. Only numbers, letters, spaces and symbols #:;()@ are accepted.";
    return
  }
  mqttclient.publish(`/app/from/${clientId}/nameset`,`${tokenId},${hash},${newCodename}`, {qos: 0, retain: false});
  document.getElementById("setcodenamestatus").innerHTML="Updating...";
});

setPassword.addEventListener("click", async () => {
  password=document.getElementById("newpw1").value;
  if (password.length>0) {
    if (password.length<12) {
      document.getElementById("setpwstatus").innerHTML='Password too short.';
      return;
    }
    if (password.length>72) {
      document.getElementById("retrievestatus").innerHTML='Password too long.';
      return;
    }    
  } else {
    document.getElementById("setpwstatus").innerHTML='Password too short.';
    return;
  }
  if (password!=document.getElementById("newpw2").value) {
    document.getElementById("setpwstatus").innerHTML='Passwords don't match.';
    return;
  }
  document.getElementById("setpwstatus").innerHTML='Hashing...';
  let bcrypt = dcodeIO.bcrypt;
  var newsalt = bcrypt.genSaltSync(12);
  var newhash = bcrypt.hashSync(password, newsalt);  
  password='';
  document.getElementById("setpwstatus").innerHTML='Updating...';
  mqttclient.publish(`/app/from/${clientId}/passwordset`,`${tokenId},${hash},${newhash}`, {qos: 0, retain: false});
  
});
  

</script>
