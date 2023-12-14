def COLOR_MAP = [
    'SUCCESS' : 'good',
    'FAILURE' : 'danger'
]

pipeline{
    agent{
        label 'slave2'
    }

    tools{
        maven 'workers-mvn'
        jdk 'workers-jdk'
        git 'workers-git'
    }

    

    stages{
        stage('Cloning'){
            steps{
                sh 'echo Cloning Started ..........'
                git url:'git@github.com:sanjeetcalgary/pipeline.git', branch:'docker_ecr'
                sh 'echo Cloning Completed ..........'
            }
        }
        
        stage('Cleaning'){
            steps{
                 sh 'echo Cleaning the build ..........'
                 sh 'mvn clean'                 
            }
            post{
                success{
                    sh 'echo Cleaning build is completed ..........'
                }
            }
        }

        stage('Build'){
            steps{
                sh 'echo Build started'
                sh 'mvn install'
            }
            post{
                success{
                    sh 'echo Build is completed ..........'
                    archiveArtifacts artifacts: '**/*.war'
                    sh 'echo Build has been archieved...........'
                }
            }
        }

        stage('Test'){
            steps{
                sh 'echo Testing started ..........'
                sh 'mvn test'
                sh 'echo Testing is completed ..........'
            }
        } 
    }
    post{
        always{
            junit skipPublishingChecks: true, testResults: "**/target/surefire-reports/*.xml"
            echo "Slack Notification"
            slackSend channel: '#devops-cicd',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info @: ${env.BUILD_URL}"
        }
    }
}