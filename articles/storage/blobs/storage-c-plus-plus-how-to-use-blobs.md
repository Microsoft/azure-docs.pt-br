---
title: Como usar o armazenamento de objeto (Blob) do C++ - Azure | Microsoft Docs
description: Saiba como armazenar dados não estruturados (blobs) na nuvem com o armazenamento de blobs do Azure (objeto) usando o C++.
author: twooley
ms.author: twooley
ms.date: 07/16/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 61c7dca25e90450c695a5137dd2ee900c1282bf0
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278041"
---
# <a name="how-to-use-blob-storage-from-c"></a>Como usar o armazenamento de Blobs do C++

Este guia demonstra como executar cenários comuns usando o armazenamento de Blobs do Azure. Os exemplos mostram como carregar, listar, fazer o download e excluir blobs. Os exemplos são escritos em C++ e usam a [Biblioteca do Cliente de Armazenamento do Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).
Para saber mais sobre armazenamento de Blobs, consulte [Introdução ao armazenamento de Blobs do Azure](storage-blobs-introduction.md).

> [!NOTE]
> Este guia tem como alvo a Biblioteca do Cliente de Armazenamento do Azure para C++, versão 1.0.0 e superior. A Microsoft recomenda o uso da última versão da Biblioteca de Clientes de Armazenamento para C++, disponível por meio de [NuGet](https://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Criar um aplicativo em C++
Neste guia, você usará os recursos de armazenamento que podem ser executados em um aplicativo C++.

Para isso, você precisará instalar a Biblioteca do Cliente de Armazenamento do Azure para C++ e criar uma conta de armazenamento do Azure em sua assinatura do Azure.

Para instalar a Biblioteca do Cliente de Armazenamento do Azure para C++, você pode usar os seguintes métodos:

- **Linux:** Siga as instruções fornecidas na página [LEIAME da Biblioteca de Clientes do Armazenamento do Microsoft Azure para C++: Introdução ao Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux).
- **Windows:** No Windows, use [vcpkg](https://github.com/microsoft/vcpkg) como o gerenciador de dependências. Siga o [início rápido](https://github.com/microsoft/vcpkg#quick-start) para inicializar o vcpkg. Em seguir, use este comando para instalar a biblioteca:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Você pode encontrar um guia de criação do código-fonte e exportá-lo para o NuGet no arquivo [LEIAME](https://github.com/Azure/azure-storage-cpp#download--install).

## <a name="configure-your-application-to-access-blob-storage"></a>Configurar seu aplicativo para acessar o armazenamento de blobs
Adicione as seguintes instruções include à parte superior do arquivo C++ no qual deseja usar as APIs de armazenamento do Azure para acessar os blobs:

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Configurar uma cadeia de conexão de armazenamento do Azure
Um cliente de armazenamento do Azure usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais para acessar serviços de gerenciamento de dados. Ao executar em um aplicativo cliente, é necessário informar a cadeia de conexão de armazenamento no formato a seguir, usando o nome e a chave de acesso da sua conta de armazenamento listada no [portal do Azure](https://portal.azure.com) nos valores *AccountName* e *AccountKey*. Para obter informações sobre as contas de armazenamento e as chaves de acesso, consulte [Sobre as Contas de Armazenamento do Azure](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Este exemplo mostra como você pode declarar um campo estático para armazenar a cadeia de conexão:

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Para testar um aplicativo no computador local do Windows, você pode usar o [emulador de armazenamento do Azure](../common/storage-use-azurite.md). O Azurite é um utilitário que simula os serviços de blob e de fila disponíveis no Azure no computador local de desenvolvimento. Este exemplo mostra como você pode declarar um campo estático para manter a cadeia de conexão em seu emulador de armazenamento local:

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));
```

Para iniciar o Azurite, confira [Usar o emulador Azurite para o desenvolvimento local do Armazenamento do Azure](../common/storage-use-azurite.md).

Os exemplos abaixo pressupõem que você usou um desses dois métodos para obter a cadeia de conexão do armazenamento.

## <a name="retrieve-your-storage-account"></a>Recupere sua conta de armazenamento
É possível usar a classe **cloud_storage_account** para representar as informações da conta de armazenamento. Para recuperar as informações da conta de armazenamento na cadeia de conexão de armazenamento, você pode usar o método **Analisar** .

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Em seguida, obtenha uma referência a uma classe **cloud_blob_client**, pois ela permite recuperar objetos que representam contêineres e blobs armazenados no armazenamento de Blobs. O código a seguir cria um objeto **cloud_blob_client** usando o objeto da conta de armazenamento que recuperamos acima:

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Como criar um contêiner
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Este exemplo mostra como criar um contêiner caso ele ainda não exista:

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

Por padrão, o novo contêiner é privado e você deve especificar sua chave de acesso do armazenamento para baixar blobs desse contêiner. Se você quiser disponibilizar os arquivos (blobs) dentro do contêiner para todas as pessoas, poderá definir o contêiner como público usando o seguinte código:

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Qualquer pessoa na Internet pode ver blobs em um contêiner público, mas você só pode modificar ou excluí-los se tiver a chave de acesso apropriada.

## <a name="how-to-upload-a-blob-into-a-container"></a>Como: carregar um blob em um contêiner
O Armazenamento de Blob do Azure oferece suporte a blobs de blocos e a blobs de páginas. Na maioria dos casos, o blob de blocos é o tipo recomendado a ser usado.

Para carregar um arquivo em um blob de blocos, obtenha uma referência de contêiner e use-a para obter uma referência de blob de blocos. Depois de ter uma referência do blob, você pode carregar qualquer fluxo de dados para ele chamando o método **upload_from_stream**. Essa operação criará o blob, caso ele não exista, ou o substituirá, caso ele já exista. O exemplo a seguir mostra como carregar um blob em um contêiner e pressupõe que o contêiner já tenha sido criado.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

Como alternativa, você pode usar o método **upload_from_file** para carregar um arquivo em um blob de blocos.

## <a name="how-to-list-the-blobs-in-a-container"></a>Como: listar os blobs em um contêiner
Para listar blobs em um contêiner, primeiro obtenha uma referência ao contêiner. Você pode usar o método **list_blobs** do contêiner para recuperar os blobs e/ou diretórios dentro dele. Para acessar o conjunto avançado de propriedades e métodos para um **list_blob_item** retornado, você deve chamar o método **list_blob_item.as_blob** para obter um objeto **cloud_blob** ou o método **list_blob.as_directory** para obter um objeto cloud_blob_directory. O código a seguir demonstra como recuperar e apresentar a URI de cada item no contêiner **my-sample-container** :

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

Para obter mais detalhes sobre a lista de operações, consulte [Listar recursos de Armazenamento do Azure em C++](../common/storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Como: baixar blobs
Para baixar blobs, primeiro recupere uma referência do blob, em seguida, chame o método **download_to_stream**. O exemplo a seguir usa o método **download_to_stream** para transferir o conteúdo do blob para um objeto de fluxo que você pode persistir em um arquivo local.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

Como alternativa, você pode usar o método **download_to_file** para baixar o conteúdo de um blob em um arquivo.
Você também pode usar o método **download_text** para baixar o conteúdo de um blob como uma cadeia de texto.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>Como: excluir blobs
Para excluir um blob, primeiro obtenha uma referência do blob e, em seguida, chame o método **delete_blob**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu os conceitos básicos do armazenamento de blobs, siga estes links para saber mais sobre o armazenamento do Azure.

- [Como usar o armazenamento de filas do C++](../queues/storage-c-plus-plus-how-to-use-queues.md)
- [Como usar o Armazenamento de Tabelas do C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [Listar recursos de Armazenamento do Azure em C++](../common/storage-c-plus-plus-enumeration.md)
- [Referência da Biblioteca de Cliente de Armazenamento para C++](https://azure.github.io/azure-storage-cpp)
- [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Transferir dados com o utilitário de linha de comando AzCopy](../common/storage-use-azcopy-v10.md)