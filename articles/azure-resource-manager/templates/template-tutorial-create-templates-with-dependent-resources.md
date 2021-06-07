---
title: Modelo com recursos dependentes
description: Saiba como criar um modelo do ARM (modelo do Azure Resource Manager) com vários recursos e como implantá-lo usando o portal do Azure
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a77f64e51a26e1f916f9f96704c55412a870a509
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673929"
---
# <a name="tutorial-create-arm-templates-with-dependent-resources"></a>Tutorial: Criar modelos do ARM com recursos dependentes

Saiba como criar um modelo do ARM (modelo do Azure Resource Manager) para implantar vários recursos e configurar a ordem de implantação. Depois de criar o modelo, você poderá implantá-lo usando o Cloud Shell no portal do Azure.

Neste tutorial, você criará uma conta de armazenamento, uma máquina virtual, uma rede virtual e alguns outros recursos dependentes. Alguns recursos não podem ser implantados até que outro recurso exista. Por exemplo, não é possível criar a máquina virtual enquanto a conta de armazenamento e o adaptador de rede não existirem. Defina essa relação marcando um recurso como dependente dos outros. O Gerenciador de Recursos avalia as dependências entre os recursos e os implanta na ordem de dependência. Quando os recursos não dependem uns dos outros, o Gerenciador de Recursos os implanta paralelamente. Para obter mais informações, consulte [Definir a ordem de implantação dos recursos em modelos do ARM](./define-resource-dependency.md).

![diagrama de ordem de implantação dos recursos dependentes do modelo do Resource Manager](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Explorar o modelo
> * Implantar o modelo

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Para obter um módulo do Microsoft Learn que aborda as dependências de recursos, confira [Gerenciar implantações de nuvem complexas usando recursos avançados do modelo do ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).

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
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Escolha **Abrir** para abrir o arquivo.
4. Selecione **Arquivo** > **Salvar como** para salvar uma cópia do arquivo no computador local com o nome _azuredeploy.json_.

## <a name="explore-the-template"></a>Explorar o modelo

Ao explorar o modelo nesta seção, tente responder a essas perguntas:

* Quantos recursos do Azure estão definidos nesse modelo?
* Um dos recursos é uma conta de armazenamento do Azure. A definição se parece com a usada no último tutorial?
* Você consegue encontrar as referências de modelo para os recursos definidos neste modelo?
* Você consegue encontrar as dependências dos recursos?

1. No Visual Studio Code, recolha os elementos até ver apenas os elementos de primeiro nível e os elementos de segundo nível dentro dos `resources`:

    ![Modelos do ARM no Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Há seis recursos definidos pelo modelo:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     É útil examinar a referência do modelo antes de personalizá-lo.

1. Expanda o primeiro recurso. Trata-se de uma conta de armazenamento. Compare a definição de recurso com a [referência de modelo](/azure/templates/Microsoft.Storage/storageAccounts).

    ![Definição da conta de armazenamento dos modelos do ARM no Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

1. Expanda o segundo recurso. O tipo de recurso é `Microsoft.Network/publicIPAddresses`. Compare a definição de recurso com a [referência de modelo](/azure/templates/microsoft.network/publicipaddresses).

    ![Definição do endereço IP público dos modelos do ARM no Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)

1. Expanda o terceiro recurso. O tipo de recurso é `Microsoft.Network/networkSecurityGroups`. Compare a definição de recurso com a [referência de modelo](/azure/templates/microsoft.network/networksecuritygroups).

    ![Definição do grupo de segurança de rede dos modelos do ARM no Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-network-security-group-definition.png)

1. Expanda o quarto recurso. O tipo de recurso é `Microsoft.Network/virtualNetworks`:

    ![Elemento dependsOn da rede virtual dos modelos do ARM no Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-virtual-network-definition.png)

    O elemento `dependsOn` permite definir um recurso como dependente de um ou mais recursos. O recurso depende de um outro recurso:

    * `Microsoft.Network/networkSecurityGroups`

1. Expanda o quinto recurso. O tipo de recurso é `Microsoft.Network/networkInterfaces`. O recurso depende de dois outros recursos:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

1. Expanda o sexto recurso. Esse recurso é uma máquina virtual. Depende de dois outros recursos:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

O diagrama a seguir ilustra os recursos e as informações de dependência para o modelo:

![Diagrama de dependência dos modelos do ARM no Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Ao especificar as dependências, o Gerenciador de Recursos implanta a solução eficientemente. Ele implanta a conta de armazenamento, o endereço IP público e a rede virtual em paralelo porque eles não têm dependências. Depois que o endereço IP público e a rede virtual são implantados, o adaptador de rede é criado. Quando todos os outros recursos são implantados, o Gerenciador de Recursos implanta a máquina virtual.

## <a name="deploy-the-template"></a>Implantar o modelo

1. Entrar no [Azure Cloud Shell](https://shell.azure.com)

1. Escolha seu ambiente preferencial selecionando **PowerShell** ou **Bash** (para a CLI) no canto superior esquerdo.  Ao alternar, é necessário reiniciar o shell.

    ![Carregar arquivo do Cloud Shell no portal do Azure](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Escolha **Carregar/fazer o download dos arquivos** e, em seguida, escolha **Carregar**. Consulte a captura de tela anterior. Selecione o arquivo que você salvou anteriormente. Depois de carregar o arquivo, use os comandos `ls` e `cat` para verificar se o arquivo foi carregado com êxito.

1. Execute o script do PowerShell a seguir para implantar o modelo.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    echo "Enter the virtual machine admin username:" &&
    read adminUsername &&
    echo "Enter the DNS label prefix:" &&
    read dnsLabelPrefix &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location $location &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters adminUsername=$adminUsername dnsLabelPrefix=$dnsLabelPrefix
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    ---

1. RDP para a máquina virtual para verificar se a máquina virtual foi criada com êxito.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos. Você verá um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você desenvolveu e implantou um modelo para criar uma máquina virtual, uma rede virtual e os recursos dependentes. Para saber como usar scripts de implantação para executar operações pré/pós-implantação, confira:

> [!div class="nextstepaction"]
> [Usar script de implantação](./template-tutorial-deployment-script.md)
