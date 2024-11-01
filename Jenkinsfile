pipeline {
    agent any

    tools {
        maven 'Maven'
    }
   environment {
        SCANNER_HOME=tool 'sonarscanner'
    }

    stage("Compile & install"){
            steps{
                sh "mvn clean compile"
                 sh "mvn clean install"
            }
        }
    stages {
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Run Unit Tests') {
            steps {
                echo 'Running Maven Unit Tests'
                sh 'mvn clean test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Running SonarQube Analysis'
                withSonarQubeEnv('SonarQube') { 
                    sh ''' $SCANNER_HOME/bin/sonar-scanner
                    -Dsonar.projectName=Petclinic \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Petclinic '''
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
        stage('Enforce Additional Conditions') {
            steps {
                script {
                    def coverageThreshold = 80
                    def highSeverityVulnerabilitiesAllowed = 0
                    def analysisResults = sh(script: "curl ${env.SONAR_HOST_URL}/api/measures/component?componentKey=PetClinic&metricKeys=coverage,vulnerabilities", returnStdout: true).trim()
                    def json = readJSON text: analysisResults
                    
                    def coverage = json.component.measures.find { it.metric == 'coverage' }?.value as Integer
                    def highVulnerabilities = json.component.measures.find { it.metric == 'vulnerabilities' && it.severity == 'HIGH' }?.value as Integer
                    
                    if (coverage < coverageThreshold) {
                        error "Code coverage is below ${coverageThreshold}%"
                    }
                    if (highVulnerabilities > highSeverityVulnerabilitiesAllowed) {
                        error "High-severity vulnerabilities exceed the allowed limit"
                    }
                }
            }
        }  
        
    }
}


// stage('Quality Gate') {
//             steps {
//                 echo 'Checking SonarQube Quality Gate and Coverage Requirements'
//                 timeout(time: 3, unit: 'MINUTES') {
//                     script {
//                         def qg = waitForQualityGate()
//                         echo "Quality Gate status: ${qg.status}"
//                         if (qg.status != 'OK') {
//                             error "Pipeline failed due to SonarQube Quality Gate status: ${qg.status}"
//                         }
//                     }
//                 }
//             }
//         }

// stage('Enforce Additional Conditions') {
        //     steps {
        //         script {
        //             def coverageThreshold = 80
        //             def highSeverityVulnerabilitiesAllowed = 0
        //             def analysisResults = sh(script: "curl ${env.SONAR_HOST_URL}/api/measures/component?componentKey=PetClinic&metricKeys=coverage,vulnerabilities", returnStdout: true).trim()
        //             def json = readJSON text: analysisResults
                    
        //             def coverage = json.component.measures.find { it.metric == 'coverage' }?.value as Integer
        //             def highVulnerabilities = json.component.measures.find { it.metric == 'vulnerabilities' && it.severity == 'HIGH' }?.value as Integer
                    
        //             if (coverage < coverageThreshold) {
        //                 error "Code coverage is below ${coverageThreshold}%"
        //             }
        //             if (highVulnerabilities > highSeverityVulnerabilitiesAllowed) {
        //                 error "High-severity vulnerabilities exceed the allowed limit"
        //             }
        //         }
        //     }
        // }





// pipeline {
//     agent any 
    
//     tools{
//         maven 'Maven'
//     }
    
//     environment {
//         SCANNER_HOME=tool 'sonarscanner'
//     }
    
//     stages{
        
//         stage("Git Checkout"){
//             steps{
//                 checkout scm
//             }
//         }
        
//         stage("Compile"){
//             steps{
//                 sh "mvn clean compile"
//             }
//         }
        
//          stage("Test Cases"){
//             steps{
//                 sh "mvn test"
//             }
//         }
        
//         stage("Sonarqube Analysis "){
//             steps{
//                 withSonarQubeEnv('SonarQube') {
//                     sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Petclinic \
//                     -Dsonar.java.binaries=. \
//                     -Dsonar.projectKey=Petclinic '''
    
//                 }
//             }
//         }
        
//         // stage("OWASP Dependency Check"){
//         //     steps{
//         //         dependencyCheck additionalArguments: '--scan ./ --format HTML ', odcInstallation: 'DP'
//         //         dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
//         //     }
//         // }
        
//          stage("Build"){
//             steps{
//                 sh " mvn clean install"
//             }
//         }
        
//         // stage("Docker Build & Push"){
//         //     steps{
//         //         script{
//         //            withDockerRegistry(credentialsId: '58be877c-9294-410e-98ee-6a959d73b352', toolName: 'docker') {
                        
//         //                 sh "docker build -t image1 ."
//         //                 sh "docker tag image1 adijaiswal/pet-clinic123:latest "
//         //                 sh "docker push adijaiswal/pet-clinic123:latest "
//         //             }
//         //         }
//         //     }
//         // }
        
//         // stage("TRIVY"){
//         //     steps{
//         //         sh " trivy image adijaiswal/pet-clinic123:latest"
//         //     }
//         // }
        
//         // stage("Deploy To Tomcat"){
//         //     steps{
//         //         sh "cp  /var/lib/jenkins/workspace/CI-CD/target/petclinic.war /opt/apache-tomcat-9.0.65/webapps/ "
//         //     }
//         // }
//     }
// }
