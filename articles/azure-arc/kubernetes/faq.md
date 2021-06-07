---
title: Perguntas frequentes sobre o Kubernetes habilitado para Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Este artigo contém uma lista de perguntas frequentes relacionadas ao Kubernetes habilitado para Azure Arc
keywords: Kubernetes, Arc, Azure, contêineres, configuração, GitOps, perguntas frequentes
ms.openlocfilehash: 84368cc63bd9aaf1df4fb281395b47a6e886cb7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025842"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Perguntas frequentes – Kubernetes habilitado para Azure Arc

Este artigo aborda as perguntas frequentes sobre o Kubernetes habilitado para Azure Arc.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Qual é a diferença entre o Kubernetes habilitado para o Azure Arc e o AKS (Serviço Kubernetes do Azure)?

O AKS é a oferta de Kubernetes gerenciado do Azure. O AKS simplifica a implantação de um cluster do Kubernetes gerenciado no Azure, transferindo grande parte da complexidade e sobrecarga operacional para o Azure. Como os mestres do Kubernetes são gerenciados pelo Azure, você só gerencia e mantém os nós do agente.

O Kubernetes habilitado para Azure Arc permite que você estenda as funcionalidades de gerenciamento do Azure (como Azure Monitor e Azure Policy) conectando os clusters do Kubernetes para o Azure. Você mantém o próprio cluster do Kubernetes subjacente.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Preciso conectar meus clusters do AKS em execução no Azure ao Azure Arc?

Não. Todos os recursos do Kubernetes habilitado para Azure Arc, incluindo Azure Monitor e Azure Policy (Gatekeeper), estão disponíveis no AKS (um recurso nativo no Azure Resource Manager).
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Devo conectar meu cluster AKS-HCI e clusters do Kubernetes no Azure Stack Hub e no Azure Stack Edge ao Azure Arc?

Sim, a conexão do cluster AKS-HCI ou dos clusters do Kubernetes no Azure Stack Edge ou no Azure Stack Hub ao Azure Arc fornece clusters com representação de recursos no Azure Resource Manager. Essa representação de recursos estende funcionalidades como a Configuração de Cluster, o Azure Monitor e o Azure Policy (Gatekeeper) para esses clusters do Kubernetes.

Se o cluster do Kubernetes habilitado para o Azure Arc estiver no Azure Stack Edge, AKS no Azure Stack HCI (>= atualização de abril de 2021) ou AKS no Windows Server 2019 Datacenter (>= atualização de abril de 2021), a configuração do Kubernetes será incluída sem nenhum custo.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Como lidar com os recursos expirados do Kubernetes habilitados para o Azure Arc?

O certificado de MSI (Identidade de Serviço Gerenciada) associado ao seu Kubernetes habilitado para Azure Arc tem uma janela de expiração de 90 dias. Depois que esse certificado expirar, o recurso será considerado `Expired` e todos os recursos (como configuração, monitoramento e política) deixarão de funcionar nesse cluster. Para que o cluster do Kubernetes volte a funcionar com o Azure Arc:

1. Exclua recursos e agentes do Kubernetes habilitados para o Azure Arc no cluster. 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Recrie o recurso do Kubernetes habilitado para Azure Arc implantando agentes no cluster.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` também excluirá as configurações sobre o cluster. Após a execução de `az connectedk8s connect`, recrie as configurações no cluster manualmente ou usando o Azure Policy.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>Se já estiver usando pipelines de CI/CD, ainda poderei usar as configurações e o Kubernetes habilitados para o Azure Arc?

Sim, você ainda pode usar configurações em um cluster que recebe implantações por meio de um pipeline de CI/CD. Em comparação com os pipelines de CI/CD tradicionais, as configurações apresentam dois benefícios adicionais:

**Reconciliação de descompasso**

O pipeline de CI/CD aplica as alterações apenas uma vez durante a execução de pipeline. No entanto, o operador GitOps no cluster pesquisa continuamente o repositório Git para buscar o estado desejado dos recursos do Kubernetes no cluster. Se o operador GitOps encontrar o estado desejado dos recursos como diferente do estado real dos recursos no cluster, esse descompasso será reconciliado.

**Aplicar GitOps em escala**

Os pipelines de CI/CD são úteis para implantações controladas por eventos no seu cluster do Kubernetes (por exemplo, um push para um repositório Git). No entanto, se você quiser implantar a mesma configuração em todos os seus clusters do Kubernetes, precisará configurar manualmente cada credencial do cluster do Kubernetes para o pipeline de CI/CD. 

Para o Kubernetes habilitado para o Azure Arc, como o Azure Resource Manager gerencia suas configurações, você pode automatizar a criação da mesma configuração em todos os recursos do Kubernetes habilitados para o Azure Arc usando o Azure Policy, dentro do escopo de uma assinatura ou de um grupo de recursos. Essa funcionalidade é ainda aplicável aos recursos do Kubernetes habilitados para Azure Arc criados após a atribuição da política.

Esse recurso aplica configurações de linha de base (como políticas de rede, associações de função e políticas de segurança de pod) em todo o inventário de cluster do Kubernetes para atender aos requisitos de conformidade e governança.

## <a name="next-steps"></a>Próximas etapas

* Percorra nosso guia de início rápido para [conectar um cluster do Kubernetes ao Azure Arc](./quickstart-connect-cluster.md).
* Já tem um cluster do Kubernetes conectado ao Azure Arc? [Crie configurações no seu cluster do Kubernetes habilitado para Arc](./tutorial-use-gitops-connected-cluster.md).
* Saiba como [usar o Azure Policy para aplicar configurações em escala](./use-azure-policy.md).