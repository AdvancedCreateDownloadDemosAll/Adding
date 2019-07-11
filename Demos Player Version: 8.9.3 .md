
<!--
https://developer.jwplayer.com/jw-player/demos/
https://developer.jwplayer.com/jw-player/docs/developer-guide/api/javascript_api_reference/
https://github.com/jwplayer/jwdeveloper-demos/tree/master/demos/basic/add-download-button
https://github.com/jwplayer/jwdeveloper-demos/tree/master/demos/developer-showcase/multiple-playlists
https://github.com/jwplayer/jwdeveloper-demos/tree/master/demos/toolbox/live-streaming
https://github.com/jwplayer/jwdeveloper-demos/tree/master/demos/basic/live-tv
https://github.com/jwplayer/jwdeveloper-demos/tree/master/demos/customization/custom-icons
https://github.com/jwplayer/jwdeveloper-demos/tree/master/demos/developer-showcase/css-skin-floating-time-slider

-->

### JW Player
---

#### Custom Icons
###### This demo shows how to replace the player's default control icons with your own.
###### For a guide on how to use CSS to replace icons in the player see our documentation on [Custom Icons](https://developer.jwplayer.com/jw-player/docs/developer-guide/customization/css-skinning/custom-icons/) in the JW Player Developer Guide.

***
##### CSS Skin with Floating Time Slider
###### Example of customizing the player theme with floating time slider.

```javascript
<script type="text/javascript">

var player = jwplayer('player');

var file = '1b02B03R';

player.setup({
	file: '//content.jwplatform.com/manifests/' + file + '.m3u8',
	tracks: [{
		kind: 'thumbnails',
		file: '//content.jwplatform.com/strips/' + file + '-120.vtt'
	}],
	autostart: true,
	width: '100%',
  skin: {
  	name: 'custom',
		url: 'css/build.css'
  }
});

</script>
```


***
---
#### Adding a Download Button

###### This demo includes:
> addButton()

```javascript
<script type="text/javascript">

var player = jwplayer('player');

player.setup({
  playlist: 'https://cdn.jwplayer.com/v2/media/8L4m9FJB'
});

player.addButton(
  //This portion is what designates the graphic used for the button
  "./assets/download.svg",
  //This portion determines the text that appears as a tooltip
  "Download Video",
  //This portion designates the functionality of the button itself
  function() {
    //With the below code, we're grabbing the file that's currently playing
    window.location.href = player.getPlaylistItem()['file'];
  },
  //And finally, here we set the unique ID of the button itself.
  "download"
);

</script>
```
***
---

#### [Simulating Live TV](https://developer.jwplayer.com/jw-player/demos/basic/live-tv)
###### A demo that simulates live TV. The playlist item and position are determined by system date/time.

```m3u
This demo includes:
on('ready')
play()
seek()
playlistItem()

```

```javascript
<script type="text/javascript">

var playerInstance = jwplayer("myElement");

// In order for this demo to work, duration must be included for each playlist item.

playerInstance.setup({
    "playlist": "//content.jwplatform.com/feeds/DrqpQIzP.rss"
});

playerInstance.on('displayClick', function() {
	playerInstance.pause();
});
    
playerInstance.on('ready', function() {
    var seconds = new Date().getMinutes()*60 + new Date().getSeconds();
    var playlist = playerInstance.getPlaylist();
    var offset = 0;

    for (var index=0; index < playlist.length; index++) {
        var duration = Math.round(playlist[index]['duration']);
        if(offset + duration > seconds) {
            playerInstance.playlistItem(index);
            playerInstance.seek(seconds-offset);
            break;
        } else {
            offset += duration;
        }
    }
});

</script>
```
***

#### [Live Player](https://developer.jwplayer.com/jw-player/demos/toolbox/live-streaming) Using [Live Streaming](https://wowzaec2demo.streamlock.net/live/bigbuckbunny/playlist.m3u8)
###### This demo shows the JW Player's live streaming capabilities with multiple streams to choose from.

###### Utilize live streaming with JW Platform and JW Player by using JW Live as your live streaming provider, using JW Platform to manage an external live stream, or using JW Player only. [Visit our support site](https://support.jwplayer.com/customer/portal/articles/2549038-live-streaming) for a tutorial on live streaming with JW Platform and JW Player, or contact us for more information on JW Live.



***
---
#### Custom UI for JW8
###### ***```How to modify the JW8 player CSS with custom fonts, an inline timeslider, and branded colors in the control bar & settings menu.```***

```javascript

<script type="text/javascript">

  var player = jwplayer('player').setup({
    playlist: '//cdn.jwplayer.com/v2/media/jumBvHdL',
    skin: {
      name: 'alaska',
      url: 'skin/alaska.css'
    }
  });

  player.on('ready', function() {
    // Move the timeslider in-line with other controls
    var playerContainer = player.getContainer();
    var buttonContainer = playerContainer.querySelector('.jw-button-container');
    var spacer = buttonContainer.querySelector('.jw-spacer');
    var timeSlider = playerContainer.querySelector('.jw-slider-time');
    buttonContainer.replaceChild(timeSlider, spacer);

    // Gather all animating elements into an array
    var titlePrimary = playerContainer.querySelector('.jw-title-primary');
    var titleSecondary = playerContainer.querySelector('.jw-title-secondary');
    var previewImage = playerContainer.querySelector('.jw-preview');
    var playIcon = playerContainer.querySelector('.jw-display-icon-display');
    var animatedItems = [titlePrimary, titleSecondary, previewImage, playIcon];

    // Add animation class to each element in array
    playerContainer.addEventListener('mouseenter', function() {
      animatedItems.forEach(function(element) {
        element.classList.add('shift');
      });
    });

    // Remove animation class from each element in array
    playerContainer.addEventListener('mouseleave', function() {
      animatedItems.forEach(function(element) {
        element.classList.remove('shift');
      });
    });
  });

</script>
```

###### ***```Please Note: This player implementation is a Proof of Concept only provided to show the possibilities of the JW Player and Platform and should not be taken as an offer to create, edit or maintain custom integration or development.```***


