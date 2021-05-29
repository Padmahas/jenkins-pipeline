pipeline {
    agent any
    environment {
        VBOX_CREDS = credentials('padmahasa-vbox-creds')
    }
    stages {
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
                    remote.allowAnyHosts = true

                    echo 'Listing the files available on the server'
                    sshCommand remote: remote, command: 'ls -lrt'
                    sshCommand remote: remote, command: "for i in {1..5}; do echo -n \"Loop \$i \"; date ; sleep 1; done"
                    
                    echo 'Changing directory to apache tomcat server folder.'
                    // sshCommand remote: remote, command: 'cd /opt/apache-tomcat-9.0.45/webapps && /opt/apache-tomcat-9.0.45/bin/startup.sh'
                    // sshCommand remote: remote, command: 'cd /opt/apache-tomcat-9.0.45/webapps && /opt/apache-tomcat-9.0.45/bin/shutdown.sh'

                    echo 'Transferring file from Jenkins server to Remote server.'
                    sshPut remote: remote, from: 'jenkins_pipeline_workspace.code-workspace', into: '/opt/filetransfer/'
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
}
