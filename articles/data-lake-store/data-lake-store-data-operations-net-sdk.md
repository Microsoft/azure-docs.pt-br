---
title: 'SDK do .NET: operações de sistema de arquivos no Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Use o SDK do .NET para do Azure Data Lake Storage Gen1 para executar operações de sistema de arquivos do Data Lake Storage Gen1, tais como criar pastas, etc.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 02091f1b650e3e9932f9924bf36a5841861d3b1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878844"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Operações de sistema de arquivos no Azure Data Lake Storage Gen1 usando o SDK do .NET
> [!div class="op_single_selector"]
> * [SDK .NET](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

Neste artigo, você aprenderá a executar operações do sistema de arquivos no Data Lake Storage Gen1 usando o SDK do .NET. As operações de filesytem incluem a criação de pastas em uma conta do Data Lake Storage Gen1, carregamento de arquivos, download de arquivos etc.

Para obter instruções sobre como executar operações de gerenciamento de conta no Data Lake Storage Gen1 usando o SDK do .NET, veja [Operações de gerenciamento de conta no Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2013, 2015 ou 2017**. As instruções abaixo usam o Visual Studio 2017.

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta do Azure Data Lake Storage Gen1**. Para obter instruções sobre como criar uma conta, confira [Introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="create-a-net-application"></a>Criar um aplicativo .NET
O exemplo de código disponível [no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) explica o processo de criação de arquivos no armazenamento, concatenação de arquivos, download de um arquivo e exclusão de alguns arquivos no armazenamento. Esta seção do artigo percorre com você as principais partes do código.

1. Abra o Visual Studio e crie um aplicativo de console.
2. No menu **Arquivo**, clique em **Novo** e em **Projeto**.
3. Em **Novo Projeto**, digite ou selecione os seguintes valores:

   | Propriedade | Value |
   | --- | --- |
   | Category |Modelos/Visual C#/Windows |
   | Modelo |Aplicativo de console |
   | NOME |CreateADLApplication |

4. Clique em **OK** para criar o projeto.

5. Adicione os pacotes NuGet ao seu projeto.

   1. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**.
   2. Na guia **Gerenciador de Pacotes NuGet**, verifique se a **Origem do pacote** está definida como **nuget.org** e se a caixa de seleção **Incluir pré-lançamento** está marcada.
   3. Procure e instale os seguintes pacotes NuGet:

      * `Microsoft.Azure.DataLake.Store` - Este tutorial usa a versão v1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Este tutorial usa a versão v2.3.1.
    
      Feche o **Gerenciador de Pacotes NuGet**.

6. Abra **Program.cs**, exclua o código existente e inclua as instruções a seguir para adicionar referências aos namespaces.

        using System;
        using System.IO;using System.Threading;
        using System.Linq;
        using System.Text;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.DataLake.Store;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. Declare as variáveis, conforme mostrado abaixo, e forneça os valores para os espaços reservados. Além disso, o caminho local e o nome de arquivo fornecido aqui existem no computador.

        namespace SdkSample
        {
            class Program
            {
                private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";        
            }
        }

Nas seções restantes do artigo, você pode ver como usar o métodos do .NET disponíveis para executar operações como autenticação, carregamento de arquivos, etc.

## <a name="authentication"></a>Authentication

* Para autenticação do usuário final para seu aplicativo, veja [Autenticação de usuário final com o Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-end-user-authenticate-net-sdk.md).
* Para autenticação de serviço a serviço do aplicativo, confira [Autenticação de serviço a serviço com o Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-object"></a>Criar objeto de cliente
O snippet de código a seguir cria o objeto de cliente de sistema de arquivos do Data Lake Storage Gen1, que é usado para emitir solicitações para o serviço.

    // Create client objects
    AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);

## <a name="create-a-file-and-directory"></a>Criar um arquivo e diretório
Adicione o snippet de código a seguir ao seu aplicativo. Este snippet de código adiciona um arquivo, bem como qualquer diretório pai que não exista.

    // Create a file - automatically creates any parent directories that don't exist
    // The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store
    using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);

        textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="append-to-a-file"></a>Anexar a um arquivo
O snippet de código a seguir acrescenta dados a um arquivo existente na conta do Data Lake Storage Gen1.

    // Append to existing file
    using (var stream = client.GetAppendStream(fileName))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="read-a-file"></a>Ler um arquivo
O snippet de código a seguir lê o conteúdo de um arquivo na conta do Data Lake Storage Gen1.

    //Read file contents
    using (var readStream = new StreamReader(client.GetReadStream(fileName)))
    {
        string line;
        while ((line = readStream.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }

## <a name="get-file-properties"></a>Obter propriedades de arquivo
O snippet de código a seguir retorna as propriedades associadas a um arquivo ou diretório.

    // Get file properties
    var directoryEntry = client.GetDirectoryEntry(fileName);
    PrintDirectoryEntry(directoryEntry);

A definição do método `PrintDirectoryEntry` está disponível como parte do exemplo [no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted). 

## <a name="rename-a-file"></a>Renomear um arquivo
O snippet de código a seguir renomeia um arquivo existente em uma conta do Data Lake Storage Gen1.

    // Rename a file
    string destFilePath = "/Test/testRenameDest3.txt";
    client.Rename(fileName, destFilePath, true);

## <a name="enumerate-a-directory"></a>Enumerar um diretório
O snippet de código a seguir enumera os diretórios em uma conta do Data Lake Storage Gen1

    // Enumerate directory
    foreach (var entry in client.EnumerateDirectory("/Test"))
    {
        PrintDirectoryEntry(entry);
    }

A definição do método `PrintDirectoryEntry` está disponível como parte do exemplo [no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Excluir diretórios recursivamente
O snippet de código a seguir exclui um diretório e todos os seus subdiretórios, recursivamente.

    // Delete a directory and all its subdirectories and files
    client.DeleteRecursive("/Test");

## <a name="samples"></a>Exemplos
Aqui estão alguns exemplos sobre como usar o SDK do sistema de arquivos do Data Lake Storage Gen1.
* [Exemplo básico no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Exemplo avançado no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Consulte também
* [Operações de gerenciamento de conta no Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
* [Referência de SDK do .NET do Data Lake Storage Gen1](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Próximas etapas
* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)
