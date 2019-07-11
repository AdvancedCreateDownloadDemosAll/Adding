
<!--
https://developer.jwplayer.com/jw-player/demos/
https://developer.jwplayer.com/jw-player/docs/developer-guide/api/javascript_api_reference/
https://github.com/jwplayer/jwdeveloper-demos/tree/master/demos/basic/add-download-button
https://github.com/jwplayer/jwdeveloper-demos/tree/master/demos/developer-showcase/multiple-playlists
https://github.com/jwplayer/jwdeveloper-demos/tree/master/demos/toolbox/live-streaming
https://github.com/jwplayer/jwdeveloper-demos/tree/master/demos/basic/live-tv
https://github.com/jwplayer/jwdeveloper-demos/tree/master/demos/customization/custom-icons
https://github.com/jwplayer/jwdeveloper-demos/tree/master/demos/developer-showcase/css-skin-floating-time-slider
https://github.com/jwplayer/jwdeveloper-demos/tree/master/demos/advanced/dynamic-ad-playback
https://github.com/jwplayer/jwdeveloper-demos/tree/master/demos/basic/audio-metadata
https://github.com/jwplayer/jwdeveloper-demos/tree/master/demos/developer-showcase/branded-player-ads
https://github.com/jwplayer/jwdeveloper-demos/tree/master/demos/developer-showcase/video-background

-->

### JW Player
---


#### Video Background
###### Use JW Player to display a video as a background.

```js
This demo includes:
on('complete')
on('firstFrame')
on('time')
```
###### Check out our JW Player Blog post for more details about this demo. [complete](https://www.jwplayer.com/blog/make-jw-player-websites-hero/)

```javascript
<script type="text/javascript">

var videoContainer = $('#js-background-video-container'),
    fullPlaylist;

function setupPlayer() {
  // This flag is used to prevent unnecessary cycles with the time event
  var isReady = false;

  var backgroundVideo = jwplayer('js-background-video').setup({
    autostart: true,
    controls: false,
    playlist: fullPlaylist,
    mute: true,
    repeat: true,
    stretching: 'fill',
    height: '100%',
    width: '100%'
  });


  /**
   * When the next playlist item is triggered, we can remove the video's opacity
   */
  backgroundVideo.on('firstFrame', function(e) {
    videoContainer.removeClass('is-fading');
  });

  /**
   * We use the video container's CSS transition-end event to queue up the next
   * video's poster image.
   */
  videoContainer.on('transitionend', function(e) {
    if (!videoContainer.hasClass('is-fading')) {
      setPosterImage();
    }
  }).trigger('transitionend');


  /**
   * Two seconds before the video ends, we take the poster image of the next-up
   * video and set it as the background of the container.
   */
  backgroundVideo.on('time', function(e) {
    if (e.position >= (e.duration - 2) && !isReady) {
      isReady = true;
      videoContainer.addClass('is-fading');
    }
  });


  /**
   * When the video is done, we reset the isReady flag to make sure we can check
   * the time event properly for the next video.
   */
  backgroundVideo.on('complete', function() {
    isReady = false;
  });


  /**
   * setPosterImage just makes sure the poster image is loaded and ready to go
   * before setting the video container background and fading the video out.
   */
  function setPosterImage() {
    var posterImage;
    // Get the next video's playlist index, so we can set the poster image for
    // the fadeout animation
    var nextIndex = backgroundVideo.getPlaylistIndex() + 1;
    nextIndex = nextIndex >= fullPlaylist.length ? 0 : nextIndex;
    posterImage = fullPlaylist[nextIndex]['image'];

    $('<img>').attr('src', posterImage).load(function() {
      $(this).remove();
      videoContainer.css('background-image', 'url(' + posterImage + ')');
    });
  }
}


/**
 * If we grab the playlist feed before setting up the player, we can manipulate
 * the playlist object and set up any specific adjustments. For instance, you
 * could shuffle the order of the playlist items.
 */
$.ajax({
  /**
   * Fun fact: You can use a single video's media ID with the Feeds API and it
   * will return a playlist with one video. Pretty nifty!
   */
  url: '//content.jwplatform.com/feeds/CSh1f2GU.json',
  dataType: 'JSON'
}).done(function(data) {
  /**
   * We store the playlist so we can set the background image of the next video
   * for a smooth transition between videos.
   */
  fullPlaylist = data.playlist;
  setupPlayer();
});

</script>
```

***
##### Branded Player Advertising
###### A demo of LogoBar's branded player ads and advanced event tracking capabilities. The LogoBar plugin uses JW Player's CSS Skinning model to insert branding and ad creatives.


