---
title: Usar o contêiner da Pesquisa Visual Computacional com o Kubernetes e o Helm
titleSuffix: Azure Cognitive Services
description: Saiba como implantar o contêiner da Pesquisa Visual Computacional usando o Kubernetes e o Helm.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: aahi
ms.openlocfilehash: 36091c62814cffd78c5f8132e01820070968af52
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284923"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Usar o contêiner da Pesquisa Visual Computacional com o Kubernetes e o Helm

Uma opção para gerenciar seus contêineres da Pesquisa Visual Computacional localmente é usar o Kubernetes e o Helm. Usando o Kubernetes e o Helm para definir uma imagem de contêiner da Pesquisa Visual Computacional, criaremos um pacote do Kubernetes. Esse pacote será implantado em um cluster do Kubernetes localmente. Por fim, exploraremos como testar os serviços implantados. Para obter mais informações sobre como executar os contêineres do Docker sem a orquestração do Kubernetes, confira [Instalar e executar os contêineres da Pesquisa Visual Computacional](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são necessários para usar os contêineres da Pesquisa Visual Computacional localmente:

| Obrigatório | Finalidade |
|----------|---------|
| Conta do Azure | Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][free-azure-account] antes de começar. |
| CLI do Kubernetes | A [CLI do Kubernetes][kubernetes-cli] é necessária para gerenciar as credenciais compartilhadas do registro de contêiner. O Kubernetes também é necessário antes do Helm, que é o gerenciador de pacotes do Kubernetes. |
| CLI do Helm | Instale a [CLI do Helm][helm-install], que é usada para instalar um gráfico do Helm (definição do pacote de contêiner). |
| Recurso da Pesquisa Visual Computacional |Para usar o contêiner, você precisará ter:<br><br>Um recurso da **Pesquisa Visual Computacional** do Azure, bem como a chave de API e o URI do ponto de extremidade associados. Os dois valores estão disponíveis nas páginas Visão geral e Chaves e são necessários para iniciar o contêiner.<br><br>**{API_KEY}** : uma das duas chaves de recurso disponíveis na página **Chaves**<br><br>**{ENDPOINT_URI}** : o ponto de extremidade fornecido na página **Visão Geral**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Conectar-se ao cluster do Kubernetes

Espera-se que o computador host tenha um cluster do Kubernetes disponível. Confira este tutorial sobre [Como implantar um cluster do Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) para obter um entendimento conceitual de como implantar um cluster do Kubernetes em um computador host. Encontre mais informações sobre implantações na [Documentação do Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

## <a name="configure-helm-chart-values-for-deployment"></a>Configurar os valores do gráfico do Helm para implantação

Comece criando uma pasta chamada *read*. Depois, cole o seguinte conteúdo do YAML em um novo arquivo chamado `chart.yaml`:

```yaml
apiVersion: v2
name: read
version: 1.0.0
description: A Helm chart to deploy the Read OCR container to a Kubernetes cluster
dependencies:
- name: rabbitmq
  condition: read.image.args.rabbitmq.enabled
  version: ^6.12.0
  repository: https://kubernetes-charts.storage.googleapis.com/
- name: redis
  condition: read.image.args.redis.enabled
  version: ^6.0.0
  repository: https://kubernetes-charts.storage.googleapis.com/
```

Para configurar os valores padrão do gráfico do Helm, copie e cole o YAML a seguir em um arquivo chamado `values.yaml`. Substitua os comentários `# {ENDPOINT_URI}` e `# {API_KEY}` pelos seus valores. Configure o resultExpirationPeriod, o Redis e o RabbitMQ, se necessário.

```yaml
# These settings are deployment specific and users can provide customizations
read:
  enabled: true
  image:
    name: cognitive-services-read
    registry:  mcr.microsoft.com/
    repository: azure-cognitive-services/vision/read
    tag: 3.2-preview.1
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
      
      # Result expiration period setting. Specify when the system should clean up recognition results.
      # For example, resultExpirationPeriod=1, the system will clear the recognition result 1hr after the process.
      # resultExpirationPeriod=0, the system will clear the recognition result after result retrieval.
      resultExpirationPeriod: 1
      
      # Redis storage, if configured, will be used by read OCR container to store result records.
      # A cache is required if multiple read OCR containers are placed behind load balancer.
      redis:
        enabled: false # {true/false}
        password: password

      # RabbitMQ is used for dispatching tasks. This can be useful when multiple read OCR containers are
      # placed behind load balancer.
      rabbitmq:
        enabled: false # {true/false}
        rabbitmq:
          username: user
          password: password
```

> [!IMPORTANT]
> - Se os valores `billing` e `apikey` não forem fornecidos, os serviços vão expirar após 15 minutos. Da mesma forma, a verificação falhará porque os serviços não estarão disponíveis.
> 
> - Caso implante vários contêineres de OCR de Leitura atrás de um balanceador de carga, como no Docker Compose ou no Kubernetes, será necessário obter um cache externo. Como o contêiner de processamento e o contêiner de solicitação GET podem não ser o mesmo, um cache externo armazena os resultados e compartilha-os entre os contêineres. Para obter detalhes sobre as configurações de cache, confira [Configurar contêineres do Docker da Pesquisa Visual Computacional](./computer-vision-resource-container-config.md).
>

Crie uma pasta *templates* no diretório *read*. Copie e cole o YAML a seguir em um arquivo chamado `deployment.yaml`. O arquivo `deployment.yaml` funcionará como um modelo do Helm.

> Os modelos geram arquivos de manifesto, que são descrições de recurso no formato YAML que o Kubernetes consegue entender. [– Guia de Modelo de Gráfico do Helm][chart-template-guide]

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
  template:
    metadata:
      labels:
        app: read-app       
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
        args:        
        - ReadEngineConfig:ResultExpirationPeriod={{ .Values.read.image.args.resultExpirationPeriod }}
        {{- if .Values.read.image.args.rabbitmq.enabled }}
        - Queue:RabbitMQ:HostName={{ include "rabbitmq.hostname" . }}
        - Queue:RabbitMQ:Username={{ .Values.read.image.args.rabbitmq.rabbitmq.username }}
        - Queue:RabbitMQ:Password={{ .Values.read.image.args.rabbitmq.rabbitmq.password }}
        {{- end }}      
        {{- if .Values.read.image.args.redis.enabled }}
        - Cache:Redis:Configuration={{ include "redis.connStr" . }}
        {{- end }}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}      
