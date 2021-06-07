---
title: Criar uma automação de segurança para alertas de segurança específicos usando um modelo do Azure Resource Manager (modelo do ARM)
description: Saiba como criar uma automação da Central de Segurança do Azure para disparar um aplicativo lógico, que será disparado por alertas específicos da Central de Segurança usando um modelo do Azure Resource Manager (modelo do ARM).
services: azure-resource-manager
author: memildin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: memildin
ms.date: 08/20/2020
ms.openlocfilehash: e53a5c4840cee0651090bec0b68cc53e13102299
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705482"
---
# <a name="quickstart-create-an-automatic-response-to-a-specific-security-alert-using-an-arm-template"></a>Início Rápido: Criar uma resposta automática para um alerta de segurança específico usando um modelo do ARM

Este guia de início rápido descreve como usar um modelo do ARM (Azure Resource Manager) para criar uma automação de fluxo de trabalho que dispara um aplicativo lógico quando alertas de segurança específicos são recebidos pela Central de Segurança do Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Para obter uma lista das funções e das permissões necessárias para trabalhar com o recurso de automação de fluxo de trabalho da Central de Segurança do Azure, confira [Automação de fluxo de trabalho](workflow-automation.md).

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-securitycenter-create-automation-for-alertnamecontains/).

:::code language="json" source="~/quickstart-templates/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json":::

### <a name="relevant-resources"></a>Recursos relevantes

- [**Microsoft.Security/automations**](/azure/templates/microsoft.security/automations): a automação que vai disparar o aplicativo lógico, depois de receber um alerta da Central de Segurança do Azure que contém uma cadeia de caracteres específica.
- [**Microsoft.Logic/workflows**](/azure/templates/microsoft.logic/workflows): um aplicativo lógico vazio que pode ser disparado.

Para obter outros modelos de início rápido da Central de Segurança, confira estas [contribuições de modelos da comunidade](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Security&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implantar o modelo

- **PowerShell**:

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- **CLI**:

  ```azurecli-interactive
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  az deployment group create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- **Portal**:

  [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

  Para encontrar mais informações sobre essa opção de implantação, confira [Usar um botão de implantação para implantar modelos por meio do repositório GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Use o portal do Azure para verificar se a automação de fluxo de trabalho foi implantada.

1. No [portal do Azure](https://portal.azure.com), abra a **Central de Segurança**.
1. Na barra de menus superior, selecione o ícone de filtro e a assinatura específica na qual você implantou a nova automação de fluxo de trabalho.
1. Na barra lateral da Central de Segurança, abra **Automação de fluxo de trabalho** e verifique a nova automação.
    :::image type="content" source="./media/quickstart-automation-alert/validating-template-run.png" alt-text="Lista de automações configuradas" lightbox="./media/quickstart-automation-alert/validating-template-run.png":::
    >[!TIP]
    > Caso você tenha muitas automações de fluxo de trabalho na sua assinatura, use a opção **filtrar por nome**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando a automação de fluxo de trabalho não for mais necessária, exclua-a usando o portal do Azure.

1. No [portal do Azure](https://portal.azure.com), abra a **Central de Segurança**.
1. Na barra de menus superior, selecione o ícone de filtro e a assinatura específica na qual você implantou a nova automação de fluxo de trabalho.
1. Na barra lateral da Central de Segurança, abra **Automação de fluxo de trabalho** e localize a automação a ser excluída.
    :::image type="content" source="./media/quickstart-automation-alert/deleting-workflow-automation.png" alt-text="Etapas para remover uma automação de fluxo de trabalho" lightbox="./media/quickstart-automation-alert/deleting-workflow-automation.png":::
1. Marque a caixa de seleção do item a ser excluído.
1. Na barra de ferramentas, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Para obter um tutorial passo a passo que orienta você durante o processo de criação de um modelo, confira:

> [!div class="nextstepaction"]
> [Tutorial: Criar e implantar seu primeiro modelo do Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
