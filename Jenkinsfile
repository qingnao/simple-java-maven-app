pipeline {
  // 顶层不指定 agent，改为按阶段指定
  agent none

  options {
    skipStagesAfterUnstable()
  }

  stages {
    stage('Build') {
      // 在 Maven 镜像里跑 mvn
      agent {
        docker {
          image 'maven:3.8.8-openjdk-11'
          // 把宿主的 ~/.m2 目录挂进来，加速依赖下载（可选）
          args '-v $HOME/.m2:/root/.m2'
        }
      }
      steps {
        sh 'mvn -B -DskipTests clean package'
      }
    }

    stage('Test') {
      agent {
        docker {
          image 'maven:3.8.8-openjdk-11'
          args '-v $HOME/.m2:/root/.m2'
        }
      }
      steps {
        sh 'mvn test'
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
        }
      }
    }

    stage('Deliver') {
      // 交付脚本跑在默认节点（即你的 Jenkins 容器）上
      agent any
      steps {
        sh './jenkins/scripts/deliver.sh'
      }
    }
  }
}
