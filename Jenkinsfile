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
            sh '''
                docker run --rm -v /var/jenkins_home:/var/jenkins_home cdrx/pyinstaller-linux:python2 pyinstaller --onefile sources/add2vals.py
                cp dist/add2vals ./
                chmod +x add2vals
                ./add2vals &
                sleep 60
                pkill -f add2vals
            '''
        }
    } catch (Exception e) {
        echo "Pipeline gagal: ${e.message}"
        throw e
    }
}
