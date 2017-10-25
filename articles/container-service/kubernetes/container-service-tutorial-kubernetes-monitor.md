---
title: "Tutorial do Serviço de Contêiner do Azure – monitorar o Kubernetes | Microsoft Docs"
description: "Tutorial do Serviço de Contêiner do Azure – monitorar o Kubernetes com o OMS (Microsoft Operations Management Suite)"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: danlep
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ebf35877dcd6f980af75f46b437070a6a29b7c54
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---

# <a name="monitor-a-kubernetes-cluster-with-operations-management-suite"></a>Monitorar um cluster do Kubernetes com o Operations Management Suite

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Monitorar seu cluster do Kubernetes e os contêineres é fundamental, principalmente ao gerenciar um cluster de produção em grande escala com vários aplicativos. 

Você pode usufruir de várias soluções de monitoramento do Kubernetes, da Microsoft ou de outros provedores. Neste tutorial, você deve monitorar Contêineres no [Operations Management Suite](../../operations-management-suite/operations-management-suite-overview.md), a solução de gerenciamento de TI baseada em nuvem da Microsoft. (A solução Contêineres do OMS está na versão prévia).

Este tutorial, parte sete de sete, aborda as seguintes tarefas:

> [!div class="checklist"]
> * Obter configurações de espaço de trabalho do OMS
> * Configurar agentes do OMS nos nós do Kubernetes
> * Acessar informações de monitoramento no portal do OMS ou no portal do Azure

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um aplicativo foi empacotado em um cluster de contêiner, essas imagens foram carregadas no Registro de Contêiner do Azure e um cluster do Kubernetes foi criado. 

Se você ainda não realizou essas etapas e deseja continuar acompanhando, retorne ao [Tutorial 1 – Criar imagens de contêiner](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="get-workspace-settings"></a>Obter configurações de espaço de trabalho

Quando você puder acessar o [portal do OMS](https://mms.microsoft.com), acesse **Configurações** > **Fontes Conectadas** > **Servidores Linux**. Lá, você poderá encontrar a *ID do Espaço de Trabalho* e uma *Chave do Espaço de Trabalho* primária ou secundária. Anote esses valores, que serão necessários para configurar os agentes do OMS no cluster.

## <a name="set-up-oms-agents"></a>Configurar agentes do OMS

Aqui está um arquivo YAML para configurar agentes do OMS em nós de cluster do Linux. Ele cria um [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) do Kubernetes, que executa um único pod idêntico em cada nó de cluster. O recurso DaemonSet é ideal para a implantação de um agente de monitoramento. 

Salve o texto a seguir em um arquivo chamado `oms-daemonset.yaml` e substitua os valores de espaço reservado *myWorkspaceID* e *myWorkspaceKey* pela ID e a chave do espaço de trabalho do OMS. (Em produção, você pode codificar esses valores como segredos).

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: v1.3.4-127
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent 
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: myWorkspaceID
       - name: KEY 
         value: myWorkspaceKey
       - name: DOMAIN
         value: opinsights.azure.com
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP 
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log 
          name: host-log
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   volumes:
    - name: docker-sock 
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
```

Crie o DaemonSet com o seguinte comando:

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

Para ver se o DaemonSet foi criado, execute:

```azurecli-interactive
kubectl get daemonset
```

A saída deverá ser semelhante a esta:

```azurecli-interactive
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

Depois que os agentes estiverem em execução, levará vários minutos para que o OMS ingira e processe os dados.

## <a name="access-monitoring-data"></a>Acessar dados de monitoramento

Exiba e analise os dados de monitoramento com o contêiner do OMS com a [solução Contêiner](../../log-analytics/log-analytics-containers.md) no portal do OMS ou no portal do Azure. 

Para instalar a solução Contêiner usando o [portal do OMS](https://mms.microsoft.com), acesse a **Galeria de Soluções**. Em seguida, adicione a **solução Contêiner**. Como alternativa, adicione a solução Contêineres por meio do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

No portal do OMS, procure um bloco de resumo **Contêineres** no painel do OMS. Clique no bloco para obter detalhes, incluindo: eventos de contêiner, erros, status, inventário de imagem e uso da CPU e de memória. Para obter informações mais detalhadas, clique em uma linha em qualquer bloco ou execute uma [pesquisa de logs](../../log-analytics/log-analytics-log-searches.md).

![Painel de contêineres no portal do OMS](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Da mesma forma, no portal do Azure, acesse o **Log Analytics** e selecione o nome do espaço de trabalho. Para ver o bloco de resumo **Contêineres** clique em **Soluções** > **Contêineres**. Para ver os detalhes, clique no bloco.

Consulte a [documentação do Azure Log Analytics](../../log-analytics/index.yml) para obter orientações detalhadas de como consultar e analisar dados de monitoramento.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você monitorou o cluster do Kubernetes com o OMS. As tarefas abordadas incluíram:

> [!div class="checklist"]
> * Obter configurações de espaço de trabalho do OMS
> * Configurar agentes do OMS nos nós do Kubernetes
> * Acessar informações de monitoramento no portal do OMS ou no portal do Azure


Siga este link para ver exemplos de scripts criados previamente para o Serviço de Contêiner.

> [!div class="nextstepaction"]
> [Exemplos de script do Serviço de Contêiner do Azure](cli-samples.md)

