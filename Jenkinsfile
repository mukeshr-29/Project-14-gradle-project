pipeline{
    agent any
    tools{
        jdk 'jdk17'
        gradle 'gradle'
    }
    stages{
        stage('clean work space'){
            steps{
                cleanWs()
            }
        }
        stage('git checkout'){
            steps{
                git branch: 'main', credentialsId: 'git', url: 'https://github.com/mukeshr-29/Project-14-gradle-project.git'
            }
        }
        stage('gradle compile'){
            steps{
                sh 'chmod +x gradlew'
                sh './gradlew compileJava'
            }
        }
        stage('test gradle'){
            steps{
                sh 'chmod +x gradlew'
                sh './gradlew test'
            }
        }
        stage('sonarqube analysis'){
            steps{
                script{
                    withSonarQube(credentialsId: 'sonarqube') {
                        sh './gradlew sonarqube'
                    }
                    timeout(time: 10, unit: 'MINUTES'){
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK'){
                            error "pipeline is aborted due to qualitygate failure: ${qg.status}"
                        }
                    }
                }
            }
        }
    }
}