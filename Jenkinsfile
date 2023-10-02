pipeline{
    agent any
    tools{
        maven 'Maven'
        jdk 'JDK'
    }
    stages{
        stage('Git Checkout'){
            steps{
                git branch: 'master', url: 'https://github.com/Abionaraji/docker-project.git'
            }
        }
        stage('Maven Build'){
            steps{
                sh 'mvn clean install'
            }
            post{
                success{
                    echo 'New achiving'
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
        stage('Unit Test'){
            steps{
                sh 'mvn test'
            }
        }
        stage('Checkstyle Analysis'){
            steps{
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage('Integrated Testing'){
            steps{
                sh 'mvn verify -DiskipUnitTests'
            }
        }
        stage('Sonar Scanner'){
            steps{
                withSonarQubeEnv(credentialsId: 'batch-3', installationName: 'SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        stage('Sonar Gate'){
            steps{
                timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
        }
        stage('Uploading War'){
            steps{
                nexusArtifactUploader artifacts: [[artifactId: 'vprofile', classifier: '', file: 'target/vprofile-v2.war', type: 'war']], credentialsId: 'nexus-jenkins', groupId: 'QA', nexusUrl: '3.84.34.63:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'vpro-maven', version: 'v2'
            }
        }
    }
}