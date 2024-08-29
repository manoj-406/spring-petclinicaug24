pipeline {
    agent any
    tools {
        maven 'MAVEN_3.9.9'
    }
    triggers {
        pollSCM('* * * * *') // Polling SCM every minute
    }
    stages {
        stage('SCM') {
            steps {
                git url: 'https://github.com/spring-projects/spring-petclinic.git',
                    branch: 'main'
            }
        }
        stage('BUILD') {
            steps {
                withSonarQubeEnv(credentialsId: 'Sonarcloud_token', installationName: 'Sonar_cloud') {
                    sh 'mvn clean package org.sonarsource.scanner.maven:sonar-maven-plugin:3.7.0.1746:sonar -Dsonar.organization=springpetclinicqt -Dsonar.projectKey=springpetclinicqt_spc'
                }
                junit testResults: '**/surefire-reports/*.xml'
                archiveArtifacts artifacts: '**/target/spring-petclinic-*.jar', allowEmptyArchive: false
            }
        }
        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
