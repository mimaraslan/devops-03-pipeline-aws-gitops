pipeline {
    agent {
        node {
            label 'My-Jenkins-Agent'
        }
    }
    //agent any
 
    environment {
        APP_NAME = "devops-03-pipeline-aws-gitops"
    }



    stages {


     stage('Cleanup Workspace') {
            steps {
                script {
                    cleanWs()
                }
            }
        }


        stage('SCM GitHub') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/mimaraslan/devops-03-pipeline-aws-gitops']])
            }
        }

      







    }
}