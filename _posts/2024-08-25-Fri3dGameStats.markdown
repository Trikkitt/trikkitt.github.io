---
layout: post
title:  "Fri3d 2024 Game Results"
date:   2024-08-25 06:00:00 +0100
categories: FRI3D2024
---

Fri3d Camp 2024 is over and so we must analyse the stats from this year's PolyGen game!

## General Stats

- Total players: 525
- Total players scoring over 1,000 points: 386
- Total players score 4,579,331
- Total unit captures: 12,437
- Total unit visits: 12,722
- Total output boosts: 1,280
- Players who set a codename: 52
- Players too lazy to set a codename: 473
- Maximum Units Online: 19
- Units Failed: 1 (Coder Dojo - Swapped out)
- Player reusing tokens from previous events: 3

Some players decided it was good to use a payment card or secure card to play:
- Total payment cards used: 6
- Unidentified Type: 5
- Mastercard: 1

The game records which audio clip it played to the player when they registered.  It doesn't save any card information or try to access the payment applications on the cards, it just queries the 2PAY.SYS file to see which payment applications exist on the card.  It doesn't try hard, and gives up quickly if it can't.  This is why most cards are simply identified as payment cards.  Players who used a payment card would have heard something like "Visa Debit card accepted, authorising donation to Polybius Biotech please wait... Just kidding.".

## Team Stats

| Team Name | Total Players | Highest Single Capture Score | Team Score | Team Visits | Total Unique Player Visits | Total Captures | Total Player Unique Captures | Units Discovered | Average Player Score | 
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | 
| GFY Corporation | 144 | 31960 | 1698113 | 3994 | 1216 | 3688 | 1043 | 2 | 11792.4514 | 
| Malevolant Enterprises | 116 | 20501 | 818573 | 2856 | 941 | 3024 | 857 | 5 | 7056.6638 | 
| Nepharodine Group | 178 | 48174 | 1442739 | 4394 | 1394 | 4124 | 1208 | 4 | 8105.2753 | 
| Rebels | 87 | 37558 | 619906 | 1478 | 627 | 1601 | 578 | 10 | 7125.3563 | 

## Units by total captures.

| Unit Name | Total Captures | 
| --- | ---: | 
| Showers Central | 1380 | 
| Washing Up Central | 1377 | 
| Piller in central area | 1197 | 
| Bar (Back) | 1104 | 
| Central Crossroads | 926 | 
| Infodesk | 832 | 
| Bar (Front) | 822 | 
| Central Sign | 765 | 
| Dome (Main Area) | 688 | 
| Chapel | 576 | 
| Shelter Path | 492 | 
| Passage to Coder Dojo | 429 | 
| Main Area Entrance | 424 | 
| Car park near chapel | 393 | 
| Coder Dojo | 258 | 
| Blokhut | 221 | 
| Stone Table | 163 | 
| Octo Benches | 149 | 
| Coder Dojo (old) | 120 | 
| PolyGen Home Base | 120 | 

## Top 10 Players

| Player Name | Total Score | Team Name | 
| --- | ---: | --- | 
| oenieke | 218851 | GFY Corporation | 
| Brixel2 | 142653 | GFY Corporation | 
| [Prowler] | 141639 | Nepharodine Group | 
| Mopkop | 135985 | Rebels | 
| [Spider-Gwen] | 94021 | GFY Corporation | 
| Monkey.D.purple | 93144 | Nepharodine Group | 
| [The Meddling Monk] | 87202 | Malevolant Enterprises | 
| [Happy Hogan] | 72319 | Rebels | 
| Benny | 71150 | GFY Corporation | 
| Fofi | 69917 | Malevolant Enterprises | 


## Unit Battery Monitoring

Each unit reports its battery voltage regularly.  The following shows how each unit was doing, and as you can see, one unit wasn't fully charged when deployed!

![Unit Voltage Graph](https://gen.polyb.io/assets/img/Fri3dCampVoltages.png "Unit Voltage History")
You can also access the more interactive version if the game server is online on <https://scores.gen.polyb.io/public/question/4d8249a6-bb09-4ad9-b7ae-222e9e3b4ee2>

Very pleased to report that they still had a fair bit of power left even at the end of the event.  The units with the most captures had the lowest battery voltage at the end of the event at 3.54 volts.

Units are regarded as low battery and start sending alerts when they reach 3.35 volts.  When they reach 3.05 volts they are regarded as depleted and will power off.  There is a secondary protection built into the power regulator that will shutdown when the battery voltage drops to 2.5 volts.  Thankfully no recharging was required!

