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
                        rsync -avz --exclude='myenv' ./ ${DEPLOY_USER}@${DEPLOY_SERVER}:${DEPLOY_DIR}

                        ssh ${DEPLOY_USER}@${DEPLOY_SERVER} << 'EOF'
                            cd ${DEPLOY_DIR}

                            # Ensure Python and venv are installed
                            if ! command -v python3 &> /dev/null; then
                                echo "Python3 is not installed. Install it first."
                                exit 1
                            fi

                            if ! python3 -m venv --help &> /dev/null; then
                                echo "Python venv module is missing. Run: sudo apt install python3-venv"
                                exit 1
                            fi

                            # Remove and recreate virtual environment if necessary
                            if [ ! -d "${VENV_DIR}" ]; then
                                echo "Creating virtual environment..."
                                python3 -m venv ${VENV_DIR}
                            else
                                echo "Resetting virtual environment..."
                                rm -rf ${VENV_DIR}
                                python3 -m venv ${VENV_DIR}
                            fi

                            # Ensure pip is installed
                            ${VENV_DIR}/bin/python -m ensurepip --default-pip
                            ${VENV_DIR}/bin/python -m pip install --upgrade pip

                            # Check if pip and python are correctly set up
                            ls -l ${VENV_DIR}/bin/
                            ${VENV_DIR}/bin/python --version
                            ${VENV_DIR}/bin/pip --version

                            # Activate virtual environment
                            source ${VENV_DIR}/bin/activate

                            # Install dependencies
                            pip install -r requirements.txt

                            # Collect static files, make migrations, and migrate
                            python manage.py collectstatic --noinput
                            python manage.py makemigrations
                            python manage.py migrate

                            # Create superuser
                            echo "from django.contrib.auth.models import User; User.objects.filter(username='admin').delete(); User.objects.create_superuser('admin', 'admin@gmail.com', 'admin')" | python manage.py shell

                            # Install Gunicorn
                            pip install gunicorn

                            # Restart Gunicorn service
                            sudo systemctl restart gunicorn
                        EOF
                    """
                }
            }
        }
    }
}
