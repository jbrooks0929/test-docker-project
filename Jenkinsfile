pipeline{
  agent any
  environment{
    VENV = 'venv'
  }
  stages{
    stage('Checkout Out'){
      steps{
        git branch: 'main', url: 'https://github.com/Parth2k3/test-django'
      }
    }
    stage('Set up VENV'){
      steps{
        bat 'python -m venv %VENV%'
        bat '%VENV%\\Scripts\\python -m pip install --upgrade pip'
        bat '%VENV%\\Scripts\\pip install -r requirements.txt'
      }
    }
    stage('Run the tests'){
      steps{
        bat '%VENV%\\Scripts\\python manage.py test'
      }
    }
  }
}
