pipeline {
    agent any 

    environment {
        appsName = "demo"
        version =  ${env.BUILD_NUMBER} //"${GIT_COMMIT}"
        dockerImage = "${appsName}:${version}"
        registry = "pancaaa/$appsName"
        registryCredential = 'dockerhublogin'

        gitUrl = 'https://github.com/war3wolf/maven-springboot.git'
        gitBranch = 'main'
    }

    stages {
        stage('Checkout Source') {
            steps {
                sh 'rm -rf *'
                git branch: gitBranch,
                credentialsId: 'Github-Connection',
                url: gitUrl
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    dockerImage = docker.build registry + ":$version"

                }
            }
        }

	    stage('Pushing Image') {
            steps{
                script {
                    docker.withRegistry('', registryCredential) {
                    dockerImage.push()
                    sh "docker rmi -f $registry:$version"
                    }
                }   
            }
        }

        stage('Trigger Update Manifest') {
            echo "triggering Update manifest Job"
                build job: 'springboot-demo-cd', parameters: [string(name: 'DOCKERTAG', value: "$version")]
        }

        // stage('Deploy to Kube Cluster'){
        //     steps{
        //         script{
        //             sh ''' 
        //             #!/bin/bash
        //             sed -i "s/development/$version/g" deployment/deployment.yaml
        //             kubectl config current-context && kubectl apply -f deployment/deployment.yaml  
        //             '''

        //             sh '''
        //             #!/bin/bash
        //             echo "Checking deployment status"
        //             kubectl rollout status deployment/hello-world -n default --timeout=60s
        //             '''
        //         }
        //     }

        // }
    }

    post {
        always {
            deleteDir()
        }

        success {
            echo "Build image Success"
        }

        failure {
            echo "Build image failed"
        }

        cleanup {
            echo "Clean up in post workspace"
            cleanWs()
        }
    }
}