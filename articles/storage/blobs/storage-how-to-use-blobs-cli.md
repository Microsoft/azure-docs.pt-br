---
title: "Executar operações no armazenamento de Blobs do Azure (armazenamento de objeto) com a CLI do Azure | Microsoft Docs"
description: Saiba como carregar e baixar blobs no Armazenamento de Blobs do Azure, bem como criar uma SAS (Assinatura de acesso compartilhado) para gerenciar o acesso a um blob em sua conta de armazenamento.
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/15/2017
ms.author: marsma
ms.openlocfilehash: c37fc0b701b668ab6bb9213a487ec8baa33fe663
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="perform-blob-storage-operations-with-azure-cli"></a>Executar operações de armazenamento de blobs com a CLI do Azure

A Armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados de objeto não estruturados, como texto ou dados binários, que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Este tutorial aborda as operações básicas no armazenamento de Blobs do Azure como carregar, baixar e excluir blobs. Você aprenderá como:

> [!div class="checklist"]
> * Criar um contêiner
> * Carregar um arquivo (blob) para um contêiner
> * Listar os blobs em um contêiner
> * Baixar um blob de um contêiner
> * Copiar um blob entre contas de armazenamento
> * Excluir um blob
> * Exibir e modificar propriedades e metadados de blob
> * Gerenciar segurança com uma SAS (Assinatura de acesso compartilhado)

Este tutorial exige a CLI do Azure, versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Criar um contêiner

Os contêineres são semelhantes aos diretórios em seu computador, permitindo que você organize grupos de blobs em contêineres, da mesma forma que você organiza arquivos em um diretório. Uma conta de armazenamento pode ter qualquer quantidade de contêineres. Você pode armazenar até 500 TB de dados de blob em um contêiner, que é a quantidade máxima de dados em uma conta de armazenamento.

