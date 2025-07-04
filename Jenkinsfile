#!/usr/bin/env groovy

library identifier: 'jenkins-shared-library@master', retriever: modernSCM(
	[$class: 'GitSCMSource',
	remote: 'https://github.com/Sora-mmh/jenkins-shared-library.git',
	credentialsId: 'github-credentials'])

pipeline {   
  agent any
  tools {
    maven 'maven-3.9'
  }
  environment {
    IMAGE_NAME = 'sorammh8/demo-app:java-maven-2.0'
  }
  stages {
    stage("build app") {
      steps {
        script {
          echo 'building application jar...'
          buildJar()
        }
      }
    }
    stage("build image") {
      steps {
        script {
          echo 'building docker image...'
          buildImage(env.IMAGE_NAME)
          dockerLogin()
          dockerPush(env.IMAGE_NAME)
        }
      }
    }
    stage ("provision server") {
      // tf provision server
      steps {
	
      }	
    }
    stage("deploy") {
      steps {
        script {
          echo 'deploying docker image to EC2...'
          
          def shellCmd = "bash ./server-cmds.sh ${IMAGE_NAME}"
          def ec2Instance = "ec2-user@$35.180.151.121"

          sshagent(['server-ssh-key']) {
            sh "scp -o server-cmds.sh ${ec2Instance}:/home/ec2-user"
            sh "scp -o docker-compose.yaml ${ec2Instance}:/home/ec2-user"
            sh "ssh -o StrictHostKeyChecking=no ${ec2Instance} ${shellCmd}"
          }
        }
      }
    }               
  }
}


// pipeline {
//     agent any
//     tools {
//         maven 'maven-3.9'
//     }
//     environment {
//         DOCKER_REPO = 'sorammh8/demo-app'
//     }
//     stages {
//         stage('increment version') {
//             steps {
//                 script {
//                     echo 'incrementing app version...'
//                     sh 'mvn build-helper:parse-version versions:set \
//                         -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
//                         versions:commit'
//                     def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
//                     def version = matcher[0][1]
//                     env.IMAGE_NAME = "$version-$BUILD_NUMBER"
//                 }
//             }
//         }
//         stage('build app') {
//             steps {
//                 script {
//                     echo 'building the application...'
//                     sh 'mvn clean package'
//                 }
//             }
//         }
//         stage('build image') {
//             steps {
//                 script {
//                     echo "building the docker image..."
//                     withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]){
//                         sh "docker build -t ${DOCKER_REPO}:${IMAGE_NAME} ."
//                         sh 'echo $PASS | docker login -u $USER --password-stdin ${DOCKER_REPO_SERVER}'
//                         sh "docker push ${DOCKER_REPO}:${IMAGE_NAME}"
//                     }
//                 }
//             }
//         }
//         stage('deploy') {
//             steps {
//                 script {
//                    echo 'deploying docker image...'
//                 }
//             }
//         }
//         stage('commit version update'){
//             steps {
//                 script {
//                     withCredentials([usernamePassword(credentialsId: 'github-credentials', passwordVariable: 'PASS', usernameVariable: 'USER')]){
//                         sh 'git config --global user.email "jenkins@example.com"'
//     					sh 'git config --global user.name "jenkins"'
    					
//     					sh 'git status'
//     					sh 'git branch'
//     					sh 'git config --list'
                        
//                         sh 'git remote set-url origin https://$USER:$PASS@github.com/Sora-mmh/twn-baseline-app.git'
//                         sh 'git add .'
//                         sh 'git commit -m "ci: version bump"'
//                         sh 'git push origin HEAD:jenkins-jobs'
//                     }
//                 }
//             }
//         }
//     }
// }
