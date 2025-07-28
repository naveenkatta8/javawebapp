pipeline {
    agent any
    tools {
        maven 'maven'
    }
    environment {
        SLACK_CHANNEL = '#all-jenkins-workspace'
        SLACK_COLOR = '#00FF00'
        BUCKET_NAME = 'jenkins_artifcats'
    }
    stages {
        stage ('Slack') {
            steps{
                slackSend (
                    channel: "${env.SLACK_CHANNEL}", 
                    color: '#FFFF00',
                    message: "Build Started: ${env.JOB_NAME} [${env.BUILD_NUMBER}] <${env.BUILD_URL}|Open>"
                )

            }
        }
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
        stage ('CodeQuality ') {
            steps {
            withSonarQubeEnv ('sonarqube'){
            sh 'mvn clean install -f pom.xml sonar:sonar'  
            }}
        }
        stage ('save artifact') {
            steps {
                sh 'gsutil cp target/**.*war gs://jenkins_artifcats/'
            }
        }
        stage ('deploy') {
            steps {
                deploy adapters: [tomcat9(alternativeDeploymentContext: '', credentialsId: 'deployer', path: '', url: 'http://34.56.79.108:8080/')], contextPath: null, war: '**/*.war'
            }
        }
        }
        post {
        success {
            slackSend(
                channel: "${env.SLACK_CHANNEL}",
                color: "${env.SLACK_COLOR}",
                message: "✅ *Build Succeeded*: ${env.JOB_NAME} [${env.BUILD_NUMBER}] <${env.BUILD_URL}|Open>"
            )
        }
        failure {
            slackSend(
                channel: "${env.SLACK_CHANNEL}",
                color: '#FF0000',
                message: "❌ *Build Failed*: ${env.JOB_NAME} [${env.BUILD_NUMBER}] <${env.BUILD_URL}|Open>"
            )
        }
        }
        
    }

