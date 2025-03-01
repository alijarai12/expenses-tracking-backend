pipeline {
    agent any

    environment {
        VENV = 'myenv'  // Virtual environment name
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Checkout your code from the Git repository
                git 'https://github.com/alijarai12/expenses-tracking-backend.git'
            }
        }

        stage('Setup Python Environment') {
            steps {
                // Create and activate virtual environment, then install dependencies
                sh '''
                    python3 -m venv $VENV  # Create virtual environment
                    source $VENV/bin/activate  # Activate the virtual environment
                    pip install --upgrade pip  # Upgrade pip
                    pip install -r requirements.txt  # Install dependencies
                '''
            }
        }

        stage('Run Migrations') {
            steps {
                // Run Django migrations inside the virtual environment
                sh '''
                    source $VENV/bin/activate  # Activate virtual environment
                    python manage.py migrate  # Run migrations
                '''
            }
        }

        stage('Run Application') {
            steps {
                // Run Django development server inside the virtual environment
                sh '''
                    source $VENV/bin/activate  # Activate virtual environment
                    python manage.py runserver 0.0.0.0:8000  # Run Django server
                '''
            }
        }
    }
}
