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
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml=test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }

        stage('Manual Approval') {
            agent any
            steps {
                input message: 'Lanjutkan ke tahap Deployment?', ok: 'Yes'
            }
        }

        stage('Deployment') {
            agent {
                docker {
                    image 'python:2-alpine'
                    args '--user root'
                }
            }
            steps {
                sh '''
                    apk add --no-cache gcc musl-dev libffi-dev libc-dev python2-dev py-pip
                    pip install --no-cache-dir pyinstaller==3.6
                    pyinstaller --onefile sources/add2vals.py
                    echo "Menjeda selama 60 detik sebelum menyelesaikan tahap Deployment..."
                    sleep 60
                '''
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
    }
}
