pipeline {
    agent any
    // tools {
    //     maven "MAVEN3"
        
    // }

    stages {
        stage('Fetch code') {
            steps {
                git branch: 'vp-rem', url:'https://github.com/devopshydclub/vprofile-project.git'
            }
        }

        stage('Build') {
            steps{
                sh 'mvn install -DskipTests'
            }
            

            post {
                success {
                    echo 'Now Archiving it..'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Checkstyle Analysis'){ 
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage('Sonar Analysis'){
            environment {
                scannerHome = tool 'sonar4.8'
            }
            steps {
                withSonarQubeEnv('sonar') {
                    sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile-repo \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                }
            }
        }

        stage("Quality Gate"){
            steps {
                time(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}