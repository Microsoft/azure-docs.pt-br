---
title: "SDK do Java: operações de filesystem no Azure Data Lake Store | Microsoft Docs"
description: "Use o SDK do Java para Azure Data Lake Store para executar operações de filesystem no Data Lake Store, como criar pastas etc."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/29/2017
ms.author: nitinme
ms.openlocfilehash: e8c7b788061b3eb18b3e6c282339a03d93ab8b1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="filesystem-operations-on-data-lake-store-using-java-sdk"></a>Operações de filesystem no Data Lake Store usando o SDK do Java
> [!div class="op_single_selector"]
> * [SDK .NET](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Saiba como usar o SDK Java para o Azure Data Lake Store a fim de executar operações básicas, como criar pastas, carregar e baixar arquivos de dados etc. Para obter mais informações sobre o Data Lake, veja [Azure Data Lake Store](data-lake-store-overview.md).

Você pode acessar os documentos de API do Java SDK do Azure Data Lake Store em [Documentos de API Java do Azure Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Pré-requisitos
* Java Development Kit (JDK 7 ou superior, usando Java versão 1.7 ou superior)
* Conta do Azure Data Lake Store. Siga as instruções em [Introdução ao Azure Data Lake Store usando o portal do Azure](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Este tutorial usa o Maven para compilação e dependências de projeto. Embora seja possível compilar sem usar um sistema de compilação como Maven ou Gradle, com esses sistemas é muito mais fácil gerenciar dependências.
* (Opcional) E IDE como [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) ou [Eclipse](https://www.eclipse.org/downloads/) ou semelhante.

## <a name="create-a-java-application"></a>Criar um aplicativo Java
O exemplo de código disponível [no GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) explica o processo de criação de arquivos no armazenamento, concatenação de arquivos, download de um arquivo e exclusão de alguns arquivos no armazenamento. Esta seção do artigo percorre com você as principais partes do código.

1. Crie um projeto do Maven usando [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) na linha de comando ou usando um IDE. Para obter instruções sobre como criar um projeto Java usando IntelliJ, veja [aqui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para obter instruções sobre como criar um projeto usando o Eclipse, veja [aqui](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Adicione as dependências a seguir para o arquivo **pom.xml** do Maven. Adicione o trecho a seguir antes da marca **\</project>**:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.5</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    A primeira dependência é usar o SDK do Data Lake Store (`azure-data-lake-store-sdk`) do repositório do maven. A segunda dependência serve para especificar qual estrutura de registros (`slf4j-nop`) usar para o aplicativo. O SDK do Data Lake Store usa a fachada de registro em log [slf4j](http://www.slf4j.org/), que permite que você escolha entre uma série de estruturas de log populares, como log4j, registro em log Java, logback etc. ou nenhum log. Para este exemplo, desabilitamos o registro em log, por isso, usamos a associação **slf4j-nop**. Para usar outras opções de log em seu aplicativo, veja [aqui](http://www.slf4j.org/manual.html#projectDep).

3. Adicione as seguintes instruções import ao seu aplicativo.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

        import java.io.*;
        import java.util.Arrays;
        import java.util.List;

## <a name="authentication"></a>Autenticação

* Para saber sobre autenticação do usuário final em seu aplicativo, consulte [Autenticação do usuário final com Data Lake Store usando Java](data-lake-store-end-user-authenticate-java-sdk.md).
* Para saber sobre autenticação do usuário final em seu aplicativo, consulte [Autenticação de serviço para serviço com Data Lake Store usando Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="create-an-azure-data-lake-store-client"></a>Criar um cliente do Azure Data Lake Store
A criação de um objeto [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) exige a especificação do nome da conta do Data Lake Store, e o provedor de token gerado durante a autenticação com o Data Lake Store (Confira a seção [Autenticação](#authentication)). O nome da conta do Data Lake Store deve ser um nome de domínio totalmente qualificado. Por exemplo, substitua **FILL-IN-HERE** por algo como **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

Os trechos de código nas seções a seguir contêm exemplos de algumas operações comuns de filesystem. Você pode examinar os [documentos de API do SDK Javado Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/) completos do objeto **ADLStoreClient** para ver outras operações.

## <a name="create-a-directory"></a>Criar um diretório

O trecho a seguir cria uma estrutura de diretórios na raiz da conta do Data Lake Store especificada.

    // create directory
    client.createDirectory("/a/b/w");
    System.out.println("Directory created.");

## <a name="create-a-file"></a>Criar um arquivo

O trecho a seguir cria um arquivo (c.txt) na estrutura de diretório e grava alguns dados no arquivo.

    // create file and write some content
    String filename = "/a/b/c.txt";
    OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
    PrintStream out = new PrintStream(stream);
    for (int i = 1; i <= 10; i++) {
        out.println("This is line #" + i);
        out.format("This is the same line (%d), but using formatted output. %n", i);
    }
    out.close();
    System.out.println("File created.");

Você também pode criar um arquivo (d.txt) usando matrizes de byte.

    // create file using byte arrays
    stream = client.createFile("/a/b/d.txt", IfExists.OVERWRITE);
    byte[] buf = getSampleContent();
    stream.write(buf);
    stream.close();
    System.out.println("File created using byte array.");

A definição para a função `getSampleContent` usada no trecho de código anterior está disponível como parte do exemplo [no GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/). 

## <a name="append-to-a-file"></a>Anexar a um arquivo

O trecho a seguir acrescenta o conteúdo a um arquivo existente.

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();
    System.out.println("File appended.");

A definição para a função `getSampleContent` usada no trecho de código anterior está disponível como parte do exemplo [no GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="read-a-file"></a>Ler um arquivo

O trecho a seguir lê conteúdo de um arquivo na conta do Data Lake Store.

    // Read File
    InputStream in = client.getReadStream(filename);
    BufferedReader reader = new BufferedReader(new InputStreamReader(in));
    String line;
    while ( (line = reader.readLine()) != null) {
        System.out.println(line);
    }
    reader.close();
    System.out.println();
    System.out.println("File contents read.");

## <a name="concatenate-files"></a>Concatenar arquivos

O trecho a seguir concatena dois arquivos em uma conta do Data Lake Store. Se houver êxito, o arquivo concatenado substituirá os dois arquivos existentes.

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);
    System.out.println("Two files concatenated into a new file.");

## <a name="rename-a-file"></a>Renomear um arquivo

O trecho a seguir renomeia um arquivo na conta do Data Lake Store.

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");
    System.out.println("New file renamed.");

## <a name="get-metadata-for-a-file"></a>Obter metadados de um arquivo

O trecho a seguir recupera os metadados de um arquivo na conta do Data Lake Store.

    // get file metadata
    DirectoryEntry ent = client.getDirectoryEntry(filename);
    printDirectoryInfo(ent);
    System.out.println("File metadata retrieved.");

## <a name="set-permissions-on-a-file"></a>Definir permissões em um arquivo

O trecho a seguir define permissões no arquivo que você criou na seção anterior.

    // set file permission
    client.setPermission(filename, "744");
    System.out.println("File permission set.");

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

O trecho a seguir lista o conteúdo de um diretório, recursivamente.

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }
    System.out.println("Directory contents listed.");

A definição para a função `printDirectoryInfo` usada no trecho de código anterior está disponível como parte do exemplo [no GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="delete-files-and-folders"></a>Excluir arquivos e pastas

O trecho a seguir exclui os arquivos e pastas especificados em uma conta do Data Lake Store, recursivamente.

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");
    System.out.println("All files and folders deleted recursively");
    promptEnterKey();

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo
1. Para executar por meio de um IDE, localize e pressione o botão **Executar**. Para executar por meio do Maven, use [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. Para produzir um jar autônomo que você pode executar na linha de comando, compile o jar com todas as dependências incluídas, usando o [plug-in de assembly do Maven](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). O pom.xml no [código-fonte de exemplo no github](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) tem um exemplo.

## <a name="next-steps"></a>Próximas etapas
* [Explorar o JavaDoc para o Java SDK](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)


