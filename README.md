# Simple File Sharing

본 문서에서는 Serverless architecture 기반의 파일공유 서비스를 소개하고자 합니다. 전체적인 Serverless Architecture는 아래와 같습니다.

<img width="894" alt="image" src="https://user-images.githubusercontent.com/52392004/154693997-e302b36f-8b84-4447-bcc6-907842cc5acd.png">

주요 사용 시나리오는 아래와 같습니다. 

1) 사용자가 업로드한 컨텐츠는 RESTful API를 통해 API Gateway를 통해 전송되는데 이때 보안을 위해 https를 이용해 전달됩니다. 이후, Lambda에 전달된 event에서 파일을 추출해서 S3에 저장하게 됩니다. 
2) 두번째 Lambda는 S3의 Put event notification을 받아서 trigger 되는데, CloudFront와 S3가 연결하는 작업을 하게 되며, 이때 생성된 파일 다운로드 URL을 SNS에 전달 합니다. 
3) SNS는 Lambda로 부터 전달된 URL로 사용자에게 email 을 발송하게 됩니다. 해당 URL은 별도로 인증 절차 없이 사용 할 수 있으므로, 파일 공유등 필요에 따라 활용 할 수 있습니다. 

간단하게 구현하여 사용할 수 있도록, 멀티 Account를 고려하지 않고, 고정비용을 줄이기 위하여 데이터베이스도 사용하지 않습니다. 


문서 가독성을 위하여 아래와 같이 3개의 파일을 별도로 생성하여 설명 합니다. 

## Lambda-upload 설정

https://github.com/kyopark2014/simple-serverless-filesharing/blob/main/docs/lambda-upload.md

## S3 설정

https://github.com/kyopark2014/simple-serverless-filesharing/blob/main/docs/S3.md

## API Gateway 설정

https://github.com/kyopark2014/simple-serverless-filesharing/blob/main/docs/api-gateway.md

## CloudFront 설정 

https://github.com/kyopark2014/simple-serverless-filesharing/blob/main/docs/cloudfront.md


상기와 같이 Lambda, S3, API Gateway를 모두 적절히 설치한후 아래와 같이 테스트를 할 수 있습니다. 

## 테스트 방법

### curl 을 활용한 바이너리 업로드 테스트

[curl 명령어의 예]

$ curl -i https://viwb8crpob.execute-api.ap-northeast-2.amazonaws.com/dev/upload -X POST --data-binary '@sample1.jpeg' -H 'Content-Type: image/jpeg' -H 'Content-Disposition: form-data; name="sample1"; filename="sample1.jpeg"'


curl 실행 결과는 아래와 같습니다. 


<img width="1213" alt="image" src="https://user-images.githubusercontent.com/52392004/154499486-c0c4f841-5636-4eaf-b91f-9f31ccd44ba1.png">


### Postman을 이용한 이미지 업로드 테스트

Content-Type 및 Content-Disposition을 설정합니다. 


<img width="1024" alt="image" src="https://user-images.githubusercontent.com/52392004/154497888-9b166748-f6ad-4a0e-9805-24e29fbfa811.png">

Body에 “binary” 형태로 이미지 파일을 전송합니다.


<img width="1007" alt="image" src="https://user-images.githubusercontent.com/52392004/154497991-5c9917f8-44ac-4370-bd79-7804daa64094.png">

Postman을 이용해 이미지 파일을 업로드시에 아래와 같이, 성공의 의미로 버켓이름, Key(파일이름), 컨텐츠 타입을 전달합니다. 

<img width="1013" alt="image" src="https://user-images.githubusercontent.com/52392004/154499778-f1ba0c5a-0d8f-4dea-90dd-a8881e8f236f.png">

### 본 과제에 필요한 Lambda upload와 notification 에 대한 코드 및 설명은 아래를 참조 바랍니다. 

#### Lambda-upload

https://github.com/kyopark2014/simple-serverless-filesharing-upload

#### Lambda-notification

https://github.com/kyopark2014/simple-serverless-filesharing-nodification
