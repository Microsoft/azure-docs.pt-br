---
title: 'Início Rápido: Biblioteca de clientes do Armazenamento de Blobs do Azure – Ruby'
description: Crie uma conta de armazenamento e um contêiner no Armazenamento de Blobs do Azure. Use a biblioteca de clientes de armazenamento para Ruby para criar um blob, baixar um blob e listar os blobs em um contêiner.
author: twooley
ms.author: twooley
ms.date: 12/04/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 96b47afb11a0105e8f6d6b58e8862994493389f4
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277969"
---
# <a name="quickstart-azure-blob-storage-client-library-for-ruby"></a>Início Rápido: Biblioteca de clientes do Armazenamento de Blobs do Azure para Ruby

Saiba como usar o Ruby para criar, baixar e listar os blobs em um contêiner no Armazenamento de Blobs do Microsoft Azure.

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Verifique se você tem os pré-requisitos adicionais a seguir instalados:

- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Biblioteca do Armazenamento do Azure para Ruby](https://github.com/azure/azure-storage-ruby) usando o [pacote RubyGem](https://rubygems.org/gems/azure-storage-blob):

    ```console
    gem install azure-storage-blob
    ```

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo

O [aplicativo de exemplo](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) usado neste guia de início rápido é um aplicativo Ruby.

Use o [Git](https://git-scm.com/) para baixar uma cópia do aplicativo em seu ambiente de desenvolvimento. Este comando clona o repositório para o computador local:

```console
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git
```

Navegue até a pasta *storage-blobs-ruby-quickstart* e abra o arquivo *example.rb* no editor de código.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento

Informe o nome de sua conta de armazenamento e a chave de conta para criar uma instância de [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) para seu aplicativo.

O código a seguir no arquivo *example.rb* cria uma instância de um novo objeto [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService). Substitua os valores *accountname* e *accountkey* pelo nome e chave da conta.

```ruby
# Create a BlobService object
account_name = "accountname"
account_key = "accountkey"

blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
)
```

## <a name="run-the-sample"></a>Execute o exemplo

O exemplo cria um contêiner no Armazenamento de Blobs, cria um blob no contêiner, lista os blobs no contêiner e baixa o blob em um arquivo local.

Execute o exemplo. Veja um exemplo da saída da execução do aplicativo:

```console
C:\azure-samples\storage-blobs-ruby-quickstart> ruby example.rb

Creating a container: quickstartblobs18cd9ec0-f4ac-4688-a979-75c31a70503e

Creating blob: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

List blobs in the container following continuation token
        Blob name: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Downloading blob to C:/Users/azureuser/Documents/QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Paused, press the Enter key to delete resources created by the sample and exit the application
```

Quando você pressiona Enter para continuar, o programa de exemplo exclui o contêiner de armazenamento e o arquivo local. Antes de continuar, verifique se o arquivo baixado está na pasta *Documentos*.

Você também pode usar o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com) para ver os arquivos na conta de armazenamento. O Gerenciador de Armazenamento do Azure é uma ferramenta gratuita de multiplataforma que permite que você acesse as informações da sua conta de armazenamento.

Depois de verificar os arquivos, pressione a tecla Enter para excluir os arquivos de teste e encerrar a demonstração. Abra o arquivo *example.rb* para examinar o código.

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Em seguida, vamos percorrer o código de exemplo para que você entenda como ele funciona.

### <a name="get-references-to-the-storage-objects"></a>Obter referências a objetos de armazenamento

A primeira coisa a fazer é criar instâncias dos objetos usados para acessar e gerenciar o Armazenamento de Blobs. Esses objetos dependem uns dos outros. Cada um é usado pelo próximo na lista.

- Crie uma instância do objeto do [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) do Armazenamento do Azure para configurar as credenciais de conexão.
- Crie o objeto [Contêiner](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container/Container), que representa o contêiner que você está acessando. Os contêineres são usados para organizar seus blobs da mesma forma que você usa pastas no seu computador para organizar seus arquivos.

Quando tiver o objeto de contêiner, você poderá criar um objeto de blob [Bloco](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) que aponta para um blob específico no qual tem interesse. Use o objeto de [Bloco](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) para criar, baixar e copiar blobs.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Para obter mais informações sobre nomes de contêiner e de blobs, consulte [Nomeando e referenciando contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

O seguinte código de exemplo:

- Cria um contêiner
- Define permissões no contêiner para que os blobs sejam públicos. O contêiner é chamado de *quickstartblobs*, com uma ID exclusiva acrescentada.

```ruby
# Create a container
container_name = "quickstartblobs" + SecureRandom.uuid
puts "\nCreating a container: " + container_name
container = blob_client.create_container(container_name)

# Set the permission so the blobs are public
blob_client.set_container_acl(container_name, "container")
```

### <a name="create-a-blob-in-the-container"></a>Criar um blob no contêiner

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimos e blobs de páginas. Para criar um blob, chame o método [create_block_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#create_block_blob-instance_method) passando os dados para o blob.

O exemplo a seguir cria um blob chamado *QuickStart_* com uma ID exclusiva e uma extensão de arquivo *.txt* no contêiner criado anteriormente.

```ruby
# Create a new block blob containing 'Hello, World!'
blob_name = "QuickStart_" + SecureRandom.uuid + ".txt"
blob_data = "Hello, World!"
puts "\nCreating blob: " + blob_name
blob_client.create_block_blob(container.name, blob_name, blob_data)
```

Os blobs de blocos podem ter até 4,7 TB e podem ser desde planilhas até arquivos de vídeo grandes. Os blobs de páginas são usados principalmente para os arquivos VHD que auxiliam as máquinas virtuais de IaaS. Os blobs de acréscimos costumam ser usados para registro em log, como quando você quer gravar em um arquivo e continuar adicionando mais informações.

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Obter uma lista de arquivos no contêiner usando o método [list_blobs](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#list_blobs-instance_method). O código a seguir recupera a lista de blobs e exibe seus nomes.

```ruby
# List the blobs in the container
puts "\nList blobs in the container following continuation token"
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name: #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-a-blob"></a>Baixar um blob

Baixe os blobs para o disco local usando o método [get_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#get_blob-instance_method). O código a seguir baixa o blob criado em uma seção anterior.

```ruby
# Download the blob

# Set the path to the local folder for downloading
if(is_windows)
    local_path = File.expand_path("~/Documents")
else 
    local_path = File.expand_path("~/")
end

# Create the full path to the downloaded file
full_path_to_file = File.join(local_path, blob_name)

puts "\nDownloading blob to " + full_path_to_file
blob, content = blob_client.get_blob(container_name, blob_name)
File.open(full_path_to_file,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Limpar recursos

Se um blob deixar de ser necessário, use [delete_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#delete_blob-instance_method) para removê-lo. Exclua um contêiner inteiro usando o método [delete_container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#delete_container-instance_method). Excluir um contêiner também exclui todos os blobs armazenados nele.

```ruby
# Clean up resources, including the container and the downloaded file
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
```

## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Recursos para desenvolvimento de aplicativos Ruby com blobs

Confira estes recursos adicionais para desenvolvimento com Ruby:

- Exiba e baixe o [código-fonte da biblioteca do cliente Ruby](https://github.com/Azure/azure-storage-ruby) para o Armazenamento do Azure no GitHub.
- Explore [exemplos do Azure](/samples/browse/?products=azure&languages=ruby) gravados usando a biblioteca de clientes de Ruby.
- [Exemplo: Introdução ao Armazenamento do Azure em Ruby](https://github.com/Azure-Samples/storage-blob-ruby-getting-started)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a transferir arquivos entre o Armazenamento de Blobs do Azure e um disco local usando o Ruby. Para saber mais sobre como trabalhar com o Armazenamento de Blobs, prossiga para a visão geral sobre a conta de armazenamento.

> [!div class="nextstepaction"]
> [Visão geral da conta de armazenamento](../common/storage-account-overview.md)

Para saber mais sobre o Gerenciador de Armazenamento e os Blobs, confira [Gerenciar recursos do Armazenamento de Blobs do Azure com o Gerenciador de Armazenamento](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
