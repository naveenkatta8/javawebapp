pipeline {
    agent {label 'slave01'}
    tools {
        maven 'maven'
    }
    environment {
        BUCKET_NAME = 'jenkins_artifcats'
    }
    stages {
        stage ('Auth with GCP') {
            steps {
                withCredentials([file(credentialsId: 'gcp', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    sh 'gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS'
            }}}
        stage ('checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/naveenkatta8/javawebapp']])
            }}
        stage ('build') {
            steps {
                sh 'mvn clean install -f pom.xml'
            }
        }
        stage ('save artifact') {
            steps {
                sh 'gsutil cp target/**.*war gs://jenkins_artifcats/'
            }
        }
        stage ('deploy') {
            steps {
                deploy adapters: [tomcat9(alternativeDeploymentContext: '', credentialsId: 'deployer', path: '', url: 'http://34.68.82.188:8080/')], contextPath: null, war: '**/*.war'
            }
        }
        }
    }

