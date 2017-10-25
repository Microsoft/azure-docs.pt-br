---
title: "Introdução à linha de comando Java do Azure AD | Microsoft Docs"
description: "Como criar um aplicativo de linha de comando Java que conecta os usuários para acessar uma API."
services: active-directory
documentationcenter: java
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 51e1a8f9-6ff0-4643-a350-0ba794e26fd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 01/23/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 91e4a7b2ac454465d5cce4948a4d5f0b542d2b55
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-java-command-line-app-to-access-an-api-with-azure-ad"></a>Usando um aplicativo de linha de comando Java para acessar uma API com o AD do Azure
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

O AD do Azure torna simples e direto terceirizar o gerenciamento da identidade de seu aplicativo Web, fornecendo uma única entrada e uma única saída com apenas algumas linhas de código.  Em aplicativos Web do Java, você pode conseguir isso usando a implementação da Microsoft do ADAL4J voltado para a comunidade.

  Aqui usaremos o ADAL4J para:

* Conectar o usuário ao aplicativo usando o Azure AD como o provedor de identidade.
* Exibir algumas informações sobre o usuário.
* Desconectar o usuário do aplicativo.

Para isso, você precisará:

1. Registrar um aplicativo com o Active Directory do Azure
2. Configure seu aplicativo para usar a biblioteca ADAL4J.
3. Use a biblioteca ADAL4J para emitir solicitações de entrada e saída ao AD do Azure.
4. Imprimir dados sobre o usuário.

Para começar, [baixe o esqueleto do aplicativo](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) ou [baixe o exemplo concluído](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\\/archive/complete.zip).  Você também precisará de um locatário do AD do Azure no qual registrar seu aplicativo.  Se você ainda não tiver um locatário, [saiba como obter um](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>1.  Registrar um aplicativo com o AD do Azure
Para habilitar seu aplicativo a autenticar usuários, primeiro você precisará registrar um novo aplicativo em seu locatário.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique na sua conta e, na lista **Diretório**, escolha o locatário do Active Directory em que você deseja registrar seu aplicativo.
3. Clique em **Mais Serviços** no painel de navegação à esquerda e escolha **Azure Active Directory**.
4. Clique em **Registros do Aplicativo** e escolha **Adicionar**.
5. Siga os prompts e crie um novo **Aplicativo Web e/ou WebAPI**.
  * O **nome** do aplicativo descreverá seu aplicativo para os usuários finais
  * A **URL de logon** é a URL base do seu aplicativo.  O padrão do esqueleto é `http://localhost:8080/adal4jsample/`.
6. Depois de concluir o registro, o AAD atribuirá a seu aplicativo uma ID do Aplicativo única.  Você precisará desse valor nas próximas seções, portanto, copie-o da guia do aplicativo.
7. Na página **Configurações** -> **Propriedades** do aplicativo, atualize o URI da ID do Aplicativo. O **URI da ID do aplicativo** é um identificador exclusivo para seu aplicativo.  A convenção é usar `https://<tenant-domain>/<app-name>`, por exemplo, `http://localhost:8080/adal4jsample/`.

Quando estiver no portal de seu aplicativo, crie uma **Chave** na página **Configurações** para seu aplicativo e copie-a.  Você precisará dele em breve.

## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisites-using-maven"></a>2. Configurar seu aplicativo para usar a biblioteca ADAL4J e pré-requisitos para o uso do Maven
Aqui, configuraremos o ADAL4J para usar o protocolo de autenticação OpenID Connect.  O ADAL4J será usado para emitir solicitações de entrada e saída, gerenciar a sessão do usuário e obter informações sobre o usuário, entre outras coisas.

* No diretório raiz do projeto, abra/crie `pom.xml`, localize o `// TODO: provide dependencies for Maven` e substitua-o pelo seguinte:

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>public-client-adal4j-sample</artifactId>
    <packaging>jar</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>public-client-adal4j-sample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.nimbusds</groupId>
            <artifactId>oauth2-oidc-sdk</artifactId>
            <version>4.5</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20090211</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>
</dependencies>
    <build>
        <finalName>public-client-adal4j-sample</finalName>
        <plugins>
                <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install</id>
                        <phase>install</phase>
                        <goals>
                            <goal>sources</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
        </plugins>
    </build>

</project>


```



## <a name="3-create-the-java-publicclient-file"></a>3. Criar o arquivo PublicClient Java
Conforme indicado acima, usaremos a Graph API para obter dados sobre o usuário conectado. Para que isso seja fácil, devemos criar um arquivo para representar um **Objeto de Diretório** e um arquivo individual para representar o **Usuário**, para que o padrão OO do Java possa ser usado.

* Crie um arquivo chamado `DirectoryObject.java` , que usaremos para armazenar os dados básicos sobre qualquer DirectoryObject (fique a vontade para usá-lo posteriormente para qualquer outra Consulta de Gráfico que você possa fazer). Você pode recortar/colar isso abaixo:

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


## <a name="compile-and-run-the-sample"></a>Compilar e executar o exemplo
Volte para seu diretório raiz e execute o comando a seguir para compilar o exemplo que você acabou de montar usando o `maven`. Isso usará o arquivo `pom.xml` que você escreveu para as dependências de arquivo.

`$ mvn package`

Agora você deve ter um arquivo `adal4jsample.war` em seu diretório do `/targets`. Você pode implantá-lo em seu contêiner do Tomcat e visitar a URL 

`http://localhost:8080/adal4jsample/`

> [!NOTE]
> É muito fácil de implantar um WAR com os servidores do Tomcat mais recentes. Basta navegar até `http://localhost:8080/manager/` e seguir as instruções sobre como carregar o arquivo ``adal4jsample.war`. Ele será implantado automático com o ponto de extremidade correto.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Parabéns! Agora você tem um aplicativo Java com a capacidade de autenticar usuários, chamar APIs da Web com segurança usando OAuth 2.0 e obter informações básicas sobre o usuário.  Se você ainda não fez isso, agora é o momento de preencher seu locatário com alguns usuários.

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um .zip aqui](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

