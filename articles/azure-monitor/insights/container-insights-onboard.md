---
title: Como habilitar o Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como habilitar e configurar Azure Monitor para contêineres para que você possa entender como o contêiner está sendo executado e quais problemas relacionados ao desempenho foram identificados.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: 25be8f166fec8a311fdc2ed1fa3fca6339185e94
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867528"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Como habilitar o Azure Monitor para contêineres

Este artigo fornece uma visão geral das opções disponíveis para configurar Azure Monitor para contêineres para monitorar o desempenho de cargas de trabalho implantadas em ambientes kubernetes e hospedadas no [serviço kubernetes do Azure](https://docs.microsoft.com/azure/aks/).

O Azure Monitor para contêineres pode ser habilitado para novas implantações ou para uma ou mais implantações existentes do AKS usando os seguintes métodos compatíveis:

* Na portal do Azure, Azure PowerShell ou com CLI do Azure
* Usando [Terraform e AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, verifique se você tem o seguinte:

* **Um espaço de trabalho Log Analytics.**

    Azure Monitor para contêineres dá suporte a um espaço de trabalho Log Analytics nas regiões listadas em produtos do Azure [por região](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor), excluindo a região **US gov-Virgínia**.

    Você pode criar um espaço de trabalho ao habilitar o monitoramento do novo cluster AKS ou permitir que a experiência de integração crie um espaço de trabalho padrão no grupo de recursos padrão da assinatura do cluster AKS. Se optar por criá-lo por conta própria, será possível criá-lo por meio do [Azure Resource Manager](../platform/template-workspace-configuration.md), do [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) ou no [portal do Azure](../learn/quick-create-workspace.md). Para obter uma lista dos pares de mapeamento com suporte usados para o espaço de trabalho padrão, consulte [mapeamento de região para Azure monitor para contêineres](container-insights-region-mapping.md).

* Você é membro da **função colaborador de log Analytics** para habilitar o monitoramento de contêiner. Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, veja [Gerenciar espaços de trabalho](../platform/manage-access.md).

* Você é membro da função **[proprietário](../../role-based-access-control/built-in-roles.md#owner)** no recurso de cluster AKs.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* As métricas de Prometheus não são coletadas por padrão. Antes de [Configurar o agente](container-insights-agent-config.md) para coletá-los, é importante examinar a [documentação](https://prometheus.io/) do Prometheus para entender o que você pode definir.

## <a name="components"></a>Componentes

A capacidade de monitorar o desempenho se baseia em um agente do Log Analytics para Linux em contêineres desenvolvido especificamente para o Azure Monitor para contêineres. Esse agente especializado coleta dados de desempenho e eventos de todos os nós no cluster e é implantado e registrado automaticamente com o espaço de trabalho do Log Analytics especificado durante a implantação. A versão do agente é microsoft/oms:ciprod04202018 ou posterior e é representada por uma data no seguinte formato: *mmddaaaa*.

>[!NOTE]
>Com a versão de visualização do suporte do Windows Server para AKS, um cluster AKS com nós do Windows Server não tem um agente instalado para coletar dados e encaminhá-los para Azure Monitor. Em vez disso, um nó do Linux implantado automaticamente no cluster como parte da implantação padrão coleta e encaminha os dados para Azure Monitor em nome de todos os nós do Windows no cluster.  
>

Quando uma nova versão do agente é lançada, ele é atualizado automaticamente nos clusters gerenciados do Kubernetes hospedados no AKS (Serviço de Kubernetes do Azure). Para seguir as versões lançadas, confira [comunicados sobre lançamentos de agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Se você já implantou um cluster do AKS, habilite o monitoramento usando a CLI do Azure ou um modelo do Azure Resource Manager fornecido, conforme demonstrado posteriormente neste artigo. Não é possível usar `kubectl` para atualizar, excluir, implantar ou reimplantar o agente.
>O modelo precisa ser implantado no mesmo grupo de recursos que o cluster.

Habilite Azure Monitor para contêineres usando um dos seguintes métodos descritos na tabela a seguir.

| Estado da implantação | Método | DESCRIÇÃO |
|------------------|--------|-------------|
| Novo cluster AKS | [Criar cluster usando CLI do Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Você pode habilitar o monitoramento de um novo cluster AKS criado com CLI do Azure. |
| | [Criar cluster usando Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Você pode habilitar o monitoramento de um novo cluster AKS que você cria usando a ferramenta de código-fonte aberto Terraform. |
| Cluster AKS existente | [Habilitar usando CLI do Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Você pode habilitar o monitoramento de um cluster AKS já implantado usando CLI do Azure. |
| |[Habilitar usando Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Você pode habilitar o monitoramento de um cluster AKS já implantado usando a ferramenta de código-fonte aberto Terraform. |
| | [Habilitar do Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Você pode habilitar o monitoramento de um ou mais clusters AKS já implantados na página de vários clusters do AKS no Azure Monitor. |
| | [Habilitar do cluster AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Você pode habilitar o monitoramento diretamente de um cluster AKS no portal do Azure. |
| | [Habilitar usando um modelo de Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Você pode habilitar o monitoramento de um cluster AKS com um modelo de Azure Resource Manager pré-configurado. |

## <a name="next-steps"></a>Próximas etapas

* Com o monitoramento habilitado para capturar métricas de integridade para os nós de cluster do AKS e pods, essas métricas de integridade estão disponíveis no portal do Azure. Para saber como usar o Azure Monitor para contêineres, veja [Exibir integridade do Serviço de Kubernetes do Azure](container-insights-analyze.md).
