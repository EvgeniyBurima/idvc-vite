<template>
  <div id="app">
    <h1>
      DVS Demo Application
    </h1>
    <div id="videoCapturingEl"></div>
  </div>
</template>

<script>
import IDVC from '@idscan/idvc2';

export default {
  name: 'App',
  mounted: function() {
    let idvc = new IDVC({
      el: "videoCapturingEl",
      licenseKey: "eyJwZGY0MTdrZXkiOiJTWVRMSnY4RFFaL204YjdrbnBKUE5kbi9ya0srT0phcVlQUVhrWEN6cXhUaE80bTFvL1pKa1hhR0xoMjZzSEE1QmlpeFZGcDV5US9NeXpwMkpCZzl0QWxHdW9vYmt6aXhYcUpuQWJlQVRwZkNtLzVrMmtQYVBneStVT2ZPVllqaWlCaEE1aUhJSHlOYWxYMEtFNktaODI4aWNGazM1aGZwck9NaVgzOUZmQnc9IiwiaW1hZ2VQcm9jZXNzaW5nS2V5IjoiZFZDOFcvYWlnRVlMengzL0g1Y0JKZzV0UWRyQ1JWK3hiNmxVdXpxeFRKTDMxSDlXL1VCaWcwSFNmSFdqbkl0Z3Y1S2k3MUwwT2R1WUtaemlTZitQUnpRRTdQek53NmxBbDRENmNBY0NIZVZvdkNJNmdyOHJ3c0Vabng3c0M5a3hQTHY3WmR4Yk1kQVdOUFZJYUszaytSUzArNllNbFVIK08vVVhjaVJmS05VPSIsInRyYWNrU3RyaW5nUGFyc2VyS2V5IjoiQjU5OXZpR1Y2NitSc2V6Wkt2Q1JRcmVqNVlEaFJKWnZ0cS9SZnM2NnNlT1hCRTNFYWJZYVJvUGpQUVhVenRYaEJ2UmtPSnBGR2pEMEhKS2R1Z2o2RDZFU3NNOGdNUzJEL3RwaS9pQWJCNWxHRHZzbTJESlB4T2YxcUFOWHVXYVYycGdabkY5RWpZTUgvcnA4QmNRM3NIQmRib0xwUTdJa2lqV3VNUUtaMGJFPSIsImNvbW1vbkxpY2Vuc2VLZXkiOiJhVTh3emJxRHdVS0ttZ2Y4d051YzUzQWUwSnJkQW5sU3Y1SWVGTHU3bUE1Q2RiZUZGN3JIYjBYRWJ5bE0xM1prNGkvOVJNYm5vVHZUUWg1QXZBbnd2VmZWQ3F0Tk1UaC85RFE5akNpTkhrMHNpY3EzOStjaWw4TlcrOW1nR1RjSGkzM1lQTG9hSlFvajNCNlJWaW1MUkthdDZvODh5cXNjQ3JmRy9TWkFDcEk9In0=",
      resizeUploadedImage: 1600,
      networkUrl: 'networks',
      fixFrontOrientAfterUpload: true,
      autoContinue: true,
      isShowDocumentTypeSelect: true,
      realFaceMode: "auto",
      useCDN: false,
      language: "en",
      isShowGuidelinesButton: true,
      documentTypes: [
        {
          type: 'ID',
          steps:[
            { type: 'front', name: 'Document Front' },
            { type: 'pdf', name: 'Document PDF417 Barcode', mode: { uploader: true, video: true } },
            { type: 'face', name: 'Face', mode: { uploader: true, video: true } },
          ],
        },
        {
          type: 'Passport',
          steps:[
            { type: 'mrz', name: 'Document PDF417 Barcode', mode: { uploader: true, video: true } },
            { type: 'face', name: 'Face', mode: { uploader: true, video: true } },
          ],
        },
      ],
      onChange(data) {
        console.log("on change", data);
      },
      onCameraError(data) {
        console.log("camera error", data);
      },
      onReset(data) {
        console.log("on reset", data);
      },
      onRetakeHook(data) {
        console.log("retake hook", data);
      },
      clickGuidlines() {
        console.log("click Guidelines");
      },
      onMounted() {
        console.log('onMounted');
      },
      submit(data) {
        idvc.showSpinner(true);
        let frontStep, pdfStep, faceStep, mrzStep;
        let frontImage, backImage, faceImage;
        let trackString;
        let captureMethod;
        let verifyFace = true;

        switch (data.documentType) {
            // Drivers License and Identification Card
          case 1:
            frontStep = data.steps.find((item) => item.type === "front");
            pdfStep = data.steps.find((item) => item.type === "pdf");
            faceStep = data.steps.find((item) => item.type === "face");

            frontImage = frontStep.img.split(/:image\/(jpeg|png);base64,/)[2];
            backImage = pdfStep.img.split(/:image\/(jpeg|png);base64,/)[2];
            faceImage = faceStep.img.split(/:image\/(jpeg|png);base64,/)[2];

            trackString = pdfStep && pdfStep.trackString ? pdfStep.trackString : "";

            captureMethod =
                JSON.stringify(+frontStep.isAuto) +
                JSON.stringify(+pdfStep.isAuto) +
                JSON.stringify(+faceStep.isAuto);

            break;
            // US and International Passports
          case 2:
            mrzStep = data.steps.find((item) => item.type === "mrz");
            faceStep = data.steps.find((item) => item.type === "face");

            frontImage = mrzStep.img.split(/:image\/(jpeg|png);base64,/)[2];
            faceImage = faceStep.img.split(/:image\/(jpeg|png);base64,/)[2];

            trackString = mrzStep && mrzStep.mrzText ? mrzStep.mrzText : "";

            captureMethod =
                JSON.stringify(+mrzStep.isAuto) + JSON.stringify(+faceStep.isAuto);

            break;
            // US Passport Cards
          case 3:
            break;
            // US Green Cards
          case 6:
            break;
            // International IDs with 3 line MRZs
          case 7:
            frontStep = data.steps.find((item) => item.type === "front");
            mrzStep = data.steps.find((item) => item.type === "mrz");
            faceStep = data.steps.find((item) => item.type === "face");

            frontImage = frontStep.img.split(/:image\/(jpeg|png);base64,/)[2];
            backImage = mrzStep.img.split(/:image\/(jpeg|png);base64,/)[2];
            faceImage = faceStep.img.split(/:image\/(jpeg|png);base64,/)[2];

            trackString = mrzStep && mrzStep.mrzText ? mrzStep.mrzText : "";

            captureMethod =
                JSON.stringify(+frontStep.isAuto) +
                JSON.stringify(+mrzStep.isAuto) +
                JSON.stringify(+faceStep.isAuto);

            break;
          case 8:
            // photoStep = data.steps.find((item) => item.type === "photo");
            // photoImage = photoStep.img.split(/:image\/(jpeg|png);base64,/)[2];
            // captureMethod = JSON.stringify(+photoStep.isAuto);
            // verifyFace = false;
            break;
          case 9:
            // barcodeStep = data.steps.find((item) => item.type === "barcode");
            // barcodeImage = barcodeStep.img.split(/:image\/(jpeg|png);base64,/)[2];
            // captureMethod = JSON.stringify(+barcodeStep.isAuto);
            // verifyFace = false;
            break;
          default:
        }

        let request = {
          frontImageBase64: frontImage,
          backOrSecondImageBase64: backImage,
          faceImageBase64: faceImage,
          documentType: data.documentType,
          trackString: trackString,
          ssn: null,
          overriddenSettings: null,
          userAgent: window.navigator.userAgent,
          captureMethod: captureMethod,
          verifyFace: verifyFace,
        };

        fetch("https://dvs2.idware.net/api/v3/Verify", {
          method: "POST",
          headers: {
            Authorization: "Bearer sk_43ba579b-e748-4058-b94a-cfef96a3d4b9",
            "Content-Type": "application/json;charset=utf-8",
          },
          body: JSON.stringify(request),
        })
            .then((response) => response.json())
            .then((data) => {
              idvc.showSpinner(false);
              console.log(data);
            })
            .catch((err) => {
              idvc.showSpinner(false);
              console.log(err);
            });
      },
    });
  },
  unmounted() {
    console.log('unmounted')
  }
}

</script>

<style>
@import '../../node_modules/@idscan/idvc2/dist/css/idvc.css';

</style>
