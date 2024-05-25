---
title: Set Codename
permalink: /codename/
layout: post
---

From here you can set your codename.

Password: <input type="password" id="existingpw" name="existingpw">
<p> </p> <button id="getCodename">Retrieve Codename</button><br/>
If you haven't set a password yet then leave the password field blank.
<br><br>

<p id="codenametext" ></p><input type="hidden" id="codename" name="codename" visible=false>

<script src="https://unpkg.com/mqtt/dist/mqtt.min.js"></script>
<script src="/assets/js/bcrypt.min.js"></script>

<script>
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
    mqttclient.subscribe(`/app/to/${clientId}/error`, {qos: 0});
  });
  mqttclient.on('message', (topic, message, packet) => {
    if (topic = `/app/to/${clientId}/name`) {
      document.getElementById("codenametext").innerHTML='Codename: ';
      document.getElementById("codename").type='text';
      document.getElementById("codename").value=message;
      
    }
    if (topic = `/app/to/${clientId}/error`) {
      document.getElementById("codenametext").innerHTML=message;
      document.getElementById("codename").type='hidden';
    }
  });

  
getCodename.addEventListener("click", async () => {
  const searchParams = new URLSearchParams(window.location.search);
  if (!searchParams.has('token_id')) {
    document.getElementById("codenametext").innerHTML='No token ID';
    return;
  }
  tokenId=searchParams.get('token_id');
  password=document.getElementById("existingpw").value;
  
  if (password.length>0) {
    if (password.length<12) {
      document.getElementById("codenametext").innerHTML='Password invalid';
      return;
    }
    if (password.length>72) {
      document.getElementById("codenametext").innerHTML='Password invalid';
      return;
    }    
  } else {
    password='PolyGenNewUser';
  }
  var bcrypt = dcodeIO.bcrypt;
  document.getElementById("codenametext").innerHTML='Hashing...';
  salt='$2b$12$PG'.concat(tokenId);
  pw=document.getElementById("existingpw").value;
  hash=bcrypt.hashSync(pw, salt);
  pw='';
  document.getElementById("codenametext").innerHTML='Checking...';
  mqttclient.publish(`/app/from/${clientId}/namequery`,`${tokenId},${hash}`, {qos: 0, retain: false});
});
</script>
