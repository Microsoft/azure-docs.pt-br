---
title: Como configurar o aplicativo Inicializador do Spring Boot para usar o Cache Redis
description: Saiba como configurar um aplicativo do Spring Boot criado com o Inicializador do Spring para usar o Cache Redis do Microsoft Azure.
services: redis-cache
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
keywords: Spring, Spring Boot Starter, Cache Redis
ms.assetid: 
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: java
ms.topic: article
ms.date: 08/31/2017
ms.author: robmcm;zhijzhao;yidon
ms.openlocfilehash: 7a6ec549654d00975494bac8594a6777af5ec415
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-a-spring-boot-initializer-app-to-use-redis-cache"></a>Como configurar o aplicativo Inicializador do Spring Boot para usar o Cache Redis

## <a name="overview"></a>Visão geral

O **[Spring Framework]** é uma solução de software livre que ajuda os desenvolvedores Java criar aplicativos de nível empresarial. Um dos projetos mais populares que é criado com base nessa plataforma é o [Spring Boot], que fornece uma abordagem simplificada para a criação de aplicativos Java autônomos. Para ajudar os desenvolvedores a começarem a usar o Spring Boot, vários exemplos de pacotes do Spring Boot estão disponíveis em <https://github.com/spring-guides/>. Além de escolher na lista de projetos básicos do Spring Boot, o  **[Spring Initializr]** ajuda os desenvolvedores a começarem a criar aplicativos personalizados do Spring Boot.

Este artigo ensina a criar um cache Redis usando o Portal do Azure, em seguida, usar o **Spring Initializr** para criar um aplicativo personalizado e, depois disso, criar um aplicativo Web de Java que armazena e recupera os dados usando o cache Redis.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são obrigatórios para que você siga as etapas neste artigo:

* Uma assinatura do Azure; se ainda não tiver uma assinatura do Azure, você poderá ativar o [benefício de assinante do MSDN] ou inscrever-se para uma [conta gratuita do Azure].

* Um [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/) versão 1.7 ou posterior.

