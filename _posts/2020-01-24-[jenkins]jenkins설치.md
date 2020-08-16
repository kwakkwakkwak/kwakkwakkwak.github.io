---
 title: "[jenkins] jenkins & CI/CD 설치"
 description: "jenkins 설치"
 categories: jenkins CI CD 
 tags: jenkins CI CD
 date: 2020-01-24
 comments : true
 ---
 
 # Jenkins & CI / CD 란? 
 
 * jenkins
     * 소프트웨어 개발 시 지속적 통합 ( `CI` ) 서비스를 제공하는 툴이다.
 * `CI`(Continuous Integration) 
     * Build 및 Test 를 상시 로 실시해 주는 것
     * 지속적인 통합
 * `CD`(Continuous Delivery or Continuous Deploy)
     * 짧은 주기로 소프트웨어를 개발하는 소프트웨어 공학적 접근의 하나로, 소프트웨어를 더 빠르게, 더 주기적으로 빌드하고 테스트하고 출시하는 것을 목표
     * 지속적인 배포
 
 # jenkins 설치
 
 * jenkins 설치 서버 스펙
     * ubuntu 16.4 lts
     * java jdk 1.8 설치
     
 * 프로젝트를 jenkins - gitlab - slack 연동으로 관리 예정
     * gitlab 에서 push 받기 
     * jenkins build 및 deploy
     * 결과 slack 메시지 전송 
 
* 설치 
 
1. 설치 명령어
   
 ```
 wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
 
 deb https://pkg.jenkins.io/debian-stable binary/
 
 sudo apt-get update
 
 sudo apt-get install jenkins
 ```
 
2. 설치후 localhost:8080 으로 접속하면 `unlock password` 를 필요로 한다.
     * `/var/lib/jenkins/secrets/initialAdminPassword` 에 비밀번호가 있다.
     
![jenkins 비밀번호 확인](/assets/images/jenkins/jenkins_1.png)
 
 4. 계정 생성 진행
 
* jenkins 계정설정 까지 진행하면 기본적인 설치가 끝난다.