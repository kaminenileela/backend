pipeline {
    agent{
        label 'AGENT-1'
    }
    options{
        timeout(time:30,unit:'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    environment{
        def appVersion = '' //variable declaration
        nexusUrl = 'http://http://3.235.1.47:8081/'

    }
    stages{
         stage('read the version') {
            steps{
                script{
               def packageJson = readJSON file: 'package.json'
               appVersion = packageJson.version
             }

           }
        }
        stage('Install Dependencies') {
            steps{
                sh """
                 npm install
                 ls -ltr
                """
            }
        }
        stage('Build'){
            steps{
                sh """
                zip -r backend-${appVersion}.zip * -x Jenkinsfile -x backend-${appVersion}.zip 
                ls -ltr
                """
            }
        }
         stage('Nexus Artifact Upload'){
            steps{
                script{
                     nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${nexusUrl}",
                        groupId: 'com.expense',
                        version: "${appVersion}",
                        repository: "backend",
                        credentialsId: 'nexus-auth',
                        artifacts: [
                            [artifactId: backend,
                            classifier: '',
                            file: "backend-" + "${appVersion}" + '.zip',
                            type: 'zip']
                        ]
                    )
                }
            }
        }
        stage('Deploy'){
            steps{
                script{
                     def params = [
                        string(name: 'appVersion', value: "${appVersion}")
                     ]
                     build job: 'backend-deploy', parameters: params, wait: false
                }
            }
        }
    }
    post{
        always{
            echo 'I will always say hello'
            deleteDir()
        }
        success{
            echo 'I will run when pipeline is success'
        }
        failure{
            echo 'I will run when pipeline is failure'
        }
    }
}