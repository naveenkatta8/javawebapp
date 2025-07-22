pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage('checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/naveenkatta8/javawebapp']])
            }}
        stage ('build') {
            steps {
                sh 'mvn clean install -f pom.xml'
            }
        }
        stage ('deploy') {
            steps {
                deploy adapters: [tomcat9(alternativeDeploymentContext: '', credentialsId: 'deployer', path: '/opt/tomcat/webapps/target/', url: 'http://34.55.157.220:8080/')], contextPath: null, war: '**/*.war'
            }
        }
        }
    }

