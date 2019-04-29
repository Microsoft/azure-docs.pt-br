---
title: (PRETERIDO) Monitorar o cluster do Azure Kubernetes – Gerenciamento de Operações
description: Monitorando o cluster Kubernetes no Serviço de Contêiner do Azure usando o Log Analytics
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: d7370fc14a5ede23744e04ac9d35140f2368e21f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711755"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>(PRETERIDO) Monitorar um cluster do Serviço de Contêiner do Azure com Log Analytics

> [!TIP]
> Para ver a versão atualizada deste artigo que usa o Serviço de Kubernetes do Azure, confira [Azure Monitor para contêineres](../../azure-monitor/insights/container-insights-overview.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este passo a passo presume que você tenha [criado um cluster Kubernetes usando o Serviço de contêiner do Azure](container-service-kubernetes-walkthrough.md).

Isso também pressupõe que você tenha as ferramentas `az` cli e `kubectl` do Azure instaladas.

Você pode testar se tem a ferramenta `az` instalada executando:

```console
$ az --version
```

Se você não tem a ferramenta `az` instalada, há instruções [aqui](https://github.com/azure/azure-cli#installation).
Como alternativa, você pode usar o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), que tem a CLI do Azure `az` e as ferramentas `kubectl` já instaladas para você.

Você pode testar se tem a ferramenta `kubectl` instalada executando:

```console
$ kubectl version
```

Se não tem `kubectl` instalado, você pode executar:
```console
$ az acs kubernetes install-cli
```

Para testar se as chaves kubernetes estão instaladas na ferramenta kubectl, você pode executar:
```console
$ kubectl get nodes
```

Se o comando acima emitir erros, você precisará instalar as chaves do cluster kubernetes na ferramenta kubectl. Você pode fazer isso com o seguinte comando:
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Monitorar contêineres como Log Analytics

O Log Analytics é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda você a gerenciar e proteger sua infraestrutura local e de nuvem. A Solução de Contêiner é uma solução do Log Analytics, que ajuda você a exibir o inventário, o desempenho e os logs de contêineres em um único local. É possível realizar auditoria e solucionar problemas de contêineres vendo os logs em um local centralizado, e localizar contêineres com ruídos e excesso de consumo em um host.

![](media/container-service-monitoring-oms/image1.png)

Para saber mais sobre a Solução de Contêiner, veja [Solução de Contêiner do Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Instalando o Log Analytics em Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Obter sua ID do workspace e a chave
Para que o agente do Log Analytics fale com o serviço, ele precisa ser configurado com uma ID do workspace e uma chave do workspace. Para obter a ID e a chave do workspace, é necessário criar uma conta em <https://mms.microsoft.com>.
Execute as etapas a seguir para criar uma conta. Quando estiver concluída a criação da conta, você poderá obter sua ID e chave clicando na folha **Log Analytics** e, em seguida, o nome do seu workspace. Em seguida, em **Configurações Avançadas**, **Fontes Conectadas** e então **Servidores Linux**, você encontrará as informações necessárias, conforme mostrado abaixo.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Instalar o agente do Log Analytics usando um DaemonSet
DaemonSets são usados pelo Kubernetes para executar uma única instância de um contêiner em cada host no cluster.
Eles são perfeitos para a execução de agentes de monitoramento.

Este é o arquivo [DaemonSet YAML](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Salve-o em um arquivo nomeado `oms-daemonset.yaml` e substitua os valores de espaço reservado `WSID` e `KEY` pela ID do workspace e a chave no arquivo.

Depois de adicionar a ID do workspace e a chave para a configuração de DaemonSet, será possível instalar o agente do Log Analytics no cluster com a `kubectl` ferramenta de linha de comando:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Instalar o agente do Log Analytics usando um Segredo do Kubernetes
Para proteger a ID e a chave de espaço de trabalho do Log Analytics, você pode usar o segredo do Kubernetes como parte do arquivo YAML do DaemonSet.

- Copie o script, o arquivo de modelo de segredo e o arquivo de YAML do DaemonSet (do [repositório](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) e verifique se eles estão no mesmo diretório.
  - script de geração de segredo – secret-gen.sh
  - modelo de segredo – secret-template.yaml
    - Arquivo YAML do DaemonSet – omsagent-ds-secrets.yaml
- Execute o script. O script solicitará a ID e a chave primária do espaço de trabalho do Log Analytics. Insira isso e o script criará um arquivo yaml secreto para que você possa executá-lo.
  ```
  #> sudo bash ./secret-gen.sh
  ```

  - Crie o pod de segredos executando o seguinte: ```kubectl create -f omsagentsecret.yaml```

  - Para verificar, execute o seguinte:

  ```
  root@ubuntu16-13db:~# kubectl get secrets
  NAME                  TYPE                                  DATA      AGE
  default-token-gvl91   kubernetes.io/service-account-token   3         50d
  omsagent-secret       Opaque                                2         1d
  root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
  Name:           omsagent-secret
  Namespace:      default
  Labels:         <none>
  Annotations:    <none>

  Type:   Opaque

  Data
  ====
  WSID:   36 bytes
  KEY:    88 bytes
  ```

  - Criar o conjunto de daemon omsagent executando ```kubectl create -f omsagent-ds-secrets.yaml```

### <a name="conclusion"></a>Conclusão
É isso! Após alguns minutos, você poderá ver os dados que fluem para o painel do Log Analytics.
