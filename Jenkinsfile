def COLOR_MAP = [
    'SUCCESS'; 'good',
    'FAILURE': 'danger',
]
pipeline {
    agent any
    tools {
        maven "MAVEN3"
        jdk "OpenJDK11"
    }
    stages{
        stage('Fetch Code'){
            steps {
                git branch: 'main', url: 'https://github.com/Codyle212/jenkins-pipeline'
            }
        }
        stage('Build'){
            steps {
                sh 'mvn install -DskipTests'
            }

            post{
                success {
                    echo 'Archiving artifacts now.'
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
        stage('Sonar Analysis'){
            // Environmental Variable
            environment{
                scannerHome = tool 'sonar4.7'
            }
            steps{
                // url already configure in sonar
                withSonarQubeEnv('sonar'){
                    // For Running sonar cube using binary
                    // https://docs.sonarsource.com/sonarqube/9.8/analyzing-source-code/scanners/sonarscanner/
                    sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile\
                   -Dsonar.projectName=vprofile-repo \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml
                    '''
                }
            }
        }
        stage('Quality Gate'){
            steps {
                timeout(time: 1, unit: 'HOURS'){
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate Fails
                    // true = set pipline ro UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('Upload Artifact'){
            steps{
                nexusArtifactUploader(
                nexusVersion: 'nexus3',
                protocol: 'http',
                //private IP of nexus repo in vpc
                nexusUrl: '172.31.56.23:8081',
                groupId: 'QA',
                version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                repository: 'vprofile-repo',
                credentialsId: 'nexuslogin',
                artifacts: [
                    [artifactId: 'vproapp',
                    classifier: '',
                    file: 'target/vprofile-v2.war',
                    type: 'war']
                ])
            }
        }
    }
    post {
        always {
            // slack workspace and branch is already configured in the integration steps
            echo 'Slack Notification'
            slackSend channel: '#jenkins-cicd',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More Information at: ${env.BUILD_URL}"
        }
    }
}