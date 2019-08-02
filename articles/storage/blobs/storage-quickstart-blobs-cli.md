---
title: Início Rápido do Azure – Criar um blob no armazenamento de objeto usando a CLI do Azure | Microsoft Docs
description: Neste início rápido, você usa a CLI do Azure no armazenamento de objeto (Blob). Em seguida, use a CLI para carregar um blob no Armazenamento do Azure, baixar um blob e listar os blobs em um contêiner.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: tamram
ms.reviewer: seguler
ms.openlocfilehash: 6a0aef9b2fc7a99183ebd6991691245731e00200
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565961"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>Início Rápido: carregar, baixar e listar blobs usando a CLI do Azure

A CLI do Azure é a experiência de linha de comando do Azure para gerenciar recursos do Azure. Você pode usá-la em seu navegador com o Azure Cloud Shell. Você também pode instalá-lo no Windows, no Linux ou no macOS e executá-lo na linha de comando. Neste guia de início rápido, você aprende a usar a CLI do Azure para carregar e baixar dados para e do Armazenamento de Blobs do Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para determinar sua versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Criar um contêiner

Os blobs são sempre carregados em um contêiner. É possível organizar grupos de blobs de forma similar àquela em que você organiza os arquivos em pastas no seu computador.

Crie um contêiner para armazenar os blobs com o comando [az storage container create](/cli/azure/storage/container).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Carregar um blob

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. A maioria dos arquivos armazenados no Armazenamento de Blobs são armazenados como blobs de blocos. Os blobs de acréscimo são usados quando os dados devem ser adicionados a um blob existente sem modificar o conteúdo existente, como para o registro em log. Os blobs de página auxiliam os arquivos VHD das máquinas virtuais IaaS.

Primeiramente, crie um arquivo para ser carregado em um blob.
Se você estiver usando o Azure Cloud Shell, use o seguinte para criar um arquivo: `vi helloworld` quando o arquivo é aberto, pressione **insert**, digite "Olá, Mundo" e, em seguida, pressione **Esc**, digite `:x` e pressione **Enter**.

Nesse exemplo, você carregou um blob no contêiner criado na etapa anterior usando o comando [az storage blob upload](/cli/azure/storage/blob).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Se você tiver usado o método descrito anteriormente para criar um arquivo no Azure Cloud Shell, será possível usar esse comando de CLI no lugar desse método (observe que, embora normalmente fosse necessário especificar um caminho, esse não é o caso agora porque o arquivo foi criado no diretório base):

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name helloworld \
    --file helloworld
```

Essa operação criará o blob se ele ainda não existir e o substituirá se já existir. Carregue quantos arquivos desejar antes de continuar.

Para carregar vários arquivos ao mesmo tempo, você pode usar o comando [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Liste os blobs em um contêiner com o comando [az storage blob list](/cli/azure/storage/blob).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Baixar um blob

Use o comando [az storage blob download](/cli/azure/storage/blob) para baixar o blob que você carregou anteriormente.

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Transferência de dados com AzCopy

O utilitário [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é outra opção para a transferência de dados programável por script de alto desempenho para o Armazenamento do Azure. Você pode usar o AzCopy para transferir dados para e do armazenamento de Blobs, Arquivo e Tabela.

Como um exemplo rápido, aqui está o comando do AzCopy para carregar um arquivo chamado *myfile.txt* para o contêiner *mystoragecontainer*.

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://mystorageaccount.blob.core.windows.net/mystoragecontainer \
    --dest-key <storage-account-access-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de qualquer um dos recursos no seu grupo de recursos, incluindo a conta de armazenamento que você criou neste guia de início rápido, exclua o grupo de recursos com comando [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Nesse guia de início rápido, você aprendeu a transferir arquivos entre o disco local e um contêiner no Armazenamento de Blobs do Azure. Para saber mais sobre como trabalhar com blobs no Armazenamento do Azure, continue o tutorial para trabalhar com Armazenamento de Blobs do Azure.

> [!div class="nextstepaction"]
> [Como: operações de armazenamento de blobs com a CLI do Azure](storage-how-to-use-blobs-cli.md)
