---
title: Set Codename
permalink: /codename/
layout: post
---

From here you can set your codename.

Password: <input type="password" id="existingpw" name="existingpw">
<input type="checkbox" id="nopw" name="nopw" value="nopw">
<label for="nopw">I haven't set a password yet.</label><br>
<button id="getCodename">Retrieve Codename</button>
<br><br>

<p id="codenametext" visible=false >Codename: </p><input type="text" id="codename" name="codename" visible=false>

<script src="<https://unpkg.com/mqtt/dist/mqtt.min.js>"></script>

<script>
   // Globally initializes an mqtt variable
   console.log(mqtt)
</script>

<script>
  const clientId='web_' + Math.random().toString(16).substr(2, 8)
  const host='wss://scores.gen.polyb.io:8002/mqtt'
  const option = {
    keepalive: 60,
    clientId: clientId,
    protocolId: 'MQTT',
    protocolVersion: 4,
    clean: true,
    reconnectPeriod: 1000,
    connectTimeout: 30 * 1000
  }
  console.log('Connection to MQTT...')
  const client=mqtt.connection(host,options)
  client.on('error',(err) => {
    console.log('Connection error: ', err);
    client.end();
  })
  client.on('reconnect', () => {
    console.log('Reconnecting...');
  })
  client.on('connect', () => {
    console.log('MQTT Connected: ${clientId}');
    client.subscribe('/app/to/${clientId}/name', {qos: 0});
    client.subscribe('/app/to/${clientId}/error', {qos: 0});
  })
  client.on('message', (topic, message, packet) => {
    console.log(`Received Message: ${message.toString()} On topic: ${topic}`)
    if (topic = '/app/to/${clientId}/name') {
      document.getElementById("codenametext").innerHTML='Codename: ';
      document.getElementById("codenametext").visible=true;
      document.getElementById("codename").visible=true;
      document.getElementById("codename").value=message;
      
    }
    if (topic = '/app/to/${clientId}/error') {
      document.getElementById("codenametext").innerHTML=message;
      document.getElementById("codenametext").visible=true;
      document.getElementById("codename").visible=false; 
    }
  })

  
getCodename.addEventListener("click", async () => {
  const searchParams = new URLSearchParams(window.location.search);
  if (searchParams.has('token_id')) {
    document.getElementById("codenametext").innerHTML='Checking...';
    document.getElementById("codenametext").visible=true;
    tokenId=searchParams.get('token_id');
    pw=document.getElementById("existingpw").value;
    client.publish('/app/from/${clientId}/namequery','${tokenId},${pw}', {qos: 0, retain: false});
  } else {
      document.getElementById("codenametext").innerHTML='No token ID';
      document.getElementById("codenametext").visible=true;
  }
})
</script>
