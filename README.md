# Kubernetes

Kubernetes é um plataforma de código aberto, portável e extensiva para o gerenciamento de cargas de trabalho e serviços distribuídos em contêineres, que facilita tanto a configuração declarativa quanto a automação. Ele possui um ecossistema grande, e de rápido crescimento. Serviços, suporte, e ferramentas para Kubernetes estão amplamente disponíveis.

O Google tornou Kubernetes um projeto de código-aberto em 2014. O Kubernetes combina mais de 15 anos de experiência do Google executando cargas de trabalho produtivas em escala, com as melhores idéias e práticas da comunidade.

O nome Kubernetes tem origem no Grego, significando timoneiro ou piloto. K8s é a abreviação derivada pela troca das oito letras "ubernete" por "8", se tornado K"8"s.

Era da implantação tradicional: No início, as organizações executavam aplicações em servidores físicos. Não havia como definir limites de recursos para aplicações em um mesmo servidor físico, e isso causava problemas de alocação de recursos. Por exemplo, se várias aplicações fossem executadas em um mesmo servidor físico, poderia haver situações em que uma aplicação ocupasse a maior parte dos recursos e, como resultado, o desempenho das outras aplicações seria inferior. Uma solução para isso seria executar cada aplicação em um servidor físico diferente. Mas isso não escalava, pois os recursos eram subutilizados, e se tornava custoso para as organizações manter muitos servidores físicos.

Era da implantação virtualizada: Como solução, a virtualização foi introduzida. Esse modelo permite que você execute várias máquinas virtuais (VMs) em uma única CPU de um servidor físico. A virtualização permite que as aplicações sejam isoladas entre as VMs, e ainda fornece um nível de segurança, pois as informações de uma aplicação não podem ser acessadas livremente por outras aplicações.

A virtualização permite melhor utilização de recursos em um servidor físico, e permite melhor escalabilidade porque uma aplicação pode ser adicionada ou atualizada facilmente, reduz os custos de hardware e muito mais. Com a virtualização, você pode apresentar um conjunto de recursos físicos como um cluster de máquinas virtuais descartáveis.

Cada VM é uma máquina completa que executa todos os componentes, incluindo seu próprio sistema operacional, além do hardware virtualizado.

Era da implantação em contêineres: Contêineres são semelhantes às VMs, mas têm propriedades de isolamento flexibilizados para compartilhar o sistema operacional (SO) entre as aplicações. Portanto, os contêineres são considerados leves. Semelhante a uma VM, um contêiner tem seu próprio sistema de arquivos, compartilhamento de CPU, memória, espaço de processo e muito mais. Como eles estão separados da infraestrutura subjacente, eles são portáveis entre nuvens e distribuições de sistema operacional.



Os contêineres são uma boa maneira de agrupar e executar suas aplicações. Em um ambiente de produção, você precisa gerenciar os contêineres que executam as aplicações e garantir que não haja tempo de inatividade. Por exemplo, se um contêiner cair, outro contêiner precisa ser iniciado. Não seria mais fácil se esse comportamento fosse controlado por um sistema?

É assim que o Kubernetes vem ao resgate! O Kubernetes oferece uma estrutura para executar sistemas distribuídos de forma resiliente. Ele cuida do escalonamento e do recuperação à falha de sua aplicação, fornece padrões de implantação e muito mais. Por exemplo, o Kubernetes pode gerenciar facilmente uma implantação no método canário para seu sistema.

O Kubernetes oferece a você:

Descoberta de serviço e balanceamento de carga O Kubernetes pode expor um contêiner usando o nome DNS ou seu próprio endereço IP. Se o tráfego para um contêiner for alto, o Kubernetes pode balancear a carga e distribuir o tráfego de rede para que a implantação seja estável.
Orquestração de armazenamento O Kubernetes permite que você monte automaticamente um sistema de armazenamento de sua escolha, como armazenamentos locais, provedores de nuvem pública e muito mais.
Lançamentos e reversões automatizadas Você pode descrever o estado desejado para seus contêineres implantados usando o Kubernetes, e ele pode alterar o estado real para o estado desejado em um ritmo controlada. Por exemplo, você pode automatizar o Kubernetes para criar novos contêineres para sua implantação, remover os contêineres existentes e adotar todos os seus recursos para o novo contêiner.
Empacotamento binário automático Você fornece ao Kubernetes um cluster de nós que pode ser usado para executar tarefas nos contêineres. Você informa ao Kubernetes de quanta CPU e memória (RAM) cada contêiner precisa. O Kubernetes pode encaixar contêineres em seus nós para fazer o melhor uso de seus recursos.
Autocorreção O Kubernetes reinicia os contêineres que falham, substitui os contêineres, elimina os contêineres que não respondem à verificação de integridade definida pelo usuário e não os anuncia aos clientes até que estejam prontos para servir.
Gerenciamento de configuração e de segredos O Kubernetes permite armazenar e gerenciar informações confidenciais, como senhas, tokens OAuth e chaves SSH. Você pode implantar e atualizar segredos e configuração de aplicações sem reconstruir suas imagens de contêiner e sem expor segredos em sua pilha de configuração.

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

                
## Conclusão:

- O Deployment é quem faz o controle de versões no kubernetes, e ele faz isso gerando versões do ReplicaSet;
- O ReplicaSet fica responsável por gerenciar a quantidade de Pods, a distribuição e por manter o estado;
- O Pod é o responsável por fazer sua aplicação rodar e funcionar conforme o esperado;

Obs: Outro conceito importante relacionado são os Services que são uma coleção de Pods que são expostos como um endpoint. O Serviço propaga informações de estado e rede para todos os Worker Nodes.
                
