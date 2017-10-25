---
title: Monitorar um cluster Azure Kubernetes com o CoScale | Microsoft Docs
description: "Monitorar um cluster Kubernetes no Serviço de Contêiner do Azure usando CoScale"
services: container-service
documentationcenter: 
author: fryckbos
manager: 
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: f894191baced710fc0f5a8c8692df98033341a48
ms.contentlocale: pt-br
ms.lasthandoff: 08/12/2017

---

# <a name="monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>Monitorar um cluster Kubernetes do Serviço de Contêiner do Azure usando CoScale

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Neste artigo, mostramos como implantar o agente [CoScale](https://www.coscale.com/) para monitorar todos os nós e contêineres em seu cluster Kubernetes do Serviço de Contêiner do Azure. Você precisa de uma conta com CoScale para essa configuração. 


## <a name="about-coscale"></a>Sobre o CoScale 

CoScale é uma plataforma de monitoramento que reúne as métricas e eventos de todos os contêineres em várias plataformas de orquestração. CoScale oferece monitoramento de pilha completa para ambientes Kubernetes. Ele fornece visualizações e análise para todas as camadas da pilha: sistema operacional, Kubernetes, Docker e aplicativos executados dentro de seu contêiner. O CoScale oferece vários painéis de monitoramentos internos e conta com detecção integrada de anomalias, a fim de permitir que operadores e desenvolvedores encontrem rapidamente os problemas de infraestrutura e de aplicativos.

![Interface do usuário do CoScale](./media/container-service-kubernetes-coscale/coscale.png)

Conforme mostra este artigo, você pode instalar agentes em um cluster Kubernetes para executar o CoScale como uma solução SaaS. Se você quiser manter seus dados no local, o CoScale também está disponível para instalação local.


## <a name="prerequisites"></a>Pré-requisitos

Primeiro, você precisa [criar uma conta do CoScale](https://www.coscale.com/free-trial).

Este passo a passo presume que você tenha [criado um cluster Kubernetes usando o Serviço de contêiner do Azure](container-service-kubernetes-walkthrough.md).

Isso também pressupõe que você tenha as ferramentas `az` da CLI do Azure e `kubectl`.

Você pode testar se tem a ferramenta `az` instalada executando:

```azurecli
az --version
```

Se você não tem a ferramenta `az` instalada, há instruções [aqui](/cli/azure/install-azure-cli).

Você pode testar se tem a ferramenta `kubectl` instalada executando:

```bash
kubectl version
```

Se não tem `kubectl` instalado, você pode executar:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Instalar o agente do CoScale com um DaemonSet
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) são usados pelo Kubernetes para executar uma única instância de um contêiner em cada host no cluster.
Eles são perfeitos para a execução de agentes de monitoramento, como o agente do CoScale.

Depois de fazer logon no CoScale, acesse a [página do agente](https://app.coscale.com/) para instalar agentes do CoScale em seu cluster usando um DaemonSet. A interface do usuário do CoScale fornece etapas de configuração interativas para criar um agente e começar a monitorar todo seu cluster Kubernetes.

![Configuração do agente do CoScale](./media/container-service-kubernetes-coscale/installation.png)

Para iniciar o agente no cluster, execute o comando fornecido:

![Iniciar o agente CoScale](./media/container-service-kubernetes-coscale/agent_script.png)

É isso! Quando seus agentes estiverem em execução, você deverá ver os dados no console em alguns minutos. Visite a [página do agente](https://app.coscale.com/) para ver um resumo de seu cluster, execute etapas adicionais de configuração e veja painéis, por exemplo, **Visão geral do cluster Kubernetes**.

![Visão geral do cluster Kubernetes](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

O agente do CoScale é implantado automaticamente em novas máquinas no cluster. O agente é atualizado automaticamente quando uma nova versão é lançada.


## <a name="next-steps"></a>Próximas etapas

Consulte a [documentação do CoScale](http://docs.coscale.com/) e o [blog](https://www.coscale.com/blog) para saber mais sobre soluções de monitoramento do CoScale. 


