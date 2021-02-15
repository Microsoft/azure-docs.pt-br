---
title: Criar ou modificar laboratórios usando modelos de Azure Resource Manager
description: Saiba como usar modelos de Azure Resource Manager com o PowerShell para criar ou modificar laboratórios automaticamente em um laboratório DevTest
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 645f1d0717514d2c7e7b16844513327127e4e1a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87272633"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Criar ou modificar laboratórios automaticamente usando modelos do Azure Resource Manager e o PowerShell

Laboratórios DevTest fornece muitos modelos de Azure Resource Manager e scripts do PowerShell que podem ajudá-lo rapidamente e criar automaticamente os novos laboratórios ou modificar laboratórios existentes e implantar esses recursos.

Este artigo ajuda a orientar você durante o processo de usar esses modelos e scripts para automatizar a criação, modificação e implantação de seus laboratórios. Este artigo também mostra onde você pode encontrar mais informações sobre como usar o PowerShell para realizar algumas tarefas comuns nos laboratórios de DevTest.

## <a name="step-1-gather-your-templates-and-scripts"></a>Etapa 1: Coletar seus modelos e scripts
Você pode encontrar modelos predefinidos do [Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) e [scripts do PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) em nosso [repositório GitHub](https://github.com/Azure/azure-devtestlab)público. Use-os como estão, ou personalize-os para suas necessidades e armazene-os em seu próprio [repositório particular do Git](devtest-lab-add-artifact-repo.md).

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Etapa 2: Modificar o modelo do Azure Resource Manager
Você pode seguir as etapas em [Criar seu primeiro modelo do Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) se você nunca tiver criado um modelo antes.

Além disso, [Práticas recomendadas para criação de modelos do Azure Resource Manager](../azure-resource-manager/templates/template-best-practices.md) oferece muitas diretrizes e sugestões para lhe ajudar a criar modelos do Azure Resource Manager que são confiáveis e fáceis de usar. Normalmente, você usará uma variação de um dos exemplos ou abordagens fornecidos e modificará o modelo para suas necessidades.

## <a name="step-3-deploy-resources-with-powershell"></a>Etapa 3: Implantar recursos com o PowerShell
Depois que você personalizou os modelos e scripts, siga as etapas necessárias para [implantar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md). O artigo fornece informações gerais sobre como usar o Azure PowerShell com modelos do Azure Resource Manager para implantar os recursos no Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Tarefas comuns que você pode executar nos laboratórios de DevTest usando o PowerShell
Há várias outras tarefas comuns que você pode automatizar usando o PowerShell. As seções da documentação a seguir descrevem as etapas necessárias para executar essas tarefas.

* [Criar uma imagem personalizada de um arquivo VHD usando o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Carregar arquivo VHD na conta de armazenamento do laboratório usando o PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Adicionar um usuário externo a um laboratório usando o PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Criar uma função personalizada do laboratório usando o PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Próximas etapas
* Saiba como criar um [repositório gito](devtest-lab-add-artifact-repo.md) em que você armazenará seus modelos personalizados ou scripts.
* Explore os [modelos de Azure Resource Manager da Galeria de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).
