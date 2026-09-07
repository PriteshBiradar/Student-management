pipeline {
agent any

```
stages {

    stage('Checkout') {
        steps {
            checkout scm
        }
    }

    stage('Build') {
        steps {
            sh 'mvn clean package -DskipTests'
        }
    }

}

post {
    success {
        echo 'Student Management build successful!'
    }

    failure {
        echo 'Student Management build failed!'
    }
}
```

}
