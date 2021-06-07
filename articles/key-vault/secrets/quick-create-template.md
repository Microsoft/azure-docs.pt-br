---
title: Início Rápido do Azure – criar um Azure Key Vault e um segredo usando o modelo do Azure Resource Manager | Microsoft Docs
description: Início Rápido que mostra como criar cofres de chaves do Azure, assim como adicionar segredos aos cofres, usando o modelo do Azure Resource Manager.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: 1cbe5f986ca36ecc3b45cf4bb7ecffa7067a27bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936610"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-an-arm-template"></a>Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando um modelo do Resource Manager

[O Azure Key Vault](../general/overview.md) é um serviço de nuvem que fornece um armazenamento seguro de segredos, como chaves, senhas e certificados, entre outros. Este guia de início rápido tem como foco o processo de implantação de um modelo do Azure Resource Manager para criação de um cofre de chaves e um segredo.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, será preciso:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Sua ID de objeto de usuário do Azure AD é necessária para o modelo configurar permissões. O procedimento a seguir obtém a ID do objeto (GUID).

    1. Execute o comando do Azure PowerShell ou CLI do Azure a seguir selecionando **Experimentar** e colando o script no painel do shell. Para colar o script, clique com o botão direito do mouse no shell e, em seguida, selecione **Colar**.

        # <a name="cli"></a>[CLI](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Anote a ID do objeto. Você precisará dela na próxima seção deste início rápido.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-key-vault-create/).

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json":::

Há dois recursos do Azure definidos no modelo:

* [**Microsoft.KeyVault/vaults**](/azure/templates/microsoft.keyvault/vaults): crie um Azure Key Vault.
* [**Microsoft.KeyVault/vaults/secrets**](/azure/templates/microsoft.keyvault/vaults/secrets): crie um segredo do cofre de chaves.

Encontre mais amostras de modelo do Azure Key Vault nos [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria um cofre de chaves e um segredo.

    [![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Selecione ou insira os seguintes valores.

    ![Modelo do Resource Manager, integração do Key Vault, portal de implantação](../media/quick-create-template/create-key-vault-using-template-portal.png)

    A menos que o valor seja especificado, use o valor padrão para criar o cofre de chaves e um segredo.

    * **Assinatura**: selecione uma assinatura do Azure.
    * **Grupo de recursos**: selecione **Criar novo**, insira um nome exclusivo para o grupo de recursos e, em seguida, clique em **OK**.
    * **Local**: selecione um local. Por exemplo, **Centro dos EUA**.
    * **Nome do Key Vault**: insira um nome para o cofre de chaves, que precisa ser global exclusivo no namespace .vault.azure.net. Você precisará do nome na próxima seção ao validar a implantação.
    * **ID de Locatário**: a função de modelo recupera automaticamente sua ID de locatário. Não altere o valor padrão.
    * **ID de Usuário do AD**: insira sua ID de objeto de usuário do Azure AD que recuperou de [Pré-requisitos](#prerequisites).
    * **Nome do segredo**: insira um nome para o segredo armazenado no cofre de chaves. Por exemplo, **adminpassword**.
    * **Valor do segredo**: insira o valor do segredo. Se você armazenar uma senha, recomendaremos usar a senha gerada que você criou em Pré-requisitos.
    * **Concordo com os termos e condições declarados acima**: selecione.
3. Selecione **Comprar**. Depois que o cofre de chaves for implantado com êxito, você receberá uma notificação:

    ![Modelo do Resource Manager, integração do Key Vault, notificação do portal de implantação](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

O portal do Azure é usado para implantar o modelo. Além do portal do Azure, você também pode usar o Azure PowerShell, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Você pode usar o portal do Azure para verificar o cofre de chaves e o segredo ou usar o script da CLI do Azure ou Azure PowerShell a seguir para listar o segredo criado.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

A saída é semelhante à seguinte:

# <a name="cli"></a>[CLI](#tab/CLI)

![Captura de tela que mostra a saída de validação do portal de implantação na CLI.](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![Modelo do Resource Manager, integração do Key Vault, saída de validação do portal de implantação](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---

## <a name="clean-up-resources"></a>Limpar os recursos

Outros tutoriais e inícios rápidos do Key Vault complementam este início rápido. Se você planeja continuar a trabalhar com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão.
Quando não for mais necessário, exclua o grupo de recursos, que excluirá o Key Vault e os recursos relacionados. Para excluir o grupo de recursos usando a CLI do Azure ou o Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um cofre de chaves e um segredo usando um modelo do Resource Manager e validou a implantação. Para saber mais sobre o Key Vault e o Azure Resource Manager, continue com os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Saiba mais sobre o [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Examine a [Visão geral de segurança do Key Vault](../general/security-overview.md)
