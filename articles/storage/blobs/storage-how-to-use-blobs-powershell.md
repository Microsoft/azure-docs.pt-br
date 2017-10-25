---
title: "Executar operações no armazenamento de Blobs do Azure (armazenamento de objeto) com o PowerShell | Microsoft Docs"
description: "Tutorial - Executar operações no armazenamento de Blobs do Azure (armazenamento de objeto) com o PowerShell"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: 565bcba848de1c518b25ff4c55a9a47aaa45bfb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="perform-azure-blob-storage-operations-with-azure-powershell"></a>Executar operações de armazenamento de Blobs do Azure com o Azure PowerShell

A Armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados de objeto não estruturados, como texto ou dados binários, que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Este artigo aborda as operações básicas no armazenamento de Blobs do Azure como carregar, baixar e excluir blobs. Você aprenderá como:

> [!div class="checklist"]
> * Criar um contêiner 
> * Carregar blobs
> * Listar os blobs em um contêiner 
> * Baixar blobs
> * Copiar blobs
> * Excluir blobs
> * Exibir e definir propriedades e metadados de um blob
> * Gerenciar a segurança usando as Assinaturas de Acesso Compartilhado

Este tutorial requer o módulo do Azure PowerShell, versão 3.6 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Criar um contêiner

Os blobs são sempre carregados em um contêiner. Os contêineres são semelhantes aos diretórios em seu computador, permitindo que você organize grupos de blobs em contêineres como você organiza seus arquivos nas pastas do seu computador. Uma conta de armazenamento pode ter qualquer número de contêineres. Ela é limitada somente pela quantidade de espaço utilizada na conta de armazenamento (até 500 TB). 

Quando você cria um contêiner, é possível definir o nível de acesso, o que ajuda a definir quem pode acessar os blobs nesse contêiner. Por exemplo, podem ser privados (nível de acesso = `Off`), o que significa que ninguém pode acessá-los sem uma assinatura de acesso compartilhado ou as chaves de acesso da conta de armazenamento. Se você não especificar o nível de acesso ao criar o contêiner, o padrão é particular.

Talvez você queira que as imagens em seu contêiner estejam acessíveis publicamente. Por exemplo, se deseja armazenar as imagens a serem exibidas em seu site, você deseja que elas sejam públicas. Nesse caso, defina o nível de acesso do contêiner para `blob`, e qualquer pessoa com uma URL que aponta para um blob nesse contêiner pode acessar o blob.

