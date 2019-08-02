---
title: Integrar o Azure Key Vault à implantação de modelo do Resource Manager | Microsoft Docs
description: Saiba como usar o Azure Key Vault para passar valores de parâmetro seguros durante a implantação de modelo do Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: b91a3488750795e8ee9df6602bb2b3df8a9b08ec
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436584"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-resource-manager-template-deployment"></a>Tutorial: Integrar o Azure Key Vault à implantação do modelo do Resource Manager

Saiba como recuperar segredos de um cofre de chaves do Azure e transmitir os segredos como parâmetros durante a implantação do Resource Manager. O valor do parâmetro nunca fica exposto porque você só faz referência à ID do cofre de chaves. Para saber mais, confira [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](./resource-manager-keyvault-parameter.md).

No tutorial [Definir a ordem de implantação do recurso](./resource-manager-tutorial-create-templates-with-dependent-resources.md), você cria uma VM (máquina virtual). Você precisará fornecer o nome de usuário e a senha do administrador da VM. Em vez de fornecer a senha, armazene previamente a senha em um cofre de chaves do Azure e personalize o modelo para recuperar a senha do cofre de chaves durante a implantação.

![Diagrama mostrando a integração de um modelo do Resource Manager com um cofre de chaves](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um cofre de chaves
> * Abrir um modelo de início rápido
> * Edite o arquivo de parâmetros
> * Implantar o modelo
> * Validar a implantação
> * Limpar recursos

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* [Visual Studio Code](https://code.visualstudio.com/) com a [extensão Ferramentas do Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Para aumentar a segurança, use uma senha gerada para a conta Administrador da VM. Veja um exemplo de geração de senha:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Verifique se a senha gerada atende aos requisitos de senha da VM. Cada serviço do Azure tem requisitos de senha específicos. Para os requisitos de senha da VM, confira [Quais são os requisitos de senha ao criar uma VM?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Preparar um cofre de chaves

Nesta seção, você criará um cofre de chaves e adicionará um segredo a ele para poder recuperá-lo durante a implantação do seu modelo. Há muitas maneiras de criar um cofre de chaves. Neste tutorial, você usará o Azure PowerShell para implantar um [modelo do Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). O modelo faz o seguinte:

* Crie um cofre de chaves com a propriedade `enabledForTemplateDeployment` habilitada. Essa propriedade precisa ser *true* para que o processo de implantação de modelo possa acessar os segredos definidos no cofre de chaves.
* Adicione um segredo ao cofre de chaves. O segredo armazena a senha do administrador da VM.

> [!NOTE]
> Como é o usuário que está implantando o modelo de máquina virtual, se você não for o Proprietário ou um Colaborador do cofre de chaves, um destes precisará conceder acesso à permissão *Microsoft.KeyVault/vaults/deploy/action* do cofre de chaves. Para saber mais, confira [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](./resource-manager-keyvault-parameter.md).

Para executar o script do Azure PowerShell a seguir, selecione **Experimentar** para abrir o Cloud Shell. Para colar o script, clique com o botão direito do mouse no painel do shell e selecione **Colar**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue
```

> [!IMPORTANT]
> * O nome do grupo de recursos é o nome do projeto mais as letras **rg**. Para facilitar a [limpeza dos recursos que você criou neste tutorial](#clean-up-resources), use o mesmo grupo de recursos e o mesmo nome de projeto durante a [implantação do próximo modelo](#deploy-the-template).
> * O nome padrão do segredo é **vmAdminPassword**. Ele é codificado no modelo.
> * Para habilitar a recuperação do segredo pelo modelo, ative uma política de acesso chamada “Habilitar acesso ao Azure Resource Manager para implantação de modelos” no cofre de chaves. Essa política está habilitada no modelo. Para saber mais sobre a política de acesso, confira [Implantar cofres de chaves e segredos](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

O modelo tem um valor de saída denominado *keyVaultId*. Anote o valor da ID para usá-lo quando implantar a máquina virtual. O formato da ID do recurso é:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Ao copiar e colar a ID, ela pode ficar dividida em várias linhas. Você deve mesclar as linhas e remover os espaços adicionais.

Para validar a implantação, execute o seguinte comando do PowerShell no mesmo painel de shell para recuperar o segredo em texto não criptografado. O comando funciona somente na mesma sessão de shell, pois usa a variável *$keyVaultName*, que está definida no script do PowerShell anterior.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Agora você preparou um cofre de chaves e um segredo. As seções a seguir mostrarão como personalizar um modelo existente para recuperar o segredo durante a implantação.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de início rápido

Modelos de Início Rápido do Azure é um repositório de modelos do Resource Manager. Em vez de criar um modelo do zero, você pode encontrar um exemplo de modelo e personalizá-lo. O modelo usado neste tutorial é chamado [Implantar uma VM do Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Visual Studio Code, escolha **Arquivo** > **Abrir Arquivo**.

1. Na caixa **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Escolha **Abrir** para abrir o arquivo. O cenário é o mesmo usado no [Tutorial: Criar modelos do Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
   O modelo define cinco recursos:

   * `Microsoft.Storage/storageAccounts`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   É útil ter algumas noções básicas do modelo antes de personalizá-lo.

1. Selecione **Arquivo** > **Salvar como** e salve uma cópia do arquivo no computador local com o nome *azuredeploy.json*.

1. Repita as etapas de 1 a 3 para abrir a URL a seguir e salve o arquivo como *azuredeploy.parameters.json*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Edite o arquivo de parâmetros

Você não precisa fazer nenhuma alteração ao arquivo de modelo.

1. No Visual Studio Code, abra *azuredeploy.parameters.json* se ainda não estiver aberto.
1. Atualize o parâmetro `adminPassword` para:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > Substitua o valor de **ID** pela ID do recurso do cofre de chaves que você criou no procedimento anterior.

    ![Integrar o cofre de chaves e o arquivo de parâmetros da implantação de máquina virtual do modelo do Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Atualize os seguintes valores:

    * **adminUsername**: o nome da conta de administrador da máquina virtual.
    * **dnsLabelPrefix**: dê um nome ao valor dnsLabelPrefix.

    Para ver exemplos de nomes, confira a imagem anterior.

1. Salve as alterações.

## <a name="deploy-the-template"></a>Implantar o modelo

Siga as instruções em [Implantar o modelo](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Carregue *azuredeploy. JSON* e *azuredeploy.parameters.json* para o Cloud Shell e use o seguinte script do PowerShell para implantar o modelo:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Ao implantar o modelo, use o mesmo grupo de recursos usado no cofre de chaves. Essa abordagem facilita a limpeza dos recursos, já que você precisará excluir apenas um grupo de recursos ao invés de dois.

## <a name="validate-the-deployment"></a>Validar a implantação

Depois de implantar a máquina virtual com êxito, teste as credenciais de entrada usando a senha armazenada no cofre de chaves.

1. Abra o [Portal do Azure](https://portal.azure.com).

1. Selecione **Grupos de recursos** >  **\<*NomedoSeuGrupodeRecursos*>**  > **simpleWinVM** .
1. Selecione **conectar** na parte superior.
1. Selecione **Baixar arquivo RDP** e siga as instruções para entrar na máquina virtual usando a senha que está armazenada no cofre de chaves.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais dos recursos do Azure, limpe-os excluindo o grupo de recursos.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você recuperou um segredo de seu cofre de chaves do Azure. Em seguida, você usou o segredo na implantação do modelo. Para saber como criar modelos vinculados, veja:

> [!div class="nextstepaction"]
> [Criar modelos vinculados](./resource-manager-tutorial-create-linked-templates.md)
