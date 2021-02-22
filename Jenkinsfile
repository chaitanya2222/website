pipeline {
    agent any
    podTemplate(label: 'mypod', containers: [
        containerTemplate(name: 'git', image: 'alpine/git', ttyEnabled: true, command: 'cat'),
        containerTemplate(name: 'maven', image: 'maven:3.3.9-jdk-8-alpine', command: 'cat', ttyEnabled: true),
        containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true)
    ],
    volumes: [
        hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
    ]
    ) {
        node('mypod') {
            stages {
                stage('Check running containers') {
                    container('docker') {
                        // example to show you can run docker commands when you mount the socket
                        sh 'hostname'
                        sh 'docker images'
                    }
                }

                //stage("Checkout code") {
                //    container('git') {
                //        checkout scm
                //    }
                //}

                stage('Clone repository') {
                    container('git') {
                        sh 'git clone -b master https://github.com/aviralharsh/website.git'
                    }
                }

                //stage('Maven Build') {
                //    container('maven') {
                //        dir('website/') {
                //            sh 'hostname'
                //            sh 'mvn clean install'
                //        }
                //    }
                //}

                //build image

                stage("Build Image")
                {
                    container('docker'){
                            checkout scm
                            //sh 'git clone -b master https://github.com/aviralharsh/website.git'
                            website = docker.build("aviralharsh05/website")
                    }
                }

                stage("Push image") {
                    container('docker') {
                        script {
                            docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                                    website.push("${env.BUILD_NUMBER}")
                                    website.push("1.0.0")
                            }
                        }
                    }
                }
            }
        }
    }
}
