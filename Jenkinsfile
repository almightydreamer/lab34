pipeline {

  agent any

  triggers {
    cron('0 0-23/2 * * *')
  }

  options {
    buildDiscarder(logRotator(numToKeepStr: '10'))
    timestamps()
  }

  parameters {
    booleanParam(name: "CLEAN_WORKSPACE", description: "Check this option if you would like the workspace to be cleaned at the end.", defaultValue: true)
    booleanParam(name: "TESTING_FRONTEND", description: "Check this option if you would like to test the frontend.", defaultValue: false)
  }

  environment {
    DB_FILE = "db.sqlite3"
    DEVELOPMENT_MODE = 'True'
    ON_SUCCESS_SEND_EMAIL = "True"
    ON_FAILURE_SEND_EMAIL = "True"
  }

  stages() {

    stage("GITHUB") {
      steps {
        git branch: 'staging',
          url: 'https://github.com/almightydreamer/lab34.git'
      }
    }

    stage("BUILD") {
      steps {
        echo "INFO: Build NUMBER ${BUILD_NUMBER}"
        echo "INFO: Build TAG ${BUILD_TAG}"

        sh 'python3 -m venv "${BUILD_TAG}" && \
                    . ${BUILD_TAG}/bin/activate && \
                    ${BUILD_TAG}/bin/pip install --upgrade pip && \
                    ${BUILD_TAG}/bin/pip install -r requirements.txt && \
                    python manage.py makemigrations && python manage.py migrate && python manage.py collectstatic && deactivate'
      }
    }

    stage("TEST_BACKEND") {
      steps {
        sh '. ${BUILD_TAG}/bin/activate && python manage.py test && deactivate'
      }
    }

    stage("TEST_FRONTEND") {
      when {
        expression {
          return params.TESTING_FRONTEND;
        }
      }

      steps {
        echo "${params.TESTING_FRONTEND}"
      }
    }

    stage("DEPLOY") {
      steps {
        echo "Deployment stage"
      }
    }
  }

  post {
    always {
      echo "INFO: Build NUMBER ${BUILD_NUMBER}"
      echo "INFO: Build TAG ${BUILD_TAG}"

      junit '**/test-reports/unittest/*.xml'

      sh 'ls -lR test-reports/'
    }

    success {
      echo "I am running because the job ran successfully"

      script {

        def existsDB = fileExists "${DB_FILE}"

        if (existsDB) {

          echo "Database exists"

        } else {

          echo "No database exists"

        }
      }

      script {
        if (ON_SUCCESS_SEND_EMAIL == "True") {
          emailext subject: '$DEFAULT_SUBJECT',
            body: '$DEFAULT_CONTENT',
            replyTo: '$DEFAULT_REPLYTO',
            to: '$DEFAULT_RECIPIENTS'
        }
      }

      cleanWs()
    }

    unstable {
      echo "The build is unstable. Try fix it"

      cleanWs()
    }

    failure {
      echo "Something happened"

      script {
        if (ON_FAILURE_SEND_EMAIL == "True") {
          emailext subject: '$DEFAULT_SUBJECT',
            body: '$DEFAULT_CONTENT',
            replyTo: '$DEFAULT_REPLYTO',
            to: '$DEFAULT_RECIPIENTS'
        }
      }

      cleanWs()
    }

  }

}
