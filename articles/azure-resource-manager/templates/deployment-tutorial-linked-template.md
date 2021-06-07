---
title: Tutorial – Implantar um modelo vinculado
description: Saiba como implantar um modelo vinculado
ms.date: 02/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 8f2bbd327adca6eef62d5e79f422f61d460ea7a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589271"
---
# <a name="tutorial-deploy-a-linked-template"></a>Tutorial: Implantar um modelo vinculado

Nos [tutoriais anteriores](./deployment-tutorial-local-template.md), você aprendeu a implantar um modelo que é armazenado em seu computador local. Para implantar soluções complexas, você pode dividir um modelo em vários modelos e implantar esses modelos por meio de um modelo principal. Neste tutorial, você aprenderá a implantar um modelo principal que contém a referência a um modelo vinculado. Quando o modelo principal é implantado, ele dispara a implantação do modelo vinculado. Saiba também como armazenar e proteger os modelos usando um token SAS. Esse procedimento demora cerca de **12 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o tutorial anterior, mas isso não é obrigatório.

## <a name="review-template"></a>Examinar modelo

Nos tutoriais anteriores, você implanta um modelo que cria uma conta de armazenamento, um plano do Serviço de Aplicativo e um aplicativo Web. O modelo usado foi:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Criar um modelo vinculado

Você pode separar o recurso de conta de armazenamento em um modelo vinculado:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

O modelo a seguir é o modelo principal. O objeto `Microsoft.Resources/deployments` realçado mostra como chamar um modelo vinculado. O modelo vinculado não pode ser armazenado como um arquivo local nem como um arquivo que esteja disponível apenas em sua rede local. Você pode fornecer um valor de URI do modelo vinculado que inclui HTTP ou HTTPS ou usar a propriedade _relativePath_ para implantar um modelo vinculado remoto em uma localização relativa ao modelo pai. Uma opção é colocar o modelo principal e o modelo vinculado em uma conta de armazenamento.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="34-52":::

## <a name="store-the-linked-template"></a>Armazenar o modelo vinculado

O modelo principal e o modelo vinculado são armazenados no GitHub:

O script do PowerShell a seguir cria uma conta de armazenamento e um contêiner, além de copiar os dois modelos de um repositório GitHub para o contêiner. Esses dois modelos são:

- O modelo principal: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
- O modelo vinculado: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Selecione **Experimentar** para abrir o Cloud Shell, selecione **Copiar** para copiar o script do PowerShell e clique com o botão direito do mouse no painel do shell para colar o script:

> [!IMPORTANT]
> Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres, usar números e apenas letras minúsculas. O nome deve ser exclusivo. No modelo, o nome da conta de armazenamento é o nome do projeto com **store** acrescentado e o nome do projeto deve ter entre 3 e 11 caracteres. Portanto, o nome do projeto deve atender aos requisitos de nome da conta de armazenamento e ter menos de 11 caracteres.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$mainTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json"
$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json"

$mainFileName = "azuredeploy.json" # A file name used for downloading and uploading the main template.Add-PSSnapin
$linkedFileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the templates
Invoke-WebRequest -Uri $mainTemplateURL -OutFile "$home/$mainFileName"
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$linkedFileName"

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

# Upload the templates
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$mainFileName" `
    -Blob $mainFileName `
    -Context $context

Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$linkedFileName" `
    -Blob $linkedFileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Implantar modelo

Para implantar modelos em uma conta de armazenamento, gere um token SAS e forneça-o ao parâmetro _-QueryString_. Defina a hora de vencimento de forma a permitir que haja tempo suficiente para concluir a implantação. Os blobs que contêm o modelo ficam acessíveis somente para o proprietário da conta. No entanto, quando você cria um token SAS para um blob, o blob fica acessível para qualquer pessoa com o token SAS. Se outro usuário intercepta o URI e o token SAS, esse usuário pode acessar o modelo. Um token SAS é uma boa maneira de limitar o acesso aos seus modelos, mas você não deve incluir dados confidenciais como senhas diretamente no modelo.

Caso você não tenha criado o grupo de recursos, confira [Criar grupo de recursos](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> No código da CLI do Azure abaixo, o parâmetro de `date` `-d` é um argumento inválido no macOS. Portanto, para adicionar 2 horas à hora atual no terminal no macOS, os usuários do macOS devem usar `-v+2H`.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive

$projectName = Read-Host -Prompt "Enter the same project name:"   # This name is used to generate names for Azure resources, such as storage account name.

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

$mainTemplateUri = $context.BlobEndPoint + "$containerName/azuredeploy.json"
$sasToken = New-AzStorageContainerSASToken `
    -Context $context `
    -Container $containerName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0)
$newSas = $sasToken.substring(1)


New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri $mainTemplateUri `
  -QueryString $newSas `
  -projectName $projectName `
  -verbose

Write-Host "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
echo "Enter a project name that is used to generate resource names:" &&
read projectName &&

resourceGroupName="${projectName}rg" &&
storageAccountName="${projectName}store" &&
containerName="templates" &&

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv) &&

sasToken=$(az storage container generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --name $containerName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'`) &&
sasToken=$(echo $sasToken | sed 's/"//g')&&

blobUri=$(az storage account show -n $storageAccountName -g $resourceGroupName -o tsv --query primaryEndpoints.blob) &&
templateUri="${blobUri}${containerName}/azuredeploy.json" &&

az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-uri $templateUri \
  --parameters projectName=$projectName \
  --query-string $sasToken \
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