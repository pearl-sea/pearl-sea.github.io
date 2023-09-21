---
layout: post
title: 프로젝트에 도메인과 HTTPS 적용
categories: ["Study"]
---

20일정도 팀원들과 열심히 제작한 프로젝트에 새로 구입한 도메인과 HTTPS를 적용해보았습니다.  
이제부터는 세개의 서비스를 추가로 사용해야합니다.

* **AWS Certificate Manager**  
  SSL/TLS 인증서를 관리하고 제공하는 서비스입니다.
  HTTPS로 통신하려면 반드시 필요합니다.

  * SSL/TLS  
    Secure Scokets Layer / Transport Layer Security

    보안 프로토콜의 일종입니다.
    SSL의 향상된 버전이 TLS입니다.
    일반적으로 SSL이라고 하는 것은 TLS를 가리킵니다.

* **Route53**  
  도메인네임을 IP주소와 연결해주는 서비스입니다.

* **Cloudfront**  
  S3에서 직접 사용자에게 콘텐츠를 전송하는것보다 더 빠른 속도를 지원하는 CDN 서비스입니다.

  Cloudfront로 S3 버킷을 배포하면서 HTTPS와 인증서 설정을 할 수 있습니다.

  * CDN  
  서버와 사용자 사이의 물리적인 거리를 줄여 로딩에 소요되는 시간을 최소화 하기 위해 연결된 서버 네트워크입니다.

#### 주의사항
**구입한 도메인과 버킷의 이름이 같아야 합니다.**  
처음에 프로젝트를 진행할때 도메인 연결에 대해 고려하지 않아서 버킷을 다시 생성했습니다. 꼭 확인하고 진행하세요 !

**CloudFront에서 HTTPS를 사용하려면 AWS Certificate Manager(ACM) 인증서를 미국 동부(버지니아 북부) 리전으로 설정해놓고 요청해야 합니다.**  
S3 버킷리전은 신경쓰지 않아도 됩니다 인증서 요청 시에만 리전을 변경해주세요.


#### 도메인 구입
goDaddy라는 사이트에서 wonprice.shop을 구입했습니다.

![구입한 도메인](/assets/img/image-1.png)  

#### Route 53에 도메인 등록
호스팅 영역 생성으로 이동합니다.

![route53 대시보드](/assets/img/image-2.png)  

구입한 도메인을 입력하고 호스팅 영역을 생성합니다.

![호스팅 영역 생성](/assets/img/image-3.png)  

#### 도메인 구입한 업체에서 네임서버 변경

* **네임서버**  
IP주소와 도메인주소를 연결해주는 역할을 하는 서버입니다.

호스팅 영역을 생성하면 4개의 네임 서버가 생성됩니다.

![네임서버](/assets/img/image-4.png)  

도메인을 구입한 호스팅서비스 업체로 이동해서  
방금 전에 Route 53에서 발급받았던 네임서버로 변경해줍니다.

![네임서버 변경](/assets/img/image-5.png)  

#### 인증서 발급

AWS Certificate Manager로 이동해서 퍼블릭 인증서 발급을 선택한 후 구입한 도메인 이름을 적습니다.

![인증서 발급](/assets/img/image-6.png)<br>  
![인증서 발급](/assets/img/image-7.png)<br>  
![인증서 발급 완료](/assets/img/image-8.png)  

#### Route 53에서 레코드 생성

Route 53에서 레코드 생성을 클릭합니다.

![레코드 생성](/assets/img/image-11.png)  

트래픽 라우팅 대상을 다음과 같이 설정합니다.

![레코드 생성 설정](/assets/img/image-10.png)  

여기까지 하면 https없이 도메인 연결이 완료됩니다.

#### Cloudfront

Cloudfront로 이동해서 배포 생성을 클릭하고    
원본 도메인과 인증서, 구입한 도메인, HTTPS 설정 등을 입력합니다.

![배포 생성 설정](/assets/img/image-12.png)<br>  
![배포 생성 설정](/assets/img/image-13.png)<br> 
![배포 생성 설정](/assets/img/image-14.png)  

배포에 성공했다면 배포 도메인 이름을 복사한 뒤

![배포 도메인 이름 복사](/assets/img/image-15.png)  

다시 Route 53으로 돌아와서 처음에 설정한 트래픽 라우팅 대상을 CloudFront 배포로 변경해 줍니다.

![트래픽 라우팅 대상 변경](/assets/img/image-16.png)  

적용이 완료되었습니다.

![HTTPS 적용 완료](/assets/img/image-17.png)