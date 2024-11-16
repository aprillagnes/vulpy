properties([
  parameters([
    choice(
      name: 'BUILD_TYPE',
      choices: ['Scan Only', 'Scan + Build'],
      description: 'Pilih tipe build: Scan Only atau Scan + Build'
    ),
    choice(
      name: 'APP_VERSION',
      choices: ['good', 'bad'],
      description: 'Pilih versi aplikasi yang akan dianalisis (good/bad)'
    )
  ])
])

node {
  stage('SCM') {
    // Checkout source code from SCM
    checkout scm
  }

  stage('SonarQube Analysis') {
    // Set SonarQube Scanner
    def scannerHome = tool 'Sonarqube-Scanner';

    // Jalankan SonarQube Analysis hanya untuk folder aplikasi yang dipilih
    withSonarQubeEnv() {
      sh """
      ${scannerHome}/bin/sonar-scanner \
      -Dsonar.projectBaseDir=${params.APP_VERSION} \
      -Dsonar.sources=.
      """
    }
  }

  // Conditional Build Stage
  if (params.BUILD_TYPE == 'Scan + Build') {
    stage('Setup Python Environment') {
      echo 'Setting up Python environment for ${params.APP_VERSION} version...'

      // Install Python dependencies (using virtual environment)
      sh """
      python3 -m venv venv
      source venv/bin/activate
      pip install --upgrade pip
      pip install -r requirements.txt
      """
    }

    stage('Run Tests') {
      echo 'Running Python tests for ${params.APP_VERSION} version...'

      // Run tests using pytest in the selected folder
      sh """
      source venv/bin/activate
      pytest ${params.APP_VERSION}
      """
    }

    stage('Build Application') {
      echo 'Building the Python application for ${params.APP_VERSION} version...'

      // Simulate a Python build step (e.g., packaging or deployment preparation)
      sh """
      source venv/bin/activate
      cd ${params.APP_VERSION}
      python setup.py sdist
      """
    }
  }

  // Final notification
  stage('Post-Analysis') {
    echo "Pipeline completed for APP_VERSION: ${params.APP_VERSION} with BUILD_TYPE: ${params.BUILD_TYPE}"
  }
}
