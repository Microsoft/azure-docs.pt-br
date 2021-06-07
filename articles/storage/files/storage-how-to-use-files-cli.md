---
title: Início Rápido para o gerenciamento de compartilhamentos de arquivos do Azure usando a CLI do Azure
description: Neste início rápido, aprenda a usar a CLI do Azure a fim de gerenciar os Arquivos do Azure. Crie um grupo de recursos e uma conta de armazenamento e, em seguida, crie e use um compartilhamento de arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5611088b76d8acf785fc0951100dcd4a2f439250
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593162"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Início Rápido: Criar e gerenciar compartilhamentos de arquivos do Azure usando a CLI do Azure
Este guia percorre os fundamentos de trabalhar com [compartilhamentos de arquivos do Azure](storage-files-introduction.md) usando a CLI do Azure. Os compartilhamentos de arquivos do Azure são iguais a outros compartilhamentos de arquivos, mas são armazenados na nuvem e compatíveis com a plataforma do Azure. Os compartilhamentos dos Arquivos do Azure dão suporte ao protocolo SMB padrão e ao protocolo NFS (Network File System – versão prévia) e habilita o compartilhamento de arquivos entre vários computadores, aplicativos e instâncias. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.4 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

- Por padrão, os comandos da CLI do Azure são em JavaScript Object Notation (JSON). JSON é a forma padrão de enviar e receber mensagens a partir de APIs REST. Para facilitar o trabalho com as respostas em JSON, alguns dos exemplos neste artigo usam o parâmetro *query* nos comandos da CLI do Azure. Esse parâmetro utiliza a [linguagem de consulta JMESPath](http://jmespath.org/) para a análise do JSON. Você pode aprender mais sobre como usar os resultados dos comandos da CLI do Azure seguindo a linguagem de consulta JMESPath, consulte o [Tutorial de JMESPath](http://jmespath.org/tutorial.html).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Se você ainda não tiver um grupo de recursos do Azure, pode usar o comando [az group create](/cli/azure/group) para criar um. 

O seguinte exemplo cria um grupo de recursos chamado *myResourceGroup* na localização *Oeste dos EUA 2*:

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Uma conta de armazenamento é um pool compartilhado de armazenamento no qual você pode implantar o compartilhamento de arquivos do Azure ou outros recursos de armazenamento, como blobs ou filas. Uma conta de armazenamento pode conter uma quantidade ilimitada de compartilhamentos de arquivos. Um compartilhamento pode conter uma quantidade ilimitada de arquivos, até os limites de capacidade da conta de armazenamento.

O exemplo a seguir cria uma conta de armazenamento usando o comando [az storage account create](/cli/azure/storage/account). Os nomes de conta de armazenamento devem ser exclusivo e, portanto, use `$RANDOM` para acrescentar um número ao nome e torná-lo exclusivo.

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```

> [!Note]  
> Compartilhamentos maiores que 5 TiB (até o máximo de 100 TiB por compartilhamento) só estão disponíveis em contas de armazenamento LRS (com redundância local) e ZRS (com redundância de zona). Para criar uma conta de armazenamento GRS (com redundância geográfica) ou GZRS (com redundância de zona geográfica), remova o parâmetro `--enable-large-file-share`.

### <a name="get-the-storage-account-key"></a>Obter a chave da conta de armazenamento
As chaves de conta de armazenamento controlam o acesso aos recursos em uma conta de armazenamento. As chaves são criadas automaticamente quando você cria uma conta de armazenamento. Você pode obter as chaves da conta de armazenamento usando o comando [az storage account keys list](/cli/azure/storage/account/keys): 

```azurecli-interactive 
export storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure
Agora você pode criar seu primeiro compartilhamento de arquivos do Azure. Criar compartilhamentos de arquivos usando o comando [az storage share create](/cli/azure/storage/share). Este exemplo cria um compartilhamento de arquivos do Azure chamado *myshare*: 

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

Os nomes de compartilhamento podem conter somente letras em minúsculas, números e hifens (mas não podem começar com um hífen). Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, confira [Nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Usar o compartilhamento de arquivos do Azure
Os Arquivos do Azure fornecem dois métodos para trabalhar com arquivos e pastas dentro do seu compartilhamento de arquivos do Azure: o padrão do setor [protocolo SMB (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) e o [protocolo REST de arquivo](/rest/api/storageservices/file-service-rest-api). 

Para montar um compartilhamento de arquivos com SMB, consulte o documento abaixo com base em seu sistema operacional:
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Usar um compartilhamento de arquivos do Azure com o protocolo REST de arquivo 
É possível trabalhar diretamente com o protocolo REST de Arquivo (criar manualmente chamadas REST HTTP por conta própria), mas a maneira mais comum para usar o protocolo REST de Arquivo é usar a CLI do Azure, o [módulo do Azure PowerShell](storage-how-to-use-files-powershell.md) ou um SDK do Armazenamento do Microsoft Azure, que fornecem um bom wrapper do protocolo REST de Arquivo na linguagem de scripts/programação de sua escolha.  

Acreditamos que a maioria dos usos dos arquivos do Azure será com o compartilhamento em vez do protocolo SMB, pois isso permite usar os aplicativos e as ferramentas existentes que se espera poder usar, mas há vários motivos que mostram como é vantajoso usar a API REST de arquivo em vez de SMB, a saber:

- Você está buscando seu compartilhamento de arquivo no Azure Bash Cloud Shell (que não pode montar os compartilhamentos de arquivos via SMB).
- Você está aproveitando as vantagens dos recursos sem servidor, como o [Azure Functions](../../azure-functions/functions-overview.md). 
- Você está criando um serviço de valor agregado que interagirá com muitos compartilhamentos de arquivos do Azure, como realizar backup ou verificações antivírus.

Os exemplos a seguir mostram como usar a CLI do Azure para manipular o compartilhamento de arquivos do Azure com o protocolo REST de Arquivo. 

### <a name="create-a-directory"></a>Criar um diretório
Para criar um novo diretório chamado *myDirectory* na raiz do seu compartilhamento de arquivos do Azure, use o comando [`az storage directory create`](/cli/azure/storage/directory):

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

### <a name="upload-a-file"></a>Carregar um arquivo
Para demonstrar como carregar um arquivo usando o comando [`az storage file upload`](/cli/azure/storage/file), primeiro crie um arquivo para carregar a unidade temporária do Cloud Shell. No exemplo a seguir, crie e carregue o arquivo:

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Se você estiver executando a CLI do Azure localmente, substitua `~/clouddrive` por um caminho existente em seu computador.

Depois de carregar o arquivo, você pode usar o comando [`az storage file list`](/cli/azure/storage/file) para verificar se o arquivo foi carregado no compartilhamento de arquivos do Azure:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Baixar um arquivo
Você pode usar o comando [`az storage file download`](/cli/azure/storage/file) para baixar uma cópia do arquivo que você carregou na unidade temporária do Cloud Shell:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

### <a name="copy-files"></a>Copiar arquivos
Uma tarefa comum é copiar arquivos de um compartilhamento de arquivo para outro. Para demonstrar essa funcionalidade, crie um novo compartilhamento. Copie o arquivo que você carregou neste novo compartilhamento usando o comando [az storage file copy](/cli/azure/storage/file/copy): 

```azurecli-interactive
otherShareName="myshare2"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $otherShareName \
    --quota 1024 \
    --output none

az storage directory create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --name "myDirectory2" \
    --output none

az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-share $shareName \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share $otherShareName \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Agora, se você listar os arquivos no novo compartilhamento, deverá ver o arquivo copiado:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --path "myDirectory2" \
    --output table
```

Embora o comando `az storage file copy start` seja conveniente para movimentações de arquivo entre compartilhamentos de arquivo do Azure, para migrações e grandes movimentações de dados, recomendamos o `rsync` no macOS e Linux e `robocopy` no Windows. `rsync` e `robocopy` usam o SMB para executar movimentações de dados em vez da API FileREST.

## <a name="create-and-manage-share-snapshots"></a>Criar e gerenciar instantâneos de compartilhamento
Outra tarefa útil que você pode fazer com um compartilhamento de arquivos do Azure é criar instantâneos de compartilhamento. Um instantâneo preserva um ponto no tempo para uma cópia de um compartilhamento de arquivos do Azure. Os instantâneos de compartilhamento são semelhantes a algumas tecnologias de sistema operacional que você talvez já conheça, como:

- Instantâneos do [LVM (Gerenciador de Volumes Lógicos)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) para sistemas Linux.
- Instantâneos do [APFS (Sistema de arquivos da Apple)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) para macOS.
- [VSS (Serviço de Cópias de Sombra de Volume)](/windows/desktop/VSS/volume-shadow-copy-service-portal) para sistemas de arquivos Windows, como NTFS e ReFS.
 
Você pode criar um instantâneo de compartilhamento usando o comando [`az storage share snapshot`](/cli/azure/storage/share):

```azurecli-interactive
snapshot=$(az storage share snapshot \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Procurar conteúdo do instantâneo de compartilhamento
Você pode procurar o conteúdo de um instantâneo de compartilhamento passando o carimbo de data/hora do instantâneo de compartilhamento capturado na variável `$snapshot` para o comando `az storage file list`:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --snapshot $snapshot \
    --output table
```

### <a name="list-share-snapshots"></a>Listar instantâneos de compartilhamento
Para ver a lista de instantâneos tirados para seu compartilhamento use o comando a seguir:

```azurecli-interactive
az storage share list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --include-snapshot \
    --query "[? name== '$shareName' && snapshot!=null].snapshot" \
    --output tsv
```

### <a name="restore-from-a-share-snapshot"></a>Restaurar de um instantâneo de compartilhamento
Você pode restaurar um arquivo usando o comando `az storage file copy start` usado anteriormente. Primeiro, exclua o arquivo SampleUpload.txt que você carregou, para que você possa restaurá-lo a partir do instantâneo:

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --output none

# Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI$shareName"/myDirectory/SampleUpload.txt?sharesnapshot="$snapshot

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-uri $URI \
    --destination-share $shareName \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Excluir um instantâneo de compartilhamento
Você pode excluir um instantâneo de compartilhamento usando o comando [`az storage share delete`](/cli/azure/storage/share). Use a variável que contém a referência `$SNAPSHOT` ao parâmetro `--snapshot`:

```azurecli-interactive
az storage share delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --snapshot $snapshot \
    --output none
```

## <a name="clean-up-resources"></a>Limpar os recursos
Quando concluir, você poderá usar o comando [`az group delete`](/cli/azure/group) para remover o grupo de recursos e todos os recursos relacionados: 

```azurecli-interactive 
az group delete --name $resourceGroupName
```

Alternativamente, você pode remover os recursos individualmente.
- Para remover os compartilhamentos de arquivos do Azure que você criou com este artigo:

    ```azurecli-interactive
    az storage share list \
            --account-name $storageAccountName \
            --account-key $storageAccountKey \
            --query "[].name" \
            --output tsv | \
        xargs -L1 bash -ec '\
            az storage share delete \
                --account-name "$storageAccountName" \
                --account-key "$storageAccountKey" \
                --name $0 \
                --delete-snapshots include \
                --output none'
    ```

- Para remover a própria conta de armazenamento. (Isso remove de forma implícita os compartilhamentos de arquivo do Azure que você criou e quaisquer outros recursos que tenham sido criados, como o contêiner de armazenamento de Blobs do Azure.)

    ```azurecli-interactive
    az storage account delete \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --yes
    ```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [O que Arquivos do Azure?](storage-files-introduction.md)