--- 
apiVersion: v1
kind: Service
metadata:
  name: read-service
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

Na mesma pasta *templates*, copie e cole as funções auxiliares a seguir no `helpers.tpl`. `helpers.tpl` define funções úteis que ajudam a gerar o modelo do Helm.

> [!NOTE]
> Este artigo contém referências ao termo "servidor subordinado", um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.

```yaml
{{- define "rabbitmq.hostname" -}}
{{- printf "%s-rabbitmq" .Release.Name -}}
{{- end -}}

{{- define "redis.connStr" -}}
{{- $hostMain := printf "%s-redis-master:6379" .Release.Name }}
{{- $hostReplica := printf "%s-redis-slave:6379" .Release.Name -}}
{{- $passWord := printf "password=%s" .Values.read.image.args.redis.password -}}
{{- $connTail := "ssl=False,abortConnect=False" -}}
{{- printf "%s,%s,%s,%s" $hostMain $hostReplica $passWord $connTail -}}
{{- end -}}
```
O modelo especifica um serviço de balanceador de carga e a implantação do contêiner/imagem para Leitura.

### <a name="the-kubernetes-package-helm-chart"></a>O pacote do Kubernetes (gráfico do Helm)

O *gráfico do Helm* contém a configuração de quais imagens do Docker devem ser extraídas do registro de contêiner `mcr.microsoft.com`.

> Um [gráfico do Helm][helm-charts] é uma coleção de arquivos que descrevem um conjunto relacionado de recursos do Kubernetes. É possível usar um só gráfico para implantar algo simples, como um pod memcached, ou algo complexo, como uma pilha completa de aplicativo Web com servidores HTTP, bancos de dados, caches e assim por diante.

Os *gráficos do Helm* fornecidos efetuam pull das imagens do Docker do Serviço Pesquisa Visual Computacional e do serviço correspondente do registro de contêiner `mcr.microsoft.com`.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instalar o gráfico do Helm no cluster do Kubernetes

Para instalar o *gráfico do Helm*, precisaremos executar o comando [`helm install`][helm-install-cmd]. Execute o comando de instalação no diretório acima da pasta `read`.

```console
helm install read ./read
```

Aqui está um exemplo de saída esperada de uma execução de instalação bem-sucedida:

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

