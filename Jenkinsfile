node {
    stage('Checkout') {
        checkout scm
    }

    stage('Build') {
        docker.image('python:3.10-slim').inside('-p 5000:5000') {
            sh '''
                python -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
            '''
        }
    }
}

