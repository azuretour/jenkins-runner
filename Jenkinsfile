pipeline {
  agent {
    kubernetes {
      defaultContainer 'kaniko'
      yaml '''
kind: Pod
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:v1.9.0-debug
    imagePullPolicy: Always
    command:
    - sleep
    args:
    - 99d
    envFrom:
      - secretRef:
          name: acr-sp
    volumeMounts:
      - name: docker-config
        mountPath: /kaniko/.docker/
  volumes:
    - name: docker-config
      configMap:
        name: docker-config

'''
    }
  }
  stages {
    stage('Docker Build') {
      steps {
        sh '/kaniko/executor --dockerfile=${PWD}/Dockerfile -c ${PWD} --cache=true --destination=jenkinsacr9482.azurecr.io/jenkins-demo:v1.0.0'
      }
    }
    stage('Deploy Application') {
      agent {
    kubernetes {
      defaultContainer 'azcli'
      yaml '''
  kind: Pod
  spec:
    containers:
    - name: azcli
      image: bitnami/kubectl:latest
      imagePullPolicy: Always
      command:
      - sleep
      args:
      - 99d
      envFrom:
        - secretRef:
            name: acr-sp
      volumeMounts:
        - name: docker-config
          mountPath: /kaniko/.docker/
    volumes:
      - name: docker-config
        configMap:
          name: docker-config
'''
    }
  }
      steps {
        sh 'kubectl apply -f ${PWD}/pod.yaml'
      }
    }
  }
}
