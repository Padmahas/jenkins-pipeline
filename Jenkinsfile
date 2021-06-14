pipeline {
    agent any
    tools {
        maven 'maven-3.8.1'
    }
    environment {
        VBOX_CREDS = credentials('padmahasa-vbox-creds')
    }
    stages {
        stage ('List folder conents') {
            steps {
                sh 'mvn -v'
            }
        }
        stage('development branch') {
            when {
                branch 'development'
            }
            steps {
                echo 'Configuring Remote SSH for "' + env.BRANCH_NAME + '" environment.'
                script {
                    def remote = [:]
                    remote.name = 'padmahasa-VirtualBox'
                    remote.host = '192.168.0.104'
                    remote.user = VBOX_CREDS_USR
                    remote.password = VBOX_CREDS_PSW
                    remote.pty = true
                    remote.allowAnyHosts = true

                    // echo 'Listing the files available on the server'
                    // sshCommand remote: remote, command: 'ls -lrt'
                    // sshCommand remote: remote, command: 'for i in {1..5};' +
                    // ' do echo -n \"Loop \$i \"; date ; sleep 1; done'

                    /**
                     * #####################################################################################
                     * Setting ENV variables on Remote host using shell script inside profile.d
                     * #####################################################################################
                     */
                    // echo 'Capturing new file name to be used for back up, using ENV variable'
                    // sshCommand remote: remote, sudo: true, command:
                    // // 'sudo rm /etc/profile.d/devOpsEnvVars.sh ' +
                    // 'touch /etc/profile.d/devOpsEnvVars.sh ' +
                    // '&& sudo chmod 755 /etc/profile.d/devOpsEnvVars.sh ' +
                    // '&& sudo sh -c \'echo "export datetime=$(date +"%Y-%m-%d_%H_%M_%S")" ' +
                    // '> /etc/profile.d/devOpsEnvVars.sh\''
                    // echo 'pty before resetting = ' + remote.get("pty")
                    // sshCommand remote: remote, sudo: true, command: 'sudo sh -c ' +
                    // '\'echo "export backedUpFileName=ProjectName_\\$datetime.war" ' +
                    // '>> /etc/profile.d/devOpsEnvVars.sh\''

                    /**
                     * #####################################################################################
                     * Setting ENV variables on Remote host using shell script
                     * in HOME directory and manually invoking before each sshCommand
                     * #####################################################################################
                     */
                    echo 'Capturing new file name to be used for back up, using ENV variable'
                    sshCommand remote: remote, sudo: true, command:
                    // 'sudo rm devOpsEnvVars.sh ' +
                    'touch devOpsEnvVars.sh ' +
                    '&& sudo chmod 755 devOpsEnvVars.sh ' +
                    '&& sudo sh -c \'echo "export datetime=$(date +"%Y-%m-%d_%H_%M_%S")" ' +
                    '> devOpsEnvVars.sh\'' +
                    '&& sudo sh -c ' +
                    '\'echo "export backedUpFileName=Jenkins_ALGORITHM_\\$datetime" ' +
                    '>> devOpsEnvVars.sh\''

                    sshCommand remote: remote, sudo: true, command:
                    'sudo sh -c ' +
                    '\'echo "echo \\"Last time this script was executed on \\" + $(date) > devOpsEnvVars.log" ' +
                    '>> devOpsEnvVars.sh\''

                    /**
                     * #####################################################################################
                     * Verifying whether all Environment variables are available and accessible.
                     * #####################################################################################
                     */
                    echo 'pty = ' + remote.get('pty')
                    echo 'Verifying whether all Environment variables are available and accessible.'
                    // sshCommand remote:remote, command: 'cat /etc/profile.d/devOpsEnvVars.sh'
                    sshCommand remote:remote, command: 'cat devOpsEnvVars.sh'
                    // sshCommand remote:remote, command: 'ls -l /etc/profile.d/devOpsEnvVars.sh'
                    sshCommand remote:remote, command: 'source ./devOpsEnvVars.sh ' +
                    '&& echo $datetime && echo $backedUpFileName'
                    sshCommand remote:remote, command: 'cat devOpsEnvVars.log'
                    sshCommand remote:remote, command: 'echo $JAVA_HOME'
                    sshCommand remote:remote, command: 'source ./devOpsEnvVars.sh ' +
                    '&& echo $custEnvVar'
                    sshCommand remote:remote, command: 'cat devOpsEnvVars.log'

                    /**
                     * #####################################################################################
                     * Backing up the current build.
                     * #####################################################################################
                     */
                    echo 'Backing up the current build.'
                    sshCommand remote:remote, command: 'source ./devOpsEnvVars.sh ' +
                    '&& cp /opt/apache-tomcat-9.0.45/webapps/Jenkins_ALGORITHM' +
                    ' /opt/backup/$backedUpFileName.war'

                    sshCommand remote:remote, command: 'source ./devOpsEnvVars.sh ' +
                    '&& cp -R /opt/apache-tomcat-9.0.45/webapps/Jenkins_ALGORITHM' +
                    ' /opt/backup/$backedUpFileName'

                    /**
                     * #####################################################################################
                     * #################### Transferring build to Remote server ############################
                     * #####################################################################################
                     */
                    echo 'Transferring file from Jenkins server to Remote server.'
                    sshPut remote: remote, from: 'Jenkins_ALGORITHM', into: '/opt/filetransfer/'

                    /**
                     * #####################################################################################
                     * Shutting down Tomcat server
                     * #####################################################################################
                    */

                    echo 'Shutting down Tomcat server'
                    sshCommand remote: remote, command: 'cd /opt/apache-tomcat-9.0.45/webapps ' +
                    '&& /opt/apache-tomcat-9.0.45/bin/shutdown.sh'

                    /**
                     * #####################################################################################
                     * Deploying the build to "webapps" folder
                     * #####################################################################################
                     */
                    echo 'Deploying the build to "webapps" folder'
                    sshCommand remote: remote, command: 'cp /opt/filetransfer/Jenkins_ALGORITHM ' +
                    '/opt/apache-tomcat-9.0.45/webapps/'

                    /**
                     * #####################################################################################
                     * Starting Tomcat server
                     * #####################################################################################
                     */
                    echo 'Starting Tomcat server.'
                    echo 'Added "nohup", since Tomcat won\'t start if immediately exited out of session.'
                    sshCommand remote: remote, command: 'cd /opt/apache-tomcat-9.0.45/webapps ' +
                    '&& nohup /opt/apache-tomcat-9.0.45/bin/startup.sh'

                }
            }
        }
        stage ('buildToPreProd branch') {
            when {
                branch 'buildToPreProd'
            }
            steps {
                echo 'Deploying to ' + env.BRANCH_NAME
            }
        }
    // stage('Remote SSH') {
    //     steps {
    //         script {
    //             def remote = [:]
    //             remote.name = 'padmahasa-VirtualBox'
    //             remote.host = '192.168.0.104'
    //             remote.user = VBOX_CREDS_USR
    //             remote.password = VBOX_CREDS_PSW
    //             remote.allowAnyHosts = true
    //             sshCommand remote: remote, command: 'ls -lrt'
    //         sshCommand remote: remote, command: "for i in {1..5}; do echo -n \"Loop \$i \"; date ; sleep 1; done"
    //         }
    //     }
    // }
    }
    post {
        always {
            echo 'Build and Deployment finished execution.'
        }
        success {
            echo 'Build and Deployment process has been SUCCESSFUL.'
        }
        failure {
            echo 'Build and Deployment process has been FAILED.'
        }
        unstable {
            echo 'This will run only if the run was marked as unstable'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }
    }
}
