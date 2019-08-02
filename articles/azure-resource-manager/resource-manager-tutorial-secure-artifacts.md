---
title: Proteger os artefatos em implantações de modelo do Azure Resource Manager | Microsoft Docs
description: Saiba como proteger os artefatos usados em seus modelos do Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/25/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: bf004f07558ae1f252a6bd26b4fd59ea9e4eea6e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069266"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Tutorial: Proteger os artefatos em implantações de modelo do Azure Resource Manager

Saiba como proteger os artefatos usados em modelos do Azure Resource Manager usando a conta de Armazenamento do Azure com SAS (assinaturas de acesso compartilhado). Artefatos de implantação são quaisquer arquivos, além do arquivo de modelo principal, necessários para concluir uma implantação. Por exemplo, no [Tutorial: Importar arquivos BACPAC do SQL com modelos do Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md), o modelo principal cria um Banco de Dados SQL do Azure; ele também chama um arquivo BACPAC para criar tabelas e inserir dados. O arquivo BACPAC é um artefato. O artefato é armazenado em uma conta de armazenamento do Azure com acesso público. Neste tutorial, você pode usar SAS para conceder acesso limitado ao arquivo BACPAC em sua própria conta de Armazenamento do Azure. Para saber mais sobre SAS, confira [Usar SAS (assinaturas de acesso compartilhado)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Para saber como proteger o modelo vinculado, confira [Tutorial: Criar modelos do Azure Resource Manager vinculados](./resource-manager-tutorial-create-linked-templates.md).

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um arquivo BACPAC
> * Abrir um modelo existente
> * Editar o modelo
> * Implantar o modelo
> * Verificar a implantação

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* [Visual Studio Code](https://code.visualstudio.com/) com a extensão de Ferramentas do Resource Manager. Confira [Instalar a extensão](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Examine [Tutorial: Importar arquivos BACPAC do SQL com modelos do Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md). O modelo usado neste tutorial é o desenvolvido naquele tutorial. Um link de download do modelo concluído é fornecido neste artigo.
* Para aumentar a segurança, use uma senha gerada para a conta de administrador do SQL Server. Veja um exemplo para gerar uma senha:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    O Azure Key Vault é projetado para proteger chaves de criptografia e outros segredos. Para obter mais informações, confira [Tutorial: Integrar o Azure Key Vault na implantação de Modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md). Também recomendamos que você atualize sua senha a cada três meses.

## <a name="prepare-a-bacpac-file"></a>Preparar um arquivo BACPAC

Nesta seção, você deve preparar um arquivo BACPAC para que o arquivo esteja acessível com segurança quando você implantar o modelo do Resource Manager. Há cinco procedimentos nesta seção:

* Baixe o arquivo BACPAC.
* Criar uma conta do Armazenamento do Azure.
* Crie um contêiner de blobs da conta de armazenamento.
* Carregue o arquivo BACPAC para o contêiner.
* Recupere o token SAS do arquivo BACPAC.

Para automatizar essas etapas usando um script do PowerShell, confira o script em [Carregar o modelo vinculado](./resource-manager-tutorial-create-linked-templates.md#upload-the-linked-template).

### <a name="download-the-bacpac-file"></a>Baixe o arquivo BACPAC

Baixe o [arquivo BACPAC](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac) e salve o arquivo em seu computador local com o mesmo nome, **SQLDatabaseExtension.bacpac**.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. Selecione a imagem a seguir para abrir um modelo do Resource Manager no portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json" target="_blank"><img src="./media/resource-manager-tutorial-secure-artifacts/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Insira as seguintes propriedades:

    * **Assinatura**: Selecione sua assinatura do Azure.
    * **Grupo de recursos**: Selecione **Criar novo** e dê um nome. Um grupo de recursos é um contêiner para recursos do Azure para fins de gerenciamento. Neste tutorial, você pode usar o mesmo grupo de recursos para a conta de armazenamento e Banco de Dados SQL do Azure. Anote esse nome do grupo de recursos, você precisará dele quando criar o Banco de Dados SQL do Azure posteriormente nos tutoriais.
    * **Localização**: Selecione uma região. Por exemplo, **Centro dos EUA**.
    * **Tipo de Conta de Armazenamento**: use o valor padrão, que é **Standard_LRS**.
    * **Localização**: Use o valor padrão, que é **[resourceGroup().location]** . Isso significa que você usa a localização do grupo de recursos para a conta de armazenamento.
    * **Eu concordo com os termos e condições declarados acima**: (selecionados)
3. Selecione **Comprar**.
4. Selecione o ícone de notificação (o ícone de sino) no canto superior direito do portal para ver o status da implantação.

    ![Painel de notificações do portal do tutorial do Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-portal-notifications-pane.png)
5. Depois que a conta de armazenamento for implantada com êxito, selecione **Ir para o grupo de recursos** no painel de notificação para abrir o grupo de recursos.

### <a name="create-a-blob-container"></a>Criar um contêiner de Blob

Um contêiner de Blob é necessário antes de carregar todos os arquivos.

1. Selecione a conta de armazenamento para abrir. Você deverá ver apenas uma conta de armazenamento listada no grupo de recursos. O nome da sua conta de armazenamento é diferente daquele mostrado na seguinte captura de tela.

    ![Conta de armazenamento do tutorial do Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-storage-account.png)

2. Selecione o bloco **Blobs**.

    ![Blobs do tutorial do Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-blobs.png)
3. Selecione **+ Contêiner** na parte superior para criar um novo contêiner.
4. Insira os valores a seguir:

    * **Nome**: insira **sqlbacpac**.
    * **Nível de acesso público**: use o valor padrão, **Privado (sem acesso anônimo)** .
5. Selecione **OK**.
6. Selecione **sqlbacpac** para abrir o contêiner recém-criado.

### <a name="upload-the-bacpac-file-to-the-container"></a>Carregar o arquivo BACPAC para o contêiner

1. Escolha **Carregar**.
2. Insira os valores a seguir:

    * **Arquivos**: Seguindo as instruções para selecionar o arquivo BACPAC baixado anteriormente. O nome padrão é **SQLDatabaseExtension.bacpac**.
    * **Tipo de autenticação**: Selecione **SAS**.  *SAS* é o valor padrão.
3. Escolha **Carregar**.  Depois que o arquivo tiver sido carregado com êxito, o nome do arquivo deverá ser listado no contêiner.

### <a name="a-namegenerate-a-sas-token-generate-a-sas-token"></a><a name="generate-a-sas-token" />Gerar um token SAS

1. Clique com o botão direito do mouse em **SQLDatabaseExtension.bacpac** do contêiner e, em seguida, selecione **Gerar SAS**.
2. Insira os valores a seguir:

    * **Permissão**: Use o padrão, **Leitura**.
    * **Data/hora de início e término**: O valor padrão fornece oito horas para usar o token SAS. Se você precisar de mais tempo para concluir este tutorial, atualize **Término**.
    * **Endereços IP permitidos**: Deixe esse campo em branco.
    * **Protocolos permitidos**: use o valor padrão: **HTTPS**.
    * **Chave de assinatura**: use o valor padrão: **Chave 1**.
3. Selecione **Gerar URL e token SAS do blob**.
4. Faça uma cópia da **URL de SAS do Blob**. No meio da URL está o nome do arquivo **SQLDatabaseExtension.bacpac**.  O nome do arquivo divide a URL em três partes:

   - **Localização do artefato**: https://xxxxxxxxxxxxxx.blob.core.windows.net/sqlbacpac/. Verifique se a localização termina com "/".
   - **Nome do arquivo BACPAC**: SQLDatabaseExtension.bacpac.
   - **Token de SAS de localização do artefato**: verifique se o token é precedido por um "?".

     Você precisa desses três valores em [Implantar o modelo](#deploy-the-template).

## <a name="open-an-existing-template"></a>Abrir um modelo existente

Nesta sessão, você modifica o modelo criado no [Tutorial: Importar arquivos BACPAC do SQL com modelos do Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) para chamar o arquivo BACPAC com um token SAS.  O modelo desenvolvido no tutorial de extensão do SQL é compartilhado em [https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json).

1. No Visual Studio Code, escolha **Arquivo**>**Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json
    ```
3. Escolha **Abrir** para abrir o arquivo.

    Há cinco recursos definidos no modelo:

   * `Microsoft.Sql/servers`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/securityalertpolicies).
   * `Microsoft.SQL/servers/filewallRules`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`.  Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`.  Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

     É útil ter algumas noções básicas do modelo antes de personalizá-lo.
4. Selecione **Arquivo**>**Salvar como** para salvar uma cópia do arquivo no computador local com o nome **azuredeploy.json**.

## <a name="edit-the-template"></a>Editar o modelo

Adicione os seguintes parâmetros extras:

```json
"_artifactsLocation": {
    "type": "string",
    "metadata": {
        "description": "The base URI where artifacts required by this template are located."
    }
},
"_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
        "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
},
"bacpacFileName": {
    "type": "string",
    "defaultValue": "SQLDatabaseExtension.bacpac",
    "metadata": {
        "description": "The bacpac for configure the database and tables."
    }
}
```

![Parâmetros de artefatos de seguro do tutorial do Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

Atualize o valor dos dois elementos a seguir:

```json
"storageKey": "[parameters('_artifactsLocationSasToken')]",
"storageUri": "[uri(parameters('_artifactsLocation'), parameters('bacpacFileName'))]",
```

## <a name="deploy-the-template"></a>Implantar o modelo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Consulte a seção [Implantar o modelo](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) para obter o procedimento de implantação. Use o seguinte script de implantação do PowerShell:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$artifactsLocation = Read-Host -Prompt "Enter the artifacts location"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString
$bacpacFileName = Read-Host -Prompt "Enter the BACPAC file name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocation $artifactsLocation `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacFileName $bacpacFileName `
    -TemplateFile "$HOME/azuredeploy.json"
```

Use a senha gerada. Consulte [Pré-requisitos](#prerequisites).
Para os valores de _artifactsLocation, _artifactsLocationSasToken e bacpacFileName, confira [Gerar um token SAS](#generate-a-sas-token).

## <a name="verify-the-deployment"></a>Verificar a implantação

No portal, selecione o banco de dados SQL no grupo de recursos implantado recentemente. Selecione **Editor de consultas (versão prévia)** e insira as credenciais de administrador. Você deverá ver duas tabelas importadas para o banco de dados:

![O Azure Resource Manager implanta extensões de SQL BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Escolha o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou um SQL Server e um Banco de Dados SQL e importou um arquivo BACPAC usando o token SAS. Para saber como criar um Pipeline do Azure para desenvolver e implantar modelos do Gerenciador de Recursos

> [!div class="nextstepaction"]
> [Integração contínua com o Azure Pipeline](./resource-manager-tutorial-use-azure-pipelines.md)
