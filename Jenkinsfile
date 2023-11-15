pipeline{
    agent any
    tools{
        jdk 'jdk11'
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
                    withSonarQubeEnv(credentialsId: 'sonarqube', installationName: 'sonarqube') {
                        sh 'chmod +x gradlew'
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
        stage('build gradle'){
            steps{
                sh './gradlew build'
            }
        }
        stage('dependency check'){
            steps{
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('build & push to nexus'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'nexus_docker', variable: 'nexus_docker-pass')]){
                        sh '''
                            docker build -t 3.88.197.117:8083/gradle-project:latest .
                            docker login -u admin -p mukesh123 3.88.197.117:8083
                            docker push 3.88.197.117:8083/gradle-project:latest
                        '''
                    }
                }
            }
        }
        stage('deploy to container'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'nexus_docker', variable: 'nexus_docker-pass')]){
                        sh 'docker run -d --name gradle -p 9090:8080 3.88.197.117:8083/gradle-project:latest'
                    }
                }
            }
        }
    }
}