<!DOCTYPE html>
<html>
  <head>
    <script src="webrtc.js"></script>
    <title>WebRTC Test</title>
  </head>
  
  <body>
    <video id="localVideo" autoplay/>
    <script>
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
      
      var peerConn= new RTCPeerConnection();
peerConn.onaddstream = function (evt) {
  var videoElem = document.createElement("video");
  document.appendChild(videoElem);
  videoElem.src = URL.createObjectURL(evt.stream);
};   navigator.getUserMedia({video: true}, function(stream) {
  videoElem.src = URL.createObjectURL(stream);
  peerConn.addStream(stream);

  peerConn.setRemoteDescription(new RTCSessionDescription(offer), function() {
    peerConn.createAnswer(function(answer) {
      peerConn.setLocalDescription(new RTCSessionDescription(answer), function() {
        // send the answer to a server to be forwarded back to the caller
      }, error);
    }, error);
  }, error);
});</script>
  </body>
</html>
