---
title: Usando a CLI 1.0 do Azure com o Armazenamento do Azure | Microsoft Docs
description: "Saiba como usar a CLI (Interface de Linha de Comando) 1.0 do Azure com o Armazenamento do Azure para criar e gerenciar contas de armazenamento, bem como trabalhar com blobs e arquivos do Azure. A CLI do Azure é uma ferramenta de plataforma cruzada"
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: b502232a-e8f6-4d6c-befd-3476592e0e35
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
ms.openlocfilehash: 55b857bd5b3d0c32553a60ff0649aeeb721cd6da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-cli-10-with-azure-storage"></a>Usando a CLI 1.0 do Azure com o Armazenamento do Azure

## <a name="overview"></a>Visão geral

A CLI do Azure fornece um conjunto de comandos entre plataformas de software livre para trabalhar com a Plataforma Azure. Ela fornece grande parte das mesmas funcionalidades encontradas no [Portal do Azure](https://portal.azure.com) , bem como funcionalidades avançadas de acesso a dados.

Neste guia, exploraremos como usar a [CLI (Interface de Linha de Comando) do Azure](../../cli-install-nodejs.md) para executar uma variedade de tarefas de administração e desenvolvimento com o Armazenamento do Azure. É recomendável baixar e instalar ou atualizar para a CLI mais recente do Azure antes usar este guia.

Este guia pressupõe que você conhece os conceitos básicos do Armazenamento do Azure. Este guia fornece vários scripts que demonstram o uso da CLI do Azure com o Armazenamento do Azure. Não se esqueça de atualizar as variáveis de script com base na sua configuração antes de executar cada script.

> [!NOTE]
> O guia fornece exemplos de comando e scripts da CLI do Azure para contas de armazenamento clássico. Consulte [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) para ver comandos da CLI do Azure para contas de armazenamento do Resource Manager.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-cli-in-5-minutes"></a>Introdução ao Armazenamento do Azure e à CLI do Azure em 5 minutos
Nos exemplos deste guia, usamos o Ubuntu, mas outras plataformas de sistema operacional devem funcionar de forma semelhante.

**Novo no Azure:** obtenha uma assinatura do Microsoft Azure e uma conta da Microsoft associada a essa assinatura. Para saber mais sobre as opções de compra do Azure, confira [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/), [Opções de compra](https://azure.microsoft.com/pricing/purchase-options/) e [Ofertas para membros](https://azure.microsoft.com/pricing/member-offers/) (para membros do MSDN, Microsoft Partner Network e BizSpark, entre outros programas da Microsoft).

Consulte [Atribuindo funções de administrador no Azure AD (Azure Active Directory)](https://msdn.microsoft.com/library/azure/hh531793.aspx) para obter mais informações sobre as assinaturas do Azure.

**Depois de criar uma assinatura e conta do Microsoft Azure:**

1. Baixe e instale a CLI do Azure seguindo as instruções descritas em [Instalar a CLI do Azure](../../cli-install-nodejs.md).
2. Quando a CLI do Azure estiver instalada, você poderá usar o comando azure na sua interface de linha de comando (Bash, Terminal, Prompt de comando) para acessar os comandos da CLI do Azure. Digite o comando _azure_ e você deverá ver a saída a seguir.

    ![Saída de comando do Azure](./media/storage-azure-cli/azure_command.png)   
3. Na interface de linha de comando, digite `azure storage` para listar todos os comandos de armazenamento do Azure e obter uma primeira impressão das funcionalidades fornecidas pela CLI do Azure. Você pode digitar o nome do comando com o parâmetro **-h** (por exemplo, `azure storage share create -h`) para ver os detalhes da sintaxe do comando.
4. Agora, você terá um script simples que mostra os comandos básicos da CLI do Azure para acessar o Armazenamento do Azure. Primeiramente, o script solicitará que você defina duas variáveis para sua conta e chave de armazenamento. Em seguida, o script criará um novo contêiner nessa nova conta de armazenamento e carregará um arquivo de imagem existente (blob) nesse contêiner. Depois que o script listar todos os blobs nesse contêiner, ele baixará o arquivo de imagem no diretório de destino, que existe no computador local.

    ```azurecli
    #!/bin/bash
    # A simple Azure storage example

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export image_to_upload=<image_to_upload>
    export destination_folder=<destination_folder>

    echo "Creating the container..."
    azure storage container create $container_name

    echo "Uploading the image..."
    azure storage blob upload $image_to_upload $container_name $blob_name

    echo "Listing the blobs..."
    azure storage blob list $container_name

    echo "Downloading the image..."
    azure storage blob download $container_name $blob_name $destination_folder

    echo "Done"
    ```

5. No computador local, abra o editor de texto de sua preferência (vim, por exemplo). Digite o script acima no editor de texto.
6. Agora, você precisa atualizar as variáveis de script com base nas suas configurações.

   * **<nome_da_conta_de_armazenamento>** Use o nome fornecido no script ou insira um novo nome para a conta de armazenamento. **Importante:** o nome da conta de armazenamento deve ser exclusivo no Azure. Ele também deve ter somente letras minúsculas!
   * **<chave_da_conta_de_armazenamento>** A chave de acesso da conta de armazenamento.
   * **<nome_do_contêiner>** Use o nome fornecido no script ou insira um novo nome para o contêiner.
   * **<imagem_a_ser_carregada>** Insira um caminho para uma imagem em seu computador local, como: "~/images/HelloWorld.png".
   * **<pasta_de_destino>** Insira um caminho para um diretório local de modo a armazenar os arquivos baixados do Armazenamento do Azure, como: "~/downloadImages".
7. Depois de atualizar as variáveis necessárias no vim, pressione as combinações de teclas `ESC`, `:`, `wq!` para salvar o script.
8. Para executar esse script, basta digitar o nome do arquivo de script no console do bash. Após execução desse script, você deverá ter uma pasta de destino local que inclua o arquivo de imagem baixado. A captura de tela a seguir mostra um exemplo de saída:

Depois que o script é executado, você deve ter uma pasta de destino que inclui o arquivo de imagem baixada.

## <a name="manage-storage-accounts-with-the-azure-cli"></a>Gerenciar contas de armazenamento com a CLI do Azure
### <a name="connect-to-your-azure-subscription"></a>Conecte-se à sua assinatura do Azure
Embora a maioria dos comandos de armazenamento funcione sem uma assinatura do Azure, é recomendável se conectar à sua assinatura na CLI do Azure. Para configurar a CLI do Azure para trabalhar com sua assinatura, siga as etapas em [Conectar uma assinatura do Azure da CLI do Azure](../../xplat-cli-connect.md).

### <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento
Você precisa de uma conta de armazenamento para usar o Armazenamento do Azure. Depois de configurar seu computador para se conectar à sua assinatura, você pode criar uma nova conta de Armazenamento do Azure.

```azurecli
azure storage account create <account_name>
```

O nome da conta de armazenamento deve ter entre 3 e 24 caracteres, usar números e apenas letras minúsculas.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Definir uma conta de armazenamento padrão do Azure nas variáveis de ambiente
Você pode ter várias contas de armazenamento na sua assinatura. É possível escolher uma delas e defini-la nas variáveis de ambiente para todos os comandos de armazenamento na mesma sessão. Isso permite executar os comandos de armazenamento da CLI do Azure sem especificar explicitamente a chave e a conta de armazenamento.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Outra forma de definir uma conta de armazenamento padrão é usar a cadeia de conexão. Em primeiro lugar, obtenha a cadeia de conexão pelo comando:

```azurecli
azure storage account connectionstring show <account_name>
```

Em seguida, copie a cadeia de conexão de saída e a defina como variável de ambiente:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Criar e gerenciar blobs
O Armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários, que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Esta seção pressupõe que você esteja familiarizado com o conceitos de Armazenamento de Blob do Azure. Para obter informações detalhadas, confira [Introdução ao Armazenamento de Blobs do Azure usando o .NET](../blobs/storage-dotnet-how-to-use-blobs.md) e [Blob Service Concepts](http://msdn.microsoft.com/library/azure/dd179376.aspx) (Conceitos do serviço Blob).

### <a name="create-a-container"></a>Criar um contêiner
Todos os blobs no armazenamento do Azure devem residir em um contêiner. Você pode criar um contêiner privado usando o comando `azure storage container create` :

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> Há três níveis de acesso de leitura anônimo: **Desativado**, **Blob** e **Contêiner**. Para evitar o acesso anônimo a blobs, defina o parâmetro de permissão como **Desativado**. Por padrão, o novo contêiner é privado e pode ser acessado apenas pelo proprietário da conta. Para permitir acesso de leitura público anônimo a recursos de blob, mas não aos metadados do contêiner ou à lista de blobs no contêiner, defina o parâmetro de permissão como **Blob**. Para permitir acesso de leitura público completo a recursos, metadados do contêiner e à lista de blobs no contêiner, defina o parâmetro de permissão como **Contêiner**. Para obter mais informações, consulte [Gerenciar acesso anônimo de leitura aos contêineres e blobs](../blobs/storage-manage-access-to-resources.md).
>
>

### <a name="upload-a-blob-into-a-container"></a>Carregar um blob em um contêiner
O Armazenamento de Blob do Azure oferece suporte a blobs de blocos e a blobs de páginas. Para saber mais, confira [Noções básicas sobre Blobs de bloco, Blobs de acréscimo e Blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Para carregar blobs em um contêiner, você poderá usar `azure storage blob upload`. Por padrão, esse comando carrega os arquivos locais para um blob de blocos. Para especificar o tipo de blob, você pode usar o parâmetro `--blobtype` .

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Baixar blobs de um contêiner
O exemplo a seguir demonstra como baixar blobs de um contêiner.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Copiar blobs
É possível copiar blobs em ou entre regiões e contas de armazenamento de modo assíncrono.

O exemplo a seguir demonstra como copiar blobs de uma conta de armazenamento para outra. Neste exemplo, podemos criar um contêiner onde os blobs podem ser acessados pública e anonimamente.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

Neste exemplo, é executada uma cópia assíncrona. Você pode monitorar o status de cada operação de cópia, executando a operação `azure storage blob copy show` .

Observe que a URL de origem fornecida para a operação de cópia deve ser acessível publicamente ou incluir um token SAS (assinatura de acesso compartilhado).

### <a name="delete-a-blob"></a>Excluir um blob
Para excluir um blob, use o comando abaixo:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Criar e gerenciar compartilhamentos de arquivos
Os Arquivos do Azure ofereciam o armazenamento compartilhado para aplicativos com o uso do protocolo SMB padrão. As máquinas virtuais e os serviços de nuvem do Microsoft Azure, bem como aplicativos locais, podem compartilhar dados de arquivos por meio de compartilhamentos montados. Você pode gerenciar compartilhamentos de arquivos e dados de arquivos por meio da CLI do Azure. Para obter mais informações sobre Arquivos do Azure, consulte [Introdução aos Arquivos do Azure](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Criar um compartilhamento de arquivos
Um compartilhamento de Arquivos do Azure é um compartilhamento de arquivos do SMB no Azure. Todos os arquivos e diretórios devem ser criados em um compartilhamento de arquivos. Uma conta de armazenamento pode conter um número ilimitado de compartilhamentos, e um compartilhamento pode conter um número ilimitado de arquivos, até os limites de capacidade da conta de armazenamento. O exemplo a seguir cria um compartilhamento de arquivos denominado **myshare**.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Criar um diretório
Um diretório fornece uma estrutura hierárquica opcional para um compartilhamento de arquivos do Azure. O exemplo a seguir cria um diretório denominado **myDir** no compartilhamento de arquivos.

```azurecli
azure storage directory create myshare myDir
```

Observe que esse caminho de diretório pode incluir vários níveis, *por exemplo*: **a/b**. No entanto, você deve garantir a existência de todos os diretórios pai. Por exemplo, para o caminho **a/b**, você deve criar o diretório **a** primeiro e, em seguida, o diretório **b**.

### <a name="upload-a-local-file-to-directory"></a>Carregar um arquivo local no diretório
O exemplo a seguir carrega um arquivo de **~/temp/samplefile.txt** no diretório **myDir**. Edite o caminho do arquivo para que ele aponte para um arquivo válido em seu computador local:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Observe que um arquivo no compartilhamento pode ter até 1 TB.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Listar os arquivos no diretório ou na raiz de compartilhamento
É possível listar os arquivos e subdiretórios em um diretório ou uma raiz de compartilhamento usando o comando a seguir:

```azurecli
azure storage file list myshare myDir
```

Observe que o nome do diretório é opcional para a operação de listagem. Se omitido, o comando listará o conteúdo do diretório raiz do compartilhamento.

### <a name="copy-files"></a>Copiar arquivos
A partir da versão 0.9.8 da CLI do Azure, você pode copiar um arquivo para outro arquivo, um arquivo para um blob ou um blob para um arquivo. A seguir, demonstramos como executar essas operações de cópia usando comandos CLI. Para copiar um arquivo para o novo diretório:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

Para copiar um blob para um diretório de arquivos:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar a referência de comandos da CLI 1.0 do Azure para trabalhar com recursos de Armazenamento aqui:

* [Comandos da CLI do Azure no modo Resource Manager](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)
* [Comandos da CLI do Azure no modo Gerenciamento de Serviços do Azure](../../cli-install-nodejs.md)

Talvez você também queira testar a [CLI do Azure 2.0](../storage-azure-cli.md), nossa CLI de próxima geração escrita em Python para uso com o modelo de implantação do Resource Manager.
