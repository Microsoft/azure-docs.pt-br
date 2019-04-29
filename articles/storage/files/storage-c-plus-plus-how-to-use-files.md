---
title: Desenvolvimento para o Arquivos do Azure com C++ | Microsoft Docs
description: Saiba como desenvolver aplicativos e serviços C++ que usam o Arquivos do Azure para armazenar dados de arquivo.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 424db4435c569475f9463894b5364fc22190689a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766396"
---
# <a name="develop-for-azure-files-with-c"></a>Desenvolvimento para o Arquivos do Azure com C++

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>Sobre este tutorial

Neste tutorial, você aprenderá a executar operações básicas no Arquivos do Azure. Por meio de exemplos escritos em C++, você aprenderá a criar compartilhamentos e diretórios, carregar, listar e excluir arquivos. Se você for novo no Arquivos do Azure, será muito útil percorrer os conceitos nas seções a seguir para entender os exemplos.

* Criar e excluir Compartilhamentos de Arquivos do Azure
* Criar e excluir diretórios
* Enumerar arquivos e diretórios em um Compartilhamento de Arquivos do Azure
* Carregar, baixar e excluir um arquivo
* Definir a cota (tamanho máximo) para um compartilhamento de arquivos do Azure
* Crie uma assinatura de acesso compartilhado (chave SAS) para um arquivo que usa uma política de acesso compartilhado definida no compartilhamento.

> [!Note]  
> Como o Arquivos do Azure pode ser acessado via SMB, é possível criar aplicativos simples que acessam o Compartilhamento de Arquivos do Azure usando as classes e funções padrão de E/S do C++. Este artigo descreverá como criar aplicativos que usam o SDK do Armazenamento do Azure C++, que usa a [API REST de Arquivo](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) para se comunicar com o Arquivos do Azure.

## <a name="create-a-c-application"></a>Criar um aplicativo em C++

Para criar os exemplos, você precisará instalar a Biblioteca do Cliente de Armazenamento do Azure 2.4.0 para C++. Você também deverá ter criado uma conta de armazenamento do Azure.

Para instalar o Cliente de Armazenamento do Azure 2.4.0 para C++, você poderá usar os seguintes métodos:

* **Linux:** Siga as instruções dadas na página README da [Biblioteca do Cliente de Armazenamento do Microsoft Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).
* **Windows:** No Visual Studio, clique em **Ferramentas &gt; Gerenciador de Pacotes NuGet &gt; Console do Gerenciador de Pacotes**. Digite o seguinte comando no console do [Gerenciador de Pacotes do NuGet](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) e pressione **ENTER**.
  

```powershell
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar seu aplicativo para usar os Arquivos do Azure

Adicione as seguintes instruções include na parte superior do arquivo de origem C++ no qual você deseja manipular Arquivos do Azure:

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de conexão de armazenamento do Azure

Para usar o armazenamento de arquivos, será necessário se conectar à sua conta de armazenamento do Azure. A primeira etapa é configurar uma cadeia de conexão que usaremos para nos conectar à sua conta de armazenamento. Vamos definir uma variável estática para fazer isso.

```cpp
// Define the connection-string with your values.
const utility::string_t
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Conectar-se a uma conta de armazenamento do Azure

É possível usar a classe **cloud_storage_account** para representar as informações da conta de armazenamento. Para recuperar as informações da conta de armazenamento na cadeia de conexão de armazenamento, você pode usar o método **Analisar** .

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure

Todos os arquivos e diretórios em um compartilhamento de Arquivos do Azure residem em um contêiner chamado **Compartilhamento**. Sua conta de armazenamento pode a quantidade de compartilhamentos que a capacidade da conta permitir. Para obter acesso a um compartilhamento e seu conteúdo, é necessário usar um cliente de Arquivos do Azure.

```cpp
// Create the Azure Files client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();
```

Usando o cliente do Arquivos do Azure, será possível obter uma referência a um compartilhamento.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

Para criar o compartilhamento, use o método **create_if_not_exists** do objeto **cloud_file_share**.

