# FaceSearchResponse<a name="streaming-video-kinesis-output-reference-facesearchresponse"></a>

Information about a face detected in a streaming video frame and the faces in a collection that match the detected face\. You specify the collection in a call to [CreateStreamProcessor](https://docs.aws.amazon.com/rekognition/latest/APIReference/API_CreateStreamProcessor.html)\. For more information, see [Working with streaming video events](streaming-video.md)\. 

**DetectedFace**

Face details for a face detected in an analyzed video frame\.

Type: [DetectedFace](streaming-video-kinesis-output-reference-detectedface.md) object

**MatchedFaces**

An array of face details for faces in a collection that matches the face detected in `DetectedFace`\.

Type: [MatchedFace](streaming-video-kinesis-output-reference-facematch.md) object array