---
layout: ../../layout/MarkdownLayout.astro
title: OBS Scoreboard Project
description: "A tool to help streamers run esports tournaments."
pubDate: 2024-02-03
tags: ["Development"]
---

# OBS Scoreboard
## A tool to help run esports tournaments.

### Overview

The **scoreboard.html** overlay is added as a browser source to OBS.

Player and score information is entered into the **'StreamControl'** app, which outputs to **JSON** file to be read by the **HTML5** overlay and displayed to viewers.

![](/images/obs-scoreboard/scoreboard02.webp)

The overlay has multiple layouts depending on the game selected in the **'StreamControl'** app. Take note of the repositioned scorebars to align better with the HUD elements in the second example. Also take note of the logo repositioning and scaling to avoid important HUD elements.

![](/images/obs-scoreboard/scoreboard01.webp)

I have also added support to display country flags and team name prefixes, as well a downtime overlay, with customisable messages and a countdown timer.
A lower thirds commentary overlay is also being worked on.

[Download on GitHub](https://github.com/mx3th/obs-scoreboard)

### Basic Customisation

For basic changes, such as colour and font theming, simply modify the following variables in the **style.scss** file.

For changing the colours.
```
$accent-color:#995afc;
$font-color: white;
```
For changing the font.
```
@font-face {
  src: url("../fonts/MyFont.otf");
  font-family: "MyFont"; }
```
Then simply compile the **style.scss** as **style.css**.

Alternatively you can make changes to **style.css** directly for any more drastic changes.

### Changing the logo

Place your logo images inside **/_overlays/imgs/**. You can replace my logos with your own, and you can even add more by adding to the following code block inside **scoreboard.html**.
```
<div id="logoWrapper">
  <img id="logo1" class="logos" src="imgs/logo1.png"></img>
  <img id="logo2" class="logos" src="imgs/logo2.png"></img>
</div>
```
The stream will automatically cycle through the defined logos when live.

(Note: The default logo dimensions are 240px x 140px)

### Changing the flag style

![](/images/obs-scoreboard/flag.webp)

The download comes with multiple flag styles that can be changed depending on preference.
Change the path to the flags to **imgs/iso/64shiny/** inside **/js/scoreboard.js** for the style shown above.

```
$("#p1Country").attr("src","imgs/iso/64flat/"+p1Country+".png").on("error",function(){
    $("#p1Country").attr("src", "imgs/iso/64flat/unknown.png");
});

$("#p2Country").attr("src","imgs/iso/64flat/"+p2Country+".png").on("error",function(){
    $("#p2Country").attr("src", "imgs/iso/64flat/unknown.png");
});
```
Remember to repeat for player 2 side.

## Adding layouts

It's possible to add new layouts to support other games. To do this we will first need to add our new game to the StreamControl app.
Add your game as a combo item inside **/sc/layout.xml**.

```
  <comboBox id="game" editable="1" x="60" y="100" width="120" height="20">
   <comboItem>UNI2</comboItem>
   <comboItem>MBTL</comboItem>
   <comboItem>YOURGAMEHERE</comboItem>
  </comboBox>
```

Now open **/js/scoreboard.js** and find **function scoreboard()**:

```
function scoreboard(){

		if(startup == true){
			game = scObj['game'];
			gameHold = game; //sets 'game' value into placeholder div

			if(game == 'MBTL' || game == 'UNI2'){
				// Shifts the scoreboard BG wrappers down to match HP bars
				offset = document.getElementById("leftBGWrapper").offsetTop;
				TweenMax.fromTo('#leftBGWrapper', 0.5, {css:{y: offset}}, {css:{y: adjust1, x: '0px'}})
				TweenMax.fromTo('#rightBGWrapper', 0.5, {css:{y: offset}}, {css:{y: adjust1, x: '0px'}})
				TweenMax.set('#leftWrapper',{css:{y: '4px', x: '0px'}});
				TweenMax.set('#rightWrapper',{css:{y: '4px', x: '0px'}});
			}
			else if(game == 'TEKKEN8' || game == 'BBCF'){
				// Shifts the scoreboard text wrappers up to match HP bars
				TweenMax.set('#leftWrapper',{css:{y: adjust2, x: '0px'}});
				TweenMax.set('#rightWrapper',{css:{y: adjust2, x: '0px'}});
			}
```

It's likely that your new game may work with one of the pre-existing layouts, in which case all you'd need to do is add your game alongside the most appropriate layout.
e.g:

```
if(game == 'MBTL' || game == 'UNI2' || game == 'YOURGAMEHERE'){
```

However if none of these work for you, you can create a new layout by adding to the if statement. In the below example I will be adding Guilty Gear Strive:

```
else if(game == 'GGST'){
				offset = document.getElementById("leftBGWrapper").offsetTop;
				TweenMax.fromTo('#leftBGWrapper', 0.5, {css:{y: offset}}, {css:{y: adjust3, x:'-64px'}})
				TweenMax.fromTo('#rightBGWrapper', 0.5, {css:{y: offset}}, {css:{y: adjust3, x:'64px'}})
				TweenMax.set('#leftWrapper',{css:{y: '32px', x: '-64px'}});
				TweenMax.set('#rightWrapper',{css:{y: '32px', x: '64px'}});
			}
```

Simply adjust the 'x' and 'y' positions to make room for new HUD elements.

NOTE: Be sure to copy any adjustments to **if(gameHold != game)** inside **function getData()**.

#### New layout example

![](/images/obs-scoreboard/ggst.webp)

In this example we have moved the scorebars further down to make use of the empty space, and adjusted the x axis positioning to avoid covering the round indicators (heart symbols).

#### Downtime layout example

![](/images/obs-scoreboard/downtime.webp)

[Download on GitHub](https://github.com/mx3th/obs-scoreboard)