```cpp
if (share.create_if_not_exists()) {
    std::wcout << U("New share created") << std::endl;
}
```

Neste ponto, **compartilhar** contém uma referência a um compartilhamento chamado **my-sample-share**.

## <a name="delete-an-azure-file-share"></a>Excluir um Compartilhamento de Arquivos do Azure

Para excluir um compartilhamento deve-se chamar o método **delete_if_exists** em um objeto cloud_file_share. O código fornecido a seguir é um exemplo de como fazer isso.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>Criar um diretório

Também é possível organizar o armazenamento colocando arquivos em subdiretórios em vez de manter todos eles no diretório raiz. Os Arquivos do Azure permitem que você crie quantos diretórios a conta permitir. O código a seguir criará um diretório chamado **my-sample-directory** no diretório raiz, bem como um subdiretório nomeado **my-sample-subdirectory**.

```cpp
// Retrieve a reference to a directory
azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Return value is true if the share did not exist and was successfully created.
directory.create_if_not_exists();

// Create a subdirectory.
azure::storage::cloud_file_directory subdirectory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
subdirectory.create_if_not_exists();
```

## <a name="delete-a-directory"></a>Excluir um diretório

Excluir um diretório é uma tarefa simples, embora deva-se observar que não é possível excluir um diretório que ainda contenha arquivos ou outros diretórios.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// Get a reference to the directory.
azure::storage::cloud_file_directory directory =
  share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Get a reference to the subdirectory you want to delete.
azure::storage::cloud_file_directory sub_directory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));

// Delete the subdirectory and the sample directory.
sub_directory.delete_directory_if_exists();

directory.delete_directory_if_exists();
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar arquivos e diretórios em um Compartilhamento de Arquivos do Azure

A lista de arquivos e diretórios em um compartilhamento pode ser obtida facilmente chamando **list_files_and_directories** em uma referência **cloud_file_directory**. Para acessar o conjunto avançado de propriedades e métodos para um **list_file_and_directory_item** retornado, você deverá chamar o método **list_file_and_directory_item.as_file** a fim de obter um objeto **cloud_file** ou o método **list_file_and_directory_item.as_directory** a fim de obter um objeto **cloud_file_directory**.

O código a seguir demonstra como recuperar e apresentar a saída do URI de cada item no diretório raiz do compartilhamento.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

// Output URI of each item.
azure::storage::list_file_and_directory_result_iterator end_of_results;

