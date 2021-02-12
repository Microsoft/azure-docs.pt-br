---
title: Tutorial – Implantar um modelo vinculado
description: Saiba como implantar um modelo vinculado
ms.date: 01/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 4ec49fad35e958f010461abf2ee0e3dab8077d55
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134187"
---
# <a name="tutorial-deploy-a-linked-template"></a>Tutorial: Implantar um modelo vinculado

Nos [tutoriais anteriores](./deployment-tutorial-local-template.md), você aprendeu a implantar um modelo que é armazenado em seu computador local. Para implantar soluções complexas, você pode dividir um modelo em vários modelos e implantar esses modelos por meio de um modelo principal. Neste tutorial, você aprenderá a implantar um modelo principal que contém a referência a um modelo vinculado. Quando o modelo principal é implantado, ele dispara a implantação do modelo vinculado. Você também aprende como armazenar e proteger o modelo vinculado usando um token SAS. Esse procedimento demora cerca de **12 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o tutorial anterior, mas isso não é obrigatório.

## <a name="review-template"></a>Examinar modelo

Nos tutoriais anteriores, você implanta um modelo que cria uma conta de armazenamento, um plano do Serviço de Aplicativo e um aplicativo Web. O modelo usado foi:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Criar um modelo vinculado

Você pode separar o recurso de conta de armazenamento em um modelo vinculado:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

O modelo a seguir é o modelo principal. O objeto `Microsoft.Resources/deployments` realçado mostra como chamar um modelo vinculado. O modelo vinculado não pode ser armazenado como um arquivo local nem como um arquivo que esteja disponível apenas em sua rede local. Você só pode fornecer um valor de URI que inclua HTTP ou HTTPS. O Resource Manager precisa ser capaz de acessar o modelo. Uma opção é colocar o modelo vinculado em uma conta de armazenamento e usar o URI do item. O URI é passado para o modelo usando um parâmetro. Consulte a definição do parâmetro realçado.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

Salve uma cópia do modelo principal no computador local com a extensão _.json_, por exemplo, _azuredeploy.json_. Você não precisa salvar uma cópia do modelo vinculado. O modelo vinculado será copiado de um repositório GitHub para uma conta de armazenamento.

## <a name="store-the-linked-template"></a>Armazenar o modelo vinculado

O script do PowerShell a seguir cria uma conta de armazenamento, cria um contêiner e copia o modelo vinculado de um repositório GitHub para o contêiner. Uma cópia do modelo vinculado é armazenada no [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json).

Selecione **Experimentar** para abrir o Cloud Shell, selecione **Copiar** para copiar o script do PowerShell e clique com o botão direito do mouse no painel do shell para colar o script:

> [!IMPORTANT]
> Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres, usar números e apenas letras minúsculas. O nome deve ser exclusivo. No modelo, o nome da conta de armazenamento é o nome do projeto com **store** acrescentado e o nome do projeto deve ter entre 3 e 11 caracteres. Portanto, o nome do projeto deve atender aos requisitos de nome da conta de armazenamento e ter menos de 11 caracteres.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Implantar modelo

Para implantar um modelo privado em uma conta de armazenamento, gere um token SAS e inclua-o no URI para o modelo. Defina a hora de vencimento de forma a permitir que haja tempo suficiente para concluir a implantação. O blob que contém o modelo fica acessível somente para o proprietário da conta. No entanto, quando você cria um token SAS para o blob, o blob fica acessível para qualquer pessoa com o URI. Se outro usuário interceptar o URI, esse usuário será capaz de acessar o modelo. Um token SAS é uma boa maneira de limitar o acesso aos seus modelos, mas você não deve incluir dados confidenciais como senhas diretamente no modelo.

Caso você não tenha criado o grupo de recursos, confira [Criar grupo de recursos](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> No código da CLI do Azure abaixo, o parâmetro de `date` `-d` é um argumento inválido no macOS. Portanto, para adicionar 2 horas à hora atual no terminal no macOS, os usuários do macOS devem usar `-v+2H`.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell

$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$templateFile = Read-Host -Prompt "Enter the main template file and path"

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

# Generate a SAS token
$linkedTemplateUri = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0) `
    -FullUri

# Deploy the template
New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -linkedTemplateUri $linkedTemplateUri `
  -verbose
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli

echo "Enter a project name that is used to generate resource names:"
read projectName
echo "Enter the main template file:"
read templateFile

resourceGroupName="${projectName}rg"
storageAccountName="${projectName}store"
containerName="templates"
fileName="linkedStorageAccount.json"

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv)

linkedTemplateUri=$(az storage blob generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --container-name $containerName \
  --name $fileName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'` \
  --full-uri)

linkedTemplateUri=$(echo $linkedTemplateUri | sed 's/"//g')
az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName linkedTemplateUri=$linkedTemplateUri \
  --verbose
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe os recursos que você implantou excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu a implantar um modelo vinculado. No próximo tutorial, você aprenderá a criar um pipeline de DevOps para implantar um modelo.

> [!div class="nextstepaction"]
> [Criar uma pipeline](./deployment-tutorial-pipeline.md)
