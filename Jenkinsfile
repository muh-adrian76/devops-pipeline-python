node {
    try {
        stage('Build') {
            docker.image('python:2-alpine').inside {
                checkout scm
                sh 'python -m py_compile ./sources/add2vals.py ./sources/calc.py || exit 1'
            }
        }

        stage('Test') {
            docker.image('qnib/pytest').inside {
                try {
                    sh 'py.test --verbose --junit-xml test-reports/results.xml ./sources/test_calc.py || exit 1'
                } finally {
                    junit 'test-reports/results.xml'
                }
            }
        }

        stage('Manual Approval') {
            input message: 'Lanjutkan ke tahap Deploy?'
        }

        stage('Deploy') {
            def dockerArgs = '--privileged -v /var/jenkins_home:/var/jenkins_home'

            docker.image('cdrx/pyinstaller-linux:python2').withRun(dockerArgs) { c ->
                sh "docker exec ${c.id} pyinstaller --onefile sources/add2vals.py"
                archiveArtifacts 'dist/add2vals'
                sh "docker exec ${c.id} ./dist/add2vals &"
                echo 'Aplikasi berhasil di-deploy dan akan dijalankan selama 1 menit.'
                sleep(time: 1, unit: 'MINUTES')
                sh "docker exec ${c.id} pkill -f add2vals"
                echo 'Aplikasi berhasil dihentikan.'
            }
        }
    } catch (Exception e) {
        echo "Pipeline gagal: ${e.message}"
        throw e
    }
}
