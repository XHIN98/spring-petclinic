pipeline {
  agent any
  tools {
    jdk 'jdk17'
    maven 'M3'
  }
  // Docker Hub 접속 정보 (환경설정 해주기)
  environment{
    DOCKERHUB_CREDENTIALS = credentials('dockerCredentials')
  }
  
  // 1. 코드 복제
  // 2. 빌드
  // 3. 도커 이미지 만들기
  // 4. 만든 도커 이미지 허브에 올리기
  // 5. 다운 받아서 도커에서 실행
  stages {
    stage('Git Clone'){
        steps{
          //Github에서 Jenkins로 소스코드 복제
            echo 'Git Clone'
            git url: 'https://github.com/XHIN98/spring-petclinic.git', branch: 'main'
        }
    }
    stage('Maven Build'){
        steps{
            sh 'mvn -Dmaven.test.failure.ignore=true clean package'
        }
    }
    stage('Docker Image'){
      steps {
          dir("${env.WORKSPACE}"){
            sh """
            docker build -t xoodongxoo/spring-petclinic:$BUILD_NUMBER .
            docker tag xoodongxoo/spring-petclinic:$BUILD_NUMBER xoodongxoo/spring-petclinic:latest
              """
          }
      }
    }
    stage('Docker Image Push'){
      steps{
        sh """
        echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
        docker push xoodongxoo/spring-petclinic:latest
        """
      }
    }
    stage('Remove Docker Image'){
      steps{
        sh """
        docker rmi xoodongxoo/spring-petclinic:$BUILD_NUMBER
        docker rmi xoodongxoo/spring-petclinic:latest
        """
      }
    }
    stage('Docker Container'){
      steps{
        sshPublisher(publishers: [sshPublisherDesc(configName: 'target', 
        transfers: [sshTransfer(cleanRemote: false, 
        excludes: '', 
        execCommand: '''
        docker rm -f $(docker ps -aq)
        docker rmi $(docker images -q)
        docerk run -d -p 80:8080 --name spring-petclinic xoodongxoo/spring-petclinic:latest
        ''', 
        execTimeout: 120000, flatten: false, 
        makeEmptyDirs: false, 
        noDefaultExcludes: false, 
        patternSeparator: '[, ]+', 
        remoteDirectory: '', 
        remoteDirectorySDF: false, 
        removePrefix: '', 
        sourceFiles: '')], 
        usePromotionTimestamp: false, 
        useWorkspaceInPromotion: false, 
        verbose: false)])
      }
    }
  }
}
