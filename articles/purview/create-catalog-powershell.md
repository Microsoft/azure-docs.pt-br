---
title: 'Início Rápido: Criar uma conta do Azure Purview usando o Azure PowerShell/a CLI do Azure (versão prévia)'
description: Este início rápido descreve como criar uma conta do Azure Purview usando o Azure PowerShell/a CLI do Azure.
author: hophanms
ms.author: hophan
ms.date: 11/23/2020
ms.topic: quickstart
ms.service: purview
ms.subservice: purview-data-catalog
ms.custom:
- mode-api
ms.openlocfilehash: 6266aedaec8f171a1a6ff3e0d15abdad0263767a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530870"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>Início Rápido: Criar uma conta do Azure Purview usando o Azure PowerShell/a CLI do Azure

> [!IMPORTANT]
> Atualmente, o Azure Purview está em VERSÃO PRÉVIA. Os [Termos de Uso Complementares para Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, versão prévia ou que, de outra forma, ainda não foram lançados em disponibilidade geral.

Neste início rápido, você criará uma conta do Azure Purview usando o Azure PowerShell/a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* A conta de usuário que você usa para entrar no Azure precisa ser um membro da função colaborador ou proprietário ou um administrador da assinatura do Azure.

* O próprio [locatário do Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Instale o Azure PowerShell ou a CLI do Azure em seu computador cliente para implantar o modelo: [Implantação de linha de comando](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment)

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com) com sua conta do Azure.

## <a name="configure-your-subscription"></a>Configurar sua assinatura

Se necessário, siga essas etapas para configurar sua assinatura para permitir que o Azure Purview seja executado em sua assinatura:

   1. No Portal do Azure, pesquise e selecione **Assinaturas**.

   1. Na lista de assinaturas, selecione a assinatura que você deseja usar. É necessária a permissão de acesso administrativo para a assinatura.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Captura de tela mostrando como selecionar uma assinatura no portal do Azure.":::

   1. Em sua assinatura, selecione **Provedores de recursos**. No painel **Provedores de recursos**, pesquise e registre todos os três provedores de recursos: 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      Se não estiverem registrados, registre-os selecionando **Registrar**.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Captura de tela mostrando como registrar o provedor de recursos do Microsoft dot Azure Purview no portal do Azure.":::

## <a name="create-an-azure-purview-account-instance"></a>Criar uma instância da conta do Azure Purview

1. Entrar com sua credencial do Azure

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. Se você tiver várias assinaturas do Azure, selecione a que deseja usar:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. Crie um grupo de recursos para sua conta do Purview. Você poderá ignorar esta etapa se já tiver:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. Crie um arquivo de modelo do Purview, como `purviewtemplate.json`. Você pode atualizar `name`, `location` e `capacity` (`4` ou `16`):

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. Implantar modelo do Purview

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    Para executar esse comando de implantação, você precisa ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. O comando de implantação retorna os resultados. Procure `ProvisioningState` para ver se a implantação foi bem-sucedida.
    
## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a criar uma conta do Azure Purview.

Passe para o próximo artigo para saber como permitir que os usuários acessem sua conta do Azure Purview. 

> [!div class="nextstepaction"]
> [Adicionar usuários à sua conta do Azure Purview](catalog-permissions.md)
