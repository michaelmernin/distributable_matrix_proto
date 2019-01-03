#!/usr/bin/env groovy

pipeline {
    agent { label 'master' }

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
                dir("SkillsMatrixProto") {
                    sh 'chmod +x gradlew'
                    sh './gradlew -s build'
                }
            }
        }
        stage('Containerize') {
            steps {
                echo 'Containerizing..'
                dir("SkillsMatrixProto") {
                    sh 'docker build -t michaelmernin/skills_matrix_test_perf .'
                    withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'p', usernameVariable: 'u')]) {
                        sh "docker login -u=${u} -p=${p}"
                        sh 'docker push michaelmernin/skills_matrix_test_perf:latest'
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
                dir("ansible") {
                    sh 'chmod 400 ec2.pem'
                    sh 'ansible-playbook main.yml -i aws'
                }
            }
        }
    }
}
