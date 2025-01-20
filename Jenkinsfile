pipeline {
  agent any
  tools {
    jdk 'JDK17'
    maven 'M3'
  }
  // Docker Hub 접속 정보 (환경설정 해주기)
  environment{
    DOCKERHUB_CREDENTIALS = credentials('docker-credential')
    AWS_CREDENTIALS = credentials('AWSCredential')
    GIT_CREDENTIALS = credentials('gitCredential')
    REGION = 'ap-northeast-2'
  }
  
  // 1. 코드 복제
  // 2. 빌드
  // 3. 도커 이미지 만들기
  // 4. 만든 도커 이미지 허브에 올리기
  // 5. 다운 받아서 도커에서 실행
  stages {
    //Github에 가서 소스코드 가져오기
    stage('Git Clone'){
      steps{
        echo 'Git Clone'
        git url: 'https://github.com/XHIN98/spring-petclinic.git',
          branch: 'main', credentialIdL: 'GIT_CREDENTIALS'
      }
    }
  }
}