* [Apache Maven](http://maven.apache.org/) versão 3.0 ou posterior.

## <a name="create-a-redis-cache-on-azure"></a>Criar um cache Redis no Azure

1. Navegue até o portal do Azure em <https://portal.azure.com/> e clique no item para **+Novo**.

   ![Portal do Azure][AZ01]

1. Clique em **Banco de Dados**e, em seguida, clique em **Cache Redis**.

   ![Portal do Azure][AZ02]

1. Na página **Novo Cache Redis**, especifique as seguintes informações:

   * Insira o **nome de DNS** para seu cache.
   * Especifique sua **Assinatura**, **Grupo de recursos**, **Local** e **Tipo de preço**.
   * Neste tutorial, selecione **Desbloquear porta 6379**.

   > [!NOTE]
   >
   > Você pode usar o SSL com caches Redis, mas você precisa usar um cliente Redis diferente, como o Jedis. Para mais informações, consulte [Como usar o Cache Redis do Azure com Java][Redis Cache with Java].
   >

   Quando você tiver especificado essas opções, clique em **Criar** para criar o cache.

   ![Portal do Azure][AZ03]

1. Depois que o cache for concluído, você o verá listado no **Painel** do Azure, bem como nas páginas **Todos os Recursos** e **Caches Redis**. Você pode clicar no seu cache em qualquer um desses locais para abrir a página de propriedades do cache.

   ![Portal do Azure][AZ04]

1. Quando a página que contém a lista de propriedades do cache for exibida, clique em **Chaves de acesso** e copie as chaves de acesso para o seu cache.

   ![Portal do Azure][AZ05]

## <a name="create-a-custom-application-using-the-spring-initializr"></a>Criar um aplicativo personalizado usando o Spring Initializr

1. Navegue até <https://start.spring.io/>.

1. Especifique que você deseja gerar um projeto **Maven** com **Java**, insira os nomes de **Grupo** e **Artefato** para o seu aplicativo e, em seguida, clique no link para **Alternar para a versão completa** do Spring Initializr.

   ![Opções básicas do Initializr Basic][SI01]

   > [!NOTE]
   >
   > O Spring Initializr usa os nomes de **Grupo** e **Artefato** para criar o nome do pacote; por exemplo: *com.contoso.myazuredemo*.
   >

1. Role para baixo até a seção **Web** e marque a caixa **Web**, em seguida, role para baixo até a seção **NoSQL** e marque a caixa **Redis**, em seguida, role até a parte inferior da página e clique no botão para **Gerar Projeto**.

   ![Opções do Spring Initilializr Completo][SI02]

1. Quando solicitado, baixe o projeto para um caminho no computador local.

   ![Baixe o projeto personalizado do Spring Boot][SI03]

1. Depois de ter extraído os arquivos no sistema local, seu aplicativo Spring Boot personalizado estará pronto para edição.

   ![Arquivos de projeto Spring Boot personalizados][SI04]

## <a name="configure-your-custom-spring-boot-to-use-your-redis-cache"></a>Configurar o Spring Boot personalizado para usar o Cache Redis

1. Localize o arquivo *application.properties* no diretório *recursos* do seu aplicativo, ou crie o arquivo se ele ainda não existe.

   ![Localize o arquivo application.properties][RE01]

1. Abra o arquivo *application.properties* em um editor de texto e adicione as seguintes linhas ao arquivo, então substitua os valores de exemplo pelas propriedades adequadas do seu cache:

   ```yaml
   # Specify the DNS URI of your Redis cache.
   spring.redis.host=myspringbootcache.redis.cache.windows.net

   # Specify the port for your Redis cache.
   spring.redis.port=6379

   # Specify the access key for your Redis cache.
   spring.redis.password=57686f6120447564652c2049495320526f636b73=
   ```

   ![Edição do arquivo application.properties][RE02]

   > [!NOTE]
   >
   > Se você estivesse usando outro cliente Redis, como Jedis, que habilita o SSL, seria necessário especificar a porta 6380 no seu arquivo *application.properties*. Para mais informações, consulte [Como usar o Cache Redis do Azure com Java][Redis Cache with Java].
   >

1. Salve e feche o arquivo *application.properties*.

1. Crie uma pasta chamada *controlador* sob a pasta de origem para o seu pacote; por exemplo:

   `C:\SpringBoot\myazuredemo\src\main\java\com\contoso\myazuredemo\controller`

   -ou-

   `/users/example/home/myazuredemo/src/main/java/com/contoso/myazuredemo/controller`

1. Crie um novo arquivo denominado *HelloController.java* na pasta *Controlador*. Abra o arquivo em um editor de texto e adicione o seguinte código a ele:

   ```java
   package com.contoso.myazuredemo;

   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.data.redis.core.StringRedisTemplate;
   import org.springframework.data.redis.core.ValueOperations;

   @RestController
   public class HelloController {
   
      @Autowired
      private StringRedisTemplate template;

      @RequestMapping("/")
      // Define the Hello World controller.
      public String hello() {
      
         ValueOperations<String, String> ops = this.template.opsForValue();

         // Add a Hello World string to your cache.
         String key = "greeting";
         if (!this.template.hasKey(key)) {
             ops.set(key, "Hello World!");
         }

         // Return the string from your cache.
         return ops.get(key);
      }
   }
   ```
   
   Onde você precisará substituir `com.contoso.myazuredemo` pelo nome do pacote para o seu projeto.

1. Salve e feche o arquivo *HelloController.java*.

1. Crie seu aplicativo Spring Boot com Maven e execute-o; por exemplo:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. Teste o aplicativo Web navegando até http://localhost:8080 com um navegador da Web ou use a sintaxe semelhante ao seguinte exemplo, se você tiver o curl disponível:

   ```shell
   curl http://localhost:8080
   ```

   Você deve ver "Olá, Mundo!" mensagem do seu controlador de exemplo exibido, que está sendo recuperado dinamicamente do seu cache Redis.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar aplicativos Spring Boot no Azure, confira os seguintes artigos:

* [Implantar um aplicativo Spring Boot no Serviço de Aplicativo do Azure](../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)

* [Executando um Aplicativo Spring Boot em um Cluster Kubernetes no Serviço de Contêiner do Azure](../container-service/container-service-deploy-spring-boot-app-on-kubernetes.md)

Para saber mais sobre como usar o Azure com Java, confira o [Centro de Desenvolvedores Java do Azure] e as [Ferramentas Java para Visual Studio Team Services].

Para saber mais sobre como começar a usar o Cache Redis com Java no Azure, consulte [Como usar o Cache Redis do Azure com Java][Redis Cache with Java].

<!-- URL List -->

[Centro de Desenvolvedores Java do Azure]: https://azure.microsoft.com/develop/java/
[conta gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/
[Ferramentas Java para Visual Studio Team Services]: https://java.visualstudio.com/
[benefício de assinante do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[Redis Cache with Java]: cache-java-get-started.md

<!-- IMG List -->

[AZ01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ01.png
[AZ02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ02.png
[AZ03]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ03.png
[AZ04]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ04.png
[AZ05]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ05.png

[SI01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI01.png
[SI02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI02.png
[SI03]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI03.png
[SI04]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI04.png

[RE01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/RE01.png
[RE02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/RE02.png
