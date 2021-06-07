---
title: Usar referência de modelo
description: Use a referência de modelo do ARM (modelo do Azure Resource Manager) para criar um modelo.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: a44852fb2f491dd949b58217eca3e4f3e392cf17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97584129"
---
# <a name="tutorial-utilize-the-arm-template-reference"></a>Tutorial: Utilizar a referência de modelo do ARM

Saiba como encontrar as informações de esquema de modelo e usá-las para criar modelos do ARM (modelos do Azure Resource Manager).

Neste tutorial, você usará um modelo base dos Modelos de Início Rápido do Azure. Usando a documentação de referência de modelo, você personaliza o modelo.

![Conta de armazenamento de implantação da referência de modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Entender o modelo
> * Encontrar a referência de modelo
> * Editar o modelo
> * Implantar o modelo

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* Visual Studio Code com a extensão de Ferramentas do Resource Manager. Confira [Início Rápido: Criar modelos do ARM com o Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

[Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/) é um repositório de modelos do ARM. Em vez de criar um modelo do zero, você pode encontrar um exemplo de modelo e personalizá-lo. O modelo usado neste início rápido é chamado [Criar uma conta de armazenamento padrão](https://azure.microsoft.com/resources/templates/101-storage-account-create/). O modelo define um recurso da conta de Armazenamento do Azure.

1. No Visual Studio Code, escolha **Arquivo** > **Abrir Arquivo**.
1. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Escolha **Abrir** para abrir o arquivo.
1. Escolha **Arquivo** > **Salvar como** para salvar o arquivo como _azuredeploy.json_ em seu computador local.

## <a name="understand-the-schema"></a>Entenda o esquema

1. No Visual Studio Code, recolha o modelo para o nível raiz. Você tem uma estrutura mais simples com os seguintes elementos:

    ![Estrutura mais simples do modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * `$schema`: especifique o local do arquivo de esquema JSON que descreve a versão da linguagem do modelo.
    * `contentVersion`: especifique algum valor para esse elemento a fim de documentar alterações significativas no modelo.
    * `parameters`: especifique os valores que são fornecidos quando a implantação é executada para personalizar a implantação dos recursos.
    * `variables`: especifique os valores que são usados como fragmentos JSON no modelo para simplificar expressões de linguagem do modelo.
    * `resources`: especifique os tipos de recursos que são implantados ou atualizados em um grupo de recursos.
    * `outputs`: especifique os valores que serão retornados após a implantação.

1. Expanda `resources`. Há um recurso `Microsoft.Storage/storageAccounts` definido. O nome do SKU usa um valor de parâmetro. O parâmetro é chamado `storageAccountType`.

    ![Definição da conta de armazenamento do modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

1. Expanda `parameters` para ver como `storageAccountType` está definido. O parâmetro tem quatro valores permitidos. Você encontrará os outros valores permitidos e, em seguida, revisará a definição do parâmetro.

    ![SKUs de recursos da conta de armazenamento de modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus-old.png)

## <a name="find-the-template-reference"></a>Encontrar a referência de modelo

1. Navegue até [Referência de modelo do Azure](/azure/templates/).
1. Na caixa **Filtrar por título**, insira **contas de armazenamento** e selecione a primeira **Conta de Armazenamento** em **Referência > Armazenamento**.

    ![Conta de armazenamento de referência de modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    Um provedor de recursos geralmente tem várias versões de API:

    ![Versões da conta de armazenamento de referência de modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. Selecione **Todos os recursos** em **Armazenamento** no painel esquerdo. Esta página lista os tipos de recursos e as versões do provedor de recursos de armazenamento. Recomendamos usar as últimas versões de API para os tipos de recursos definidos no modelo.

    ![Versões de tipos de conta de armazenamento de referência do modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Selecione a versão mais recente do tipo de recurso `storageAccount`. A versão mais recente era **2019-06-01** quando este artigo foi escrito. Esta versão deve corresponder à versão usada para o recurso da conta de armazenamento em seu modelo. Se você atualizar a versão da API, verifique se a definição de recurso corresponde à referência de modelo.

1. Esta página lista os detalhes do tipo de recurso storageAccount. Por exemplo, ela lista os valores permitidos para o **objeto Sku**. Há mais SKUs do que as listadas no modelo de início rápido aberto anteriormente. Você pode personalizar o modelo de início rápido para incluir todos os tipos de armazenamento disponíveis.

    ![SKUs da conta de armazenamento de referência de modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>Editar o modelo

No Visual Studio Code, adicione os tipos de conta de armazenamento extras, conforme mostra a seguinte captura de tela:

![Recursos da conta de armazenamento do modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>Implantar o modelo

1. Entrar no [Azure Cloud Shell](https://shell.azure.com)

1. Escolha seu ambiente preferencial selecionando **PowerShell** ou **Bash** (para a CLI) no canto superior esquerdo.  Ao alternar, é necessário reiniciar o shell.

    ![Carregar arquivo do Cloud Shell no portal do Azure](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Escolha **Carregar/fazer o download dos arquivos** e, em seguida, escolha **Carregar**. Consulte a captura de tela anterior. Selecione o arquivo que você salvou na seção anterior. Depois de carregar o arquivo, use os comandos `ls` e `cat` para verificar se o arquivo foi carregado com êxito.

1. No Cloud Shell, execute os comandos a seguir. Selecione a guia para mostrar o código do PowerShell ou o código da CLI.

   Ao implantar o modelo, especifique o parâmetro `storageAccountType` com um valor recém-adicionado, por exemplo, **Standard_RAGRS**. A implantação falhará se você usar o modelo de início rápido original, porque **Standard_RAGRS** não era um valor permitido.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters storageAccountType='Standard_RAGRS'
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -storageAccountType "Standard_RAGRS"
    ```

    ---

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
1. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
1. Selecione o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
1. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar a referência de modelo para personalizar um modelo existente. Para saber como criar várias instâncias da conta de armazenamento, veja:

> [!div class="nextstepaction"]
> [Criar múltiplas instâncias](./template-tutorial-create-multiple-instances.md)
