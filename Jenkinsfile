pipeline{
    agent any
     environment {
        tomcat_ip = "172.31.29.151"
        tomcat_user = "ec2-user"
     }
     parameters {
       choice choices: ['main', 'develop', 'test'], description: 'choose the branchName to deploy', name: 'branchName'
     }
    stages{
        stage("Git Checkout") {
            when {
            expression{
                params.branchName == "main"
            }
        }
            steps{
                git branch: 'main', credentialsId: 'git-creds', url: 'https://github.com/Tangala123/mvn-demo'
            }
        }
        
        stage("Build") {
            when {
            expression{
                params.branchName == "develop"
            }
        }
            steps{
                sh "mvn clean package"
            }
        }
         
        stage("Deploy") {
            when {
            expression{
                params.branchName == "test"
            }
        }
            steps{
                sshagent(['tomcat-dev']) {
                    sh "scp -o StrictHostKeyChecking=no target/mvn-demo.war $tomcat_user@$tomcat_ip:/opt/tomcat9/webapps"
                    sh "ssh $tomcat_user@$tomcat_ip /opt/tomcat9/bin/shutdown.sh"
                    sh "ssh $tomcat_user@$tomcat_ip /opt/tomcat9/bin/startup.sh"
                }
                // sshPublisher(publishers: [sshPublisherDesc(configName: 'tomcat9', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '''/opt/tomcat9/bin/shutdown.sh
                //  /opt/tomcat9/bin/startup.sh''', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/opt/tomcat9/', remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: 'target/mvn-demo.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
        }
    }
}
