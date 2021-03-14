---
title: Configurações e GitOps-kubernetes habilitado para arco do Azure
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Este artigo fornece uma visão geral conceitual da capacidade de configuração e GitOps do kubernetes habilitado para Arc do Azure.
keywords: Kubernetes, Arc, Azure, contêineres, configuração, GitOps
ms.openlocfilehash: 88a30876b25730e4cb0b4b1e19fac94b9e556adc
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121789"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Configurações e GitOps com o Azure Arc habilitado kubernetes

Em relação a kubernetes, o GitOps é a prática de declarar o estado desejado de configurações de cluster kubernetes (implantações, namespaces, etc.) em um repositório git. Essa declaração é seguida por uma implantação baseada em sondagem e pull dessas configurações de cluster usando um operador. O repositório git pode conter:
* Manifestos de formato YAML que descrevem quaisquer recursos de kubernetes válidos, incluindo namespaces, ConfigMaps, implantações, DaemonSets, etc.
* Gráficos do Helm para implantação de aplicativos.

O [fluxo](https://docs.fluxcd.io/), uma popular ferramenta de software livre no espaço GitOps, pode ser implantado no cluster kubernetes para facilitar o fluxo de configurações de um repositório Git para um cluster kubernetes. O fluxo dá suporte à implantação de seu operador no cluster e nos escopos de namespace. Um operador de fluxo implantado com escopo de namespace só pode implantar objetos kubernetes dentro desse namespace específico. A capacidade de escolher entre cluster ou escopo de namespace ajuda a obter padrões de implantação multilocatário no mesmo cluster kubernetes.

## <a name="configurations"></a>Configurações

[![Arquitetura ](./media/conceptual-configurations.png) de configurações](./media/conceptual-configurations.png#lightbox)

A conexão entre o cluster e um repositório Git é criada como um recurso de configuração ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ) na parte superior do recurso kubernetes habilitado para Arc do Azure (representado por `Microsoft.Kubernetes/connectedClusters` ) no Azure Resource Manager. 

As propriedades do recurso de configuração são usadas para implantar o operador de fluxo no cluster com os parâmetros apropriados, como o repositório Git do qual efetuar pull de manifestos e o intervalo de sondagem para recebê-los. Os dados do recurso de configuração são armazenados criptografados em repouso em um banco de dados Azure Cosmos DB para garantir a confidencialidade dos dados.

A `config-agent` execução no seu cluster é responsável por:
* Acompanhando recursos de configuração novos ou atualizados no recurso kubernetes habilitado para Arc do Azure.
* Implantando um operador de fluxo para assistir ao repositório Git para cada recurso de configuração.
* Aplicar quaisquer atualizações feitas a qualquer recurso de configuração. 

Você pode criar vários recursos de configuração com escopo de namespace no mesmo cluster de kubernetes habilitado para o Azure ARC para obter multilocação.

> [!NOTE]
> * `config-agent` monitora recursos de configuração novos ou atualizados a serem disponibilizados no recurso kubernetes habilitado para Arc. Portanto, os agentes exigem conectividade para que o estado desejado seja obtido para o cluster. Se os agentes não puderem se conectar ao Azure, haverá um atraso na propagação do estado desejado para o cluster.
> * As entradas confidenciais do cliente, como chave privada, conteúdo de hosts conhecidos, nome de usuário HTTPS e token/senha, não são armazenadas por mais de 48 horas nos serviços kubernetes habilitados para o Azure Arc. Se você estiver usando entradas confidenciais para configurações, coloque os clusters online da maneira mais regular possível.

## <a name="apply-configurations-at-scale"></a>Aplicar configurações em escala

Como Azure Resource Manager gerencia suas configurações, você pode automatizar a criação da mesma configuração em todos os recursos de kubernetes habilitados para o Azure ARC usando Azure Policy, dentro do escopo de uma assinatura ou de um grupo de recursos. 

Essa imposição em escala garante que uma configuração de linha de base comum (contendo configurações como ClusterRoleBindings, RoleBindings e NetworkPolicy) possa ser aplicada em toda a frota ou inventário de clusters de kubernetes habilitados para Arc do Azure.

## <a name="next-steps"></a>Próximas etapas

* Percorra nosso guia de início rápido para [conectar um cluster kubernetes ao Azure Arc](./connect-cluster.md).
* Já tem um kubernetes do Azure conectado ao cluster? [Crie configurações em seu cluster kubernetes habilitado para Arc](./use-gitops-connected-cluster.md).
* Saiba como [usar Azure Policy para aplicar configurações em escala](./use-azure-policy.md).