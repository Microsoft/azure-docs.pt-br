---
title: "Monitorar cluster do Azure Kubernetes - Gerenciamento de Operações | Microsoft Docs"
description: "Monitoramento do cluster Kubernetes no serviço de contêiner do Azure usando o Microsoft Operations Management Suite"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: bd5c81435c091d25bc14710589b7c043e9f56a25
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-microsoft-operations-management-suite-oms"></a>Monitorar um cluster do Serviço de Contêiner do Azure com o Microsoft Operations Management Suite (OMS)

## <a name="prerequisites"></a>Pré-requisitos
Este passo a passo presume que você tenha [criado um cluster Kubernetes usando o Serviço de contêiner do Azure](container-service-kubernetes-walkthrough.md).

Isso também pressupõe que você tenha as ferramentas `az` cli e `kubectl` do Azure instaladas.

Você pode testar se tem a ferramenta `az` instalada executando:

```console
$ az --version
```

Se você não tem a ferramenta `az` instalada, há instruções [aqui](https://github.com/azure/azure-cli#installation).  
Como alternativa, você pode usar o [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview), que tem a CLI do Azure `az` e as ferramentas `kubectl` já instaladas para você.  

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

## <a name="monitoring-containers-with-operations-management-suite-oms"></a>Contêineres de monitoramentos no Operations Management Suite (OMS)

O OMS (Microsoft Operations Management Suite) é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda você a gerenciar e proteger sua infraestrutura local e em nuvem. A Solução de Contêiner é uma solução do OMS Log Analytics, que ajuda você a exibir o inventário, o desempenho e os logs de contêineres em um único local. É possível realizar auditoria e solucionar problemas de contêineres vendo os logs em um local centralizado, e localizar contêineres com ruídos e excesso de consumo em um host.

![](media/container-service-monitoring-oms/image1.png)

Para saber mais sobre a Solução de Contêiner, veja [Solução de Contêiner do Log Analytics](../../log-analytics/log-analytics-containers.md).

## <a name="installing-oms-on-kubernetes"></a>Instalando o OMS em Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Obter sua ID do espaço de trabalho e a chave
Para o OMS agente para conversar com o serviço deve ser configurado com uma id do espaço de trabalho e uma chave do espaço de trabalho. Para obter a id do espaço de trabalho e você precisa criar uma conta do OMS no <https://mms.microsoft.com>. Execute as etapas a seguir para criar uma conta. Depois de criar a conta, você precisará obter sua id e chave clicando em **Configurações** e em **Fontes Conectadas**; depois em **Servidores Linux**, conforme mostrado abaixo.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-oms-agent-using-a-daemonset"></a>Instalar o agente OMS usando um DaemonSet
DaemonSets são usados pelo Kubernetes para executar uma única instância de um contêiner em cada host no cluster.
Eles são perfeitos para a execução de agentes de monitoramento.

Este é o arquivo [DaemonSet YAML](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Salve-o em um arquivo chamado `oms-daemonset.yaml` e substitua os valores de espaço reservado `WSID` e `KEY` pela ID de espaço de trabalho e a chave no arquivo.

Depois de adicionar a ID e a chave para a configuração de DaemonSet, você pode instalar o agente do OMS no cluster com o `kubectl` ferramenta de linha de comando:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-oms-agent-using-a-kubernetes-secret"></a>Instalando o agente do OMS usando um segredo do Kubernetes
Para proteger a ID e a chave de espaço de trabalho do OMS, você pode usar o segredo do Kubernetes como parte do arquivo YAML do DaemonSet.

 - Copie o script, o arquivo de modelo de segredo e o arquivo de YAML do DaemonSet (do [repositório](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) e verifique se eles estão no mesmo diretório. 
      - script de geração de segredo – secret-gen.sh
      - modelo de segredo – secret-template.yaml
   - Arquivo YAML do DaemonSet – omsagent-ds-secrets.yaml
 - Execute o script. O script solicitará a ID e a chave primária do espaço de trabalho do OMS. Insira-as e o script criará um arquivo yaml de segredo para que você possa executá-lo.   
   ```
   #> sudo bash ./secret-gen.sh 
   ```

   - Crie o pod de segredos executando o seguinte: ``` kubectl create -f omsagentsecret.yaml ```
 
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
 
  - Criar o conjunto de daemon omsagent executando ``` kubectl create -f omsagent-ds-secrets.yaml ```

### <a name="conclusion"></a>Conclusão
É isso! Depois de alguns minutos, você deve ser capaz de ver os dados que fluem para o painel do OMS.
