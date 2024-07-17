pipeline {
    agent any

    stages {
        stage('Clone Repo') {
            steps {
                git url: 'https://github.com/shouryadutt/argocd-deploy', branch: 'main'
            }
        }
        stage('Create ArgoCD Applications') {
            steps {
                script {
                    def argocdNamespace = 'argocd'
                    def apps = [
                        'nodeapp': 'argocd-node.yaml',
                        'database': 'argocddb.yaml'
                    ]
                    
                    apps.each { appName, appFile ->
                        def appExists = sh(script: "kubectl get application ${appName} -n ${argocdNamespace} --ignore-not-found", returnStatus: true) == 0
                        if (!appExists) {
                            sh "kubectl apply -f ${appFile} -n ${argocdNamespace}"
                        } else {
                            echo "Application ${appName} already exists"
                        }
                    }
                }
            }
        }
        stage('Sync ArgoCD Applications') {
            steps {
                script {
                    def apps = ['nodeapp', 'database']
                    
                    apps.each { appName ->
                        sh "argocd app sync ${appName} --insecure"
                    }
                }
            }
        }
    }
}
