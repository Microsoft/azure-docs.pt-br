---
title: Início rápido – Criar um blob com a CLI do Azure
titleSuffix: Azure Storage
description: Neste início rápido, você verá como usar a CLI do Azure para carregar um blob no Armazenamento do Microsoft Azure, baixar um blob e listar os blobs em um contêiner.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: 04f793f78cef938c31e7a30aad5569a54eb461a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104613100"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Início Rápido: Criar, baixar e listar blobs com a CLI do Azure

A CLI do Azure é a experiência de linha de comando do Azure para gerenciar recursos do Azure. Você pode usá-la em seu navegador com o Azure Cloud Shell. Você também pode instalá-lo no Windows, no Linux ou no macOS e executá-lo na linha de comando. Neste guia de início rápido, você aprende a usar a CLI do Azure para carregar e baixar dados para e do Armazenamento de Blobs do Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment-h3.md)]

- Este artigo exige a versão 2.0.46 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="authorize-access-to-blob-storage"></a>Autorizar o acesso ao Armazenamento de Blobs

Você pode autorizar o acesso ao Armazenamento de Blobs por meio da CLI do Azure com as credenciais do Azure AD ou usando a chave de acesso da conta de armazenamento. Recomendamos usar as credenciais do Azure AD. Este artigo mostra como autorizar operações do Armazenamento de Blobs usando o Azure AD.

Os comandos da CLI do Azure para operações de dados no Armazenamento de Blobs dão suporte ao parâmetro `--auth-mode`, que permite especificar como autorizar determinada operação. Defina o parâmetro `--auth-mode` como `login` para autorização com as credenciais do Azure AD. Para obter mais informações, confira [Autorizar o acesso a dados de blob ou de filas com a CLI do Azure](./authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Somente as operações de dados do Armazenamento de Blobs dão suporte ao parâmetro `--auth-mode`. As operações de gerenciamento, como a criação de um grupo de recursos ou uma conta de armazenamento, usam automaticamente as credenciais do Azure AD para autorização.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com o comando [az group create](/cli/azure/group). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento de uso geral com o comando [az storage account create](/cli/azure/storage/account). A conta de armazenamento de uso geral pode ser usada para todos os quatro serviços: blobs, arquivos, tabelas e filas.

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob
```

## <a name="create-a-container"></a>Criar um contêiner

Os blobs são sempre carregados em um contêiner. Você pode organizar grupos de blobs em contêineres de modo similar à maneira como organiza arquivos do computador em pastas. Crie um contêiner para armazenar os blobs com o comando [az storage container create](/cli/azure/storage/container).

O exemplo a seguir usa sua conta do Azure AD para autorizar a operação para criar o contêiner. Antes de criar o contêiner, atribua a função [Colaborador de Dados do Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a si mesmo. Porém, mesmo que você seja o proprietário da conta, precisará de permissões explícitas para executar operações de dados na conta de armazenamento. Para obter mais informações sobre como atribuir funções do Azure, confira [Usar a CLI do Azure para atribuir uma função do Azure para acesso](../common/storage-auth-aad-rbac-cli.md?toc=/azure/storage/blobs/toc.json).  

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli
az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"

az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

> [!IMPORTANT]
> As atribuições de função do Azure podem levar alguns minutos para serem propagadas.

Você também pode usar a chave de conta de armazenamento para autorizar a operação para criar o contêiner. Para obter mais informações sobre como autorizar operações de dados com a CLI do Azure, confira [Autorizar o acesso a dados de blob ou de fila com a CLI do Azure](./authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="upload-a-blob"></a>Carregar um blob

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Os exemplos deste início rápido mostram como trabalhar com blobs de blocos.

Primeiro, crie um arquivo no qual carregar um blob de blocos. Se você está usando o Azure Cloud Shell, use o seguinte comando para criar um arquivo:

```bash
vi helloworld
```

Quando o arquivo abrir, pressione **insert**. Digite *Olá mundo* e pressione **Esc**. Em seguida, digite *:x* e pressione **Enter**.

Nesse exemplo, você carregou um blob no contêiner criado na etapa anterior usando o comando [az storage blob upload](/cli/azure/storage/blob). Não é necessário especificar um caminho de arquivo, pois o arquivo foi criado no diretório raiz. Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

Essa operação criará o blob se ele ainda não existir e o substituirá se já existir. Carregue quantos arquivos desejar antes de continuar.

Para carregar vários arquivos ao mesmo tempo, você pode usar o comando [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Liste os blobs em um contêiner com o comando [az storage blob list](/cli/azure/storage/blob). Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Baixar um blob

Use o comando [az storage blob download](/cli/azure/storage/blob) para baixar o blob que você carregou anteriormente. Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Transferência de dados com AzCopy

O utilitário de linha de comando AzCopy oferece transferência de dados de alto desempenho e programável para o Armazenamento do Azure. Use o AzCopy para transferir dados bidirecionalmente no Armazenamento de Blobs e nos Arquivos do Azure. Para obter mais informações sobre o AzCopy v10, a última versão do AzCopy, confira [Introdução ao AzCopy](../common/storage-use-azcopy-v10.md). Para saber mais sobre como usar o AzCopy v10 com o Armazenamento de Blobs, confira [Transferir dados com o AzCopy e o Armazenamento de Blobs](../common/storage-use-azcopy-v10.md#transfer-data).

O exemplo a seguir usa o AzCopy para carregar um arquivo local em um blob. Lembre-se de substituir os valores de exemplo pelos próprios valores:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Limpar os recursos

Caso deseje excluir os recursos criados como parte deste início rápido, incluindo a conta de armazenamento, exclua o grupo de recursos usando o comando [az group delete](/cli/azure/group). Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como transferir arquivos entre um sistema de arquivos local e um contêiner no armazenamento do Blob do Azure. Para saber mais sobre como trabalhar com o Armazenamento de Blobs usando a CLI do Azure, explore os exemplos da CLI do Azure para o Armazenamento de Blobs.

> [!div class="nextstepaction"]
> [Exemplos da CLI do Azure para o Armazenamento de Blobs](./storage-samples-blobs-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
