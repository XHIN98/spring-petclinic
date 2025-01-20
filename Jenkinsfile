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
          branch: 'main', credentialsId: 'GIT_CREDENTIALS'
      }
    }
    //maven 빌드 작업
    stage('Maven Build'){
      steps{
        echo 'Maven Build'
        sh 'mvn -Dmaven.test.failure.ignore=true clean package'
      }
    }
    //Docerk Image 생성
    stage('Docker Image Build'){
      steps{
        echo 'Docker Image build'
        dir("${env.WORKSPACE}"){
          sh"""
          docker build -t xoodongxoo/spring-petclinic:$BUILD_NUMBER .
          docker tag xoodongxoo/spring-petclinic:$BUILD_NUMBER xoodongxoo/spring-petclinic:latest
          """
      }
    }
  }
  //Docker Hub에 로그인
  stage('Docker Login'){
    steps{
      sh """
      echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
      docker push xoodongxoo/spring-petclinic:latest
      """
    }
  }
  steage('Remove Docker Image'){
    steps{
      sh """
      docker rmi xoodongxoo/spring-petclinic:$BUILD_NUMBER .
      docker xoodongxoo/spring-petclinic:latest
    }
  }
}
}
