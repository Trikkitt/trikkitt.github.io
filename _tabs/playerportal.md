---
title: Player Portal
icon: fas fa-info-circle
order: 3
---

View your individual stats for the game here.  You'll need your RFID card or know its UID.  You can also set your codename to something you'd like rather than the randomly generated one.  To use this portal you must have already registered at one of the physical devices using your RFID card, so if you haven't - go out and find one then come back here.

<input type="radio" id="stats" name="portaltype" value="stats" checked="checked">
<label for="stats">Stats and Info</label><br>
<input type="radio" id="codename" name="portaltype" value="codename">
<label for="codename">Set Codename</label><br>

<p id="scantext">Use your phone to scan your RFID card.  Click the button then present the card to your phone.</p>
<button id="scanButton">Scan RFID Card</button>
<p id="scanstatus"></p>

Or you can manually enter the UID of your RFID card here:<br>
<input type="text" id="UID" name="UID">
<button id="manualButton">Submit</button>
<p id="manualstatus"></p>

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
      document.getElementById("UID").value=tokenid;
      setCookie("RFIDUID",tokenid,6);
      if (document.getElementById("stats").checked) {
        window.location.href = "https://scores.gen.polyb.io/public/dashboard/84d4c4b4-c4dc-4412-91a8-515c7595e398?token_id=".concat(tokenid,"#hide_parameters=token_id");
      } else {
        window.location.href = "https://gen.polyb.io/codename/?token_id=".concat(tokenid,"#hide_parameters=token_id");
      }
    });

});
  
}

tempUID=getCookie("RFIDUID");
if (tempUID) {
  document.getElementById("UID").value=tempUID;
}
  
manualButton.addEventListener("click", async () => {
  document.getElementById("manualstatus").innerHTML="";
  regexp = /^[0-9a-fA-F]+$/;
  let myUID=document.getElementById("UID").value;
  myUID=myUID.replaceAll(":","");
  myUID=myUID.replaceAll(" ","");
  if (regexp.test(myUID)) {
    if (myUID.length > 7) {
      if (myUID.length < 21 ) {
        myUID=myUID.padEnd(20,"0");
        document.getElementById("UID").value=myUID;
        setCookie("RFIDUID",myUID,6);
        if (document.getElementById("stats").checked) {
          window.location.href = "https://scores.gen.polyb.io/public/dashboard/84d4c4b4-c4dc-4412-91a8-515c7595e398?token_id=".concat(myUID,"#hide_parameters=token_id");
        } else {
          window.location.href = "https://gen.polyb.io/codename/?token_id=".concat(myUID,"#hide_parameters=token_id");
        }
      } else {
        document.getElementById("manualstatus").innerHTML="UID too long";
      }
    } else {
      document.getElementById("manualstatus").innerHTML="UID too short";
    }
  } else {
    document.getElementById("manualstatus").innerHTML="Invalid UID.";
  }
});
  
function setCookie(cname, cvalue, exdays) {
  const d = new Date();
  d.setTime(d.getTime() + (exdays*24*60*60*1000));
  let expires = "expires="+ d.toUTCString();
  document.cookie = cname + "=" + cvalue + ";" + expires + ";path=/";
}

function getCookie(cname) {
  let name = cname + "=";
  let decodedCookie = decodeURIComponent(document.cookie);
  let ca = decodedCookie.split(';');
  for(let i = 0; i <ca.length; i++) {
    let c = ca[i];
    while (c.charAt(0) == ' ') {
      c = c.substring(1);
    }
    if (c.indexOf(name) == 0) {
      return c.substring(name.length, c.length);
    }
  }
  return "";
}
</script>


