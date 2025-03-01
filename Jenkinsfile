pipeline {
    agent any

    environment {
        VENV = 'myenv'  // Virtual environment name
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/alijarai12/expenses-tracking-backend.git'
            }
        }

        stage('Setup Python Environment') {
            steps {
                sh '/bin/bash -c "python3 -m venv $VENV && source $VENV/bin/activate && pip install -r requirements.txt"'
            }
        }


        stage('Run Migrations') {
            steps {
                sh '''
                    . $VENV/bin/activate
                    python manage.py migrate
                '''
            }
        }
        
        stage('Create Superuser') {
            steps {
                sh '''
                    . $VENV/bin/activate
                    python manage.py createsuperuser --noinput --username admin --email admin@example.com
                    python manage.py shell -c "from django.contrib.auth.models import User; user = User.objects.get(username='admin'); user.set_password('admin'); user.save()"
                '''
            }
        }


        stage('Run Application') {
            steps {
                sh '''
                    . $VENV/bin/activate
                    python manage.py runserver 0.0.0.0:8000
                '''
            }
        }
    }
}
