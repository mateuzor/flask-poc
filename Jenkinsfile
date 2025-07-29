pipeline {
  agent {
    docker {
      image 'python:3.11'
    }
  }

  stages {
    stage('Install dependencies') {
      steps {
        sh 'python -m venv venv'
        sh '. venv/bin/activate && pip install -r requirements.txt'
      }
    }

    stage('Run tests') {
      steps {
        sh '. venv/bin/activate && pytest --junitxml=test-results.xml --maxfail=1 --disable-warnings -q'
      }
    }

    stage('Build') {
      steps {
        echo 'Building the project...'
      }
    }

    stage('Deploy') {
      steps {
        echo 'Deploy stage - simulado'
      }
    }
  }

  post {
    always {
      junit 'test-results.xml'
    }
  }
}
