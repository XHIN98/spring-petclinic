pipeline {
  agent any
  tools {
    jdk 'jdk17'
    maven 'M3'
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
            git url: 'httls://github.com/XHIN98/spring-petclinic.git', branch: 'main'
        }
    }
 
  }
}
