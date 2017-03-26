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


<html>
  <head>
    <script src="webrtc.js"></script>
    <title>WebRTC Audio/Video-Chat</title>
  </head>
  
  <body>
    <video id="remoteVideo" autoplay></video>
    <video id="localVideo" autoplay muted></video>
    <input id="videoCallButton" type="button" disabled value="Video Call"/>
    <input id="endCallButton" type="button" disabled value="End Call"/>
    <script type="text/javascript">
      window.addEventListener("load", pageReady);
      
      const WebSocketServer = require('ws').Server,
  express = require('express'),
  https = require('https'),
  app = express(),
  fs = require('fs');

const pkey = fs.readFileSync('./ssl/key.pem'),
  pcert = fs.readFileSync('./ssl/cert.pem'),
  options = {key: pkey, cert: pcert, passphrase: '123456789'};
var wss = null, sslSrv = null;
 
// use express static to deliver resources HTML, CSS, JS, etc)
// from the public folder 
app.use(express.static('public'));

// start server (listen on port 443 - SSL)
sslSrv = https.createServer(options, app).listen(443);
console.log("The HTTPS server is up and running");

// create the WebSocket server
wss = new WebSocketServer({server: sslSrv});  
console.log("WebSocket Secure server is up and running.");

/** successful connection */
wss.on('connection', function (client) {
  console.log("A new WebSocket client was connected.");
  /** incomming message */
  client.on('message', function (message) {
    /** broadcast message to all clients */
    wss.broadcast(message, client);
  });
});
// broadcasting the message to all WebSocket clients.
wss.broadcast = function (data, exclude) {
  var i = 0, n = this.clients ? this.clients.length : 0, client = null;
  if (n < 1) return;
  console.log("Broadcasting message to all " + n + " WebSocket clients.");
  for (; i < n; i++) {
    client = this.clients[i];
    // don't send the message to the sender...
    if (client === exclude) continue;
    if (client.readyState === client.OPEN) client.send(data);
    else console.error('Error: the client state is ' + client.readyState);
  }
};      LoadModule proxy_wstunnel_module modules/mod_proxy_wstunnel.so

ProxyPass /websocket/ ws://localhost:3434/
ProxyPassReverse /websocket/ ws://localhost:3434/      
      var localVideoElem = null, remoteVideoElem = null, localVideoStream = null,
    videoCallButton = null, endCallButton = null,
    peerConn = null, wsc = new WebSocket('ws://my-web-domain.de/websocket/'),
    peerConnCfg = {'iceServers': 
      [{'url':'stun:stun.services.mozilla.com'}, {'url':'stun:stun.l.google.com:19302'}]
    };      
      function pageReady() {
  videoCallButton = document.getElementById("videoCallButton");
  endCallButton = document.getElementById("endCallButton");
  localVideo = document.getElementById('localVideo');
  remoteVideo = document.getElementById('remoteVideo');
  // check browser WebRTC availability 
  if (navigator.getUserMedia) {
    videoCallButton = document.getElementById("videoCallButton");
    endCallButton = document.getElementById("endCallButton");
    localVideo = document.getElementById('localVideo');
    remoteVideo = document.getElementById('remoteVideo');
    videoCallButton.removeAttribute("disabled");
    videoCallButton.addEventListener("click", initiateCall);
    endCallButton.addEventListener("click", function (evt) {
      wsc.send(JSON.stringify({"closeConnection": true }));
    });
  } else {
    alert("Sorry, your browser does not support WebRTC!")
  }
};      wsc.onmessage = function (evt) {
  var signal = JSON.parse( evt.data);
  if (!peerConn) answerCall();
  if (signal.sdp) {
    peerConn.setRemoteDescription( new RTCSessionDescription( signal.sdp));
  } else if (signal.candidate) {
    peerConn.addIceCandidate( new RTCIceCandidate( signal.candidate));
  } else if (signal.closeConnection){
    endCall();
  }
};      
      function initiateCall() {
  prepareCall();
  navigator.getUserMedia({"audio": true, "video": true }, function (stream) {
    localVideo.src = URL.createObjectURL( stream);
    peerConn.addStream( stream);
    createAndSendOffer();
  }, function (error) { console.log( error);});
};      function prepareCall() {
  peerConn = new RTCPeerConnection( peerConnCfg);
  peerConn.onicecandidate = onIceCandidateHandler;
  peerConn.onaddstream = onAddStreamHandler;
};

function onIceCandidateHandler( evt) {
  if (!evt || !evt.candidate) return;
  wsc.send(JSON.stringify({"candidate": evt.candidate }));
};

function onAddStreamHandler( evt) {
  videoCallButton.setAttribute("disabled", true);
  endCallButton.removeAttribute("disabled"); 
  remoteVideo.src = URL.createObjectURL( evt.stream);
};      function createAndSendOffer() {
  peerConn.createOffer(
    function (offer) {
      var off = new RTCSessionDescription( offer);
      peerConn.setLocalDescription( new RTCSessionDescription( off), 
        function() {
          wsc.send(JSON.stringify({"sdp": off }));
        }, 
        function(error) { 
          console.log( error);
        }
      );
    }, 
    function (error) { 
      console.log( error);
    }
  );
};      
      function answerCall() {
  prepareCall();
  // get the local stream, show it in the local video element and send it
  navigator.getUserMedia({ "audio": true, "video": true }, function (stream) {
    localVideo.src = URL.createObjectURL( stream);
    peerConn.addStream(stream);
    createAndSendAnswer();
  }, function(error) { console.log(error);});
};      function createAndSendAnswer() {
  peerConn.createAnswer(
    function (answer) {
      var ans = new RTCSessionDescription( answer);
      peerConn.setLocalDescription( ans, function() {
          wsc.send(JSON.stringify({"sdp": ans }));
        }, 
        function (error) { 
          console.log( error);
        }
      );
    },
    function (error) { 
      console.log( error);
    }
  );
}      
      function pageReady() {
  if(navigator.getUserMedia) {
    // ...some more code here...
    endCallButton.addEventListener("click", function (evt) {
      wsc.send(JSON.stringify({"closeConnection": true }));
    });
  } else {
    alert("Sorry, your browser does not support WebRTC!")
  }
};
function endCall() {
  peerConn.close();
  localVideoStream.getTracks().forEach( function (track) {
    track.stop();
  });
  localVideo.src = "";
  remoteVideo.src = "";
  videoCallButton.removeAttribute("disabled");
  endCallButton.setAttribute("disabled", true);
};







    </script>
  </body>
</html>
