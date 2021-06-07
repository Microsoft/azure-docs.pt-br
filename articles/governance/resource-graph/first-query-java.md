---
title: 'Guia de início rápido: Sua primeira consulta Java'
description: Neste guia de início rápido, você seguirá as etapas para habilitar os pacotes do Maven do Resource Graph para Java e executará sua primeira consulta.
ms.date: 03/30/2021
ms.topic: quickstart
ms.custom: devx-track-java
ms.openlocfilehash: 97c04cb8b8180034bdc5109446c79deb56e457c9
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223845"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-java"></a>Guia de início rápido: Executar sua primeira consulta do Resource Graph usando o Java

A primeira etapa para usar o Azure Resource Graph é verificar se os pacotes necessários do Maven para Java estão instalados. Este guia de início rápido explica o processo de adição dos pacotes do Maven à instalação do Java.

No fim desse processo, você terá adicionado os pacotes do Maven à instalação do Java e executará sua primeira consulta do Resource Graph.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Verifique se a última CLI do Azure está instalada (no mínimo, **2.21.0**). Se ela ainda não tiver sido instalada, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

  > [!NOTE]
  > A CLI do Azure é necessária para habilitar o SDK do Azure para Java a usar a **autenticação baseada na CLI** nos exemplos a seguir. Para obter informações sobre outras opções, confira a [biblioteca de clientes da Identidade do Azure para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity).

- O [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versão
  8.

- O [Apache Maven](https://maven.apache.org/), versão 3.6 ou posterior.

## <a name="create-the-resource-graph-project"></a>Criar o projeto do Resource Graph

Para habilitar o Java a consultar o Azure Resource Graph, crie e configure um aplicativo com o Maven e instale os pacotes do Maven necessários.

1. Inicialize um novo aplicativo Java chamado "argQuery" com um [arquétipo do Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html):

   ```cmd
   mvn -B archetype:generate -DarchetypeGroupId="org.apache.maven.archetypes" -DgroupId="com.Fabrikam" -DartifactId="argQuery"
   ```

1. Altere os diretórios para a nova pasta do projeto `argQuery` e abra `pom.xml` no seu editor favorito. Adicione os seguintes nós `<dependency>` sob o nó `<dependencies>` existente:

   ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-identity</artifactId>
        <version>1.2.4</version>
    </dependency>
    <dependency>
        <groupId>com.azure.resourcemanager</groupId>
        <artifactId>azure-resourcemanager-resourcegraph</artifactId>
        <version>1.0.0-beta.1</version>
    </dependency>
   ```

1. No arquivo `pom.xml`, adicione o seguinte nó `<properties>` sob o nó base `<project>` para atualizar as versões de origem e de destino:

   ```xml
   <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. No arquivo `pom.xml`, adicione o nó `<build>` a seguir sob o nó base `<project>` para configurar a meta e a classe principal para que o projeto seja executado.

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>org.codehaus.mojo</groupId>
               <artifactId>exec-maven-plugin</artifactId>
               <version>1.2.1</version>
               <executions>
                   <execution>
                       <goals>
                           <goal>java</goal>
                       </goals>
                   </execution>
               </executions>
               <configuration>
                   <mainClass>com.Fabrikam.App</mainClass>
               </configuration>
           </plugin>
       </plugins>
   </build>
   ```

1. Substitua o `App.java` padrão em `\argQuery\src\main\java\com\Fabrikam` pelo seguinte código e salve o arquivo atualizado:

   ```java
   package com.Fabrikam;
   
   import java.util.Arrays;
   import java.util.List;
   import com.azure.core.management.AzureEnvironment;
   import com.azure.core.management.profile.AzureProfile;
   import com.azure.identity.DefaultAzureCredentialBuilder;
   import com.azure.resourcemanager.resourcegraph.ResourceGraphManager;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequest;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequestOptions;
   import com.azure.resourcemanager.resourcegraph.models.QueryResponse;
   import com.azure.resourcemanager.resourcegraph.models.ResultFormat;
   
   public class App
   {
       public static void main( String[] args )
       {
           List<String> listSubscriptionIds = Arrays.asList(args[0]);
           String strQuery = args[1];
   
           ResourceGraphManager manager = ResourceGraphManager.authenticate(new DefaultAzureCredentialBuilder().build(), new AzureProfile(AzureEnvironment.AZURE));
   
           QueryRequest queryRequest = new QueryRequest()
               .withSubscriptions(listSubscriptionIds)
               .withQuery(strQuery);
           
           QueryResponse response = manager.resourceProviders().resources(queryRequest);
   
           System.out.println("Records: " + response.totalRecords());
           System.out.println("Data:\n" + response.data());
       }
   }
   ```

1. Compile o aplicativo de console `argQuery`:

   ```bash
   mvn package
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta ao Resource Graph

Com o aplicativo de console Java compilado, é hora de experimentar uma consulta simples do Resource Graph. A consulta retorna os cinco primeiros recursos do Azure com o **Nome** e o **Tipo de Recurso** de cada um.

Em cada chamada à `argQuery`, há variáveis usadas que precisam ser substituídas com seus valores:

- `{subscriptionId}`: substitua por sua ID da assinatura
- `{query}`: substitua-o pela consulta do Azure Resource Graph

1. Use a CLI do Azure para se autenticar com `az login`.

1. Altere os diretórios para a pasta do projeto `argQuery` que você criou com o comando anterior `mvn -B archetype:generate`.

1. Execute sua primeira consulta do Azure Resource Graph usando o Maven para compilar o aplicativo de console e transmitir os argumentos. A propriedade `exec.args` identifica os argumentos por espaços. Para identificar a consulta como um só argumento, nós a encapsulamos com aspas simples (`'`).

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5'"
   ```

   > [!NOTE]
   > Por essa consulta de exemplo não fornecer um modificador de classificação, como `order by`, executar essa consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação.

1. Altere o argumento para `argQuery.exe` e a consulta para executar `order by` na propriedade **Name**:

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5 | order by name asc'"
   ```

   > [!NOTE]
   > Assim como ocorre com a primeira consulta, executar esta consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação. A ordem dos comandos de consulta é importante. Neste exemplo, o `order by` vem após o `limit`. Essa ordem de comando limita primeiro os resultados da consulta e os ordena.

1. Altere o parâmetro final para `argQuery.exe` e altere a consulta para, primeiro, `order by` a propriedade **Name** e depois `limit` conforme os primeiros cinco resultados:

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | order by name asc | limit 5'"
   ```

Quando a consulta final for executada várias vezes, supondo que nada em seu ambiente esteja sendo alterado, os resultados retornados serão consistentes e ordenados segundo a propriedade **Name**, mas ainda limitados aos cinco primeiros resultados.

## <a name="clean-up-resources"></a>Limpar os recursos

Caso deseje remover o aplicativo de console Java e os pacotes instalados, faça isso excluindo a pasta do projeto `argQuery`.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um aplicativo de console Java com os pacotes do Resource Graph necessários e executou sua primeira consulta. Para saber mais sobre a linguagem do Resource Graph, prossiga para a página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obter mais informações sobre a linguagem de consulta](./concepts/query-language.md)