pipeline {
    agent any

    environment {
        DEPLOY_DIR = '/home/deployuser/expenses-tracking-backend'
        VENV_DIR = "${DEPLOY_DIR}/myenv"
        DEPLOY_USER = 'deployuser'
        DEPLOY_SERVER = '20.120.97.51'  // Replace with actual server IP/hostname
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    // credentialsId: 'github-credentials',
                    url: 'https://github.com/alijarai12/expenses-tracking-backend.git'
            }
        }

        stage('Deploy to Server') {
            steps {
                sshagent(credentials: ['jenkins-ssh-key']) {
                    sh """
                        # Sync code to remote server, exclude venv
                        rsync -avz --exclude='venv' ./ ${DEPLOY_USER}@${DEPLOY_SERVER}:${DEPLOY_DIR}
                        
                        # SSH into the server and set up the environment
                        ssh ${DEPLOY_USER}@${DEPLOY_SERVER} << EOF
                            cd ${DEPLOY_DIR}

                            # Create virtual environment if not exists
                            if [ ! -d "${VENV_DIR}" ]; then
                                python3 -m venv ${VENV_DIR}
                            fi
                            
                            # Activate virtual environment
                            source ${VENV_DIR}/bin/activate

                            # Install dependencies
                            pip install --upgrade pip
                            pip install -r requirements.txt


                            # Collect static files (if needed)
                            python manage.py collectstatic --noinput
                            
                            # Make migrations (if any model changes)
                            python manage.py makemigrations
                        
                            # Apply migrations to the database
                            python manage.py migrate
                        
                            # Create a superuser with predefined credentials
                            echo "from django.contrib.auth.models import User; User.objects.filter(username='admin').delete(); User.objects.create_superuser('admin', 'admin@gmail.com', 'admin')" | python manage.py shell


                            # Install Gunicorn (if not already installed)
                            pip install gunicorn
                            
                            # Restart Gunicorn service (Django/FASTAPI/Gunicorn)
                            sudo systemctl restart gunicorn  # Change this based on your setup


                        EOF
                    """
                }
            }
        }
    }
}
