podTemplate(inheritFrom: 'kube-agent')
{
node(POD_LABEL) {
	stage('List all for namespace: jenkins') {
        	withKubeConfig([namespace: "jenkins"]) {
          	sh 'kubectl get all'
        	}
  	}	
}
}

