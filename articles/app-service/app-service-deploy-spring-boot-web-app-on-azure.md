---
title: "Implantar um aplicativo Spring Boot para o Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Este tutorial orientará os desenvolvedores sobre as etapas para implantar o aplicativo Web Spring Boot Getting Started para o Serviço de Aplicativo do Azure."
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/04/2017
ms.author: asirveda;robmcm
ms.openlocfilehash: 8776142d5452bf5057990702c89aa1a541382ffc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-spring-boot-application-to-the-azure-app-service"></a>Implantar um aplicativo Spring Boot no Serviço de Aplicativo do Azure

O **[Spring Framework]** é uma solução de software livre que ajuda os desenvolvedores de Java a criar aplicativos de nível empresarial. Um dos projetos mais populares que se baseia nessa plataforma é o [Spring Boot], que fornece uma abordagem simplificada para a criação de aplicativos Java autônomos.

Este tutorial o orientará durante a criação do aplicativo Web de exemplo Spring Boot Getting Started e o implantará no [Serviço de Aplicativo do Azure].

### <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste tutorial, você precisa ter o seguinte:

* Uma assinatura do Azure; se ainda não tiver uma assinatura do Azure, você poderá ativar o [benefício de assinante do MSDN] ou inscrever-se para uma [conta gratuita do Azure].
* Um [JDK (Java Developer Kit)] atualizado.
* A ferramenta de compilação [Maven] do Apache (Versão 3).
* Um cliente [Git].

## <a name="create-the-spring-boot-getting-started-web-app"></a>Criar o aplicativo Web Spring Boot Getting Started

As etapas a seguir explicarão as etapas necessárias para criar um aplicativo Web simples Spring Boot e testá-lo localmente.

1. Abra um prompt de comando, crie um diretório local para conter o aplicativo e altere para o diretório. Por exemplo:
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- ou --
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Clone o projeto de exemplo [Spring Boot Getting Started] para o diretório que você acabou de criar. Por exemplo:
   ```
   git clone https://github.com/spring-guides/gs-spring-boot.git
   ```

1. Altere o diretório para o projeto concluído. Por exemplo:
   ```
   cd gs-spring-boot
   cd complete
   ```

1. Crie o arquivo JAR usando o Maven. Por exemplo:
   ```
   mvn package
   ```

1. Quando o aplicativo Web tiver sido criado, altere o diretório para o arquivo JAR e inicie o aplicativo Web. Por exemplo:
   ```
   cd target
   java -jar gs-spring-boot-0.1.0.jar
   ```

1. Teste o aplicativo Web navegando até http://localhost:8080 com um navegador da Web ou use a sintaxe semelhante ao seguinte exemplo, se você tiver o curl disponível:
   ```
   curl http://localhost:8080
   ```

1. Você verá a seguinte mensagem exibida: **Saudações do Spring Boot!**

   ![Pesquisar aplicativo de exemplo][SB01]

## <a name="create-an-azure-web-app-for-use-with-java"></a>Criar um aplicativo Web do Azure para uso com Java

As etapas a seguir explicarão as etapas para criar um aplicativo Web do Azure, definir as configurações necessárias para Java e configurar as credenciais de FTP.

1. Navegue até o [Portal do Azure] e faça logon.

1. Depois de fazer logon em sua conta no Portal do Azure, clique no ícone de menu dos **Serviços de Aplicativos**:
   
   ![Portal do Azure][AZ01]

1. Quando a página **Serviços de Aplicativos** for exibida, clique em **+Adicionar** para criar um novo Serviço de Aplicativo.

   ![Criar Serviço de Aplicativo][AZ02]

1. Quando a lista de modelos de aplicativos Web for exibida, clique no link do Aplicativo Web da Microsoft básico.

   ![Modelos de aplicativo Web][AZ03]

1. Quando a página de informações do modelo de Aplicativo Web for exibida, clique em **Criar**.

   ![Criar um aplicativo Web][AZ04]

1. Forneça um nome exclusivo para o aplicativo Web, especifique configurações adicionais e **Criar**.

   ![Criar Configurações de Aplicativo Web][AZ05]

1. Quando o aplicativo Web tiver sido criado, clique no ícone de menu dos **Serviços de Aplicativos**e clique no aplicativo Web recém-criado:

   ![Listar Aplicativos Web][AZ06]

1. Quando o aplicativo Web for exibido, especifique a versão do Java usando as seguintes etapas:

   a. Clique no item de menu **Configurações do Aplicativo**.

   b. Escolha **Java 8** para a versão do Java.

   c. Escolha **Mais recente** para a versão secundária do Java.

   d. Escolha **Tomcat 8.5 mais recente** para o contêiner da Web. (Esse contêiner não será realmente usado; o Azure usa o contêiner do aplicativo Spring Boot.)

   e. Clique em **Salvar**.

   ![Configurações do aplicativo][AZ07]

