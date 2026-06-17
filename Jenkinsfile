#!/user/bin/env groovy

library identifier: 'jenkins-groovy-shared-library@master', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/MB-938/jenkins-groovy-shared-library.git',
    credentialsId: 'git-credentials'
    ]
)

def gv
pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage("init") {
            steps {
                script {
                    gv = load "script.groovy"
                }
            }
        }
        stage("build jar") {
            steps {
                script {
                    buildJar()
                }
            }
        }
        stage("build and push image") {
            steps {
                script {
                    buildImage 'mb938/demo-app:jma-3.0'
                    dockerLogin()
                    dockerPush 'mb938/demo-app:jma-3.0'
                }
            }
        }
        stage("deploy") {
            steps {
                script {
                    gv.deployApp()
                }
            }
        }
    }
}
