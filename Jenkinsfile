pipeline {
    agent any

    environment {
        CONSUL_ENDPOINT = 'http://54.81.175.213:8500/v1/kv/' // Replace with your Consul endpoint
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

        stage('Install Consul Agent') {
            steps {
                script {
                    // Example installation for Linux. Adjust for your OS
                    sh '''
                        curl -sSL https://releases.hashicorp.com/consul/1.10.0/consul_1.10.0_linux_amd64.zip -o consul.zip 
                        unzip consul.zip
                        ls -last
                        chmod +x consul && rm -rf consul.zip
                        ls -last
                        export PATH=$PWD:$PATH
                        consul --version
                        ls -la
                        curl http://54.81.175.213:8500/v1/kv/\?recurse=true
                    '''
                }
            }
        }
/*
        // stage('Process Config Map JSON & Upload to Consul') {
        //     steps {
        //         script {
        //             def jsonFile = 'config-map-env.json'
        //             if (fileExists(jsonFile)) {
        //                 def config = readJSON file: jsonFile
        //                 config.each { key, value ->
        //                     def consulKey = "${env.ENV}/${env.CLUSTER}/${env.APPLICATION_CONFIG_MAP}/${key}"
        //                     sh "consul kv put -http-addr=${env.CONSUL_ENDPOINT} ${consulKey} '${value}'"
        //                 }
        //             } else {
        //                 error "config-map-env.json not found!"
        //             }
        //         }
        //     }
        // }
*/
    }
}
