---
title: Autenticação do usuário final-Java com Data Lake Storage Gen1-Azure
description: Saiba como obter autenticação de usuário final com Azure Data Lake Storage Gen1 usando Azure Active Directory com Java
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: ece996f7edb8d37cea47756c5b7e635e02409b35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318854"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Autenticação de usuário final com Azure Data Lake Storage Gen1 usando Java
> [!div class="op_single_selector"]
> * [Usando Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Usando o SDK .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Usando Python](data-lake-store-end-user-authenticate-python.md)
> * [Usando a API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

Neste artigo, você aprende como usar o SDK do Java para fazer autenticação de usuário final com Azure Data Lake Storage Gen1. Para autenticação de serviço a serviço com Data Lake Storage Gen1 usando SDK do Java, consulte [Autenticação de serviço a serviço com o Data Lake Storage Gen1 usando Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar um aplicativo "Nativo" do Azure Active Directory**. Você deve ter concluído as etapas em [Autenticação de usuário final com Data Lake Storage Gen1 usando Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Este tutorial usa o Maven para compilação e dependências de projeto. Embora seja possível compilar sem usar um sistema de compilação como Maven ou Gradle, com esses sistemas é muito mais fácil gerenciar dependências.

* (Opcional) E IDE como [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) ou [Eclipse](https://www.eclipse.org/downloads/) ou semelhante.

## <a name="end-user-authentication"></a>Autenticação do usuário final
1. Crie um projeto do Maven usando [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) na linha de comando ou usando um IDE. Para obter instruções sobre como criar um projeto Java usando IntelliJ, veja [aqui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para obter instruções sobre como criar um projeto usando o Eclipse, veja [aqui](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Adicione as dependências a seguir para o arquivo **pom.xml** do Maven. Adicione o seguinte trecho antes da **\</project>** marca:
   
    ```xml
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-data-lake-store-sdk</artifactId>
        <version>2.2.3</version>
      </dependency>
      <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.21</version>
      </dependency>
    </dependencies>
    ```
   
    A primeira dependência é usar o SDK do Data Lake Storage Gen1 (`azure-data-lake-store-sdk`) do repositório maven. A segunda dependência serve para especificar qual estrutura de registros (`slf4j-nop`) usar para o aplicativo. O SDK do Data Lake Storage Gen1 usa a fachada de log [SLF4J](https://www.slf4j.org/) , que permite que você escolha entre várias estruturas de log populares, como Log4J, log Java, Logback, etc., ou nenhum registro em log. Para este exemplo, desabilitamos o registro em log, por isso, usamos a associação **slf4j-nop**. Para usar outras opções de log em seu aplicativo, veja [aqui](https://www.slf4j.org/manual.html#projectDep).

3. Adicione as seguintes instruções import ao seu aplicativo.

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;
    ```

4. Use o snippet a seguir em seu aplicativo do Java para obter o token para o aplicativo nativo do Active Directory que foi criado anteriormente usando o `DeviceCodeTokenProvider`. Substitua **FILL-IN-HERE** pelos valores reais do aplicativo nativo do Azure Active Directory.

    ```java
    private static String nativeAppId = "FILL-IN-HERE";
            
    AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   
    ```

O SDK do Data Lake armazenamento Gen1 fornece métodos convenientes que permitem que você gerencie os tokens de segurança necessários para se comunicar com a conta do Data Lake armazenamento Gen1. No entanto, o SDK não exige que apenas esses métodos sejam usados. Você pode usar qualquer outro meio de obter o token, como usar o [SDK do Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) ou seu próprio código personalizado.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como usar a autenticação de usuário final para autenticar com o Azure Data Lake Storage Gen1 usando SDK do Java. Agora você pode consultar os artigos a seguir que descrevem como usar o SDK do Java para trabalhar com o Azure Data Lake Storage Gen1.

* [Operações de dados no Data Lake Storage Gen1 usando o Java SDK](data-lake-store-get-started-java-sdk.md)


