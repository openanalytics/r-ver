pipeline {
    
    agent any
    
    parameters {
        choice(name: 'R_VERSION', choices: ['3.6.1', '3.6.0', '3.5.3', '3.3.3'], description: 'Build r-ver image for this R version')
        booleanParam(name: 'NOCACHE', defaultValue: false, description: 'Run docker build with --no-cache')
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '3'))
    }
    
    triggers {
        pollSCM('H/15 * * * *')
    }
    
    environment {
        NS = 'openanalytics'
    }
    
    stages {
    
        stage('Docker Build') {
        
            steps {
                
                dir("${params.R_VERSION}"){
            
                    script {
                         def image = docker.build(
                            "${env.NS}/r-ver:${params.R_VERSION}",
                            ("${params.NOCACHE}".toBoolean() ? '--no-cache ' : '') + "--build-arg http_proxy='http://webproxy.openanalytics.eu:8080' --build-arg https_proxy='http://webproxy.openanalytics.eu:8080' --build-arg no_proxy='localhost,127.0.0.0,127.0.0.1,openanalytics.eu' .")
                    }
                }
            
            }
            
        }
        
        stage('Docker Push') {
        
            steps {
            
                withDockerRegistry([
                        credentialsId: "52b706f7-b775-4fcc-83c2-3f51853250e5",
                        url: ""]) {
                        
                    sh "docker push ${env.NS}/r-ver:${params.R_VERSION}"
                    sh "docker tag ${env.NS}/r-ver:${params.R_VERSION} ${env.NS}/r-ver:latest"
					sh "docker push ${env.NS}/r-ver:latest"

                }
                
            }
            
        }
     
    }
    
}
