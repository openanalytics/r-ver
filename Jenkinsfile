pipeline {

    agent {
        kubernetes {
            yaml '''
              apiVersion: v1
              kind: Pod
              metadata:
                labels:
                  docker: r-ver
              spec:
                imagePullSecrets:
                - name: registry-robot
                volumes:
                - name: kaniko-dockerconfig
                  secret:
                    secretName: registry-robot
                containers:
                - name: kaniko
                  image: gcr.io/kaniko-project/executor:v1.7.0-debug
                  env:
                  - name: AWS_SDK_LOAD_CONFIG
                    value: "true"
                  command:
                  - /kaniko/docker-credential-ecr-login
                  - get
                  tty: true
                  resources:
                    requests:
                        memory: "1024Mi"
                    limits:
                        memory: "2048Mi"
                  imagePullPolicy: Always
                  volumeMounts:
                  - name: kaniko-dockerconfig
                    mountPath: /kaniko/.docker/config.json
                    subPath: .dockerconfigjson
            '''
            defaultContainer 'kaniko'
        }
    }

    parameters {
        choice(name: 'R_VERSION', choices: ['4.4.0', '4.3.3', '4.3.2', '4.3.1', '4.3.0', '4.2.3', '4.2.2', '4.2.1', '4.2.0', '4.1.3', '4.1.2', '4.1.1', '4.1.0', '4.0.5', '4.0.4', '4.0.3', '4.0.2', '4.0.1', '4.0.0', '3.6.3', '3.6.2', '3.6.1', '3.6.0', '3.5.3', '3.4.1', '3.3.3', '3.2.0'], description: 'Build r-ver image for this R version')
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
        REG_OA_PRIVATE = 'registry.openanalytics.eu'
        TS = sh(returnStdout: true, script: 'date -u +%Y%m%d%H%M%S').trim()
        REGION = "eu-west-1"
    }
    
    stages {
    
        stage('Build') {
            
            // NB: do not run builds automatically, but only on user input
            when { 
                not {
                    triggeredBy 'SCMTrigger'
                }
            }  
               
            steps {
                
                dir("${params.R_VERSION}"){
                    sh """
                    /kaniko/executor \
                        -v info \
                        --context ${env.WORKSPACE}/${params.R_VERSION} \
                        --cache=${params.NOCACHE ? 'false' : 'true'} \
                        --cleanup \
                        --destination=${env.REG_OA_PRIVATE}/${env.NS}/r-ver:${params.R_VERSION} \
                        --destination=${env.REG_OA_PRIVATE}/${env.NS}/r-ver:${params.R_VERSION}-${env.TS} \
                        ${params.LATEST ? "--destination=${env.REG_OA_PRIVATE}/${env.NS}/r-ver:latest" : ""}
                    """
                }
            }       
        }
    }
}
