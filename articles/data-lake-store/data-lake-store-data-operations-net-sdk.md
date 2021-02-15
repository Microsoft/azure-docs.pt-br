---
title: SDK do .NET – operações do sistema de arquivos no Data Lake Storage Gen1-Azure
description: Use o SDK do .NET Azure Data Lake Storage Gen1 para operações de sistema de arquivos em Data Lake Storage Gen1 como criar pastas etc.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/03/2020
ms.author: twooley
ms.custom: devx-track-dotnet
ms.openlocfilehash: cb6d5fafa8d956c296124c5a20f8a087160961da
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109231"
---
# <a name="filesystem-operations-on-data-lake-storage-gen1-using-the-net-sdk"></a>Operações do sistema de arquivos em Data Lake Storage Gen1 usando o SDK do .NET

> [!div class="op_single_selector"]
> * [SDK .NET](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

Neste artigo, você aprenderá a executar operações de sistema de arquivos em Data Lake Storage Gen1 usando o SDK do .NET. As operações de filesytem incluem a criação de pastas em uma conta do Data Lake Storage Gen1, carregamento de arquivos, download de arquivos etc.

Para obter instruções sobre como fazer operações de gerenciamento de conta no Data Lake Storage Gen1 usando o SDK do .NET, consulte [operações de gerenciamento de conta no data Lake Storage Gen1 usando o SDK do .net](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Visual Studio 2013 ou superior**. As instruções neste artigo usam o Visual Studio 2019.

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure data Lake Storage Gen1 conta**. Para obter instruções sobre como criar uma conta, consulte Introdução [ao Azure data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="create-a-net-application"></a>Criar um aplicativo .NET

O exemplo de código disponível [no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) explica o processo de criação de arquivos no armazenamento, concatenação de arquivos, download de um arquivo e exclusão de alguns arquivos no armazenamento. Esta seção do artigo percorre com você as principais partes do código.

1. No Visual Studio, selecione o menu **arquivo** , **novo**e **projeto**.
1. Escolha **aplicativo de console (.NET Framework)** e, em seguida, selecione **Avançar**.
1. Em **Nome do projeto**, digite `CreateADLApplication` e, em seguida, selecione **Criar**.
1. Adicione os pacotes NuGet ao seu projeto.

   1. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**.
   1. Na guia **Gerenciador de pacotes NuGet** , verifique se a **origem do pacote** está definida como **NuGet.org**. Além disso, verifique se a caixa de seleção **incluir pré-lançamento** está marcada.
   1. Procure e instale os seguintes pacotes NuGet:

      * `Microsoft.Azure.DataLake.Store` -Este artigo usa a versão v 1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` -Este artigo usa a v 2.3.1.

      Feche o **Gerenciador de pacotes NuGet**.

1. Abra **Program.cs**, exclua o código existente e inclua as instruções a seguir para adicionar referências aos namespaces.

    ```
    using System;
    using System.IO;using System.Threading;
    using System.Linq;
    using System.Text;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you're using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.DataLake.Store;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Declare as variáveis, conforme mostrado abaixo, e forneça os valores para os espaços reservados. Além disso, o caminho local e o nome de arquivo fornecido aqui existem no computador.

    ```
    namespace SdkSample
    {
        class Program
        {
            private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";
        }
    }
    ```

Nas seções restantes do artigo, você pode ver como usar os métodos .NET disponíveis para realizar operações como autenticação, carregamento de arquivo, etc.

## <a name="authentication"></a>Autenticação

* Para autenticação do usuário final para seu aplicativo, veja [Autenticação de usuário final com o Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-end-user-authenticate-net-sdk.md).
* Para autenticação de serviço a serviço do aplicativo, confira [Autenticação de serviço a serviço com o Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Criar objeto de cliente

O snippet de código a seguir cria o objeto de cliente de sistema de arquivos do Data Lake Storage Gen1, que é usado para emitir solicitações para o serviço.

```
// Create client objects
AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);
```

## <a name="create-a-file-and-directory"></a>Criar um arquivo e diretório

Adicione o snippet de código a seguir ao seu aplicativo. Este trecho de código adiciona um arquivo e qualquer diretório pai que não existe.

```
// Create a file - automatically creates any parent directories that don't exist
// The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store

using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);

    textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="append-to-a-file"></a>Anexar a um arquivo

O snippet de código a seguir acrescenta dados a um arquivo existente na conta do Data Lake Storage Gen1.

```
// Append to existing file

using (var stream = client.GetAppendStream(fileName))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="read-a-file"></a>Ler um arquivo

O snippet de código a seguir lê o conteúdo de um arquivo na conta do Data Lake Storage Gen1.

```
//Read file contents

using (var readStream = new StreamReader(client.GetReadStream(fileName)))
{
    string line;
    while ((line = readStream.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

## <a name="get-file-properties"></a>Obter propriedades do arquivo

O snippet de código a seguir retorna as propriedades associadas a um arquivo ou diretório.

```
// Get file properties
var directoryEntry = client.GetDirectoryEntry(fileName);
PrintDirectoryEntry(directoryEntry);
```

A definição do `PrintDirectoryEntry` método está disponível como parte do exemplo [no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="rename-a-file"></a>Renomear um arquivo

O snippet de código a seguir renomeia um arquivo existente em uma conta do Data Lake Storage Gen1.

```
// Rename a file
string destFilePath = "/Test/testRenameDest3.txt";
client.Rename(fileName, destFilePath, true);
```

## <a name="enumerate-a-directory"></a>Enumerar um diretório

O trecho a seguir enumera os diretórios em uma conta de Data Lake Storage Gen1.

```
// Enumerate directory
foreach (var entry in client.EnumerateDirectory("/Test"))
{
    PrintDirectoryEntry(entry);
}
```

A definição do `PrintDirectoryEntry` método está disponível como parte do exemplo [no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Excluir diretórios recursivamente

O trecho a seguir exclui um diretório e todos os seus subdiretórios, recursivamente.

```
// Delete a directory and all its subdirectories and files
client.DeleteRecursive("/Test");
```

## <a name="samples"></a>Exemplos

Aqui estão alguns exemplos que mostram como usar o SDK do sistema de arquivos Data Lake Storage Gen1.

* [Exemplo básico no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Exemplo avançado no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Veja também

* [Operações de gerenciamento de conta no Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
* [Referência de SDK do .NET do Data Lake Storage Gen1](/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Próximas etapas

* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)