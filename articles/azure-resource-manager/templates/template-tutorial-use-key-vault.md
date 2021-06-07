---
title: Usar o Azure Key Vault em modelos
description: Saiba como usar o Azure Key Vault para transmitir valores de parâmetro seguros durante a implantação de um modelo do ARM (modelo do Azure Resource Manager).
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 388996dc0054192f6d9f3c87e11ca1d15e8a85e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703878"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-arm-template-deployment"></a>Tutorial: Integrar o Azure Key Vault em sua implantação de modelo do ARM

Saiba como recuperar segredos de um cofre de chaves do Azure e transmitir os segredos como parâmetros durante a implantação de um modelo do ARM (modelo do Azure Resource Manager). O valor do parâmetro nunca fica exposto porque você só faz referência à ID do cofre de chaves. Você pode fazer referência ao segredo do cofre de chaves usando uma ID estática ou uma ID dinâmica. Este tutorial usa uma ID estática. Com a abordagem de ID estática, você faz referência ao cofre de chaves no arquivo de parâmetro de modelo, não no arquivo de modelo. Para saber mais sobre ambas as abordagens, confira [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](./key-vault-parameter.md).

No tutorial [Definir a ordem de implantação do recurso](./template-tutorial-create-templates-with-dependent-resources.md), você cria uma VM (máquina virtual). Você precisará fornecer o nome de usuário e a senha do administrador da VM. Em vez de fornecer a senha, armazene previamente a senha em um cofre de chaves do Azure e personalize o modelo para recuperar a senha do cofre de chaves durante a implantação.

