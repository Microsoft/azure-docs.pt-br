---
title: 'ML Studio (clássico): módulos do PowerShell – Azure'
description: Use o PowerShell para criar e gerenciar espaços de trabalho do Azure Machine Learning Studio (clássico), experimentos, serviços Web e muito mais.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: f66363ec8b64dea1a076f81f4fc89bafe5ca4151
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322326"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Módulos do PowerShell para Azure Machine Learning Studio (clássico)

**APLICA-SE A:**  ![Aplica-se a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clássico) ![Não se aplica a. ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Usando módulos do PowerShell, você pode gerenciar programaticamente seus recursos e ativos de estúdio (clássico), como espaços de trabalho, conjuntos de fontes e serviços Web.

Você pode interagir com os recursos do Studio (clássico) usando três módulos do PowerShell:

* [Az do Azure PowerShell](#az-rm) lançado em 2018, inclui todas as funcionalidades do AzureRM, embora com nomes de cmdlet diferentes
* [AzureRM](#az-rm) lançado em 2016, substituído pelo cmdlet AZ do PowerShell
* [PowerShell do Azure Machine Learning clássico](#classic) lançado em 2016

Embora esses módulos do PowerShell tenham algumas semelhanças, cada um é projetado para cenários específicos. Este artigo descreve as diferenças entre os módulos do PowerShell e ajuda você a decidir quais escolher.  

Verifique a [tabela de suporte](#support-table) abaixo para ver quais recursos são compatíveis com cada módulo. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Az e AzureRM do Azure PowerShell

O Az agora é o módulo do PowerShell indicado para interagir com o Azure e inclui toda a funcionalidade anterior do AzureRM. O AzureRM continuará a receber as correções de bugs, mas não receberá nenhum novo recurso ou cmdlet.  O Az e o AzureRM gerenciam soluções implantadas usando o modelo de implantação do **Azure Resource Manager**. Esses recursos incluem os espaços de trabalho do estúdio (clássico) e os novos serviços Web do Studio (clássico). 

O PowerShell clássico pode ser instalado juntamente com AZ ou AzureRM para cobrir os tipos de recursos "New" e "Classic". No entanto, não é recomendável ter o Az e o AzureRM instalados ao mesmo tempo. Para decidir entre o Az e o AzureRM, a Microsoft recomenda o Az para todas as implantações futuras.  Saiba mais sobre AZ versus AzureRM e o caminho de migração na [introdução ao Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

Para começar a trabalhar com o Az, siga as [instruções de instalação do Azure Az](/powershell/azure/install-az-ps).

## <a name="powershell-classic"></a><a name="classic"></a> Clássico do PowerShell

O [módulo clássico do PowerShell](https://aka.ms/amlps) do Studio (clássico) permite que você gerencie recursos implantados usando o **modelo de implantação clássico**. Esses recursos incluem ativos de usuário do estúdio (clássico), serviços Web "clássicos" e pontos de extremidade de serviço Web "clássicos".

No entanto, a Microsoft recomenda que você use o modelo de implantação do Gerenciador de recursos para todos os recursos futuros para simplificar a implantação e o gerenciamento de recursos. Se você quiser saber mais sobre os modelos de implantação, confira o artigo [Azure Resource Manager versus implantação clássica](../../azure-resource-manager/management/deployment-models.md).

Para começar a usar o PowerShell clássico, baixe o [pacote de versão](https://github.com/hning86/azuremlps/releases) do GitHub e siga as [instruções de instalação](https://github.com/hning86/azuremlps/blob/master/README.md). As instruções explicam como desbloquear a DLL baixada/descompactada e importá-la para o ambiente do PowerShell.

O PowerShell clássico pode ser instalado juntamente com AZ ou AzureRM para cobrir os tipos de recursos "New" e "Classic".

## <a name="powershell-support-table"></a><a name="support-table"></a> Tabela de compatibilidade do PowerShell


| Tarefa | **AZ** |  **Clássico do PowerShell** |
| --- | --- | --- |
| Criar/excluir espaços de trabalho | [Modelos do Gerenciador de Recursos](./deploy-with-resource-manager-template.md) |  |
| Gerenciar planos de compromisso do espaço de trabalho | [New-AzMlCommitmentPlan](/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Gerenciar os usuários do workspace |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Gerenciar serviços Web | [New-AzMlWebService](/powershell/module/az.machinelearning/new-azmlwebservice) <br>("novos" serviços Web)| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>(serviços Web "clássicos") |
| Gerenciar pontos de extremidade de serviço Web/chaves |  [Get-AzMlWebServiceKey](/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Gerenciar conjuntos de usuários/modelos treinados| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Gerenciar experimentos do usuário |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Gerenciar módulos personalizados | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Próximas etapas
Consulte a documentação completa deste módulo do PowerShell:
* [Clássico do PowerShell](https://aka.ms/amlps)
* [Az do Azure PowerShell](/powershell/module/az.machinelearning/#machine_learning)