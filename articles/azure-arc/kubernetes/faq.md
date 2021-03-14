---
title: Perguntas frequentes de kubernetes habilitadas para o Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Este artigo contém uma lista de perguntas frequentes relacionadas ao kubernetes habilitado para Arc do Azure
keywords: Kubernetes, Arc, Azure, contêineres, configuração, GitOps, perguntas frequentes
ms.openlocfilehash: d7facb842509bdf852957d46d91dfcb3912e99ba
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121704"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Perguntas frequentes-kubernetes habilitado para o Azure Arc

Este artigo aborda as perguntas frequentes sobre o kubernetes habilitado para o Azure Arc.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Qual é a diferença entre o Kubernetes habilitado para o Azure Arc e o AKS (Serviço Kubernetes do Azure)?

AKS é a oferta de kubernetes gerenciada pelo Azure. O AKS simplifica a implantação de um cluster kubernetes gerenciado no Azure, descarregando grande parte da complexidade e sobrecarga operacional no Azure. Como os mestres do Kubernetes são gerenciados pelo Azure, você só gerencia e mantém os nós do agente.

O kubernetes habilitado para Arc do Azure permite que você estenda os recursos de gerenciamento do Azure (como Azure Monitor e Azure Policy) conectando clusters kubernetes ao Azure. Você mantém o próprio cluster kubernetes subjacente.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>É necessário conectar meus clusters do AKS em execução no Azure no Azure Arc?

Não. Todos os recursos de kubernetes habilitados para o Azure Arc, incluindo Azure Monitor e Azure Policy (gatekeeper), estão disponíveis no AKS (um recurso nativo no Azure Resource Manager).
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Devo conectar meu cluster AKS-HCI e clusters kubernetes no Hub Azure Stack e Azure Stack borda ao Azure Arc?

Sim, conectar seu cluster AKS-HCI ou clusters kubernetes no Azure Stack Edge ou Azure Stack Hub ao Arc do Azure fornece clusters com representação de recursos no Azure Resource Manager. Essa representação de recursos estende os recursos como configuração de cluster, Azure Monitor e Azure Policy (gatekeeper) para clusters kubernetes conectados.

Se o cluster kubernetes habilitado para Arc do Azure estiver no Azure Stack Edge, AKS on Azure Stack HCI (>= atualização de abril de 2021) ou AKS no Windows Server 2019 datacenter (>= atualização de abril de 2021), a configuração kubernetes será incluída sem custos.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Como lidar com os recursos de kubernetes habilitados para o Arc do Azure com vencimento?

O certificado de Identidade de Serviço Gerenciada (MSI) associado à sua kubernetes habilitada para o Arc do Azure tem uma janela de expiração de 90 dias. Depois que esse certificado expirar, o recurso será considerado `Expired` e todos os recursos (como configuração, monitoramento e política) deixarão de funcionar nesse cluster. Para fazer com que o cluster kubernetes trabalhe com o arco do Azure novamente:

1. Exclua recursos e agentes kubernetes habilitados para o Azure Arc no cluster. 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Recrie o recurso kubernetes habilitado para Arc do Azure implantando agentes no cluster.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` também excluirá as configurações na parte superior do cluster. Após a execução `az connectedk8s connect` , recrie as configurações no cluster, seja manualmente ou usando Azure Policy.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>Se já estiver usando pipelines de CI/CD, ainda poderei usar as configurações e kubernetes habilitadas para o Arc do Azure?

Sim, você ainda pode usar configurações em um cluster que recebe implantações por meio de um pipeline de CI/CD. Em comparação com os pipelines de CI/CD tradicionais, as configurações apresentam dois benefícios adicionais:

**Reconciliação descompasso**

O pipeline de CI/CD aplica as alterações apenas uma vez durante a execução do pipeline. No entanto, o operador GitOps no cluster sonda continuamente o repositório Git para buscar o estado desejado dos recursos de kubernetes no cluster. Se o operador GitOps encontrar o estado desejado dos recursos para ser diferente do estado real dos recursos no cluster, essa descompasso será reconciliada.

**Aplicar GitOps em escala**

Pipelines de CI/CD são úteis para implantações controladas por eventos em seu cluster kubernetes (por exemplo, um envio por push para um repositório git). No entanto, se você quiser implantar a mesma configuração em todos os seus clusters do kubernetes, precisará configurar manualmente cada credencial do cluster do kubernetes para o pipeline de CI/CD. 

Para kubernetes habilitados para o Azure Arc, como o Azure Resource Manager gerencia suas configurações, você pode automatizar a criação da mesma configuração em todos os recursos de kubernetes habilitados para o Azure ARC usando Azure Policy, dentro do escopo de uma assinatura ou de um grupo de recursos. Esse recurso é ainda aplicável aos recursos de kubernetes habilitados para Arc do Azure criados após a atribuição de política.

Esse recurso aplica configurações de linha de base (como diretivas de rede, associações de função e políticas de segurança de Pod) em todo o inventário de cluster kubernetes para atender aos requisitos de conformidade e governança.

## <a name="next-steps"></a>Próximas etapas

* Percorra nosso guia de início rápido para [conectar um cluster kubernetes ao Azure Arc](./connect-cluster.md).
* Já tem um kubernetes do Azure conectado ao cluster? [Crie configurações em seu cluster kubernetes habilitado para Arc](./use-gitops-connected-cluster.md).
* Saiba como [usar Azure Policy para aplicar configurações em escala](./use-azure-policy.md).