---
title: Player Portal
icon: fas fa-info-circle
order: 2
---

Individual player portal coming here soon...


<p id="scantext">Use your phone to scan your RFID card.  Click the button then present the card to your phone.</p>
<button id="scanButton">Scan RFID Card</button>
<p id="scanstatus"></p>

<script>
  
if (!("NDEFReader" in window)) {
  document.getElementById("scanButton").disabled = true;
  document.getElementById("scantext").innerHTML="Your phone/browser doesn't support Web-NFC.  Chrome on Android required.";
} else {

const ndef = new NDEFReader();

scanButton.addEventListener("click", async () => {
    const ndef=new NDEFReader();
    document.getElementById("scanstatus").innerHTML="Scanning...";
    await ndef.scan();
    ndef.addEventListener("readingerror", () => {
      document.getElementById("scanstatus").innerHTML="No RFID card found.";
    });  
    ndef.addEventListener("reading", ({ message, serialNumber }) => {
      let tokenid=serialNumber.toUpperCase();
      tokenid=tokenid.replaceAll(":","");
      tokenid=tokenid.padEnd(20,"0");
      window.location.href = "https://scores.gen.polyb.io/public/dashboard/84d4c4b4-c4dc-4412-91a8-515c7595e398?token_id=".concat(tokenid,"#hide_parameters=token_id");
    });

});
  
}

</script>


