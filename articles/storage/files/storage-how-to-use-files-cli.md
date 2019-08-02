---
title: Início Rápido para o gerenciamento de compartilhamentos de arquivos do Azure usando a CLI do Azure
description: Use este início rápido para aprender a usar a CLI do Azure a fim de gerenciar os Arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 93baf275e93c28283836a92c71eb9b24151392fc
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699586"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Início Rápido: Criar e gerenciar compartilhamentos de arquivos do Azure usando a CLI do Azure
Este guia percorre os fundamentos de trabalhar com [compartilhamentos de arquivos do Azure](storage-files-introduction.md) usando a CLI do Azure. Os compartilhamentos de arquivos do Azure são iguais a outros compartilhamentos de arquivos, mas são armazenados na nuvem e compatíveis com a plataforma do Azure. Os compartilhamentos de Arquivos do Azure oferecem suporte ao protocolo SMB padrão do setor e habilitar o compartilhamento de arquivos entre vários computadores, aplicativos e instâncias. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você decidir instala e usar a CLI do Azure localmente, para as etapas neste artigo, você deve estar executando a versão 2.0.4 ou posterior da CLI do Azure. Execute **az --version** para saber qual é a versão da sua CLI do Azure. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 

Por padrão, os comandos da CLI do Azure são em JavaScript Object Notation (JSON). JSON é a forma padrão de enviar e receber mensagens a partir de APIs REST. Para facilitar o trabalho com as respostas em JSON, alguns dos exemplos neste artigo usam o parâmetro *query* nos comandos da CLI do Azure. Esse parâmetro utiliza a [linguagem de consulta JMESPath](http://jmespath.org/) para a análise do JSON. Você pode aprender mais sobre como usar os resultados dos comandos da CLI do Azure seguindo a linguagem de consulta JMESPath, consulte o [Tutorial de JMESPath](http://jmespath.org/tutorial.html).

## <a name="sign-in-to-azure"></a>Entrar no Azure
Se você estiver usando a CLI do Azure localmente, abra um prompt e entre no Azure se você ainda não fez isso.

```bash 
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Se você ainda não tiver um grupo de recursos do Azure, pode usar o comando [az group create](/cli/azure/group) para criar um. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *Leste dos EUA*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Uma conta de armazenamento é um pool compartilhado de armazenamento no qual você pode implantar o compartilhamento de arquivos do Azure ou outros recursos de armazenamento, como blobs ou filas. Uma conta de armazenamento pode conter uma quantidade ilimitada de compartilhamentos de arquivos. Um compartilhamento pode conter uma quantidade ilimitada de arquivos, até os limites de capacidade da conta de armazenamento.

O exemplo a seguir cria uma conta de armazenamento denominada *mystorageaccount\<número aleatório\>* usando o comando [az storage account create](/cli/azure/storage/account) e coloca o nome da conta de armazenamento na variável `$STORAGEACCT`. Os nomes das contas de armazenamento devem ser exclusivos. Portanto, substitua "mystorageacct" por um nome exclusivo.

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>Obter a chave da conta de armazenamento
As chaves de conta de armazenamento controlam o acesso aos recursos em uma conta de armazenamento. As chaves são criadas automaticamente quando você cria uma conta de armazenamento. Você pode obter as chaves da conta de armazenamento usando o comando [az storage account keys list](/cli/azure/storage/account/keys): 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure
Agora você pode criar seu primeiro compartilhamento de arquivos do Azure. Criar compartilhamentos de arquivos usando o comando [az storage share create](/cli/azure/storage/share). Este exemplo cria um compartilhamento de arquivos do Azure chamado *myshare*: 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

Os nomes de compartilhamento podem conter somente letras em minúsculas, números e hifens (mas não podem começar com um hífen). Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, confira [Nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Usar o compartilhamento de arquivos do Azure
Os Arquivos do Azure fornecem dois métodos para trabalhar com arquivos e pastas dentro do seu compartilhamento de arquivos do Azure: o padrão do setor [protocolo SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) e o [protocolo REST de arquivo](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Para montar um compartilhamento de arquivos com SMB, consulte o documento abaixo com base em seu sistema operacional:
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Usar um compartilhamento de arquivos do Azure com o protocolo REST de arquivo 
É possível trabalhar diretamente com o protocolo REST de Arquivo (criar manualmente chamadas REST HTTP por conta própria), mas a maneira mais comum para usar o protocolo REST de Arquivo é usar a CLI do Azure, o [módulo do Azure PowerShell](storage-how-to-use-files-powershell.md) ou um SDK do Armazenamento do Microsoft Azure, que fornecem um bom wrapper do protocolo REST de Arquivo na linguagem de scripts/programação de sua escolha.  

Acreditamos que a maioria dos usos dos arquivos do Azure será com o compartilhamento em vez do protocolo SMB, pois isso permite usar os aplicativos e as ferramentas existentes que se espera poder usar, mas há vários motivos que mostram como é vantajoso usar a API REST de arquivo em vez de SMB, a saber:

- Você está buscando seu compartilhamento de arquivo no Azure Bash Cloud Shell (que não pode montar os compartilhamentos de arquivos via SMB).
- Você precisa executar um script ou aplicativo de um cliente que não consegue montar um compartilhamento de SMB, como clientes locais que não têm a porta 445 desbloqueada.
- Você está aproveitando as vantagens dos recursos sem servidor, como o [Azure Functions](../../azure-functions/functions-overview.md). 

Os exemplos a seguir mostram como usar a CLI do Azure para manipular o compartilhamento de arquivos do Azure com o protocolo REST de Arquivo. 

### <a name="create-a-directory"></a>Criar um diretório
Para criar um novo diretório chamado *myDirectory* na raiz do seu compartilhamento de arquivos do Azure, use o comando [`az storage directory create`](/cli/azure/storage/directory):

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>Carregar um arquivo
Para demonstrar como carregar um arquivo usando o comando [`az storage file upload`](/cli/azure/storage/file), primeiro crie um arquivo para carregar a unidade temporária do Cloud Shell. No exemplo a seguir, crie e carregue o arquivo:

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Se você estiver executando a CLI do Azure localmente, substitua `~/clouddrive` por um caminho existente em seu computador.

Depois de carregar o arquivo, você pode usar o comando [`az storage file list`](/cli/azure/storage/file) para verificar se o arquivo foi carregado no compartilhamento de arquivos do Azure:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Baixar um arquivo
Você pode usar o comando [`az storage file download`](/cli/azure/storage/file) para baixar uma cópia do arquivo que você carregou na unidade temporária do Cloud Shell:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>Copiar arquivos
Uma tarefa comum é copiar arquivos de um compartilhamento de arquivos para outro ou de/para um contêiner do Armazenamento de Blobs do Azure. Para demonstrar essa funcionalidade, crie um novo compartilhamento. Copie o arquivo que você carregou neste novo compartilhamento usando o comando [az storage file copy](/cli/azure/storage/file/copy): 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Agora, se você listar os arquivos no novo compartilhamento, deverá ver o arquivo copiado:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

Embora o comando `az storage file copy start` seja conveniente para movimentações de arquivos entre compartilhamentos de arquivos do Azure e contêineres de armazenamento de Blobs do Azure, recomendamos AzCopy para movimentações maiores. (Em termos de quantidade ou tamanho de arquivos movidos). Saiba mais sobre [AzCopy para Linux](../common/storage-use-azcopy-linux.md) e [AzCopy para Windows](../common/storage-use-azcopy.md). O AzCopy deve ser instalado localmente. O AzCopy não está disponível no Cloud Shell. 

## <a name="create-and-manage-share-snapshots"></a>Criar e gerenciar instantâneos de compartilhamento
Outra tarefa útil que você pode fazer com um compartilhamento de arquivos do Azure é criar instantâneos de compartilhamento. Um instantâneo preserva um ponto no tempo para uma cópia de um compartilhamento de arquivos do Azure. Os instantâneos de compartilhamento são semelhantes a algumas tecnologias de sistema operacional que você talvez já conheça, como:

- Instantâneos do [LVM (Gerenciador de Volumes Lógicos)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) para sistemas Linux
- Instantâneos do [Sistema de arquivos da Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) para macOS
- [Serviço de Cópias de Sombra de Volume (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) para sistemas de arquivos do Windows, como NTFS e ReFS. Você pode criar um instantâneo de compartilhamento usando o comando [`az storage share snapshot`](/cli/azure/storage/share):

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Procurar conteúdo do instantâneo de compartilhamento
Você pode procurar o conteúdo de um instantâneo de compartilhamento passando o carimbo de data/hora do instantâneo de compartilhamento capturado na variável `$SNAPSHOT` para o comando `az storage file list`:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>Listar instantâneos de compartilhamento
Para ver a lista de instantâneos tirados para seu compartilhamento use o comando a seguir:

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>Restaurar de um instantâneo de compartilhamento
Você pode restaurar um arquivo usando o comando `az storage file copy start` usado anteriormente. Primeiro, exclua o arquivo SampleUpload.txt que você carregou, para que você possa restaurá-lo a partir do instantâneo:

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"
 # Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')
 URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT
 # Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Excluir um instantâneo de compartilhamento
Você pode excluir um instantâneo de compartilhamento usando o comando [`az storage share delete`](/cli/azure/storage/share). Use a variável que contém a referência `$SNAPSHOT` ao parâmetro `--snapshot`:

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>Limpar recursos
Quando concluir, você poderá usar o comando [`az group delete`](/cli/azure/group) para remover o grupo de recursos e todos os recursos relacionados: 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

Alternativamente, você pode remover os recursos individualmente.
- Para remover os compartilhamentos de arquivos do Azure que você criou com este artigo:

    ```azurecli-interactive
    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare" \
        --delete-snapshots include

    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare2" \
        --delete-snapshots include
    ```

- Para remover a própria conta de armazenamento. (Isso remove de forma implícita os compartilhamentos de arquivo do Azure que você criou e quaisquer outros recursos que tenham sido criados, como o contêiner de armazenamento de Blobs do Azure.)

    ```azurecli-interactive
    az storage account delete \
        --resource-group "myResourceGroup" \
        --name $STORAGEACCT \
        --yes
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [O que Arquivos do Azure?](storage-files-introduction.md)
