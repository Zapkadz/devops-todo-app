#!/usr/bin/env groovy

import groovy.transform.Field

@Field
String SSH_ID_REF = '<SSH_ID_PLACEHOLDER>'

pipeline {
    agent any

    tools {
        dockerTool 'docker'
    }

    stages {
        stage('Build') {
            steps {
                sh 'docker build -t vitnguyen/mgm-training-todo-app:0.0.3 .'
            }
        }
        stage("Docker login and push docker image") {
            steps {
                withBuildConfiguration {
                    sh 'docker login -u "$USER" -p "$PASSWD"'
                    sh 'docker push vitnguyen/mgm-training-todo-app:0.0.3'        		
                }
            }
        }
        stage("deploy") {
             steps {
                 withBuildConfiguration {
                     sshagent(credentials: [SSH_ID_REF]) {
                         sh '''
                            ssh -o StrictHostKeyChecking=no root@ec2-18-143-167-76.ap-southeast-1.compute.amazonaws.com
                            docker run -d --rm --name y-todo-app -p 8050:8000 vitnguyen/mgm-training-todo-app:0.0.3
                            docker ps
                         '''
                    }
                }
            }
        }
    }
}

void withBuildConfiguration(Closure body) {
    withCredentials([usernamePassword(credentialsId: 'v-docker-hub', usernameVariable: 'USER', passwordVariable: 'PASSWD')]) {
        body()
    }
}