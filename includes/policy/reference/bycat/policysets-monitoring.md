---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 02118ff7bf6568ba928ecea3658db1aa23e5015a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504813"
---
|Nome |Descrição |Políticas |Versão |
|---|---|---|---|
|[\[Versão prévia\]: implantar – configurar pré-requisitos para habilitar os agentes do Azure Monitor e de Segurança do Azure em máquinas virtuais](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Configure os computadores para instalar automaticamente os agentes do Azure Monitor e de Segurança do Azure. A Central de Segurança coleta eventos dos agentes e os usa para fornecer alertas de segurança e tarefas de proteção personalizadas (recomendações). Crie um grupo de recursos e um workspace do Log Analytics na mesma região que a do computador para armazenar os registros de auditoria. Essa política se aplica somente a VMs em algumas regiões. |5 |1.0.0 – versão prévia |
|[Habilitar o Azure Monitor em Conjuntos de Dimensionamento de Máquinas Virtuais](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Habilite o Azure Monitor para os Conjuntos de Dimensionamento de Máquinas Virtuais no escopo especificado (grupo de gerenciamento, assinatura ou grupo de recursos). Usa o espaço de trabalho do Log Analytics como parâmetro. Observação: se o conjunto de dimensionamento upgradePolicy for definido como Manual, você precisará aplicar a extensão para todas as VMs no conjunto ao chamar o upgrade nelas. Na CLI, isso seria az vmss update-instances. |6 |1.0.1 |
|[Habilitar o Azure Monitor para VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Habilite o Azure Monitor para as VMs (máquinas virtuais) no escopo (grupo de gerenciamento, assinatura ou grupo de recursos) especificado. Usa o espaço de trabalho do Log Analytics como parâmetro. |10 |2.0.0 |
