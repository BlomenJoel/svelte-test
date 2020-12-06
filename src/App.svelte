<script>
  let data;

  function getUserMedia(options, successCallback, failureCallback) {
    var api =
      navigator.getUserMedia ||
      navigator.webkitGetUserMedia ||
      navigator.mozGetUserMedia ||
      navigator.msGetUserMedia;
    if (api) {
      return api.bind(navigator)(options, successCallback, failureCallback);
    }
  }

  var theStream;

  function getStream() {
    navigator.mediaDevices
      .getUserMedia({ video: true })
      .then(gotMedia)
      .catch((error) => console.error("getUserMedia() error:", error));

    function gotMedia(mediaStream) {
      const mediaStreamTrack = mediaStream.getVideoTracks()[0];
      const imageCapture = new ImageCapture(mediaStreamTrack);
      imageCapture.takePhoto().then((blob) => {
        console.log("blob", blob);
      });
      const capabilities = mediaStreamTrack.getCapabilities();
      console.log("capabilities", capabilities);
      const settings = mediaStreamTrack.getSettings();
      console.log("settings", settings);
      imageCapture
        .getPhotoCapabilities()
        .then(function (photoCapabilities) {
          console.log("photocapabilites", photoCapabilities);
        })

        .catch((error) => console.error("takePhoto() error:", error));
    }
  }

  function takePhoto() {
    if (!("ImageCapture" in window)) {
      alert("ImageCapture is not available");
      return;
    }

    if (!theStream) {
      alert("Grab the video stream first!");
      return;
    }

    var theImageCapturer = new ImageCapture(theStream.getVideoTracks()[0]);

    theImageCapturer
      .takePhoto()
      .then((blob) => {
        var theImageTag = document.getElementById("imageTag");
        theImageTag.src = URL.createObjectURL(blob);
      })
      .catch((err) => alert("Error: " + err));
  }
</script>

<h1>Hi this is the data we are able to retrieve</h1>
<p>{data}</p>
<p><button on:click={getStream}>Grab video</button></p>

<video autoplay style="height: 180px; width: 240px;"><track
    kind="captions" /></video>