Crie um contêiner para armazenar os blobs com o comando [az storage container create](/cli/azure/storage/container#create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

Os nomes de contêiner devem começar com uma letra ou um número e podem conter apenas letras, números e o caractere de hífen (-). Para obter mais regras sobre como nomear blobs e contêineres, confira [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="enable-public-read-access-for-a-container"></a>Habilitar o acesso de leitura público para um contêiner

Um contêiner recém-criado é privado por padrão. Ou seja, ninguém pode acessar o contêiner sem um [assinatura de acesso compartilhado](#create-a-shared-access-signature-sas) ou as chaves de acesso da conta de armazenamento. Com a CLI do Azure, você pode modificar esse comportamento definindo permissões de contêiner para um dos três níveis:

| | |
|---|---|
| `--public-access off` | (Padrão) Sem acesso de leitura público |
| `--public-access blob` | Acesso de leitura público apenas aos blobs |
| `--public-access container` | Acesso de leitura público para blobs, pode listar os blobs no contêiner |

Quando você define o acesso público como `blob` ou `container`, você habilita o acesso somente leitura para qualquer pessoa na Internet. Por exemplo, se você quiser exibir imagens armazenadas como blobs em seu site, será necessário habilitar o acesso de leitura público. Se você quiser habilitar o acesso de leitura/gravação, use uma [SAS (assinatura de acesso compartilhado)](#create-a-shared-access-signature-sas).

Habilite o acesso de leitura público para o contêiner com o comando [az storage container set-permission](/cli/azure/storage/container#create).

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access container
```

## <a name="upload-a-blob-to-a-container"></a>Carregar um blob para um contêiner

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de bloco são o tipo mais comum de armazenamento de blob do Azure. Os blobs de acréscimo são usados quando os dados devem ser adicionados a um blob existente sem modificar o conteúdo existente, como para o registro em log. Os blobs de página auxiliam os arquivos VHD das máquinas virtuais IaaS.

Neste exemplo, carregamos um blob no contêiner criados na última etapa com o comando [az storage blob upload](/cli/azure/storage/blob#upload).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Essa operação cria o blob se ele ainda não existir e o substitui se já existir. Carrega vários arquivos para que você possa ver várias entradas ao listar os blobs na próxima etapa.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Liste os blobs em um contêiner com o comando [az storage blob list](/cli/azure/storage/blob#list).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

Resultado de exemplo:

```
Name            Blob Type      Length  Content Type              Last Modified
--------------  -----------  --------  ------------------------  -------------------------
ISSUE_TEMPLATE  BlockBlob         761  application/octet-stream  2017-04-21T18:29:50+00:00
LICENSE         BlockBlob       18653  application/octet-stream  2017-04-21T18:28:50+00:00
LICENSE-CODE    BlockBlob        1090  application/octet-stream  2017-04-21T18:29:02+00:00
README.md       BlockBlob        6700  application/octet-stream  2017-04-21T18:27:33+00:00
dir1/file1.txt  BlockBlob        6700  application/octet-stream  2017-04-21T18:32:51+00:00
```

## <a name="download-a-blob"></a>Baixar um blob

Baixe o blob carregado em uma etapa anterior usando o comando [az storage blob download](/cli/azure/storage/blob#download).

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="copy-a-blob-between-storage-accounts"></a>Copiar um blob entre contas de armazenamento

É possível copiar blobs em ou entre regiões e contas de armazenamento de modo assíncrono.

O exemplo a seguir demonstra como copiar blobs de uma conta de armazenamento para outra. Primeiro, criamos um contêiner na conta de armazenamento de origem, especificando o acesso de leitura público para seus blobs. Em seguida, carregue um arquivo para o contêiner e, finalmente, copie o blob desse contêiner para um contêiner na conta de armazenamento de destino.

Nesse exemplo, o contêiner de destino já deve existir na conta de armazenamento de destino para que a operação de cópia seja bem-sucedida. Além disso, o blob de origem especificado no argumento `--source-uri` deve incluir um token SAS (Assinatura de Acesso Compartilhado) ou ser acessível ao público, como neste exemplo.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/path/to/sourcefile \
    --name sourcefile

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

## <a name="delete-a-blob"></a>Excluir um blob

Exclua o blob do contêiner usando o comando [az storage blob delete](/cli/azure/storage/blob#delete).

```azurecli-interactive
az storage blob delete \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="display-and-modify-blob-properties-and-metadata"></a>Exibir e modificar propriedades e metadados de blob

Cada blob tem várias propriedades definidas pelo serviço que podem ser exibidas com o comando [az storage blob show](/cli/azure/storage/blob#show), incluindo o nome, tipo, comprimento e outras. Você também pode configurar um blob com suas propriedades e valores usando o comando [az storage blob metadata update](/cli/azure/storage/blob/metadata#update).

Neste exemplo, primeiro exibimos as propriedades definidas pelo serviço de um blob, depois atualizamos o blob com duas de nossas propriedades de metadados. Por fim, exibimos as propriedades de metadados do blob e seus valores com o comando [az storage blob metadata show](/cli/azure/storage/blob/metadata#show).

```azurecli-interactive
# Show properties of a blob
az storage blob show \
    --container-name mystoragecontainer \
    --name blobName \
    --output table

# Set metadata properties of a blob (replaces all existing metadata)
az storage blob metadata update \
    --container-name mystoragecontainer \
    --name blobName \
    --metadata "key1=value1" "key2=value2"

# Show metadata properties of a blob
az storage blob metadata show \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="create-a-shared-access-signature-sas"></a>Criar uma SAS (Assinatura de acesso compartilhado)

Uma SAS (Assinatura de acesso compartilhado) fornece uma maneira de conceder acesso limitado a objetos na sua conta de armazenamento, sem expor as chaves de acesso de sua conta de armazenamento. Para produzir um URI que permite o acesso a um recurso privado, crie um token SAS com as permissões desejadas e a janela de validade (sua duração efetiva), e anexe-a como uma cadeia de consulta à URL de um recurso, formando o URI SAS completo. Qualquer pessoa com esse URI SAS (URL do recurso mais o token SAS) pode acessá-lo durante a janela de validade do token SAS. Por exemplo, talvez você queira permitir o acesso de leitura a um blob privado por dois minutos, para que alguém possa vê-lo.

Nas etapas a seguir, você desabilitará o acesso público ao seu contêiner, testará o acesso privado e gerará e testará um URI de SAS.

### <a name="disable-container-public-access"></a>Desabilitar o acesso público ao contêiner

Primeiro, defina o nível de acesso do contêiner como `off`. Isso indica que não há acesso para o contêiner ou seus blobs sem uma assinatura de acesso compartilhado ou uma chave de acesso da conta de armazenamento.

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access off
```

### <a name="verify-private-access"></a>Verificar o acesso privado

Para verificar se não há acesso de leitura público aos blobs nesse contêiner, obtenha a URL de um dos seus blobs com o comando [az storage blob url](/cli/azure/storage/blob#url).

```azurecli-interactive
az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv
```

Navegue até a URL do blob em uma janela de navegador no modo privado. Você verá um erro `ResourceNotFound`, pois o blob é privado, e você não incluiu uma assinatura de acesso compartilhado.

### <a name="create-a-sas-uri"></a>Criar um URI de SAS

Agora, vamos criar um URI de SAS que permite o acesso ao blob. No exemplo a seguir, primeiro preencheremos uma variável com a URL para o blob com [az storage blob url](/cli/azure/storage/blob#url), depois, preencheremos outra variável com um token SAS gerado com o comando [az storage blob generate-sas](/cli/azure/storage/blob#generate-sas). Por fim, obtemos o URI de SAS completo para o blob concatenando os dois, separados pelo separador de cadeia de consulta `?`.

```azurecli-interactive
# Get UTC datetimes for SAS start and expiry (Example: 1994-11-05T13:15:30Z)
sas_start=`date -u +'%Y-%m-%dT%H:%M:%SZ'`
sas_expiry=`date -u +'%Y-%m-%dT%H:%M:%SZ' -d '+2 minute'`

# Get the URL for the blob
blob_url=`az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv`

# Obtain a SAS token granting read (r) access between the
# SAS start and expiry times
sas_token=`az storage blob generate-sas \
    --container-name mystoragecontainer \
    --name blobName \
    --start $sas_start \
    --expiry $sas_expiry \
    --permissions r \
    --output tsv`

# Display the full SAS URI for the blob
echo $blob_url?$sas_token
```

### <a name="test-the-sas-uri"></a>Testar o URI de SAS

Em uma janela de navegador no modo privado, navegue até o URI de SAS completo exibido com o comando `echo` do trecho de código anterior. Neste momento, você não recebe um erro, e pode exibir ou baixar o blob.

Aguarde o tempo suficiente para a URL expirar (2 minutos, neste exemplo), depois navegue até o URI em outra janela do navegador em modo privado. Agora você vê um erro `AuthenticationFailed`, porque o token SAS expirou.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo a conta de armazenamento que você criou e quaisquer blobs que você carregou neste tutorial, exclua o grupo de recursos com comando [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Nesse tutorial, você aprendeu os fundamentos do trabalho com blobs no Armazenamento do Azure:

> [!div class="checklist"]
> * Criar um contêiner
> * Carregar um arquivo (blob) para um contêiner
> * Listar os blobs em um contêiner
> * Baixar um blob de um contêiner
> * Copiar um blob entre contas de armazenamento
> * Excluir um blob
> * Exibir e modificar propriedades e metadados de blob
> * Gerenciar segurança com uma SAS (Assinatura de acesso compartilhado)

Os recursos a seguir fornecem informações adicionais sobre como trabalhar com a CLI do Azure, e também como trabalhar com os recursos em sua conta de armazenamento.

* CLI do Azure
  * [Faça logon com a CLI do Azure 2.0](/cli/azure/authenticate-azure-cli) - Saiba mais sobre os diferentes métodos de autenticação com a CLI, incluindo logon não interativo por meio da [entidade de serviço](/cli/azure/authenticate-azure-cli#logging-in-with-a-service-principal) para execução de scripts de CLI do Azure autônomos.
  * [Referência de comandos da CLI do Azure 2.0](/cli/azure/)
* Gerenciador do Armazenamento do Microsoft Azure
  * O [Gerenciador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.
