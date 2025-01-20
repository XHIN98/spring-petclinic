pipeline {
  agent any
  tools {
    jdk 'JDK17'
    maven 'M3'
  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('docker-credential')
    AWS_CREDENTIALS_NAME = credentials('AWSCredential')
    //GIT_CREDENTIALS = credentials('gitCredential')
    REGION = 'ap-northeast-2'
  }

  stages {
    // 1. Github에서 코드 복제
    stage('Git Clone') {
      steps {
        echo 'Git Clone'
        git url: 'https://github.com/XHIN98/spring-petclinic.git',
            branch: 'main'
      }
    }

    // 2. Maven 빌드
    stage('Maven Build') {
      steps {
        echo 'Maven Build'
        sh 'mvn -Dmaven.test.failure.ignore=true clean package'
      }
    }

    // 3. Docker 이미지 생성
    stage('Docker Image Build') {
      steps {
        echo 'Docker Image build'
        dir("${env.WORKSPACE}"){
        sh """
        docker build -t xoodongxoo/spring-petclinic:$BUILD_NUMBER .
        docker tag xoodongxoo/spring-petclinic:$BUILD_NUMBER xoodongxoo/spring-petclinic:latest
        """
        }
      }
    }

    // 4. Docker Hub에 로그인 및 이미지 푸시
    stage('Docker Login and Push') {
      steps {
        echo 'Docker Login and Push'
        sh """
        echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
        docker push xoodongxoo/spring-petclinic:$BUILD_NUMBER
        docker push xoodongxoo/spring-petclinic:latest
        """
      }
    }

    // 5. Docker 이미지 제거
    stage('Remove Docker Image') {
      steps {
        echo 'Remove Docker Image'
        sh """
        docker rmi xoodongxoo/spring-petclinic:$BUILD_NUMBER
        docker rmi xoodongxoo/spring-petclinic:latest
        """
      }
    }
    //6. s3에 APPsepc.yml Upload
    stage('Upload to S3') {
      steps{
        echo "Upload to S3"
        dir("${env.WORKSPACE}") { 
          sh 'zip -r deploy.zip ./deploy appspec.yml'
          withAWS(region:"${REGION}", credentials:"${AWS_CREDENTIALS_NAME}"){
          s3Upload(file:"deploy.zip", bucket:"user18-codedeploy-bucket")
          } 
          sh 'rm -rf ./deploy.zip'
        }
      }
    }

    
  }
}
