pipeline{
    agent any
    tools{
        jdk 'jdk17'
        gragle 'gradle'
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
    }
}