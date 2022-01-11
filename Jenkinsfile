pipeline {

    agent {
        kubernetes {
            yaml '''
              apiVersion: v1
              kind: Pod
              spec:
                containers:
                - name: dind
                  image: 196229073436.dkr.ecr.eu-west-1.amazonaws.com/oa-infrastructure/dind
                  securityContext:
                    privileged: true
            '''
            defaultContainer 'dind'
        }
    }

    parameters {
        choice(name: 'R_VERSION', choices: ['4.1.2', '4.1.1', '4.1.0', '4.0.5', '4.0.4', '4.0.3', '4.0.2', '4.0.1', '4.0.0', '3.6.3', '3.6.2', '3.6.1', '3.6.0', '3.5.3', '3.2.0'], description: 'Build r-ver image for this R version')
        booleanParam(name: 'NOCACHE', defaultValue: false, description: 'Run docker build with --no-cache')
        booleanParam(name: 'LATEST', defaultValue: true, description: 'Also tag image with the "latest" tag')
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '3'))
    }
    
    triggers {
        pollSCM('H/15 * * * *')
    }
    
    environment {
        NS = 'openanalytics'
        REG_OA_PRIVATE = '196229073436.dkr.ecr.eu-west-1.amazonaws.com'
    }
    
    stages {
    
        stage('Docker Build') {
        
            steps {
                
                dir("${params.R_VERSION}"){
            
                    script {
                         def image = docker.build(
                            "${env.NS}/r-ver:${params.R_VERSION}",
                            ("${params.NOCACHE}".toBoolean() ? '--no-cache ' : '') + " .")
                    }
                }
            
            }
            
        }
        
        stage('Docker Push') {
        
            steps {
            
                withDockerRegistry([
                        credentialsId: "openanalytics-dockerhub",
                        url: ""]) {
                        
                    sh "docker push ${env.NS}/r-ver:${params.R_VERSION}"

                }

                withOARegistry {
                    sh """
                    docker tag ${env.NS}/r-ver:${params.R_VERSION} ${env.REG_OA_PRIVATE}/${env.NS}/r-ver:${params.R_VERSION}
                    """
                    ecrPush "${env.REG_OA_PRIVATE}", "${env.NS}/r-ver", "${params.R_VERSION}", '', 'eu-west-1' 
                }
                
            }
            
        }

        stage('Docker Tag Latest') {
            
            when { 
                expression { return params.LATEST }
            }
             
            steps {
            
                withDockerRegistry([
                        credentialsId: "openanalytics-dockerhub",
                        url: ""]) {
                        
                    sh "docker tag ${env.NS}/r-ver:${params.R_VERSION} ${env.NS}/r-ver:latest"
                    sh "docker push ${env.NS}/r-ver:latest"

                }
                
            }
            
        }
     
    }
    
}
