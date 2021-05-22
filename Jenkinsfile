pipeline {
    agent any
    environment {
        VBOX_CREDS = credentials('padmahasa-vbox-creds')
    }
    stages {
        stage('Remote SSH') {
            def remote = [:]
            remote.name = 'padmahasa-VirtualBox'
            remote.host = '192.168.0.104'
            remote.user = $VBOX_CREDS_USR
            remote.password = $VBOX_CREDS_PSW
            remote.allowAnyHosts = true
            sshCommand remote: remote, command: 'ls -lrt'
        //sshCommand remote: remote, command: "for i in {1..5}; do echo -n \"Loop \$i \"; date ; sleep 1; done"
        }
        stage('Build') {
            steps {
                sh 'echo "Hello World"'
                sh '''
                    echo "Multiline shell steps works too"
                    ls -lah
                '''
            }
        }
    }
}
