---
title: 'Início Rápido: Criar um aplicativo Java no Serviço de Aplicativo do Azure'
description: Implante seu primeiro Olá, Mundo em Java no Serviço de Aplicativo do Azure em minutos. O Plug-in de Aplicativo Web do Azure para Maven torna-o conveniente para implantar aplicativos Java.
keywords: azure, serviço de aplicativo, aplicativo Web, windows, linux, java, maven, início rápido
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-java-uiex
ms.openlocfilehash: ebd189e1cf6e053f5400b8217fc1c2fc385cdac9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701715"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Início Rápido: Criar um aplicativo Java no Serviço de Aplicativo do Azure

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches.  Este guia de início rápido mostra como usar a [CLI do Azure](/cli/azure/get-started-with-azure-cli) com o [Plug-in de Aplicativo Web do Azure para Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) para implantar um arquivo .jar ou .war. Use as guias para alternar entre as instruções do Java SE e Tomcat.


> [!NOTE]
> A mesma coisa também pode ser feita usando IDEs populares, como o IntelliJ e o Eclipse. Confira nossos documentos semelhantes em [Início Rápido: Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) ou [Início Rápido: Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).


![Aplicativo de exemplo em execução no Serviço de Aplicativo do Azure](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Criar um aplicativo Java

# <a name="java-se"></a>[Java SE](#tab/javase)

Clone o projeto de exemplo [Introdução ao Spring Boot](https://github.com/spring-guides/gs-spring-boot).

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

Altere o diretório para o projeto concluído.

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Execute o seguinte comando do Maven no prompt do Cloud Shell para criar um aplicativo chamado `helloworld`:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Em seguida, altere o diretório de trabalho para a pasta do projeto:

```bash
cd helloworld
```

---

## <a name="configure-the-maven-plugin"></a>Configurar o plug-in do Maven

O processo de implantação no Serviço de Aplicativo do Azure usará suas credenciais do Azure automaticamente por meio da CLI do Azure. Se a CLI do Azure não estiver instalada localmente, o plug-in do Maven será autenticado no OAuth ou no dispositivo de logon. Para obter mais informações, confira [autenticação com plug-ins do Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

Execute o comando do Maven abaixo para configurar a implantação. Este comando ajudará você a configurar o sistema operacional do Serviço de Aplicativo, a versão do Java e a versão do Tomcat.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Na opção **Assinatura**, selecione o `Subscription` apropriado inserindo a impressão do número no início da linha.
1. Quando confrontado com a opção **Aplicativo Web**, aceite a opção padrão `<create>` pressionando Enter ou selecione um aplicativo existente.
1. Quando confrontado com a opção **SO**, selecione **Windows** inserindo `3`.
1. Quando confrontado com a opção **Tipo de preço**, selecione **B2** inserindo `2`.
1. Use a versão do Java padrão, **Java 8**, pressionando Enter.
1. Por fim, pressione Enter no último prompt para confirmar suas seleções.

    A saída resumida será semelhante ao snippet mostrado abaixo.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Na opção **Assinatura**, selecione o `Subscription` apropriado inserindo a impressão do número no início da linha.
1. Quando confrontado com a opção **Aplicativo Web**, aceite a opção padrão `<create>` pressionando Enter ou selecione um aplicativo existente.
1. Quando confrontado com a opção **SO**, selecione **Windows** inserindo `3`.
1. Quando confrontado com a opção **Tipo de preço**, selecione **B2** inserindo `2`.
1. Use a versão do Java padrão, **Java 8**, pressionando Enter.
1. Use o contêiner da Web padrão, **Tomcat 8.5**, pressionando Enter.
1. Por fim, pressione Enter no último prompt para confirmar suas seleções.

    A saída resumida será semelhante ao snippet mostrado abaixo.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. Na opção **Assinatura**, selecione o `Subscription` apropriado inserindo a impressão do número no início da linha.
1. Quando confrontado com a opção **Aplicativo Web**, aceite a opção padrão `<create>` pressionando Enter ou selecione um aplicativo existente.
1. Quando confrontado com a opção **SO**, selecione **Linux** pressionando Enter.
1. Quando confrontado com a opção **Tipo de preço**, selecione **B2** inserindo `2`.
1. Use a versão do Java padrão, **Java 8**, pressionando Enter.
1. Por fim, pressione Enter no último prompt para confirmar suas seleções.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Na opção **Assinatura**, selecione o `Subscription` apropriado inserindo a impressão do número no início da linha.
1. Quando confrontado com a opção **Aplicativo Web**, aceite a opção padrão `<create>` pressionando Enter ou selecione um aplicativo existente.
1. Quando confrontado com a opção **SO**, selecione **Linux** pressionando Enter.
1. Quando confrontado com a opção **Tipo de preço**, selecione **B2** inserindo `2`.
1. Use a versão do Java padrão, **Java 8**, pressionando Enter.
1. Use o contêiner da Web padrão, **Tomcat 8.5**, pressionando Enter.
1. Por fim, pressione Enter no último prompt para confirmar suas seleções.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

Você poderá modificar as configurações do Serviço de Aplicativo diretamente em seu `pom.xml`, se necessário. Algumas configurações comuns estão listadas abaixo:

Propriedade | Obrigatório | Descrição | Versão
---|---|---|---
`<schemaVersion>` | false | Especifique a versão do esquema de configuração. Os valores suportados são: `v1`, `v2`. | 1.5.2
`<subscriptionId>` | false | Especifique a ID de assinatura. | 0.1.0+
`<resourceGroup>` | true | Grupo de recursos do Azure para seu aplicativo Web. | 0.1.0+
`<appName>` | true | O nome do seu aplicativo Web. | 0.1.0+
`<region>` | true | Especifica a região onde seu aplicativo Web será hospedado; o valor padrão é **westeurope**. Todas as regiões válidas na seção [Regiões com suporte](https://azure.microsoft.com/global-infrastructure/services/?products=app-service). | 0.1.0+
`<pricingTier>` | false | O tipo de preço do seu aplicativo Web. O valor padrão é **P1V2** para a carga de trabalho de produção, enquanto **B2** é o mínimo recomendado para o ambiente de desenvolvimento/teste do Java. [Saiba mais](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0+
`<runtime>` | true | A configuração do ambiente de runtime. Você pode ver os detalhes [aqui](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0+
`<deployment>` | true | A configuração de implantação. Você pode ver os detalhes [aqui](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0+

Tenha cuidado com os valores de `<appName>` e `<resourceGroup>`(`helloworld-1590394316693` e `helloworld-1590394316693-rg` de acordo com a demonstração), que serão usados posteriormente.

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Implantar o aplicativo

O plug-in do Maven usa credenciais de conta da CLI do Azure para fazer a implantação nos Serviços de Aplicativos. [Entre com a CLI do Azure](/cli/azure/authenticate-azure-cli) antes de continuar.

```azurecli
az login
```

Em seguida, você poderá implantar seu aplicativo Java no Azure usando o comando a seguir.

```bash
mvn package azure-webapp:deploy
```

Depois que a implantação for concluída, seu aplicativo estará pronto em `http://<appName>.azurewebsites.net/`(`http://helloworld-1590394316693.azurewebsites.net` na demonstração). Abra a URL com o navegador da Web local para ver

![Aplicativo de exemplo em execução no Serviço de Aplicativo do Azure](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Parabéns!** Você implantou seu primeiro aplicativo Java no Serviço de Aplicativo.

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Limpar os recursos

Nas etapas anteriores, você criou os recursos do Azure em um grupo de recursos. Se você acha que não precisará desses recursos no futuro, exclua o grupo de recursos por meio do portal ou executando o seguinte comando no Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Esse comando pode demorar um pouco para ser executado.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conectar-se ao BD do Azure para PostgreSQL com Java](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [Configurar CI/CD](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [Informações sobre preços](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [Agregar logs e métricas](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [Escalar verticalmente](manage-scale-up.md)

> [!div class="nextstepaction"]
> [Recursos do Azure para desenvolvedores Java](/java/azure/)

> [!div class="nextstepaction"]
> [Configurar seu aplicativo Java](configure-language-java.md)
