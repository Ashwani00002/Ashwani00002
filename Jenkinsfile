pipeline {
    agent any

    environment {
        CONSUL_HTTP_ADDR = 'http://54.81.175.213:8500/v1/kv' // Replace with your Consul endpoint
        
    }

    stages {
        stage('Checkout & Parse Branch Name') {
            steps {
                script {
                    def branchName = env.GIT_BRANCH.replace('origin/', '') // Remove 'origin/' if present
                    def parts = branchName.split('\\.')
                    if (parts.size() == 3) {
                        env.ENV = parts[0]
                        env.CLUSTER = parts[1]
                        env.APPLICATION_CONFIG_MAP = parts[2]
                        echo "ENV: ${env.ENV}, CLUSTER: ${env.CLUSTER}, APPLICATION_CONFIG_MAP: ${env.APPLICATION_CONFIG_MAP}"
                    } else {
                        error "Invalid branch name format. Expected: env.cluster.application-config-map"
                    }
                }
                checkout scm
            }
        }
stage('Install Consul Agent & Process Config') {
    steps {
        script {
            // Install Consul Agent
            def consulZip = 'consul.zip'
            def consulUrl = 'https://releases.hashicorp.com/consul/1.10.0/consul_1.10.0_linux_amd64.zip'

            sh "curl -sSL ${consulUrl} -o ${consulZip}"
            unzip zipFile: consulZip

            // sh '''
            //     ls -last
            //     chmod +x consul && rm -rf consul.zip
            //     ls -last
            //     export PATH=$PWD:$PATH
            //     consul --version
            //     ls -la
            //     curl http://54.81.175.213:8500/v1/kv/\\?recurse=true
            //     ls -l
            // '''

            // Process Config Map JSON & Upload to Consul
            def jFile = readJSON file: 'config-map-env.json'
            jFile.each { key, value ->
                def consulKey = "${env.ENV}/${env.CLUSTER}/${env.APPLICATION_CONFIG_MAP}/${key}"
                sh '''
                echo "*******************************************"
                ls -la
                echo $CONSUL_HTTP_ADDR
                echo $BRANCH_NAME
                pwd
                curl $CONSUL_HTTP_ADDR/\\?recurse=true
                echo "*******************************************"
                curl -k --request PUT -d '${value}' '${CONSUL_HTTP_ADDR}/${consulKey}'
                '''
            }
        }
    }
}

    }
}
