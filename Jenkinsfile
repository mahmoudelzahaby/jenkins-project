pipeline {
    agent any
    parameters {
        choice(name: 'ENV', choices: ['dev', 'test', 'prod',"release"])
    } 
    stages {
        stage('build') {
            steps {
                script {
                   if (params.ENV == "release") {
                       withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                           sh """
                                docker login -u $USERNAME -p $PASSWORD
                                docker build -t mahmoudahaby/jenkins-lab:${BUILD_NUMBER} .
                                docker push mahmoudahaby/jenkins-lab:${BUILD_NUMBER}
                                echo ${BUILD_NUMBER} > ../jenkins-pro.txt
                           """
                       }
                    }
                }
            }
        }
        stage('deploy') {
            steps {
                script {
                    sh """
                        ls
                        echo "Hello"
                        """
                }
            }
        }
    }
}
