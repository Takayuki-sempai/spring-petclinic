pipeline {
    agent any
    triggers {
        pollSCM('H/5 * * * *')
    }
    tools {
        maven 'Maven3'
        jdk 'Java8'
    }
    environment {
        VERSION = VersionNumber([
                versionNumberString    : '1.0.${BUILDS_THIS_YEAR}',
                projectStartDate       : '2018-11-29',
                PrefixVariable         : '',
                worstResultForIncrement: 'SUCCESS'
        ])
    }
    stages {
        stage('Build') {
            steps {
                git url: 'https://github.com/Takayuki-sempai/spring-petclinic'
                bat 'mvn clean package'
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            }
        }
        stage('SonarQube analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    bat 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.5.0.1254:sonar'
                }
            }
        }
    }
    post {
        always {
            script {
                step([$class: 'TestExamplePublisher'])
            }
        }
        failure {
            emailext from: 'jenkins.test@inbox.ru',
                    recipientProviders: [developers(), requestor()],
                    body: '$DEFAULT_CONTENT',
                    subject: '$DEFAULT_SUBJECT'
        }
    }
}

