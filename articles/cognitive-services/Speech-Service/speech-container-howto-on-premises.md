---
title: Usar com Kubernetes e Helm
titleSuffix: Azure Cognitive Services
description: Usando kubernetes e Helm para definir as imagens de contêiner de conversão de texto em texto e de Text para fala, criaremos um pacote kubernetes. Este pacote será implantado em um cluster kubernetes local.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/16/2019
ms.author: dapine
ms.openlocfilehash: ba292a7d3bdf58ff78764bc2095fdf4a8c486070
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326199"
---
# <a name="use-with-kubernetes-and-helm"></a>Usar com Kubernetes e Helm

Uma opção para gerenciar seus contêineres de fala no local é usar kubernetes e Helm. Usando kubernetes e Helm para definir as imagens de contêiner de conversão de texto em texto e de Text para fala, criaremos um pacote kubernetes. Este pacote será implantado em um cluster kubernetes local. Por fim, exploraremos como testar os serviços implantados e várias opções de configuração. Para obter mais informações sobre como executar contêineres do Docker sem orquestração kubernetes, consulte [instalar e executar contêineres de serviço de fala](speech-container-howto.md).

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos antes de usar os contêineres de fala locais:

|Obrigatório|Finalidade|
|--|--|
| Conta do Azure | Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][free-azure-account] antes de começar. |
| Acesso ao registro de contêiner | Para que o kubernetes pegue as imagens do Docker no cluster, ele precisará de acesso ao registro de contêiner. Você precisa [solicitar o acesso ao registro de contêiner][speech-preview-access] primeiro. |
| CLI do kubernetes | A [CLI do kubernetes][kubernetes-cli] é necessária para gerenciar as credenciais compartilhadas do registro de contêiner. O kubernetes também é necessário antes de Helm, que é o Gerenciador de pacotes do kubernetes. |
| CLI do Helm | Como parte da [CLI][helm-install] install, you'll also need to initialize Helm, which will install [Tiller][tiller-install]do Helm. |
|Recurso de fala |Para usar esses contêineres, será necessário ter:<br><br>Um recurso do Azure de _fala_ para obter a chave de cobrança associada e o URI do ponto de extremidade de cobrança. Ambos os valores estão disponíveis nas páginas de visão geral de **fala** e chaves do portal do Azure e são necessários para iniciar o contêiner.<br><br>**{Api_key}** : chave de recurso<br><br>**{ENDPOINT_URI}** : o exemplo de URI do ponto de extremidade é:`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>A configuração do computador host recomendado

Consulte os detalhes do [computador host do contêiner do serviço de fala][speech-container-host-computer] como uma referência. Este *gráfico de Helm* calcula automaticamente os requisitos de CPU e memória com base em quantos decodificações (solicitações simultâneas) que o usuário especifica. Além disso, ele se ajustará com base em se as otimizações de entrada de áudio `enabled`/texto são configuradas como. O gráfico Helm usa como padrão duas solicitações simultâneas e desabilitando a otimização.

| Serviço | CPU/contêiner | Memória/contêiner |
|--|--|--|
| **Conversão de fala em texto** | um decodificador requer um mínimo de 1.150 milicores. Se o `optimizedForAudioFile` estiver habilitado, 1.950 milicores serão necessários. (padrão: dois decodificadores) | Necessário: 2 GB<br>Certo  4 GB |
| **Conversão de Texto em Fala** | uma solicitação simultânea requer um mínimo de 500 milicores. Se o `optimizeForTurboMode` estiver habilitado, 1.000 milicores serão necessários. (padrão: duas solicitações simultâneas) | Necessário: 1 GB<br> Certo 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Conectar-se ao cluster kubernetes

Espera-se que o computador host tenha um cluster kubernetes disponível. Consulte este tutorial sobre como [implantar um cluster kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) para obter um entendimento conceitual de como implantar um cluster kubernetes em um computador host.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Compartilhando as credenciais do Docker com o cluster kubernetes

Para permitir que o cluster kubernetes `docker pull` para as imagens configuradas `containerpreview.azurecr.io` do registro de contêiner, você precisa transferir as credenciais do Docker para o cluster. Execute o [`kubectl create`][kubectl-create] comando a seguir para criar um *segredo do registro do Docker* com base nas credenciais fornecidas do pré-requisito de acesso ao registro de contêiner.

Na sua interface de linha de comando de escolha, execute o comando a seguir. Certifique-se de substituir `<username>`o `<password>`, e `<email-address>` pelas credenciais de registro de contêiner.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Se você já tiver acesso ao `containerpreview.azurecr.io` registro de contêiner, poderá criar um segredo kubernetes usando o sinalizador genérico em vez disso. Considere o seguinte comando que é executado em seu JSON de configuração do Docker.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

A saída a seguir será impressa no console quando o segredo tiver sido criado com êxito.

```console
secret "containerpreview" created
```

Para verificar se o segredo foi criado, execute o [`kubectl get`][kubectl-get] com o `secrets` sinalizador.

```console
kuberctl get secrets
```

Executar o `kubectl get secrets` imprime todos os segredos configurados.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configurar valores do gráfico Helm para implantação

Visite o [Hub do Microsoft Helm][ms-helm-hub] para todos os gráficos de Helm publicamente disponíveis oferecidos pela Microsoft. No Hub do Microsoft Helm, você encontrará o **gráfico de fala local dos serviços cognitivas**. A **fala local dos serviços cognitivas** é o gráfico que iremos instalar, mas primeiro devemos criar um `config-values.yaml` arquivo com configurações explícitas. Vamos começar adicionando o repositório da Microsoft à nossa instância do Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Em seguida, configuraremos os valores do gráfico Helm. Copie e cole o YAML a seguir em um arquivo `config-values.yaml`chamado. Para obter mais informações sobre como personalizar o **gráfico Helm de fala no local dos serviços cognitivas**, consulte [personalizar gráficos do Helm](#customize-helm-charts). Substitua os `billing` valores `apikey` e pelos seus próprios.

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-speech-to-text
    tag: latest
    pullSecrets:
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-text-to-speech
    tag: latest
    pullSecrets:
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >
```

