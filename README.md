&lt;!DOCTYPE html>
&lt;html>
  &lt;head>
    &lt;script src="webrtc.js">&lt;/script>
    &lt;title>WebRTC Test&lt;/title>
  &lt;/head>
  
  &lt;body>
    &lt;video id="localVideo" autoplay/>
    &lt;script>
      window.addEventListener("load", function (evt) {
        navigator.getUserMedia({ audio: true, video: true},
          function(stream) {
            var video = document.getElementById('localVideo');
            video.src = window.URL.createObjectURL(stream);
          },
          function(err) {
            console.log("The following error occurred: " + err.name);
          }
        );
      });
    &lt;/script>
  &lt;/body>
&lt;/html>
