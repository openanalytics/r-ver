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
            '''
            defaultContainer 'kaniko'
        }
    }

    parameters {
        choice(name: 'R_VERSION', choices: ['4.2.1', '4.2.0', '4.1.3', '4.1.2', '4.1.1', '4.1.0', '4.0.5', '4.0.4', '4.0.3', '4.0.2', '4.0.1', '4.0.0', '3.6.3', '3.6.2', '3.6.1', '3.6.0', '3.5.3', '3.4.1', '3.3.3', '3.2.0'], description: 'Build r-ver image for this R version')
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
        TS = sh(returnStdout: true, script: 'date -u +%Y%m%d%H%M%S').trim()
        DOCKER_HUB_CREDS = credentials('openanalytics-dockerhub')
        REGION = "eu-west-1"
    }
    
    stages {
    
        stage('Build') {
        
            steps {
                
                dir("${params.R_VERSION}"){
                    
                    sh '''
                    set +x
                    echo "{\\"auths\\": {\\"https://index.docker.io/v1/\\": {\\"auth\\": \\"\$(echo -n $DOCKER_HUB_CREDS | base64)\\"}}}" > /kaniko/.docker/config.json
                    set -x
                    '''
                    sh """
                    /kaniko/executor \
                        -v info \
                        --context ${env.WORKSPACE}/${params.R_VERSION} \
                        --cache=${params.NOCACHE ? 'false' : 'true'} \
                        --cleanup \
                        --destination=${env.NS}/r-ver:${params.R_VERSION} \
                        --destination=${env.REG_OA_PRIVATE}/${env.NS}/r-ver:${params.R_VERSION} \
                        --destination=${env.NS}/r-ver:${params.R_VERSION}-${env.TS} \
                        --destination=${env.REG_OA_PRIVATE}/${env.NS}/r-ver:${params.R_VERSION}-${env.TS} \
                        ${params.LATEST ? "--destination=${env.NS}/r-ver:latest" : ""} \
                        ${params.LATEST ? "--destination=${env.REG_OA_PRIVATE}/${env.NS}/r-ver:latest" : ""}
                    """
                }
            }       
        }
    }
}
