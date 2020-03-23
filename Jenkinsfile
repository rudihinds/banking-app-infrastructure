def github_id = 'CHANGE_ME'

def git_repository = "https://github.com/${github_id}/banking-app-infrastructure.git"
def namespace = github_id.toLowerCase()
def rabbitmq_host = "${namespace}-rabbit.apps.dev.practices.armakuni.co.uk"
def app_host = "${namespace}-app.apps.dev.practices.armakuni.co.uk"
def kubectl_image = 'sepractices/jenkins-eks-kubectl-deployer:0.1.0'
def label = "build-${UUID.randomUUID().toString()}"
def cluster_name = "prod-ak-k8s-cluster"
def build_pod_template = """
kind: Pod
metadata:
  name: build-pod
spec:
  containers:
  - name: kubectl
    image: ${kubectl_image}
    imagePullPolicy: Always
    tty: true
"""

podTemplate(name: "${namespace}-app-infrastructure-build", label: label, yaml: build_pod_template) {
  node(label) {
    git git_repository

    stage('Deploy to Kubernetes') {
      withCredentials([
        string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_ACCESS_KEY_ID'),
        string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY'),
        string(credentialsId: 'KUBERNETES_SERVER', variable: 'KUBERNETES_SERVER'),
        file(credentialsId: 'KUBERNETES_CA', variable: 'KUBERNETES_CA')
      ]) {
        container(name: 'kubectl', shell: '/bin/sh') {
          sh '''kubectl config \
              set-cluster kubernetes \
              --server=$KUBERNETES_SERVER \
              --certificate-authority=$KUBERNETES_CA
          '''
          sh """kubectl config \
              set-credentials aws \
              --exec-arg=token \
              --exec-arg=-i \
              --exec-arg="${cluster_name}"
          """

          sh "yq w -i kubernetes/ingress.yml 'spec.rules[0].host' ${rabbitmq_host}"
          sh "yq w -i kubernetes/ingress.yml 'spec.rules[1].host' ${app_host}"

          sh "kubectl create namespace ${namespace} || true"
          sh "kubectl apply -n ${namespace} -f kubernetes/"
        }
      }
    }
  }
}
