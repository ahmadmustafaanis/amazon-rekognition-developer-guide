# Recognizing celebrities in an image<a name="celebrities-procedure-image"></a>

To recognize celebrities within images and get additional information about recognized celebrities, use the [RecognizeCelebrities](https://docs.aws.amazon.com/rekognition/latest/APIReference/API_RecognizeCelebrities.html) non\-storage API operation\. For example, in social media or news and entertainment industries where information gathering can be time critical, you can use the `RecognizeCelebrities` operation to identify as many as 64 celebrities in an image, and return links to celebrity webpages, if they're available\. Amazon Rekognition doesn't remember which image it detected a celebrity in\. Your application must store this information\. 

If you haven't stored the additional information for a celebrity that's returned by `RecognizeCelebrities` and you want to avoid reanalyzing an image to get it, use [GetCelebrityInfo](https://docs.aws.amazon.com/rekognition/latest/APIReference/API_GetCelebrityInfo.html)\. To call `GetCelebrityInfo`, you need the unique identifier that Amazon Rekognition assigns to each celebrity\. The identifier is returned as part of the `RecognizeCelebrities` response for each celebrity recognized in an image\. 

If you have a large collection of images to process for celebrity recognition, consider using [AWS Batch](https://docs.aws.amazon.com/batch/latest/userguide/) to process calls to `RecognizeCelebrities` in batches in the background\. When you add a new image to your collection, you can use an AWS Lambda function to recognize celebrities by calling `RecognizeCelebrities` as the image is uploaded into an S3 bucket\.

## Calling RecognizeCelebrities<a name="recognize-image-example"></a>

You can provide the input image as an image byte array \(base64\-encoded image bytes\) or as an Amazon S3 object, by using either the AWS Command Line Interface \(AWS CLI\) or the AWS SDK\. In the AWS CLI procedure, you upload an image in \.jpg or \.png format to an S3 bucket\. In the AWS SDK procedures, you use an image that's loaded from your local file system\. For information about input image recommendations, see [Working with images](images.md)\. 

To run this procedure, you need an image file that contains one or more celebrity faces\.

**To recognize celebrities in an image**

1. If you haven't already:

   1. Create or update an IAM user with `AmazonRekognitionFullAccess` and `AmazonS3ReadOnlyAccess` permissions\. For more information, see [Step 1: Set up an AWS account and create an IAM user](setting-up.md#setting-up-iam)\.

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 2: Set up the AWS CLI and AWS SDKs](setup-awscli-sdk.md)\.

1. Use the following examples to call the `RecognizeCelebrities` operation\.

------
#### [ Java ]

   This example displays information about the celebrities that are detected in an image\. 

   Change the value of `photo` to the path and file name of an image file that contains one or more celebrity faces\.

   ```
   //Copyright 2018 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   //PDX-License-Identifier: MIT-0 (For details, see https://github.com/awsdocs/amazon-rekognition-developer-guide/blob/master/LICENSE-SAMPLECODE.)
   
   package aws.example.rekognition.image;
   import com.amazonaws.services.rekognition.AmazonRekognition;
   import com.amazonaws.services.rekognition.AmazonRekognitionClientBuilder;
   import com.amazonaws.services.rekognition.model.Image;
   import com.amazonaws.services.rekognition.model.BoundingBox;
   import com.amazonaws.services.rekognition.model.Celebrity;
   import com.amazonaws.services.rekognition.model.RecognizeCelebritiesRequest;
   import com.amazonaws.services.rekognition.model.RecognizeCelebritiesResult;
   import java.io.File;
   import java.io.FileInputStream;
   import java.io.InputStream;
   import java.nio.ByteBuffer;
   import com.amazonaws.util.IOUtils;
   import java.util.List;
   
   
   public class RecognizeCelebrities {
   
      public static void main(String[] args) {
         String photo = "moviestars.jpg";
   
         AmazonRekognition rekognitionClient = AmazonRekognitionClientBuilder.defaultClient();
   
         ByteBuffer imageBytes=null;
         try (InputStream inputStream = new FileInputStream(new File(photo))) {
            imageBytes = ByteBuffer.wrap(IOUtils.toByteArray(inputStream));
         }
         catch(Exception e)
         {
             System.out.println("Failed to load file " + photo);
             System.exit(1);
         }
   
   
         RecognizeCelebritiesRequest request = new RecognizeCelebritiesRequest()
            .withImage(new Image()
            .withBytes(imageBytes));
   
         System.out.println("Looking for celebrities in image " + photo + "\n");
   
         RecognizeCelebritiesResult result=rekognitionClient.recognizeCelebrities(request);
   
         //Display recognized celebrity information
         List<Celebrity> celebs=result.getCelebrityFaces();
         System.out.println(celebs.size() + " celebrity(s) were recognized.\n");
   
         for (Celebrity celebrity: celebs) {
             System.out.println("Celebrity recognized: " + celebrity.getName());
             System.out.println("Celebrity ID: " + celebrity.getId());
             BoundingBox boundingBox=celebrity.getFace().getBoundingBox();
             System.out.println("position: " +
                boundingBox.getLeft().toString() + " " +
                boundingBox.getTop().toString());
             System.out.println("Further information (if available):");
             for (String url: celebrity.getUrls()){
                System.out.println(url);
             }
             System.out.println();
          }
          System.out.println(result.getUnrecognizedFaces().size() + " face(s) were unrecognized.");
      }
   }
   ```

------
#### [ Java V2 ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/javav2/example_code/rekognition/src/main/java/com/example/rekognition/RecognizeCelebrities.java)\.

   ```
       public static void recognizeAllCelebrities(RekognitionClient rekClient, String sourceImage) {
   
           try {
               InputStream sourceStream = new FileInputStream(sourceImage);
               SdkBytes sourceBytes = SdkBytes.fromInputStream(sourceStream);
               Image souImage = Image.builder()
                   .bytes(sourceBytes)
                   .build();
   
               RecognizeCelebritiesRequest request = RecognizeCelebritiesRequest.builder()
                   .image(souImage)
                   .build();
   
               RecognizeCelebritiesResponse result = rekClient.recognizeCelebrities(request) ;
               List<Celebrity> celebs=result.celebrityFaces();
               System.out.println(celebs.size() + " celebrity(s) were recognized.\n");
               for (Celebrity celebrity: celebs) {
                   System.out.println("Celebrity recognized: " + celebrity.name());
                   System.out.println("Celebrity ID: " + celebrity.id());
   
                   System.out.println("Further information (if available):");
                   for (String url: celebrity.urls()){
                       System.out.println(url);
                   }
                   System.out.println();
               }
               System.out.println(result.unrecognizedFaces().size() + " face(s) were unrecognized.");
   
           } catch (RekognitionException | FileNotFoundException e) {
               System.out.println(e.getMessage());
               System.exit(1);
           }
       }
   ```

------
#### [ AWS CLI ]

   This AWS CLI command displays the JSON output for the `recognize-celebrities` CLI operation\. 

   Change `bucketname` to the name of an Amazon S3 bucket that contains an image\. Change `input.jpg` to the file name of an image that contains one or more celebrity faces\.

   ```
   aws rekognition recognize-celebrities \
     --image "S3Object={Bucket=bucketname,Name=input.jpg}"
   ```

------
#### [ Python ]

   This example displays information about the celebrities that are detected in an image\. 

   Change the value of `photo` to the path and file name of an image file that contains one or more celebrity faces\.

   ```
   #Copyright 2018 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   #PDX-License-Identifier: MIT-0 (For details, see https://github.com/awsdocs/amazon-rekognition-developer-guide/blob/master/LICENSE-SAMPLECODE.)
   
   import boto3
   
   def recognize_celebrities(photo):
       client = boto3.client('rekognition')
   
       with open(photo, 'rb') as image:
           response = client.recognize_celebrities(Image={'Bytes': image.read()})
   
       print('Detected faces for ' + photo)
       for celebrity in response['CelebrityFaces']:
           print('Name: ' + celebrity['Name'])
           print('Id: ' + celebrity['Id'])
           print('KnownGender: ' + celebrity['KnownGender']['Type'])
           print('Smile: ' + str(celebrity['Face']['Smile']['Value']))
           print('Position:')
           print('   Left: ' + '{:.2f}'.format(celebrity['Face']['BoundingBox']['Height']))
           print('   Top: ' + '{:.2f}'.format(celebrity['Face']['BoundingBox']['Top']))
           print('Info')
           for url in celebrity['Urls']:
               print('   ' + url)
           print()
       return len(response['CelebrityFaces'])
   
   
   def main():
       photo = 'photo1.jpg'
       celeb_count = recognize_celebrities(photo)
       print("Celebrities detected: " + str(celeb_count))
   
   if __name__ == "__main__":
       main()
   ```

------
#### [ Node\.Js ]

   This example displays information about the celebrities that are detected in an image\. 

   Change the value of `photo` to the path and file name of an image file that contains one or more celebrity faces\. Change the value of `bucket` to the name of the S3 bucket containing the provided image file\. Change the value of `REGION` to the name of the region associated with your account\. 

   ```
   // Import required AWS SDK clients and commands for Node.js
   import { RecognizeCelebritiesCommand } from  "@aws-sdk/client-rekognition";
   import  { RekognitionClient } from "@aws-sdk/client-rekognition";
   
   // Set the AWS Region.
   const REGION = "region-name"; //e.g. "us-east-1"
   // Create SNS service object.
   const rekogClient = new RekognitionClient({ region: REGION });
   
   const bucket = 'bucket-name'
   const photo = 'photo-name'
   
   // Set params
   const params = {
       Image: {
         S3Object: {
           Bucket: bucket,
           Name: photo
         },
       },
     }
   
   const recognize_celebrity = async() => {
       try {
           const response = await rekogClient.send(new RecognizeCelebritiesCommand(params));
           console.log(response.Labels)
           response.CelebrityFaces.forEach(celebrity =>{
               console.log(`Name: ${celebrity.Name}`)
               console.log(`ID: ${celebrity.Id}`)
               console.log(`KnownGender: ${celebrity.KnownGender.Type}`)
               console.log(`Smile: ${celebrity.Smile}`)
               console.log('Position: ')
               console.log(`   Left: ${celebrity.Face.BoundingBox.Height}`)
               console.log(`  Top : ${celebrity.Face.BoundingBox.Top}`)
               
           })
           return response.length; // For unit tests.
         } catch (err) {
           console.log("Error", err);
         }
   }
   
   recognize_celebrity()
   ```

------
#### [ \.NET ]

   This example displays information about the celebrities that are detected in an image\. 

   Change the value of `photo` to the path and file name of an image file that contains one or more celebrity faces \(\.jpg or \.png format\)\.

   ```
   //Copyright 2018 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   //PDX-License-Identifier: MIT-0 (For details, see https://github.com/awsdocs/amazon-rekognition-developer-guide/blob/master/LICENSE-SAMPLECODE.)
   
   using System;
   using System.IO;
   using Amazon.Rekognition;
   using Amazon.Rekognition.Model;
   
   public class CelebritiesInImage
   {
       public static void Example()
       {
           String photo = "moviestars.jpg";
   
           AmazonRekognitionClient rekognitionClient = new AmazonRekognitionClient();
   
           RecognizeCelebritiesRequest recognizeCelebritiesRequest = new RecognizeCelebritiesRequest();
   
           Amazon.Rekognition.Model.Image img = new Amazon.Rekognition.Model.Image();
           byte[] data = null;
           try
           {
               using (FileStream fs = new FileStream(photo, FileMode.Open, FileAccess.Read))
               {
                   data = new byte[fs.Length];
                   fs.Read(data, 0, (int)fs.Length);
               }
           }
           catch(Exception)
           {
               Console.WriteLine("Failed to load file " + photo);
               return;
           }
   
           img.Bytes = new MemoryStream(data);
           recognizeCelebritiesRequest.Image = img;
   
           Console.WriteLine("Looking for celebrities in image " + photo + "\n");
   
           RecognizeCelebritiesResponse recognizeCelebritiesResponse = rekognitionClient.RecognizeCelebrities(recognizeCelebritiesRequest);
   
           Console.WriteLine(recognizeCelebritiesResponse.CelebrityFaces.Count + " celebrity(s) were recognized.\n");
           foreach (Celebrity celebrity in recognizeCelebritiesResponse.CelebrityFaces)
           {
               Console.WriteLine("Celebrity recognized: " + celebrity.Name);
               Console.WriteLine("Celebrity ID: " + celebrity.Id);
               BoundingBox boundingBox = celebrity.Face.BoundingBox;
               Console.WriteLine("position: " +
                  boundingBox.Left + " " + boundingBox.Top);
               Console.WriteLine("Further information (if available):");
               foreach (String url in celebrity.Urls)
                   Console.WriteLine(url);
           }
           Console.WriteLine(recognizeCelebritiesResponse.UnrecognizedFaces.Count + " face(s) were unrecognized.");
       }
   }
   ```

------

1. Record the value of one of the celebrity IDs that are displayed\. You'll need it in [Getting information about a celebrity](get-celebrity-info-procedure.md)\.

## RecognizeCelebrities operation request<a name="recognizecelebrities-request"></a>

The input to `RecognizeCelebrities` is an image\. In this example, the image is passed as image bytes\. For more information, see [Working with images](images.md)\.

```
{
    "Image": {
        "Bytes": "/AoSiyvFpm....."
    }
}
```

## RecognizeCelebrities operation response<a name="recognizecelebrities-response"></a>

The following is example JSON input and output for `RecognizeCelebrities`\. 

`RecognizeCelebrities` returns an array of recognized celebrities and an array of unrecognized faces\. In the example, note the following:
+ **Recognized celebrities** – `Celebrities` is an array of recognized celebrities\. Each [Celebrity](https://docs.aws.amazon.com/rekognition/latest/APIReference/API_Celebritiy.html) object in the array contains the celebrity name and a list of URLs pointing to related content—for example, the celebrity's IMDB or Wikidata link\. Amazon Rekognition returns an [ComparedFace](https://docs.aws.amazon.com/rekognition/latest/APIReference/API_ComparedFace.html) object that your application can use to determine where the celebrity's face is on the image and a unique identifier for the celebrity\. Use the unique identifier to retrieve celebrity information later with the [GetCelebrityInfo](https://docs.aws.amazon.com/rekognition/latest/APIReference/API_GetCelebrityInfo.html) API operation\. 
+ **Unrecognized faces** – `UnrecognizedFaces` is an array of faces that didn't match any known celebrities\. Each [ComparedFace](https://docs.aws.amazon.com/rekognition/latest/APIReference/API_ComparedFace.html) object in the array contains a bounding box \(as well as other information\) that you can use to locate the face in the image\.

```
{
    "CelebrityFaces": [{
        "Face": {
            "BoundingBox": {
                "Height": 0.617123007774353,
                "Left": 0.15641026198863983,
                "Top": 0.10864841192960739,
                "Width": 0.3641025722026825
            },
            "Confidence": 99.99589538574219,
            "Emotions": [{
                "Confidence": 96.3981749057023,
                "Type": "Happy"
                }
            ],
            "Landmarks": [{
                "Type": "eyeLeft",
                "X": 0.2837241291999817,
                "Y": 0.3637104034423828
            }, {
                "Type": "eyeRight",
                "X": 0.4091649055480957,
                "Y": 0.37378931045532227
            }, {
                "Type": "nose",
                "X": 0.35267341136932373,
                "Y": 0.49657556414604187
            }, {
                "Type": "mouthLeft",
                "X": 0.2786353826522827,
                "Y": 0.5455248355865479
            }, {
                "Type": "mouthRight",
                "X": 0.39566439390182495,
                "Y": 0.5597742199897766
            }],
            "Pose": {
                "Pitch": -7.749263763427734,
                "Roll": 2.004552125930786,
                "Yaw": 9.012002944946289
            },
            "Quality": {
                "Brightness": 32.69192123413086,
                "Sharpness": 99.9305191040039
            },
            "Smile": {
            "Confidence": 95.45394855702342,
            "Value": True
            }    
        },
        "Id": "3Ir0du6",
        "KnownGender": {
            "Type": "Male"
        },
        "MatchConfidence": 98.0,
        "Name": "Jeff Bezos",
        "Urls": ["www.imdb.com/name/nm1757263"]
    }],
    "OrientationCorrection": "NULL",
    "UnrecognizedFaces": [{
        "BoundingBox": {
            "Height": 0.5345501899719238,
            "Left": 0.48461538553237915,
            "Top": 0.16949152946472168,
            "Width": 0.3153846263885498
        },
        "Confidence": 99.92860412597656,
        "Landmarks": [{
            "Type": "eyeLeft",
            "X": 0.5863404870033264,
            "Y": 0.36940744519233704
        }, {
            "Type": "eyeRight",
            "X": 0.6999204754829407,
            "Y": 0.3769848346710205
        }, {
            "Type": "nose",
            "X": 0.6349524259567261,
            "Y": 0.4804527163505554
        }, {
            "Type": "mouthLeft",
            "X": 0.5872702598571777,
            "Y": 0.5535582304000854
        }, {
            "Type": "mouthRight",
            "X": 0.6952020525932312,
            "Y": 0.5600858926773071
        }],
        "Pose": {
            "Pitch": -7.386096477508545,
            "Roll": 2.304218292236328,
            "Yaw": -6.175624370574951
        },
        "Quality": {
            "Brightness": 37.16635513305664,
            "Sharpness": 99.9305191040039
        },
        "Smile": {
            "Confidence": 95.45394855702342,
            "Value": True
        }
    }]
}
```