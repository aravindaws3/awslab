Resources:
  S3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: my-ci-cd-trigger-bucket

  SNSTopic:
    Type: AWS::SNS::Topic
    Properties:
      TopicName: my-s3-event-topic

  EmailSubscription:
    Type: AWS::SNS::Subscription
    Properties:
      Protocol: email
      Endpoint: your-email@example.com  # Replace with your email address
      TopicArn: !Ref SNSTopic

  S3BucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket: !Ref S3Bucket
      PolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Sid: "AllowSNSPublish"
            Effect: Allow
            Principal:
              Service: sns.amazonaws.com
            Action: "s3:PutObject"
            Resource: !Sub "arn:aws:s3:::${S3Bucket}/*"

  S3BucketNotificationConfiguration:
    Type: AWS::S3::Bucket
    Properties:
      Bucket: !Ref S3Bucket
      NotificationConfiguration:
        TopicConfigurations:
          - Event: "s3:ObjectCreated:*"
            Topic: !Ref SNSTopic
