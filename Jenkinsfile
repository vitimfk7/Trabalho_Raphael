pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'mvn clean install'
      }
    }
    stage('Dockerhub') {
      steps {
        sh 'docker login -u $USERNAME -p $PASSWORD'
        sh 'mvn clean install'
      }
    }
    stage('Mariadb denp') {
      parallel {
        stage('Mariadb denp') {
          steps {
            sh 'mvn clean install'
            sh '''touch $JENKINS_HOME/Dockerfiles/Mariadb/my.cnf
touch $JENKINS_HOME/Dockerfiles/Mariadb/start.sh
'''
          }
        }
        stage('Docker root') {
          steps {
            sh '''cd $WORKSPACE/target/ProjetoContatoSimples-1.0-SNAPSHOT
rm -rf $JENKINS_HOME/Dockerfiles/Tomcat/ROOT/*
mv * $JENKINS_HOME/Dockerfiles/Tomcat/ROOT/'''
          }
        }
      }
    }
    stage('Imagem aplicação') {
      parallel {
        stage('Imagem aplicação') {
          steps {
            sh 'mvn clean install'
            sh '''cd $JENKINS_HOME/Dockerfiles/Tomcat
docker build -t $USERNAME/$APPLICATION_NAME  .
'''
          }
        }
        stage('Imagem mariadb') {
          steps {
            sh 'mvn clean install'
            sh '''cd $JENKINS_HOME/Dockerfiles/Mariadb
docker build -t $USERNAME/$DATABASE_NAME  .
'''
          }
        }
      }
    }
    stage('Upload images') {
      parallel {
        stage('Upload image application') {
          steps {
            sh 'mvn clean install'
            sh 'docker push $USERNAME/$APPLICATION_NAME'
          }
        }
        stage('Upload image mariadb') {
          steps {
            sh 'mvn clean install'
            sh 'docker push $USERNAME/$DATABASE_NAME'
          }
        }
      }
    }
  }
  environment {
    USERNAME = 'vitimfk7'
    PASSWORD = 'pk79c1'
    DATABASE_NAME = 'mariadb'
    APPLICATION_NAME = 'trabalhorafs'
  }
  post {
   failure {
     mail(to: 'victor.73.komori@gmail.com', subject: "Failed Pipeline: ${currentBuild.fullDisplayName}", body: "Something is wrong with ${env.BUILD_URL}.")
   }  
   success {
     mail(to: 'victor.73.komori@gmail.com', subject: "Successed Pipeline: ${currentBuild.fullDisplayName}", body: "${env.BUILD_URL} was successefully build.")
   }
  }
}