for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
{
    if(it->is_directory())
    {
        ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
    else if (it->is_file())
    {
        ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
    }
}
```

## <a name="upload-a-file"></a>Carregar um arquivo

No mínimo, um compartilhamento de Arquivos do Azure contém um diretório raiz no qual os arquivos podem residir. Nesta seção, você aprenderá a carregar um arquivo do armazenamento local para o diretório raiz de um compartilhamento.

A primeira etapa do carregamento de um arquivo é obter uma referência para o diretório onde ele deve residir. Para isso, você deve chamar o método **get_root_directory_reference** do objeto de compartilhamento.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

Agora que você tem uma referência para o diretório raiz do compartilhamento, pode carregar um arquivo nele. Este exemplo carrega de um arquivo, de um texto e de um fluxo.

```cpp
// Upload a file from a stream.
concurrency::streams::istream input_stream =
  concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

azure::storage::cloud_file file1 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
file1.upload_from_stream(input_stream);

// Upload some files from text.
azure::storage::cloud_file file2 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
file2.upload_text(_XPLATSTR("more text"));

// Upload a file from a file.
azure::storage::cloud_file file4 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
file4.upload_from_file(_XPLATSTR("DataFile.txt"));
```

## <a name="download-a-file"></a>Baixar um arquivo

Para baixar arquivos, primeiro recupere uma referência de arquivo e chame o método **download_to_stream** a fim de transferir o conteúdo do arquivo para um objeto de fluxo, que você pode persistir em um arquivo local. Como alternativa, você pode usar o método **download_to_file** para baixar o conteúdo de um arquivo em um arquivo local. Você pode usar o método **download_text** para baixar o conteúdo de um arquivo como uma cadeia de texto.

O exemplo a seguir usa os métodos **download_to_stream** e **download_text** para demonstrar o download dos arquivos, que foram criados nas seções anteriores.

```cpp
// Download as text
azure::storage::cloud_file text_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
utility::string_t text = text_file.download_text();
ucout << "File Text: " << text << std::endl;

// Download as a stream.
azure::storage::cloud_file stream_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
stream_file.download_to_stream(output_stream);
std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();
outfile.write((char *)&data[0], buffer.size());
outfile.close();
```

## <a name="delete-a-file"></a>Excluir um arquivo

Outra operação comum do Arquivos do Azure é a exclusão de arquivos. O código a seguir exclui um arquivo chamado my-sample-file-3 armazenado no diretório raiz.

```cpp
// Get a reference to the root directory for the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

azure::storage::cloud_file file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

file.delete_file_if_exists();
```

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Definir a cota (tamanho máximo) para um compartilhamento de arquivos do Azure

Você pode definir a cota (ou o tamanho máximo) de um compartilhamento de arquivo em gigabytes. Você também pode verificar a quantidade de dados atualmente armazenada no compartilhamento.

Ao definir a cota para um compartilhamento, você pode limitar o tamanho total dos arquivos armazenados no compartilhamento. Se o tamanho total dos arquivos no compartilhamento ultrapassar a cota definida no compartilhamento, os clientes não poderão aumentar o tamanho dos arquivos existentes ou criar novos arquivos, a menos que eles estejam vazios.

O exemplo a seguir mostra como verificar o uso atual de um compartilhamento e como definir a cota para o compartilhamento.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
if (share.exists())
{
    std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();

    //This line sets the quota to 2560GB
    share.resize(2560);

    std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();

}
```

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Gerar uma assinatura de acesso compartilhado para um arquivo ou compartilhamento de arquivos

Você pode gerar uma SAS (assinatura de acesso compartilhado) para um compartilhamento de arquivo ou para um arquivo individual. Você também pode criar uma política de acesso compartilhado em um compartilhamento de arquivos para gerenciar assinaturas de acesso compartilhado. Recomendamos a criação de uma política de acesso compartilhado, pois ela fornece um meio de revogar o SAS se ele for comprometido.

O exemplo a seguir cria uma política de acesso compartilhado em um compartilhamento e, em seguida, usa essa política para fornecer as restrições a um SAS em um arquivo no compartilhamento.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client and get a reference to the share
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

if (share.exists())
{
    // Create and assign a policy
    utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

    azure::storage::file_shared_access_policy sharedPolicy =
      azure::storage::file_shared_access_policy();

    //set permissions to expire in 90 minutes
    sharedPolicy.set_expiry(utility::datetime::utc_now() +
       utility::datetime::from_minutes(90));

    //give read and write permissions
    sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

    //set permissions for the share
    azure::storage::file_share_permissions permissions;

    //retrieve the current list of shared access policies
    azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;

    //add the new shared policy
    policies.insert(std::make_pair(policy_name, sharedPolicy));

    //save the updated policy list
    permissions.set_policies(policies);
    share.upload_permissions(permissions);

    //Retrieve the root directory and file references
    azure::storage::cloud_file_directory root_dir =
        share.get_root_directory_reference();
    azure::storage::cloud_file file =
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

    // Generate a SAS for a file in the share
    //  and associate this access policy with it.
    utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
    utility::string_t text = _XPLATSTR("My sample content");
    file_with_sas.upload_text(text);

    //Download and print URL with SAS.
    utility::string_t downloaded_text = file_with_sas.download_text();
    ucout << downloaded_text << std::endl;
    ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;

}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Armazenamento do Azure, explore estes recursos:

* [Biblioteca do Cliente de Armazenamento para C++](https://github.com/Azure/azure-storage-cpp)
* [Exemplos do Serviço de Arquivo de Armazenamento do Microsoft Azure em C++](https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Gerenciador de Armazenamento do Azure](https://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)