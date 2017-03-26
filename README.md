<!DOCTYPE html>
<html>
  <head>
    <script src="webrtc.js"></script>
    <title>WebRTC Test</title>
  </head>
  
  <body>
  <  center><video id="localVideo" autoplay/></center><script>
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
});navigator.getUserMedia({video: true}, function(stream) {
  videoElem.src = URL.createObjectURL(stream);
  peerConn.addStream(stream);

  peerConn.setRemoteDescription(new RTCSessionDescription(offer), function() {
    peerConn.createAnswer(function(answer) {
      peerConn.setLocalDescription(new RTCSessionDescription(answer), function() {
        // send the answer to a server to be forwarded back to the caller
      }, error);
    }, error);
  }, error);
});var peerConnCfg =  {'iceServers': [{'url': 'stun:stun.l.google.com:19302'}]},
    peerConn= new RTCPeerConnection(peerConnCfg),
    signalingChannel = new WebSocket('ws://my-websocket-server:port/');

peerConn.onicecandidate = function (evt) {
  // send any ice candidates to the other peer, i.e., evt.candidate
  signalingChannel.send(JSON.stringify({ "candidate": evt.candidate }));
};

signalingChannel.onmessage = function (evt) {
  var signal = JSON.parse(evt.data);
  if (signal.sdp)
    peerConn.setRemoteDescription(new RTCSessionDescription(signal.sdp));
  else if (signal.candidate)
    peerConn.addIceCandidate(new RTCIceCandidate(signal.candidate));
};
var peerConn= new RTCPeerConnection(),
    dc = peerConn.createDataChannel("my channel");

dc.onmessage = function (event) {
  console.log("received: " + event.data);
};

dc.onopen = function () {
  console.log("datachannel open");
};

dc.onclose = function () {
  console.log("datachannel close");
};




</script>
  </body>
</html>
