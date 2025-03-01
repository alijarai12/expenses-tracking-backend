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
                // Use bash explicitly to create and activate virtual environment
                sh '''
                    #!/bin/bash
                    python3 -m venv $VENV  # Create virtual environment
                    . $VENV/bin/activate  # Activate the virtual environment using dot (.)
                    pip install --upgrade pip  # Upgrade pip
                    pip install -r requirements.txt  # Install dependencies
                '''
            }
        }

        stage('Run Migrations') {
            steps {
                // Run Django migrations inside the virtual environment
                sh '''
                    #!/bin/bash
                    . $VENV/bin/activate  # Activate virtual environment using dot (.)
                    python manage.py migrate  # Run migrations
                '''
            }
        }

        // stage('Run Application') {
        //     steps {
        //         // Run Django development server inside the virtual environment
        //         sh '''
        //             #!/bin/bash
        //             . $VENV/bin/activate  # Activate virtual environment using dot (.)
        //             python manage.py runserver 0.0.0.0:8000  # Run Django server
        //         '''
        //     }
        // }
    }
}
