---
title: Módulos do PowerShell para o Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Use o PowerShell para criar e gerenciar os workspaces do Azure Machine Learning Studio, os experimentos, os serviços Web e muito mais.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 04/25/2019
ms.openlocfilehash: e3c2587fceed265c9768b6ea6f2ecf3b9a8b7b1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094305"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Módulo do PowerShell para o Azure Machine Learning Studio

Usando os módulos do PowerShell, você pode gerenciar programaticamente seus recursos e ativos do Studio como workspaces, conjuntos de dados e serviços Web.

Você pode interagir com recursos do Studio usando três módulos do PowerShell:

* [Az do Azure PowerShell](#az-rm) lançado em 2018, inclui todas as funcionalidades do AzureRM, embora com nomes de cmdlet diferentes
* [AzureRM](#az-rm) lançado em 2016, substituído pelo PowerShell Az
* [PowerShell do Azure Machine Learning clássico](#classic) lançado em 2016

Embora esses módulos do PowerShell têm algumas semelhanças, cada foi projetado para cenários específicos. Este artigo descreve as diferenças entre os módulos do PowerShell e ajuda você a decidir quais escolher.  

Verifique a [tabela de suporte](#support-table) abaixo para ver quais recursos são compatíveis com cada módulo. 

## <a name="az-rm"></a> Az e AzureRM do Azure PowerShell

O Az agora é o módulo do PowerShell indicado para interagir com o Azure e inclui toda a funcionalidade anterior do AzureRM. O AzureRM continuará a receber as correções de bugs, mas não receberá nenhum novo recurso ou cmdlet.  O Az e o AzureRM gerenciam soluções implantadas usando o modelo de implantação do **Azure Resource Manager**. Esses recursos incluem espaços de trabalho do Studio e serviços web de "Novo" Studio. 

PowerShell clássico pode ser instalado junto com Az ou AzureRM para cobrir os dois tipos de recurso de "novo" e "clássico". No entanto, não é recomendável ter o Az e o AzureRM instalados ao mesmo tempo. Para decidir entre o Az e o AzureRM, a Microsoft recomenda o Az para todas as implantações futuras.  Saiba mais sobre o Az versus AzureRM e o caminho de migração em [Introdução ao Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Para começar a trabalhar com o Az, siga as [instruções de instalação do Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> PowerShell clássico

O [módulo clássico do PowerShell](https://aka.ms/amlps) do Studio permite que você gerencie os recursos implantados usando o **modelo de implantação clássico**. Esses recursos incluem ativos do usuário Studio, serviços web "clássico" e pontos de extremidade de serviço web "clássico".

No entanto, a Microsoft recomenda que você use o modelo de implantação do Gerenciador de recursos para todos os recursos futuros para simplificar a implantação e gerenciamento de recursos. Se você quiser saber mais sobre os modelos de implantação, confira o artigo [Azure Resource Manager versus implantação clássica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

Para começar a usar o PowerShell clássico, baixe o [pacote de versão](https://github.com/hning86/azuremlps/releases) do GitHub e siga as [instruções de instalação](https://github.com/hning86/azuremlps/blob/master/README.md). As instruções explicam como desbloquear a DLL baixada/descompactada e importá-la para o ambiente do PowerShell.

PowerShell clássico pode ser instalado junto com Az ou AzureRM para cobrir os dois tipos de recurso de "novo" e "clássico".

## <a name="support-table"></a> Tabela de compatibilidade do PowerShell


| | **Az** |  **Implantação clássica do PowerShell** |
| --- | --- | --- |
| Criar/excluir espaços de trabalho | [Modelos do Gerenciador de Recursos](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Gerenciar planos de compromisso de espaço de trabalho | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Gerenciar os usuários do workspace |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Gerenciar serviços Web | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("new" web services)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>(serviços web "clássico") |
| Gerenciar pontos de extremidade de serviço web/chaves |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Gerenciar modelos de conjuntos de dados/treinados do usuário| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Gerenciar experiências do usuário |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Gerenciar módulos personalizados | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Próximas etapas
Consulte a documentação completa desses módulo do PowerShell:
* [Implantação clássica do PowerShell](https://aka.ms/amlps)
* [Az do Azure PowerShell](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
