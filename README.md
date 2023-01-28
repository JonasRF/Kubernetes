# Kubernetes

### Apresentando Pods, ReplicaSet e Deployment

![image](https://user-images.githubusercontent.com/77034798/215281845-96cce564-b716-434d-8e51-459bb5b5eb52.png)


Os Pods são a menor unidade de um cluster K8s e é onde de fato a sua aplicação  será executada.  Os Pods são responsáveis ​​por executar um mecanismo de contêiner que pode executar um ou mais contêineres dentro do Pod. É importante notar que o Docker não é o único mecanismo de contêiner compatível, mas é de longe a escolha mais popular. Outros mecanismos de containers conhecidos são : lxc, containerd, rkt, cri-o , frakti, etc.

Cada Pod tem seu próprio endereço IP e compartilha um namespace PID, rede e nome de host.

Podemos definir um Pod usando arquivos YAML, chamados a partir da linha de comando com a ferramenta CLI kubectl. (YAML é usado para configuração, deployment, serviços e replicasets)

Nota: O kubectl é uma ferramenta de linha de comando e possui comandos que você executará para gerenciar seu cluster Kubernetes

Assim podemos criar e rodar uma aplicação criando diretamente um Pod com tudo o que precisamos, e para isso podemos usar um arquivo YAML como o abaixo:

apiVersion: v1

Kind: Pod

  metadata:
      name: redis

  spec:
      containers:
         - image:  redis
           name: redis
           ports:
             - containterPort: 80
                name: http       
