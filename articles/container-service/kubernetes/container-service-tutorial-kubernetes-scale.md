---
title: "Tutorial do Serviço de Contêiner do Azure – dimensionar aplicativo | Microsoft Docs"
description: "Tutorial do Serviço de Contêiner do Azure – dimensionar aplicativo"
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
ms.date: 09/14/2017
ms.author: danlep
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: bb528c424bc71f0309439e741c30e16d0d13c7d7
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---

# <a name="scale-kubernetes-pods-and-kubernetes-infrastructure"></a>Dimensionar pods Kubernetes e a infraestrutura do Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Se você esteve seguindo os tutoriais, você tem um cluster de Kubernetes operacional no Serviço de Contêiner do Azure e você implantou o aplicativo Azure Voting. 

Neste tutorial, parte cinco de sete, você escala horizontalmente os pods no aplicativo e experimenta o dimensionamento automático do pod. Você também aprende como escalar o número de nós de agente de VM do Azure para alterar a capacidade do cluster para hospedagem de cargas de trabalho. As tarefas concluídas incluem:

> [!div class="checklist"]
> * Dimensionamento manual de pods Kubernetes
> * Configuração de pods com dimensionamento automático executando o front-end do aplicativo
> * Dimensionar os nós de agente do Azure no Kubernetes

Nos tutoriais subsequentes, o aplicativo Azure Vote é atualizado e o Operations Management Suite configurado para monitorar o cluster Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um aplicativo foi empacotado em uma imagem de contêiner, essa imagem foi carregada no Registro de Contêiner do Azure e um cluster Kubernetes foi criado. Em seguida, o aplicativo foi executado no cluster Kubernetes. 

Se você ainda não realizou essas etapas e deseja continuar acompanhando, retorne ao [Tutorial 1 – Criar imagens de contêiner](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="manually-scale-pods"></a>Dimensionar pods manualmente

Até o momento, a instância de Redis e front-end do Azure Vote foi implantada, cada uma com uma réplica única. Para verificar, execute o comando [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```azurecli-interactive
kubectl get pods
```

Saída:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Altere manualmente o número de compartimentos na implantação `azure-vote-front` usando o comando [kubectl scale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#scale). Este exemplo aumenta o número para 5.

```azurecli-interactive
kubectl scale --replicas=5 deployment/azure-vote-front
```

Execute [kubectl get pods](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) para verificar se o Kubernetes está criando os pods. Após aproximadamente um minuto, os pods adicionais estão em execução:

```azurecli-interactive
kubectl get pods
```

Saída:

```bash
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Dimensionamento automático de pods

O Kubernetes dá suporte a [dimensionamento automático horizontal de pods](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/), para ajustar o número de pods em uma implantação dependendo da utilização da CPU ou de outras métricas selecionadas. 

Para usar o dimensionador automático, seus pods devem ter limites e solicitações de CPU definidos. Na implantação, `azure-vote-front` o contêiner de front-end solicita 0,25 CPU, com um limite de 0,5 CPU. As configurações têm a seguinte aparência:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

O exemplo a seguir usa o comando [kubectl autoscale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#autoscale) para dimensionar automaticamente o número de pods na implantação `azure-vote-front`. Aqui, se a utilização da CPU exceder 50%, o dimensionador automático aumenta os pods para um máximo de 10.


```azurecli-interactive
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Execute o seguinte comando para ver o status do dimensionador automático:

```azurecli-interactive
kubectl get hpa
```

Saída:

```bash
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Após alguns minutos, com carga mínima no aplicativo Azure Vote, o número de réplicas de pod diminui automaticamente para 3.

## <a name="scale-the-agents"></a>Dimensionar os agentes

Se você criou o cluster Kubernetes usando comandos padrão no tutorial anterior, ele tem três nós de agente. Você pode ajustar o número de agentes manualmente se você planeja ter mais ou menos cargas de trabalho de contêiner no cluster. Use o comando [az acs scale](/cli/azure/acs#scale) e especifique o número de agentes com o parâmetro `--new-agent-count`.

O exemplo a seguir aumenta o número de nós de agente para 4 no cluster Kubernetes chamado *myK8sCluster*. Esse comando leva alguns minutos para ser concluído.

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

A saída do comando mostra o número de nós de agente no valor de `agentPoolProfiles:count`:

```azurecli
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou diferentes recursos de colocação em escala em seu cluster Kubernetes. As tarefas abordadas incluíram:

> [!div class="checklist"]
> * Dimensionamento manual de pods Kubernetes
> * Configuração de pods com dimensionamento automático executando o front-end do aplicativo
> * Dimensionar os nós de agente do Azure no Kubernetes

Avance para o próximo tutorial para saber mais sobre como atualizar um aplicativo no Kubernetes.

> [!div class="nextstepaction"]
> [Atualizar um aplicativo no Kubernetes](./container-service-tutorial-kubernetes-app-update.md)