1. Especifique suas credenciais de implantação de FTP usando as seguintes etapas:

   a. Clique no item de menu **Credenciais de Implantação**.

   b. Especifique o nome de usuário e a senha.

   c. Clique em **Salvar**.

   ![Especificar Credenciais de Implantação][AZ08]

1. Recupere as informações de conexão FTP usando as seguintes etapas:

   a. Clique no item de menu **Credenciais de Implantação**.

   b. Copie o nome de usuário de FTP completo e a URL e salve-os para a próxima seção deste tutorial.

   ![URL FTP e Credenciais][AZ09]

## <a name="deploy-your-spring-boot-web-app-to-azure"></a>Implantar o aplicativo Web Spring Boot no Azure

As etapas a seguir explicarão as etapas para implantar o aplicativo Web Spring Boot no Azure.

1. Abra um editor de texto como o Bloco de Notas do Windows, cole o seguinte texto em um novo documento e salve o arquivo como *web.config*:
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
     <system.webServer>
       <handlers>
         <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
       </handlers>
       <httpPlatform processPath="%JAVA_HOME%\bin\java.exe"
           arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\gs-spring-boot-0.1.0.jar&quot;">
       </httpPlatform>
     </system.webServer>
   </configuration>
   ```

1. Depois de salvar o arquivo *web.config* no sistema e conecte-se ao aplicativo Web por meio do FTP usando a URL, o nome de usuário e a senha da seção anterior deste tutorial. Por exemplo:
   ```
   ftp
   open waws-prod-sn0-000.ftp.azurewebsites.windows.net
   user wingtiptoys-springboot\wingtiptoysuser
   pass ********
   ```

1. Altere o diretório remoto para a pasta raiz do aplicativo Web (em */site/wwwroot*), copie o arquivo JAR do aplicativo Spring Boot e o *web.config* anteriores. Por exemplo:
   ```
   cd site/wwwroot
   put gs-spring-boot-0.1.0.jar
   put web.config
   ```

1. Depois de implantar os arquivos JAR e *web.config* no aplicativo Web, você precisa reiniciar o aplicativo Web usando o Portal do Azure:

   ![][AZ10]

1. Teste o aplicativo Web navegando até a URL do aplicativo Web com um navegador da Web ou use a sintaxe semelhante ao seguinte exemplo, se você tiver o curl disponível:
   ```
   curl http://wingtiptoys-springboot.azurewebsites.net/
   ```

1. Você verá a seguinte mensagem exibida: **Saudações do Spring Boot!**

   ![Pesquisar aplicativo de exemplo][SB02]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar aplicativos Spring Boot no Azure, confira os seguintes artigos:

* [Implantar um aplicativo Spring Boot no Linux no Serviço de Contêiner do Azure](../container-service/kubernetes/container-service-deploy-spring-boot-app-on-linux.md)

* [Implantar um Aplicativo Spring Boot em um Cluster Kubernetes no Serviço de Contêiner do Azure](../container-service/kubernetes/container-service-deploy-spring-boot-app-on-kubernetes.md)

Para saber mais sobre como usar o Azure com Java, confira o [Centro de Desenvolvedores Java do Azure] e as [Ferramentas Java para Visual Studio Team Services].

Para obter informações adicionais sobre a implantação dos aplicativos Web no Azure usando o FTP, confira [Implantar o aplicativo no Serviço de Aplicativo do Azure usando FTP/S].

Para obter mais detalhes sobre o projeto de exemplo Spring Boot, confira [Spring Boot Getting Started].

Para obter ajuda na introdução a seus próprios aplicativos Spring Boot, confira **Spring Initializr** em https://start.spring.io/.

Para obter mais informações sobre como definir configurações adicionais para o aplicativo Web, confira [Configurar aplicativos Web no Serviço de Aplicativo do Azure].

<!-- URL List -->

[Serviço de Aplicativo do Azure]: https://azure.microsoft.com/services/app-service/
[Azure Container Service]: https://azure.microsoft.com/services/container-service/
[Centro de Desenvolvedores Java do Azure]: https://azure.microsoft.com/develop/java/
[Portal do Azure]: https://portal.azure.com/
[Configurar aplicativos Web no Serviço de Aplicativo do Azure]: /azure/app-service/web-sites-configure
[Implantar o aplicativo no Serviço de Aplicativo do Azure usando FTP/S]: https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp
[conta gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[JDK (Java Developer Kit)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Ferramentas Java para Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[benefício de assinante do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot Getting Started]: https://github.com/spring-guides/gs-spring-boot
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/app-service-deploy-spring-boot-web-app-on-azure/SB01.png
[SB02]: ./media/app-service-deploy-spring-boot-web-app-on-azure/SB02.png

[AZ01]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ01.png
[AZ02]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ02.png
[AZ03]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ03.png
[AZ04]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ04.png
[AZ05]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ05.png
[AZ06]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ06.png
[AZ07]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ07.png
[AZ08]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ08.png
[AZ09]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ09.png
[AZ10]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ10.png