A implantação do Kubernetes pode demorar vários minutos para ser concluída. Para confirmar se os pods e os serviços foram implantados e estão disponíveis corretamente, execute o seguinte comando:

```console
kubectl get all
```

Algo semelhante à seguinte saída deverá ser exibido:

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```

## <a name="deploy-multiple-v3-containers-on-the-kubernetes-cluster"></a>Implantar vários contêineres v3 no cluster do Kubernetes

Começando com a v3 do contêiner, é possível usar os contêineres em paralelo nos níveis da tarefa e da página.

Por design, cada contêiner v3 tem um dispatcher e um trabalho de reconhecimento. O dispatcher é responsável por dividir uma tarefa de várias páginas em várias subtarefas de página única. O trabalho de reconhecimento é otimizado para reconhecer um documento de página única. Para obter o paralelismo no nível da página, implante vários contêineres v3 atrás de um balanceador de carga e permita que os contêineres compartilhem um armazenamento universal e uma fila. 

> [!NOTE] 
> No momento, há suporte apenas para o Armazenamento do Azure e as Filas do Azure. 

O contêiner que recebe a solicitação pode dividir a tarefa em subtarefas de página única e adicioná-las à fila universal. Qualquer trabalho de reconhecimento de um contêiner menos ocupado pode consumir subtarefas de página única da fila, executar o reconhecimento e carregar o resultado no armazenamento. A taxa de transferência pode ser aprimorada até `n` vezes, dependendo do número de contêineres implantados.

O contêiner v3 expõe a API de investigação de atividade no caminho `/ContainerLiveness`. Use o exemplo de implantação a seguir a fim de configurar uma investigação de atividade para o Kubernetes. 

Copie e cole o YAML a seguir em um arquivo chamado `deployment.yaml`. Substitua os comentários `# {ENDPOINT_URI}` e `# {API_KEY}` pelos seus valores. Substitua o comentário `# {AZURE_STORAGE_CONNECTION_STRING}` pela Cadeia de Conexão do Armazenamento do Azure. Configure `replicas` com o número desejado, que está definido como `3` no exemplo a seguir.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
  replicas: # {NUMBER_OF_READ_CONTAINERS}
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: cognitive-services-read
        image: mcr.microsoft.com/azure-cognitive-services/vision/read
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: accept
        - name: billing
          value: # {ENDPOINT_URI}
        - name: apikey
          value: # {API_KEY}
        - name: Storage__ObjectStore__AzureBlob__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
        - name: Queue__Azure__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
        livenessProbe:
          httpGet:
            path: /ContainerLiveness
            port: 5000
          initialDelaySeconds: 60
          periodSeconds: 60
          timeoutSeconds: 20
--- 
apiVersion: v1
kind: Service
metadata:
  name: azure-cognitive-service-read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort: 5000
  selector:
    app: read-app
```

Execute o comando a seguir. 

```console
kubectl apply -f deployment.yaml
```

Abaixo está um exemplo de saída que pode ser exibido em uma execução de implantação bem-sucedida:

```console
deployment.apps/read created
service/azure-cognitive-service-read created
```

A implantação do Kubernetes pode demorar vários minutos para ser concluída. Para confirmar se os pods e os serviços foram implantados e estão disponíveis corretamente, execute o seguinte comando:

```console
kubectl get all
```

Uma saída do console semelhante à seguinte deverá ser exibida:

```console
kubectl get all
NAME                       READY   STATUS    RESTARTS   AGE
pod/read-6cbbb6678-58s9t   1/1     Running   0          3s
pod/read-6cbbb6678-kz7v4   1/1     Running   0          3s
pod/read-6cbbb6678-s2pct   1/1     Running   0          3s

NAME                                   TYPE           CLUSTER-IP   EXTERNAL-IP    PORT(S)          AGE
service/azure-cognitive-service-read   LoadBalancer   10.0.134.0   <none>         5000:30846/TCP   17h
service/kubernetes                     ClusterIP      10.0.0.1     <none>         443/TCP          78d

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   3/3     3            3           3s

NAME                             DESIRED   CURRENT   READY   AGE
replicaset.apps/read-6cbbb6678   3         3         3       3s
```

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais detalhes sobre como instalar aplicativos com o Helm no AKS (Serviço de Kubernetes do Azure), [clique aqui][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Contêineres de Serviços Cognitivos][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: /cli/azure/install-azure-cli
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
