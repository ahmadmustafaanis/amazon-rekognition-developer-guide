# Detect faces in an image using an AWS SDK<a name="example_cross_DetectFaces_section"></a>

The following code example shows how to:
+ Save an image in an Amazon Simple Storage Service Amazon S3\) bucket\.
+ Use Amazon Rekognition \(Amazon Rekognition\) to detect facial details, such as age range, gender, and emotion \(smiling, etc\.\)\.
+ Display those details\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ Rust ]

**SDK for Rust**  
This documentation is for an SDK in preview release\. The SDK is subject to change and should not be used in production\.
 Save the image in an Amazon Simple Storage Service bucket with an **uploads** prefix, use Amazon Rekognition to detect facial details, such as age range, gender, and emotion \(smiling, etc\.\), and display those details\.   
 For complete source code and instructions on how to set up and run, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/rust_dev_preview/cross_service/detect_faces/src/main.rs)\.   

**Services used in this example**
+ Amazon Rekognition
+ Amazon S3

------

For a complete list of AWS SDK developer guides and code examples, see [Using Rekognition with an AWS SDK](sdk-general-information-section.md)\. This topic also includes information about getting started and details about previous SDK versions\.