> [!IMPORTANT]
> Se os `billing` valores `apikey` e não forem fornecidos, os serviços expirarão após 15 minutos. Da mesma forma, a verificação falhará, pois os serviços não estarão disponíveis.

### <a name="the-kubernetes-package-helm-chart"></a>O pacote kubernetes (gráfico Helm)

O *gráfico Helm* contém a configuração da imagem (s) do Docker a ser extraída `containerpreview.azurecr.io` do registro de contêiner.

> Um [gráfico do Helm][helm-charts] é uma coleção de arquivos que descrevem um conjunto relacionado de recursos do kubernetes. Um único gráfico pode ser usado para implantar algo simples, como um pod memcached, ou algo complexo, como uma pilha completa do aplicativo Web com servidores HTTP, bancos de dados, caches e assim por diante.

Os *gráficos Helm* fornecidos puxam as imagens do Docker do serviço de fala, conversão de texto em fala e os serviços de fala para texto do `containerpreview.azurecr.io` registro de contêiner.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instalar o gráfico Helm no cluster kubernetes

Para instalar o *gráfico do Helm* , precisaremos executar o [`helm install`][helm-install-cmd] comando, substituindo `<config-values.yaml>` o pelo caminho apropriado e o argumento do nome do arquivo. O `microsoft/cognitive-services-speech-onpremise` gráfico de Helm referenciado abaixo está disponível no [Hub do Microsoft Helm aqui][ms-helm-hub-speech-chart].

```console
helm install microsoft/cognitive-services-speech-onpremise \
    --version 0.1.0 \
    --values <config-values.yaml> \
    --name onprem-speech
```

Aqui está um exemplo de saída que você pode esperar ver em uma execução de instalação bem-sucedida:

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

A implantação do kubernetes pode levar mais de alguns minutos para ser concluída. Para confirmar se os pods e os serviços estão adequadamente implantados e disponíveis, execute o seguinte comando:

```console
kubectl get all
```

Você deve esperar ver algo semelhante à seguinte saída:

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>Verificar a implantação do Helm com testes do Helm

Os gráficos do Helm instalados definem *testes Helm*, que servem como uma conveniência para a verificação. Esses testes validam a prontidão do serviço. Para verificar os serviços de **conversão de fala em texto** e de **texto em fala** , executaremos o comando de [teste Helm][helm-test] .

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Esses testes falharão se o status do Pod não `Running` for ou se a implantação não estiver listada `AVAILABLE` na coluna. Seja paciente, pois isso pode levar mais de dez minutos para ser concluído.

Esses testes produzirão vários resultados de status:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Como alternativa à execução dos *testes de Helm*, você pode coletar os endereços *IP externos* e as `kubectl get all` portas correspondentes do comando. Usando o IP e a porta, abra um navegador da Web e `http://<external-ip>:<port>:/swagger/index.html` navegue até para exibir a página (s) Swagger de API.

## <a name="customize-helm-charts"></a>Personalizar gráficos do Helm

Os gráficos Helm são hierárquicos. Ser hierárquico permite a herança de gráfico, ele também atende ao conceito de especificidade, em que as configurações mais específicas substituem as regras herdadas.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais detalhes sobre como instalar aplicativos com o Helm no AKS (serviço kubernetes do Azure), [visite aqui][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Contêineres de serviços cognitivas][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/helm/#helm-install
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/developing_charts
[speech-preview-access]: https://aka.ms/speechcontainerspreview
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
