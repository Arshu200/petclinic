pipeline {
    agent any

    tools {
        maven 'Maven'
    }
   environment {
        SCANNER_HOME=tool 'sonarqubescanner'
    }

    stages {
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }
        stage("Compile"){
            steps{
                sh "mvn clean compile"
                 // sh "mvn clean install"
            }
        }

        stage('Run Unit Tests') {
            steps {
                echo 'Running Maven Unit Tests'
                sh 'mvn clean test'
            }
        }
        stage("install"){
            steps{
                // sh "mvn clean compile"
                 sh "mvn clean install"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Running SonarQube Analysis'
                withSonarQubeEnv('SonarQube') { 
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectName=Petclinic-App \
                        -Dsonar.java.binaries=. \
                        -Dsonar.projectKey=Petclinic
                    '''
                }
            }
        }  
        stage('Quality Gate') {
            steps {
                echo 'Checking SonarQube Quality Gate and Coverage Requirements'
                timeout(time: 5, unit: 'MINUTES') {
                    script {
                        def qg = waitForQualityGate()
                        echo "Quality Gate status: ${qg.status}"
                        if (qg.status != 'OK') {
                            error "Pipeline failed due to SonarQube Quality Gate status: ${qg.status}"
                        }
                    }
                }
            }
        }        
    }
}
