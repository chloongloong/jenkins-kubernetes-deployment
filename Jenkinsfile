podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
      containers:
      - name: kubectl
        image: bitnami/kubectl:latest
        securityContext:
          runAsUser: 1000
        command:
        - cat
        tty: true
      - name: kaniko
        image: gcr.io/kaniko-project/executor:debug
        command:
        - sleep
        args:
        - 9999999
        volumeMounts:
        - name: kaniko-secret
          mountPath: /kaniko/.docker
      restartPolicy: Never
      volumes:
      - name: kaniko-secret
        secret:
            secretName: cred-mydh
            items:
            - key: .dockerconfigjson
              path: config.json
''') {
  node(POD_LABEL) {

    stage('Checkout Source') {
	script {
                    git branch: 'main',
                        credentialsId: 'github-credential',
        		url: 'https://github.com/chloongloong/jenkins-kubernetes-deployment.git'
                }
    }

    stage('Build React-App Image') {
      container('kaniko') {
        stage('Build Docker image using Kaniko') {
          sh '''
             /kaniko/executor --context `pwd` --destination chloong/hello-kaniko-react-app:$BUILD_NUMBER --destination chloong/hello-kaniko-react-app:latest
          '''
        }
      }
    }

    stage('test kubectl installation') {
      container('kubectl') {
        sh 'kubectl apply -f allin-exclude-service-deployment.yaml'
	sh 'kubectl apply -f service-np.yaml'
        sh 'kubectl -n test-react-app get pods'
      }
    }

  }
}
