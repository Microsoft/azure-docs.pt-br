---
title: Habilitar Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como habilitar e configurar Azure Monitor para contêineres para que você possa entender como o contêiner está sendo executado e quais problemas relacionados ao desempenho foram identificados.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 56f60b58cff351aa37e98cdba933c929aaaedab6
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805995"
---
# <a name="enable-azure-monitor-for-containers"></a>Habilitar Azure Monitor para contêineres

Este artigo fornece uma visão geral das opções disponíveis para configurar Azure Monitor para contêineres para monitorar o desempenho das cargas de trabalho implantadas em ambientes kubernetes e hospedadas em:

- [AKS (Serviço de Kubernetes do Azure)](../../aks/index.yml)  
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) versões 3. x e 4. x  
- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) versão 4. x  
- Um [cluster kubernetes habilitado para arco](../../azure-arc/kubernetes/overview.md)

Você também pode monitorar o desempenho de cargas de trabalho implantadas em clusters kubernetes autogerenciados hospedados em:
- Azure, usando o [mecanismo AKs](https://github.com/Azure/aks-engine)
- [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) ou local, usando o mecanismo AKs.

Você pode habilitar Azure Monitor para contêineres para uma nova implantação ou para uma ou mais implantações existentes do kubernetes usando qualquer um dos seguintes métodos com suporte:

- O Portal do Azure
- Azure PowerShell
- A CLI do Azure
- [Terraform e AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você atendeu aos seguintes requisitos:

> [!IMPORTANT]
> Log Analytics agente do Linux em contêineres (Pod do réplicaset) faz chamadas à API para todos os nós do Windows na porta Kubelet Secure (10250) no cluster para coletar métricas relacionadas ao desempenho do nó e do contêiner. A porta segura do Kubelet (: 10250) deve ser aberta na rede virtual do cluster para que a coleção de métricas relacionada ao nó do Windows e o desempenho do contêiner funcionem.
>
> Se você tiver um cluster kubernetes com nós do Windows, examine e configure o grupo de segurança de rede e as políticas de rede para certificar-se de que a porta segura do Kubelet (: 10250) esteja aberta para entrada e saída na rede virtual do cluster.


- Você tem um espaço de trabalho Log Analytics.

   Azure Monitor para contêineres dá suporte a um espaço de trabalho Log Analytics nas regiões listadas em [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

   Você pode criar um espaço de trabalho ao habilitar o monitoramento para o novo cluster AKS ou pode permitir que a experiência de integração crie um espaço de trabalho padrão no grupo de recursos padrão da assinatura do cluster AKS. 
   
   Se você optar por criar o espaço de trabalho por conta própria, poderá criá-lo por meio de: 
   - [Azure Resource Manager](../samples/resource-manager-workspace.md)
   - [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [O portal do Azure](../learn/quick-create-workspace.md) 
   
   Para obter uma lista dos pares de mapeamento com suporte a serem usados para o espaço de trabalho padrão, consulte [mapeamento de região para Azure monitor para contêineres](container-insights-region-mapping.md).

- Você é um membro do grupo de *colaboradores log Analytics* para habilitar o monitoramento de contêiner. Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, veja [Gerenciar espaços de trabalho](../platform/manage-access.md).

- Você é um membro do grupo [ *proprietário*](../../role-based-access-control/built-in-roles.md#owner) no recurso de cluster AKs.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- Para exibir os dados de monitoramento, você precisa ter [*log Analytics função leitor*](../platform/manage-access.md#manage-access-using-azure-permissions) no espaço de trabalho log Analytics, configurado com Azure monitor para contêineres.

- As métricas de Prometheus não são coletadas por padrão. Antes de [Configurar o agente](container-insights-prometheus-integration.md) para coletar as métricas, é importante examinar a documentação do [Prometheus](https://prometheus.io/) para entender quais dados podem ser recolhidos e quais métodos têm suporte.

## <a name="supported-configurations"></a>Configurações com suporte

O Azure Monitor para contêineres dá suporte oficialmente às seguintes configurações:

- Ambientes: Azure Red Hat OpenShift, kubernetes local e o mecanismo de AKS no Azure e Azure Stack. Para obter mais informações, consulte [o mecanismo AKS em Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview).
- As versões do kubernetes e da política de suporte são as mesmas [com suporte no AKs (serviço kubernetes do Azure)](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Requisitos de firewall de rede

A tabela a seguir lista as informações de configuração de proxy e firewall necessárias para que o agente em contêiner se comunique com Azure Monitor para contêineres. Todo o tráfego de rede do agente é de saída para Azure Monitor.

|recurso de agente|Porta |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

A tabela a seguir lista as informações de configuração de proxy e firewall para o Azure China 21Vianet:

|recurso de agente|Porta |Descrição | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Ingestão de dados |
| `*.oms.opinsights.azure.cn` | 443 | Integração do OMS |
| `dc.services.visualstudio.com` | 443 | Para a telemetria do agente que usa a nuvem pública do Azure Application Insights |

A tabela a seguir lista as informações de configuração de proxy e firewall para o governo dos EUA do Azure:

|recurso de agente|Porta |Descrição | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Ingestão de dados |
| `*.oms.opinsights.azure.us` | 443 | Integração do OMS |
| `dc.services.visualstudio.com` | 443 | Para a telemetria do agente que usa a nuvem pública do Azure Application Insights |

## <a name="components"></a>Componentes

Sua capacidade de monitorar o desempenho depende de um agente de Log Analytics em contêineres para Linux desenvolvido especificamente para Azure Monitor para contêineres. Esse agente especializado coleta dados de desempenho e eventos de todos os nós no cluster e é implantado e registrado automaticamente com o espaço de trabalho do Log Analytics especificado durante a implantação. 

A versão do agente é Microsoft/OMS: ciprod04202018 ou posterior, e é representada por uma data no seguinte formato: *mmddyyyy*.

>[!NOTE]
>Com a disponibilidade geral do suporte do Windows Server para AKS, um cluster AKS com nós do Windows Server tem um agente de visualização instalado como um pod de daemonset em cada nó individual do Windows Server para coletar logs e encaminhá-lo para Log Analytics. Para métricas de desempenho, um nó do Linux que é implantado automaticamente no cluster como parte da implantação padrão coleta e encaminha os dados para Azure Monitor em nome de todos os nós do Windows no cluster.

Quando uma nova versão do agente é lançada, ela é atualizada automaticamente em seus clusters kubernetes gerenciados hospedados no AKS (serviço kubernetes do Azure). Para controlar quais versões são lançadas, consulte [comunicados de versão do agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

> [!NOTE]
> Se você já tiver implantado um cluster AKS, habilitou o monitoramento usando o CLI do Azure ou um modelo de Azure Resource Manager fornecido, conforme demonstrado posteriormente neste artigo. Você não pode usar `kubectl` o para atualizar, excluir, reimplantar ou implantar o agente.
>
> O modelo precisa ser implantado no mesmo grupo de recursos que o cluster.

Para habilitar Azure Monitor para contêineres, use um dos métodos descritos na tabela a seguir:

| Estado da implantação | Método | Descrição |
|------------------|--------|-------------|
| Novo cluster kubernetes | [Criar um cluster AKS usando o CLI do Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Você pode habilitar o monitoramento para um novo cluster AKS que você cria usando o CLI do Azure. |
| | [Criar um cluster AKS usando o Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Você pode habilitar o monitoramento para um novo cluster AKS que você cria usando a ferramenta de código-fonte aberto Terraform. |
| | [Criar um cluster OpenShift usando um modelo de Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Você pode habilitar o monitoramento para um novo cluster OpenShift que você cria usando um modelo de Azure Resource Manager pré-configurado. |
| | [Criar um cluster OpenShift usando o CLI do Azure](/cli/azure/openshift#az-openshift-create) | Você pode habilitar o monitoramento ao implantar um novo cluster OpenShift usando o CLI do Azure. |
| Cluster kubernetes existente | [Habilitar o monitoramento de um cluster AKS usando o CLI do Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Você pode habilitar o monitoramento para um cluster AKS que já está implantado usando o CLI do Azure. |
| |[Habilitar para o cluster AKS usando Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Você pode habilitar o monitoramento para um cluster AKS que já está implantado usando a ferramenta de código-fonte aberto Terraform. |
| | [Habilitar para o cluster AKS de Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Você pode habilitar o monitoramento para um ou mais clusters AKS que já estão implantados na página de vários clusters no Azure Monitor. |
| | [Habilitar do cluster AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Você pode habilitar o monitoramento diretamente de um cluster AKS no portal do Azure. |
| | [Habilitar para o cluster AKS usando um modelo de Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Você pode habilitar o monitoramento para um cluster AKS usando um modelo de Azure Resource Manager pré-configurado. |
| | [Habilitar para o cluster kubernetes híbrido](container-insights-hybrid-setup.md) | Você pode habilitar o monitoramento para o mecanismo AKS hospedado no Azure Stack ou para um cluster kubernetes hospedado no local. |
| | [Habilitar para o cluster kubernetes habilitado para Arc](container-insights-enable-arc-enabled-clusters.md). | Você pode habilitar o monitoramento para seus clusters kubernetes que são hospedados fora do Azure e habilitados com o Azure Arc. |
| | [Habilitar para o cluster OpenShift usando um modelo de Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Você pode habilitar o monitoramento para um cluster OpenShift existente usando um modelo de Azure Resource Manager pré-configurado. |
| | [Habilitar para o cluster OpenShift de Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Você pode habilitar o monitoramento para um ou mais clusters OpenShift que já estão implantados na página de multicluster no Azure Monitor. |

## <a name="next-steps"></a>Próximas etapas

Agora que você habilitou o monitoramento, você pode começar a analisar o desempenho dos clusters kubernetes hospedados no AKS (serviço kubernetes do Azure), Azure Stack ou em outro ambiente. Para saber como usar Azure Monitor para contêineres, consulte [Exibir o desempenho do cluster kubernetes](container-insights-analyze.md).