```js
This demo includes:
on('time')
on('seeked')
on('seek')
on('pause')
on('play')
on('fullscreen')
on('resize')
on('firstFrame')
on('adImpression')
on('adComplete')
```
******LogoBar is a patented digital advertising technology that brands components of streaming media players, and empowers marketers to interact with audiences while a video is playing without disrupting or delaying the content they are viewing. LogoBar gives publishers and advertisers a more efficient and effective way to elevate brand awareness and engagement while improving the audiences video viewing experience. LogoBar was the first to be awarded with IAB Tech Lab’s VAST 3.0 and VPAID 2.0 compliance.****** 

******BENEFITS INCLUDE:
Stronger performance than standard pre-rolls (Based on Nielsen and Newlio study, August 2016)
58% higher CTRs; 21% higher brand recall; 67% higher likelihood to engage
51% less intrusive and 15X higher user retention.
Great for live streaming content, branded content, content syndication, AVOD and sponsorships.
New non-cannibalizing inventory generating incremental revenue without having to develop more content
Premium solution to package/sell adding value to existing ads
Stronger performance leads to higher client retention
Guaranteed visibility
Not susceptible to ad blocking
LogoBar is VAST 3.0 and VPAID 2.0 compliant. Below are a sample plugin and LogoBar VAST tag that you can add to any standard JW Player javascript embed instance with a Premium license or above. Learn more at****** [logobar.tv](http://logobar.tv/)


```js
Add the following plugin as a script tag on your page:

"https://cdn2.logobar.tv/plugin/jw/lbplugin4jw.min.js"

```
##### Then add the following VAST tag next to your JW player instance:

```javascript

<script type="text/javascript">

	var lbvast = ["https://vast.logobar.tv/lbvast.php?cid=90kre6s5c66m"];

</script>
```

---
##### Audio Streaming + Metadata
###### Extract timed metadata from a live audio stream and use it to display information such as title, artist, and poster image.

```javascript
This demo includes:
on('meta')
on('error')
```
##### TITLE:
##### ARTIST:
###### *Note that an HLS stream must include timed metadata to achieve this behavior.*

```javascript
<script type="text/javascript">

  var titleDiv = document.getElementById('title');
  var artistDiv = document.getElementById('artist');
  var imageDiv = document.getElementById('image');
  var player = jwplayer('player');

  // Set up the player with an HLS stream that includes timed metadata
  player.setup({
    file: 'assets/index.m3u8'
  });

  // Retrieve metadata
  player.on('meta', function(e) {
    if (e.metadata) {
      var title = e.metadata.title;
      var artist = e.metadata.artist;
      var imageUrl = e.metadata.url;

      title ? titleDiv.innerHTML = title : titleDiv.innerHTML = 'Unknown';
      artist ? artistDiv.innerHTML = artist : artistDiv.innerHTML = 'Unknown';
      imageUrl ? imageDiv.src = imageUrl : imageDiv.src = 'assets/jwlogo.png';
    };
  });

  // Handle reset of player at end of content
  player.on('error', function(e) {
    if (e.message === 'The live stream is either down or has ended') {
      player.load({
        file: 'assets/index.m3u8'
      });
    }
  });

</script>
```

---


#### Custom Icons
###### This demo shows how to replace the player's default control icons with your own.
###### For a guide on how to use CSS to replace icons in the player see our documentation on [Custom Icons](https://developer.jwplayer.com/jw-player/docs/developer-guide/customization/css-skinning/custom-icons/) in the JW Player Developer Guide.
***
#### Dynamic Ad Playback
###### Trigger an ad to play at a selected point during a video.

 
```javascript
This demo includes:
on('play')
on('pause')
on('complete')
playAd(tag)
on('adComplete')
on('adSkipped')
on('adPlay')
on('adPause')
```
```javascript
<script type="text/javascript">

  var message = document.querySelector('.message');
  var tag = 'assets/preroll.xml';
  var player = jwplayer('player');

  player.setup({
    playlist: '//cdn.jwplayer.com/v2/media/hWF9vG66',
    advertising: {
      client: 'vast'
    }
  });

  player.on('play',function() {
    showButton();
  });

  player.on('pause',function() {
    hideButton('Unpause the video to continue.');
  });

  player.on('complete', function() {
    hideButton('Restart video to continue.');
  });

  player.on('adPlay', function() {
    hideButton('Ad playing, please wait (or skip it)')
  });

  player.on('adPause', function() {
    hideButton('Unpause the ad to continue.')
  });

  player.on('adSkipped', function() {
    showButton();
  });

  player.on('adComplete', function() {
    showButton();
  });

  function triggerAd() {
    player.playAd(tag);
  };

  function showButton() {
    message.innerHTML = 'Play an Ad';
    message.classList.add('button');
    message.addEventListener('click', triggerAd);
  };

  function hideButton(messageText) {
    message.innerHTML = messageText;
    message.classList.remove('button');
    message.removeEventListener('click', triggerAd);
  };

</script>
```


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

```javascript
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


