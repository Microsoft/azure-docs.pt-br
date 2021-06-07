---
title: Usar condição em modelos
description: Saiba como implantar recursos do Azure com base em condições. Mostra como implantar um novo recurso ou usar um recurso existente.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4affc2add2822702c1d5395f81efe01eeedf448b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98696017"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>Tutorial: Usar condição em modelos do ARM

Saiba como implantar recursos do Azure com base em condições em um modelo do ARM (modelo do Azure Resource Manager).

No tutorial [Definir ordem de implantação de recursos](./template-tutorial-create-templates-with-dependent-resources.md), você criará uma máquina virtual, uma rede virtual e alguns outros recursos dependentes, incluindo uma conta de armazenamento. Em vez de criar uma nova conta de armazenamento todas as vezes, você permitirá que as pessoas escolham entre criar uma nova conta de armazenamento e usar uma conta de armazenamento existente. Para isso, você definirá um parâmetro adicional. Se o valor do parâmetro for **new**, uma conta de armazenamento será criada. Caso contrário, uma conta de armazenamento existente com o nome fornecido é usada.

![Diagrama de condição de uso do modelo do Resource Manager](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Modificar o modelo
> * Implantar o modelo
> * Limpar os recursos

Este tutorial aborda apenas um cenário básico de como usar as condições. Para obter mais informações, consulte:

* [Estrutura de arquivos de modelo: Condição](conditional-resource-deployment.md).
* [Implantar condicionalmente um recurso em um modelo do ARM](/azure/architecture/guide/azure-resource-manager/advanced-templates/conditional-deploy).
* [Função de modelo: If](./template-functions-logical.md#if).
* [Funções de comparação para modelos do ARM](./template-functions-comparison.md)

Para obter um módulo do Microsoft Learn que aborda as condições, confira [Gerenciar implantações de nuvem complexas usando recursos avançados do modelo do ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* Visual Studio Code com a extensão de Ferramentas do Resource Manager. Confira [Início Rápido: Criar modelos do ARM com o Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).
* Para aumentar a segurança, use uma senha gerada para a conta de administrador da máquina virtual. Veja um exemplo para gerar uma senha:

    ```console
    openssl rand -base64 32
    ```

    O Azure Key Vault é projetado para proteger chaves de criptografia e outros segredos. Para saber mais, confira [Tutorial: Integrar o Azure Key Vault na implantação de modelo do ARM](./template-tutorial-use-key-vault.md). Também recomendamos que você atualize sua senha a cada três meses.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Modelos de Início Rápido do Azure é um repositório de modelos do ARM. Em vez de criar um modelo do zero, você pode encontrar um exemplo de modelo e personalizá-lo. O modelo usado neste tutorial é chamado [Implantar uma VM Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Visual Studio Code, escolha **Arquivo** > **Abrir Arquivo**.
1. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Escolha **Abrir** para abrir o arquivo.
1. Há seis recursos definidos pelo modelo:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

    É útil examinar a referência do modelo antes de personalizar um modelo.

1. Selecione **Arquivo** > **Salvar como** para salvar uma cópia do arquivo no computador local com o nome _azuredeploy.json_.

## <a name="modify-the-template"></a>Modificar o modelo

Faça duas alterações no modelo existente:

* Adicione um parâmetro de nome da conta de armazenamento. Os usuários podem especificar um nome de conta de armazenamento novo ou existente.
* Adicione um novo parâmetro chamado `newOrExisting`. A implantação usa esse parâmetro para determinar se uma nova conta de armazenamento será criada ou uma conta de armazenamento existente será usada.

Aqui está o procedimento para fazer as alterações:

1. Abra _azuredeploy.json_ no Visual Studio Code.
1. Substitua as três `variables('storageAccountName')` por `parameters('storageAccountName')` em todo o modelo.
1. Remova as declarações de variável a seguir:

    ![Captura de tela que realça as definições de variáveis que você precisa remover.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

1. Adicione os dois parâmetros seguintes ao início da seção de parâmetros:

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    Pressione Alt+Shift+F para formatar o modelo no Visual Studio Code.

    A definição dos parâmetros atualizados ficará assim:

    ![Condição de uso do Resource Manager](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

1. Adicione a seguinte linha no início da definição da conta de armazenamento.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    A condição verifica o valor do parâmetro `newOrExisting`. Se o valor do parâmetro for **new**, a implantação criará a conta de armazenamento.

    A definição da conta de armazenamento atualizada será assim:

    ![Captura de tela que mostra a definição da conta de armazenamento atualizada.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
1. Atualize a propriedade `storageUri` da definição de recurso de máquina virtual pelo seguinte valor:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Essa alteração é necessária quando você usa uma conta de armazenamento existente em um grupo de recursos diferentes.

1. Salve as alterações.

## <a name="deploy-the-template"></a>Implantar o modelo

1. Entrar no [Azure Cloud Shell](https://shell.azure.com)

1. Escolha seu ambiente preferencial selecionando **PowerShell** ou **Bash** (para a CLI) no canto superior esquerdo. Ao alternar, é necessário reiniciar o shell.

    ![Carregar arquivo do Cloud Shell no portal do Azure](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Escolha **Carregar/fazer o download dos arquivos** e, em seguida, escolha **Carregar**. Consulte a captura de tela anterior. Selecione o arquivo que você salvou na seção anterior. Depois de carregar o arquivo, use os comandos `ls` e `cat` para verificar se o arquivo foi carregado com êxito.

1. Execute o script do PowerShell a seguir para implantar o modelo.

    > [!IMPORTANT]
    > O nome da conta de armazenamento deve ser exclusivo no Azure. O nome deve ter apenas letras minúsculas ou números. Ele não pode ter mais de 24 caracteres. O nome de conta de armazenamento é o nome do projeto acrescentado de **store**. Verifique se o nome do projeto e o nome da conta de armazenamento gerada atendem aos requisitos de nome da conta de armazenamento.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name and resource names"
    $newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
    $location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
    $vmAdmin = Read-Host -Prompt "Enter the admin username"
    $vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $vmAdmin `
        -adminPassword $vmPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -storageAccountName $storageAccountName `
        -newOrExisting $newOrExisting `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    > [!NOTE]
    > A implantação falhará se `newOrExisting` for **new**, mas a conta de armazenamento com o nome da conta de armazenamento especificado já existir.

Tente criar outra implantação com `newOrExisting` definido como **existing** e especifique uma conta de armazenamento existente. Para criar uma conta de armazenamento com antecedência, confira [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos. Para excluir o grupo de recursos, selecione **Testar** para abrir o Cloud Shell. Para colar o script do PowerShell, clique com o botão direito do mouse no painel do shell e, em seguida, selecione **Colar**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você desenvolveu um modelo que permite aos usuários escolher entre criar uma nova conta de armazenamento e usar uma conta de armazenamento existente. Para saber como recuperar segredos do Azure Key Vault e usá-los como senhas na implantação de modelo, confira:

> [!div class="nextstepaction"]
> [Integrar o Key Vault à implantação de modelo](./template-tutorial-use-key-vault.md)