![Diagrama mostrando a integração de um modelo do Resource Manager com um cofre de chaves](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um cofre de chaves
> * Abrir um modelo de início rápido
> * Edite o arquivo de parâmetros
> * Implantar o modelo
> * Validar a implantação
> * Limpar os recursos

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Para obter um módulo do Microsoft Learn que usa um valor seguro de um cofre de chaves, confira [Gerenciar implantações de nuvem complexas usando recursos avançados do modelo do ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* Visual Studio Code com a extensão de Ferramentas do Resource Manager. Confira [Início Rápido: Criar modelos do ARM com o Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).
* Para aumentar a segurança, use uma senha gerada para a conta Administrador da VM. Veja um exemplo de geração de senha:

    ```console
    openssl rand -base64 32
    ```

    Verifique se a senha gerada atende aos requisitos de senha da VM. Cada serviço do Azure tem requisitos de senha específicos. Para os requisitos de senha da VM, confira [Quais são os requisitos de senha ao criar uma VM?](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Preparar um cofre de chaves

Nesta seção, você criará um cofre de chaves e adicionará um segredo a ele para poder recuperá-lo durante a implantação do seu modelo. Há muitas maneiras de criar um cofre de chaves. Neste tutorial, você usará o Azure PowerShell para implantar um [modelo do ARM](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Esse modelo faz duas coisas:

* Crie um cofre de chaves com a propriedade `enabledForTemplateDeployment` habilitada. Essa propriedade precisa ser *true* para que o processo de implantação de modelo possa acessar os segredos definidos no cofre de chaves.
* Adicione um segredo ao cofre de chaves. O segredo armazena a senha do administrador da VM.

> [!NOTE]
> Como é o usuário que está implantando o modelo de máquina virtual, se você não for o Proprietário ou um Colaborador do cofre de chaves, um destes precisará conceder acesso à permissão `Microsoft.KeyVault/vaults/deploy/action` do cofre de chaves. Para saber mais, confira [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](./key-vault-parameter.md).

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

Write-Host "Press [ENTER] to continue ..."
```

> [!IMPORTANT]
> * O nome do grupo de recursos é o nome do projeto mais as letras **rg**. Para facilitar a [limpeza dos recursos que você criou neste tutorial](#clean-up-resources), use o mesmo grupo de recursos e o mesmo nome de projeto durante a [implantação do próximo modelo](#deploy-the-template).
> * O nome padrão do segredo é **vmAdminPassword**. Ele é codificado no modelo.
> * Para habilitar a recuperação do segredo pelo modelo, ative uma política de acesso chamada **Habilitar acesso ao Azure Resource Manager para implantação de modelo** no cofre de chaves. Essa política está habilitada no modelo. Para saber mais sobre a política de acesso, confira [Implantar cofres de chaves e segredos](./key-vault-parameter.md#deploy-key-vaults-and-secrets).

O modelo tem um valor de saída denominado `keyVaultId`. Você usará essa ID junto com o nome do segredo para recuperar o valor do segredo posteriormente no tutorial. O formato da ID do recurso é:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Ao copiar e colar a ID, ela pode ficar dividida em várias linhas. Você deve mesclar as linhas e remover os espaços adicionais.

Para validar a implantação, execute o seguinte comando do PowerShell no mesmo painel de shell para recuperar o segredo em texto não criptografado. O comando funciona somente na mesma sessão de shell, pois usa a variável `$keyVaultName`, que está definida no script do PowerShell anterior.

```azurepowershell
$secret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword"
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

Agora você preparou um cofre de chaves e um segredo. As seções a seguir mostrarão como personalizar um modelo existente para recuperar o segredo durante a implantação.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de início rápido

Modelos de Início Rápido do Azure é um repositório de modelos do ARM. Em vez de criar um modelo do zero, você pode encontrar um exemplo de modelo e personalizá-lo. O modelo usado neste tutorial é chamado [Implantar uma VM do Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Visual Studio Code, escolha **Arquivo** > **Abrir Arquivo**.

1. Na caixa **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Escolha **Abrir** para abrir o arquivo. O cenário é o mesmo usado no [Tutorial: Criar modelos do ARM com recursos dependentes](./template-tutorial-create-templates-with-dependent-resources.md).
   O modelo define seis recursos:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

   É útil ter algumas noções básicas do modelo antes de personalizá-lo.

1. Selecione **Arquivo** > **Salvar como** e salve uma cópia do arquivo no computador local com o nome *azuredeploy.json*.

1. Repita as etapas de 1 a 3 para abrir a URL a seguir e salve o arquivo como *azuredeploy.parameters.json*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Edite o arquivo de parâmetros

Usando o método de ID estática, você não precisa fazer nenhuma alteração ao arquivo de modelo. A recuperação do valor do segredo é feita configurando o arquivo de parâmetro de modelo.

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
    > Substitua o valor de `id` pela ID do recurso do cofre de chaves que você criou no procedimento anterior. O `secretName` é embutido em código como **vmAdminPassword**.  Confira [Preparar um cofre de chaves](#prepare-a-key-vault).

    ![Integrar o cofre de chaves e o arquivo de parâmetros da implantação de máquina virtual do modelo do Resource Manager](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Atualize os seguintes valores:

    * `adminUsername`: o nome da conta de administrador da máquina virtual.
    * `dnsLabelPrefix`: Nomeie o valor de `dnsLabelPrefix`.

    Para ver exemplos de nomes, confira a imagem anterior.

1. Salve as alterações.

## <a name="deploy-the-template"></a>Implantar o modelo

1. Entrar no [Azure Cloud Shell](https://shell.azure.com)

1. Escolha seu ambiente preferencial selecionando **PowerShell** ou **Bash** (para a CLI) no canto superior esquerdo.  Ao alternar, é necessário reiniciar o shell.

    ![Carregar arquivo do Cloud Shell no portal do Azure](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Escolha **Carregar/fazer o download dos arquivos** e, em seguida, escolha **Carregar**. Carregue *azuredeploy.json* e *azuredeploy.parameters.json* no Cloud Shell. Após carregar o arquivo, você pode usar o comando `ls` e o comando `cat` para verificar se o arquivo foi carregado com êxito.

1. Execute o script do PowerShell a seguir para implantar o modelo.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
    $location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$HOME/azuredeploy.json" `
        -TemplateParameterFile "$HOME/azuredeploy.parameters.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    Ao implantar o modelo, use o mesmo grupo de recursos usado no cofre de chaves. Essa abordagem facilita a limpeza dos recursos, já que você precisará excluir apenas um grupo de recursos ao invés de dois.

## <a name="validate-the-deployment"></a>Validar a implantação

Depois de implantar a máquina virtual com êxito, teste as credenciais de entrada usando a senha armazenada no cofre de chaves.

1. Abra o [Portal do Azure](https://portal.azure.com).

1. Selecione **Grupos de recursos** >  **\<*YourResourceGroupName*>**  > **simpleWinVM**.
1. Selecione **Conectar** na parte superior.
1. Selecione **Baixar arquivo RDP** e siga as instruções para entrar na máquina virtual usando a senha que está armazenada no cofre de chaves.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você não precisar mais dos recursos do Azure, limpe-os excluindo o grupo de recursos.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você recuperou um segredo de seu cofre de chaves do Azure. Em seguida, você usou o segredo na implantação do modelo. Para saber como usar extensões de máquina virtual para executar tarefas de pós-implantação, confira:

> [!div class="nextstepaction"]
> [Implantar extensões de máquina virtual](./template-tutorial-deploy-vm-extensions.md)
