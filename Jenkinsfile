pipeline {

    agent any

    environment {
        image = "nongdocker/demo-nodejs"
        registry = "docker.io"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/Nong-Surapon/demo-nodejs-mongodb-rest'
            }
        }

        stage('Print Environment') {
            steps {
                sh('ls -al')
                sh('pwd')
                sh('printenv')
            }
        }

        // stage('Ansible prepareations docker ') {
        //     steps{
        //         sh 'ANSIBLE_ROLES_PATH="$PWD/ansible-script/roles" ansible-playbook -vvv ./ansible-script/playbook/web-server/web-server.yml -i ./ansible-script/host -u root -e "state=prepareation tagnumber=${BUILD_NUMBER}"'
        //     }
        // }
        
//         stage('Build docker image') {
//             steps {
//                 script {
//                     docker.withRegistry('', 'dockerhub') {
//                         def slackImage = docker.build("${env.image}:${BUILD_NUMBER}")
//                         slackImage.push()
//                         slackImage.push('latest')
//                     }
//                 }
//             }
//         }
        stage('Build docker image') {
            steps {
               sh "docker build -t ${env.image} ."
               sh "docker images"
            }
        }
        
        stage('Push docker image') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
                        sh "docker login -u nongdocker -p${dockerhubpwd}"
                        sh "docker push ${env.image}:latest"
                    }
                }
            }
        }

        stage('Deployment'){
            steps {
                sh "docker-compose up -d"
            }
        }
        // Test v0.1
        // stage('tag docker image') {
        //     steps {
        //        sh "docker tag ${env.image}:${BUILD_NUMBER} ${env.image}:latest"
        //     }
        // }

        // stage('push docker image') {
        //     steps {
        //        sh "docker push ${env.image}:latest"
        //     }
        // }

        // stage('Verify new docker image(s)') {
        //     steps {
        //         sh('docker images')
        //     }
        // }
        
    }
}