Para criar o contêiner, defina o nome do contêiner e, em seguida, crie o contêiner, definindo as permissões para 'blob'. Os nomes de contêiner devem começar com uma letra ou número e podem conter apenas letras, números e o caractere de hífen (-). Para obter mais regras sobre como nomear blobs e contêineres, confira [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Criar um novo contêiner com [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer). Defina o nível de acesso como público. O nome do contêiner neste exemplo é *howtoblobs*.

```powershell
$containerName = "howtoblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-into-a-container"></a>Carregar blobs em um contêiner

O Armazenamento de Blobs do Azure oferece suporte a blobs de blocos, blobs de acréscimo e blobs de páginas.  Os arquivos VHD usados para voltar VMs IaaS são blobs de páginas. Os blobs de acréscimo são usados para registro em log, como quando você quer gravar em um arquivo e depois adicionar mais informações. A maioria dos arquivos armazenados no Armazenamento de Blobs são blobs de blocos. 

Para carregar um arquivo em um blob de blocos, obtenha uma referência de contêiner e uma referência para o blob de blocos nesse contêiner. Após obter a referência de blob, você pode carregar dados nele usando [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Essa operação cria o blob, se ele não existir, ou o substitui, se ele já existir.

A seguir você vê como carregar um blob em um contêiner. Primeiro, defina as variáveis que apontam para o diretório no computador local onde os arquivos estão localizados e defina uma variável para o nome do arquivo a ser carregado. Isso é útil quando você deseja executar a mesma operação várias vezes. Carregue alguns arquivos para que você possa ver várias entradas ao listar os blobs no contêiner.

Os exemplos a seguir carregam image001.jpg e Image002.png de D:\\_TestImages no disco local para o contêiner que você acabou de criar.

```powershell
$localFileDirectory = "D:\_TestImages\"

$blobName = "Image001.jpg"
$localFile = $localFileDirectory + $blobName
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx 
```

Carregar outro arquivo. 

```powershell
$blobName = "Image002.png"
$localFile = $localFileDirectory + $blobName 
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx
```

Carregue quantos arquivos desejar antes de continuar.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Obtenha uma lista de blobs no contêiner usando [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) e selecionando o nome do blob a ser exibido.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Baixar blobs

Baixe os blobs em seu disco local. Primeiro, defina uma variável que aponta para a pasta local para a qual você deseja baixar os blobs. Em seguida, para cada blob a ser baixado, defina o nome e a chamada [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) para baixar o blob.

Este exemplo copia os blobs para D:\\_TestImages\Downloads no disco local. 

```powershell
# local directory to which to download the files
# example "D:\_TestImages\Downloads\"
$localTargetDirectory = "D:\_TestImages\Downloads\"

# download the first blob
$blobName = "Image001.jpg"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 

# download another blob
$blobName = "Image002.png"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 
```

## <a name="copy-blobs"></a>Copiar blobs

Há várias coisas a considerar ao copiar os blobs. Você poderia estar apenas copiando o blob em um novo nome no mesmo local. Você poderia estar apenas copiando o blob para uma conta de armazenamento separada. Você poderia estar apenas copiando um blob grande (como um arquivo VHD) para outra conta de armazenamento. Cada um seria realizado de forma diferente.

### <a name="simple-blob-copy"></a>Cópia de blob simples

Você pode fazer cópia de um dos blobs em um contêiner, copiando-o em um novo blob. Este exemplo irá copiá-lo no mesmo contêiner com um nome diferente, mas você pode facilmente criar outro contêiner e copiá-lo. Este exemplo mostra como usar [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) para copiar o blob. É necessário especificar o nome de blob de origem e de destino e o nome do contêiner.

```powershell
$blobName = "Image002.png"
$newBlobName = "CopyOf_" + $blobName 

Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName `
  -DestBlob $newBlobName `
  -Context $ctx 

# get list of blobs to verify the new one has been created
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

### <a name="copy-a-blob-to-a-different-storage-account"></a>Copiar um blob para outra conta de armazenamento 

Você pode desejar copiar um blob para uma conta de armazenamento separada. Um exemplo de como fazer isso é copiar um arquivo VHD que retorna uma de suas VMs para outra conta de armazenamento para o backup. 

Configure uma segunda conta de armazenamento, recupere o contexto, configure um contêiner nessa conta de armazenamento e execute a cópia. Esta parte do script é quase idêntica ao script acima, exceto usando o uso da segunda conta de armazenamento, em vez da primeira.

```powershell
#create new storage acount, get context 
$storageAccount2Name = "blobstutorialtestcopy"
$storageAccount2 = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccount2Name `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx2 = $storageAccount2.Context

#create a container in the second storage account 
$containerName2 = "tutorialblobscopied"
New-AzureStorageContainer -Name $containerName2 `
  -Context $ctx2 `
  -Permission blob

# copy one of the blobs from the first storage account to the second one 
# specify the source and destination container, blob name, and context
Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -SrcContext $ctx `
  -DestContext $ctx2 

# list the blobs in the container in the second storage account
# to verify that the newly copied blob is there
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

### <a name="copying-very-large-blobs-asynchronously"></a>Copiar blobs grandes de forma assíncrona

Se você estiver copiando um blob muito grande com vários GB, poderá tirar proveito da cópia assíncrona, iniciando-a e, em seguida, voltando para verificar o status até que ela seja concluída. Dessa forma, você não está vinculados enquanto ele está executando a operação de cópia.

Se você estiver copiando dentro de uma conta de armazenamento, a cópia é muito rápida. Se você estiver copiando entre duas contas de armazenamento na mesma região, a cópia é bem rápida. No entanto, se a origem e o destino estiverem em regiões separadas, pode levar várias horas para concluir a cópia, dependendo da distância e do tamanho do seu arquivo. 

Esse script usa as mesmas variáveis definidas no último exemplo de cópia. A diferença é que um captura o status da cópia e a consulta repetidamente a cada 10 segundos até que ela seja concluída. Você precisará carregar um blob muito grande para sua conta de armazenamento para ver isso levar mais de uma iteração para concluir.

Use [Get-AzureStorageBlobCopyState](/powershell/module/azure.storage/get-azurestorageblobcopystate) para consultar o status da cópia. 

```powershell
# start the blob copy, and assign the result to $blobResult
$blobResult = Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -Context $ctx `
  -DestContext $ctx2

# get the status of the blob copy
$status = $blobResult | Get-AzureStorageBlobCopyState 
# show the value of the status
$status 

# loop until it finishes copying, pausing for 10 seconds after each iteration
# it is finished when the status is no longer 'Pending'
while ($status.Status -eq "Pending") {
    $status = $blobResult | Get-AzureStorageBlobCopyState 
    $status
    Start-Sleep 10
}

# get the list of blobs to see the new file in the second storage account 
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

## <a name="delete-blobs"></a>Excluir blobs

Para remover os blobs de uma conta de armazenamento, use [Remove-AzureStorageBlob](/powershell/module/azure.storage/Remove-AzureStorageBlob). 

```powershell
$blobName = "Image001.jpg"
Remove-AzureStorageBlob -Blob $blobName -Container $containerName -Context $ctx

# get list of blobs to see the one you deleted is gone
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

## <a name="read-and-write-a-blobs-properties-and-metadata"></a>Ler e gravar propriedades e metadados de um blob

Para acessar o conjunto de propriedades e de métodos para um **IListBlobItem** retornado, você deverá convertê-lo em um objeto **CloudBlockBlob**, **CloudPageBlob** ou **CloudBlobDirectory**. Se o tipo for desconhecido, você poderá usar uma verificação de tipo para determinar no qual convertê-lo. O código a seguir demonstra como recuperar e gerar as propriedades de um dos blobs carregados anteriormente usando [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) e convertendo o resultado a um objeto CloudBlockBlob.

```powershell
# blob properties
# get a reference to the blob you uploaded -- this is an **IListBlobItem** -- then
# convert it to a CloudBlockBlob, giving you access to the properties 
# and methods of the blob 
$blobName = "Image001.jpg"
$blob = Get-AzureStorageBlob -Context $ctx `
   -Container $containerName `
   -Blob $blobName 

#convert $blob to a CloudBlockBlob object
$cloudBlockBlob = [Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob] $blob.ICloudBlob

# you can view the properties by typing in $cloudBlockBlob 
#   and hitting the period key; this brings up IntelliSense,
#   which shows you all of the available properties
Write-Host "blob type = " $cloudBlockBlob.BlobType
Write-Host "blob name = " $cloudBlockBlob.Name
Write-Host "blob uri = " $cloudBLockBlob.Uri
```

Preencha as propriedades do sistema chamando FetchAttributes e, em seguida, exiba essas propriedades. Algumas das propriedades são graváveis e você pode alterar seus valores. Este exemplo mostra como alterar o tipo de conteúdo, também conhecido como o tipo MIME.

```powershell
# populate the system properties
$cloudBlockBlob.FetchAttributes()

# view some of the system properties
# contentType is commonly referred to as MIME type
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
Write-Host "size = " $cloudBlockBlob.Properties.Length 

# change the content type to image/png
$contentType = "image/jpg"
$cloudBlockBlob.Properties.ContentType = $contentType 
$cloudBlockBlob.SetProperties() 

# get the system properties again to show that the content type has changed
$cloudBlockBlob.FetchAttributes()
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
```

Cada blob tem seus próprios metadados que podem ser definidas. Por exemplo, você pode armazenar o nome do usuário que carregou o blob ou a data/hora em que ele foi carregado pela primeira vez. Os metadados consistem em pares chave/valor. Isso pode ser modificado usando o PowerShell da seguinte maneira.

```powershell
# "filename" is the key, "original file name" is the value
$cloudBlockBlob.Metadata["filename"] = "original file name"

# "owner" is the key, "RobinS" is the value
$cloudBlockBlob.Metadata["owner"] = "RobinS"

# save the metadata and then display it
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata

# clear the metadata, save it, and show it
$cloudBlockBlob.Metadata.Clear()
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata
```

## <a name="managing-security-for-blobs"></a>Gerenciamento da segurança de blobs 

Por padrão, o Armazenamento do Microsoft Azure mantém seus dados seguros limitando o acesso ao proprietário da conta, que possui as chaves de acesso à conta de armazenamento. Quando você precisa compartilhar dados de blob em sua conta de armazenamento, é importante fazer isso sem comprometer a segurança de suas chaves de acesso à conta. Para fazer isso, você pode usar uma URL de Assinatura de Acesso Compartilhado, que é uma URL para o ativo que inclui os parâmetros de consulta e um token de segurança que permite um nível de permissão específico para um determinado período de tempo. Por exemplo, talvez você queira conceder acesso de leitura a um blob privado por 5 minutos para que alguém possa exibi-lo. 

### <a name="set-the-access-level-of-the-container-and-its-blobs-to-private"></a>Defina o nível de acesso do contêiner e seus blobs como privado

Primeiro, defina o nível de acesso do contêiner para `Off`, que designa que não há nenhum acesso sem uma assinatura de acesso compartilhado ou a chave da conta. Use [Set-AzureStorageContainerAcl](/powershell/module/azure.storage/Set-AzureStorageContainerAcl).

```powershell
Set-AzureStorageContainerAcl -Name $containerName -Context $ctx -Permission Off 
```

### <a name="test-private-access"></a>Testar acesso privado

Para verificar que você não tem acesso aos blobs nesse contêiner, construa a URL para um dos blobs sem uma assinatura de acesso compartilhado e tente exibir o blob. Ao usar o protocolo HTTPS, a URL estará no seguinte formato:

    `https://storageaccountname.blob.core.windows.net/containername/blobname`

Isso mostra como criar a URL do blob.

```powershell
$blobUrl = "https://" `
  $storageAccountName ".blob.core.windows.net/" + `
  $containerName +  "/" $blobName

Write-Host "Blob URL = " $blobUrl 
```

Copie a URL do blob e cole-a em uma janela do navegador privada – ele mostrará um erro de autorização porque o blob é privado e você não incluiu uma assinatura de acesso compartilhado. 

### <a name="create-the-sas-uri"></a>Criar a URI de SAS

Criar um URI de SAS - este é o link para o blob, incluindo os parâmetros de consulta e o token de segurança que compõem a SAS. Cole esse URI em uma janela de navegação particular - ele mostrará a imagem. 

Primeiro, crie a data/hora de início e a data/hora de expiração para o acesso. É utilizada uma janela de 2 minutos. 

```powershell
# set the start time to the current date/time 
# set the end time to 2 minutes in the future
$StartTime = Get-Date
$EndTime = $StartTime.AddMinutes(2.0)
```

Crie o URI de SAS usando [New-AzureStorageBlobSASToken](/powershell/module/azure.storage/new-azurestorageblobsastoken). É necessário o nome do contêiner, nome do blob, contexto de conta de armazenamento, as permissões a serem concedidas (neste caso, leitura, gravação e exclusão) e a hora de início e término do acesso. 

```powershell
$SASURI = New-AzureStorageBlobSASToken -Container $containerName `
    -Blob $blobName `
    -Context $ctx `
    -Permission "rwd" `
    -StartTime $StartTime `
    -ExpiryTime $EndTime `
    -FullUri

Write-Host "URL with SAS = " $SASURI
```

Copie o URI de SAS e coloque-o em uma janela de navegador privada; ela mostrará a imagem.

Aguarde o tempo suficiente para a URL expirar (2 minutos, neste exemplo) e, em seguida, cole a URL em outra janela do navegador em modo privado. Neste momento, você obterá um erro de autorização e ele não mostrará a imagem, porque o URI SAS expirou.

## <a name="clean-up-resources"></a>Limpar recursos

Remova todos os ativos que você criou. Para fazer isso, você pode remover o grupo de recursos, que também exclui todos os recursos contidos no grupo. Nesse caso, ele remove todas as contas de armazenamento e o grupo de recursos.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre o gerenciamento de armazenamento de Blob básico e como:

> [!div class="checklist"]
> * Criar um contêiner 
> * Carregar blobs
> * Listar os blobs em um contêiner 
> * Baixar blobs
> * Copiar blobs
> * Excluir blobs
> * Ler e gravar propriedades e metadados de um blob
> * Gerenciar a segurança usando as Assinaturas de Acesso Compartilhado

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Cmdlets de Armazenamento do Microsoft Azure PowerShell
* [Cmdlets do PowerShell do Armazenamento](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Gerenciador do Armazenamento do Microsoft Azure
* [O Gerenciador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.