## Implementation
1. Deploy infrastructure via using AWS CloudFomration — https://github.com/ozMrb/hello-world-rekognition
1. Upload Sample Face image called brian.jpeg to s3 bucket that created by the above cloudformation
1. Create Face Index
```
aws rekognition index-faces --image '{"S3Object":{"Bucket":"hello-world-030082980000","Name":"brian.jpeg"}}' --collection-id "hello-world" --detection-attributes "ALL" --external-image-id "brian" --region us-east-1
```
1. [Setup Streaming Locally on MacOS](https://github.com/awslabs/amazon-kinesis-video-streams-producer-sdk-cpp)

* Prerequisite - Java
```
brew install openjdk@11
sudo ln -sfn /usr/local/Cellar/openjdk@11/11.0.15 /usr/local/opt/openjdk
sudo ln -sfn /usr/local/opt/openjdk/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk.jdk
export PATH="/usr/local/opt/openjdk/bin:$PATH"
export JAVA_HOME="/usr/local/opt/openjdk/libexec/openjdk.jdk/Contents/Home"
export CPPFLAGS="-I/usr/local/opt/openjdk/include"
export JAVA_INCLUDE_PATH="/usr/local/opt/openjdk/include"
export JAVA_AWT_INCLUDE_PATH="/usr/local/opt/openjdk/include"
export JAVA_INCLUDE_PATH2="/usr/local/opt/openjdk/include"
```

* Build

```
git clone https://github.com/awslabs/amazon-kinesis-video-streams-producer-sdk-cpp
cd amazon-kinesis-video-streams-producer-sdk-cpp
brew install pkg-config openssl cmake gstreamer gst-plugins-base gst-plugins-good gst-plugins-bad gst-plugins-ugly log4cplus gst-libav

```

* Run
```
export GST_PLUGIN_PATH=`pwd`/build
export LD_LIBRARY_PATH=`pwd`/open-source/local/lib
gst-launch-1.0 -v avfvideosrc ! videoconvert ! vtenc_h264_hw allow-frame-reordering=FALSE realtime=TRUE max-keyframe-interval=45 ! kvssink name=sink stream-name="hello-world" aws-region="us-east-1"
```

1. Start Stream Processor
```
aws rekognition start-stream-processor --name hello-world --region us-east-1
aws rekognition list-stream-processors --region us-east-1
```

## Clean up
1. Stop Stream Processor
```
aws rekognition stop-stream-processor --name hello-world --region us-east-1
```
1. Delete Cloudformation

## Reference:
https://medium.com/zenofai/real-time-face-identification-on-live-camera-feed-using-amazon-rekognition-video-and-kinesis-video-52b0a59e8a9