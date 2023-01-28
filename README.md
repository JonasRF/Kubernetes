# Kubernetes

### Apresentando Pods, ReplicaSet e Deployment

![image](https://user-images.githubusercontent.com/77034798/215281845-96cce564-b716-434d-8e51-459bb5b5eb52.png)


Os Pods são a menor unidade de um cluster K8s e é onde de fato a sua aplicação  será executada.  Os Pods são responsáveis ​​por executar um mecanismo de contêiner que pode executar um ou mais contêineres dentro do Pod. É importante notar que o Docker não é o único mecanismo de contêiner compatível, mas é de longe a escolha mais popular. Outros mecanismos de containers conhecidos são : lxc, containerd, rkt, cri-o , frakti, etc.

Cada Pod tem seu próprio endereço IP e compartilha um namespace PID, rede e nome de host.

Podemos definir um Pod usando arquivos YAML, chamados a partir da linha de comando com a ferramenta CLI kubectl. (YAML é usado para configuração, deployment, serviços e replicasets)

Nota: O kubectl é uma ferramenta de linha de comando e possui comandos que você executará para gerenciar seu cluster Kubernetes

Assim podemos criar e rodar uma aplicação criando diretamente um Pod com tudo o que precisamos, e para isso podemos usar um arquivo YAML como o abaixo:

```
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
```
                
A seguir conseguimos fazer a aplicação funcionar criando a aplicação no cluster Kubernertes usando o Kubectl e emitindo o comando:  kubectl apply -f redis.yam

No entanto usar  esta abordagem não faz muito sentido, pois se o pod morrer sua aplicação pára, e além disso, não temos escalabilidade e nenhuma inteligência na aplicação.

Assim, executar a aplicação através do Pod não é a abordagem ideal, pois precisamos de um objeto que cuide dos Pods, que mantenha os seus estados, e que escale a quantidade de Pods quando necessário.

É aqui que entra o ReplicaSet.

Um ReplicaSet é um processo que executa várias instâncias de um pod e mantém o número especificado de pods constante. Seu objetivo é manter o número especificado de instâncias de Pod em execução em um cluster a qualquer momento para evitar que os usuários percam o acesso ao aplicativo quando um Pod falhar ou estiver inacessível. Ele também é responsável por alcançar e reconciliar o estado desejado de um serviço de aplicativo. Isso significa que o ReplicaSet gerencia um conjunto de pods.

Assim, ReplicaSets são abstrações de nível superior responsáveis ​​por garantir que um determinado número de cópias exatas de um determinado pod seja executado.

Podemos também definir um replicaset usando um arquivo YAML:

```
apiVersion: v1

Kind: ReplicaSet

  metadata:
      name: redis-replicaSet

  spec:
     selector:
        matchLabels:
            app:  exp-redis
     template:
         metadata:
              labels:
                  app:  exp-redis

          spec:
             containers:
                 - name: redis    
                    image:  redis
                     ports:
                     - containterPort: 80
                         name: http       

```
                
Neste exemplo não foi definida quantidade de replicas/pod portanto a aplicação vai subir com um Pod. Para aplicar podemos emitir o comando :

### kubectl apply -f replicaset.yaml 

Agora temos mais inteligência associado ao nosso Pod:

Se um Pod for deletado ele será recriado automaticamente;
Se o Pod 'morrer' o ReplicaSet vai criar outro Pod;
O ReplicaSet também pode escalar a nossa aplicação e garantir o seu estado;
Podemos escalar a aplicação com 10 Pods usando o comando:

### kubectl scale replicaset redis-replicaset --replicas=10

Com isso o ReplicaSet vai garantir que os 10 Pods sempre estejam em execução.

Mas como podemos atualizar a versão usada em nossa aplicação de forma automática ?

Aqui entra o Deployment.

Um Deployment é um conceito de nível superior que gerencia ReplicaSets e fornece atualizações declarativas para Pods, juntamente com muitos outros recursos úteis. Você pode usar deployments para aplicar uma atualização contínua de aplicativos em Pods e, da mesma forma, reverter uma atualização que falhou. 

Um Deployment é usado para informar ao Kubernetes como criar ou modificar instâncias dos pods que contêm um aplicativo em contêiner. Os deployments podem dimensionar o número de pods de réplica, habilitar a distribuição de código atualizado de maneira controlada ou reverter para uma versão de implantação anterior, se necessário.

Podemos definir um deployment usando um arquivo YAML:

```
apiVersion: v1

Kind: Deployment

  metadata:
      name: redis-deployment

  spec:
     selector:
        matchLabels:
            app:  exp-redis
     template:
         metadata:
              labels:
                  app:  exp-redis

          spec:
             containers:
                 - name: redis    
                    image:  redis
                     ports:
                     - containterPort: 80
                         name: http       
```

O comando para implantar o deployment pode ser:
#### kubectl apply -f deployment.yaml

Assim temos um hierarquia com 3 níveis : Pods, ReplicaSets e Deployments.

O primeiro bloco de construção é o Pod, que por sua vez é usado em ReplicaSets. Além disso, tanto os pods quanto os ReplicaSets são usados ​​por Deployments.

Os ReplicaSets geralmente são vistos como uma “ponte” para deployments – e seu uso é visto como uma prática recomendada ao usar o Kubernetes.

![image](https://user-images.githubusercontent.com/77034798/215282167-c39243b7-0503-475d-80b2-c0a827ffb515.png)

                
Conclusão:

- O Deployment é quem faz o controle de versões no kubernetes, e ele faz isso gerando versões do ReplicaSet;
- O ReplicaSet fica responsável por gerenciar a quantidade de Pods, a distribuição e por manter o estado;
- O Pod é o responsável por fazer sua aplicação rodar e funcionar conforme o esperado;
Obs: Outro conceito importante relacionado são os Services que são uma coleção de Pods que são expostos como um endpoint. O Serviço propaga informações de estado e rede para todos os Worker Nodes.
                
