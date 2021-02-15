---
title: Análise de alterações no Azure Monitor para VMs
description: A integração de Azure Monitor para VMs com a integração de análise de alteração de aplicativo permite que você veja as alterações feitas em uma máquina virtual que podem ter afetado o desempenho de ti.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 59799a09436d5968a441f6f17655d3138a2d84d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711151"
---
# <a name="change-analysis-in-azure-monitor-for-vms"></a>Análise de alterações no Azure Monitor para VMs
A integração de Azure Monitor para VMs com a integração de [análise de alteração de aplicativo](../app/change-analysis.md) permite que você veja as alterações feitas em uma máquina virtual que podem ter afetado o desempenho de ti.

## <a name="overview"></a>Visão geral
Suponha que você tenha uma VM que se torne um pouco lenta e queira investigar se alterações recentes em sua configuração poderiam ter afetado seu desempenho. Você exibe o desempenho da VM usando Azure Monitor para VMs e descobre que há um aumento no uso de memória na última hora. A análise de alterações pode ajudá-lo a determinar se alguma alteração de configuração feita ao contrário desse tempo foi a causa desse aumento.

O serviço de análise de alterações de aplicativo agrega alterações do [grafo de recursos do Azure](../../governance/resource-graph/how-to/get-resource-changes.md) , bem como alterações de propriedades aninhadas, como regras de segurança de rede de Azure Resource Manager. 

## <a name="enabling-change-analysis"></a>Habilitando a análise de alterações
Para carregar a análise de alterações no Azure Monitor para VMs, você deve registrar o provedor de recursos *Microsoft. ChangeAnalysis* . Na primeira vez que você iniciar Azure Monitor para VMs ou análise de alteração do aplicativo no portal do Azure, esse provedor de recursos será registrado automaticamente para você. A análise de alterações do aplicativo é um serviço gratuito que não tem sobrecarga de desempenho nos recursos.

## <a name="view-change-analysis"></a>Exibir análise de alterações
A análise de alterações está disponível na guia **desempenho** ou **mapa** de Azure monitor para VMs selecionando a opção **alterar** . 

[![Investigar alterações](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


Clique no botão **investigar alterações** para iniciar a página de análise de alteração do aplicativo filtrada para a VM. Você pode examinar as alterações listadas para ver se há alguma que possa ter causado o problema. Se não tiver certeza sobre uma alteração específica, você poderá fazer referência à **alteração por** coluna para determinar a pessoa que fez a alteração.

[![Detalhes da alteração](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre a [análise de alterações do aplicativo](../app/change-analysis.md).
- Saiba mais sobre o [grafo de recursos do Azure](../../governance/resource-graph/how-to/get-resource-changes.md). 

