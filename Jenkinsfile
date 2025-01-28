pipeline {
    agent none

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                checkout scm
                sh 'ls -l ./sources'
                sh 'python -m py_compile ./sources/add2vals.py ./sources/calc.py || exit 1'
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml ./sources/test_calc.py || exit 1'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }

        stage('Manual Approval') {
            steps {
                input message: 'Lanjutkan ke tahap Deploy?'
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'cdrx/pyinstaller-linux:python2'
                }
            }
            steps {
                sh 'pwd && ls -la'
                sh 'ls -l sources'
                sh 'pyinstaller --onefile sources/add2vals.py'
                sh 'ls -l dist'
                archiveArtifacts artifacts: 'dist/add2vals', allowEmptyArchive: false
                sh './dist/add2vals &'
                echo 'Aplikasi berhasil di-deploy dan akan dijalankan selama 1 menit.'
                sleep time: 1, unit: 'MINUTES'
                sh 'pkill -f add2vals'
                echo 'Aplikasi berhasil dihentikan.'
            }
        }
    }

    post {
        failure {
            echo "Pipeline gagal: ${currentBuild.rawBuild.getLog(10).join('\n')}"
        }
    }
}
