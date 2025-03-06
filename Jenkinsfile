pipeline {
    agent any
    environment {
        DEPLOY_DIR = '/home/deployuser/expenses-tracking-backend'
        VENV_DIR = "${DEPLOY_DIR}/myenv"
        DEPLOY_USER = 'deployuser'
        DEPLOY_SERVER = '20.120.97.51'
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/alijarai12/expenses-tracking-backend.git'
            }
        }
        stage('Deploy to Server') {
            steps {
                sshagent(credentials: ['jenkins-ssh-key']) {
                    sh """
                        # Sync code to remote server, exclude venv
                        rsync -avz --exclude='myenv' ./ ${DEPLOY_USER}@${DEPLOY_SERVER}:${DEPLOY_DIR}
                        
                        # SSH into the server and set up the environment
                        ssh ${DEPLOY_USER}@${DEPLOY_SERVER} << EOF
                            cd ${DEPLOY_DIR}
                            
                            # Make sure python3-venv is installed
                            if ! command -v python3 -m venv &> /dev/null; then
                                echo "Python venv module not found, please install: sudo apt install python3-venv python3-full"
                                exit 1
                            fi
                            
                            # Create virtual environment if not exists
                            if [ ! -d "${VENV_DIR}" ]; then
                                python3 -m venv ${VENV_DIR}
                            fi
                            
                            # Explicitly use the pip from virtual environment
                            ${VENV_DIR}/bin/pip install --upgrade pip
                            ${VENV_DIR}/bin/pip install -r requirements.txt
                            
                            # Use python from virtual environment for Django commands
                            ${VENV_DIR}/bin/python manage.py collectstatic --noinput
                            ${VENV_DIR}/bin/python manage.py makemigrations
                            ${VENV_DIR}/bin/python manage.py migrate
                            
                            # Create superuser
                            echo "from django.contrib.auth.models import User; User.objects.filter(username='admin').delete(); User.objects.create_superuser('admin', 'admin@gmail.com', 'admin')" | ${VENV_DIR}/bin/python manage.py shell
                            
                            # Install Gunicorn in the virtual environment
                            ${VENV_DIR}/bin/pip install gunicorn
                            
                            # Restart Gunicorn service
                            sudo systemctl restart gunicorn
                        EOF
                    """
                }
            }
        }
    }
}
