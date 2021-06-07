---
title: Autenticação de serviço a serviço-Data Lake Storage Gen1 – SDK do Java
description: Saiba como conseguir a autenticação de serviço a serviço com o Azure Data Lake Storage Gen1 usando o Active Directory do Azure com Java
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: 0e320557a7372af6a41038d9b3196db23d2496c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000378"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Autenticação serviço a serviço com Gen1 de armazenamento do Azure Data Lake usando o Java

> [!div class="op_single_selector"]
> * [Usando Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Usando o SDK .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Usando Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Usar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

Neste artigo, você aprende sobre como usar o Java SDK para fazer a autenticação de serviço a serviço com o Azure Data Lake Storage Gen1. A autenticação do usuário final com o Data Lake Storage Gen1 usando o Java SDK não é suportada.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar um aplicativo "Web" do Azure Active Directory**. Você deve ter concluído as etapas em [Autenticação de serviço a serviço com Azure Data Lake Storage Gen1 usando Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Este tutorial usa o Maven para compilação e dependências de projeto. Embora seja possível compilar sem usar um sistema de compilação como Maven ou gradle, esses sistemas tornam muito mais fácil gerenciar dependências.

* (Opcional) Como um IDE [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) ou [Eclipse](https://www.eclipse.org/downloads/) ou semelhantes.

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço

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

    A primeira dependência é usar o SDK do Data Lake Storage Gen1 (`azure-data-lake-store-sdk`) do repositório maven. A segunda dependência serve para especificar qual estrutura de registros (`slf4j-nop`) usar para o aplicativo. O SDK do Data Lake Storage Gen1 usa fachada de registros [slf4j](https://www.slf4j.org/), o que permite escolher entre uma série de estruturas de registros populares como log4j, registro do Java, logback, etc., ou nenhum registro. Para este exemplo, desabilitamos o registro em log, por isso, usamos a associação **slf4j-nop**. Para usar outras opções de log em seu aplicativo, veja [aqui](https://www.slf4j.org/manual.html#projectDep).

3. Adicione as seguintes instruções import ao seu aplicativo.

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;
    ```

4. Use o snippet a seguir em seu aplicativo do Java para obter o token para o aplicativo Web do Active Directory criado anteriormente usando uma das subclasses de `AccessTokenProvider` (o exemplo a seguir usa `ClientCredsTokenProvider`). O provedor de token armazena em cache as credenciais usadas para obter o token na memória e renova automaticamente o token se ele está prestes a expirar. É possível criar suas próprias subclasses de `AccessTokenProvider`, obtendo, assim, os tokens por meio do seu código de cliente. Por enquanto, vamos usar a que foi fornecida no SDK.

    Substitua **FILL-IN-HERE** pelos valores reais para o aplicativo Web do Azure Active Directory.

    ```java
    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   
    ```

O SDK do Data Lake armazenamento Gen1 fornece métodos convenientes que permitem que você gerencie os tokens de segurança necessários para se comunicar com a conta do Data Lake armazenamento Gen1. No entanto, o SDK não exige que apenas esses métodos sejam usados. Você pode usar qualquer outro meio de obter o token, como usar o [SDK do Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) ou seu próprio código personalizado.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como usar a autenticação do usuário final para autenticar com o Data Lake Storage Gen1 usando o Java SDK. Agora você pode ver os artigos a seguir que falam sobre como usar o Java SDK para trabalhar com o Data Lake Storage Gen1.

* [Operações de dados no Data Lake Storage Gen1 usando o Java SDK](data-lake-store-get-started-java-sdk.md)
