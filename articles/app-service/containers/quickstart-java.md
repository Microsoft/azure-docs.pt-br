---
title: Criar um aplicativo Web do Java no Linux – Serviço de Aplicativo do Azure
description: Neste início rápido, você implanta seu primeiro Olá, Mundo em Java no Serviço de Aplicativo do Azure no Linux em minutos.
keywords: azure, serviço de aplicativo, aplicativo Web, linux, java, maven, início rápido
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 30689e05a2567646ff541818dc68a90c13da7a56
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297251"
---
# <a name="quickstart-create-a-java-app-in-app-service-on-linux"></a>Início Rápido: Criar um aplicativo Java no Serviço de Aplicativo no Linux

O [Serviço de Aplicativo no Linux](app-service-linux-intro.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches usando o sistema operacional Linux. Este início rápido mostra como usar a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) com o [Plug-in do Maven para o Serviço de Aplicativo do Azure](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) para implantar um arquivo WAR (Arquivo Web) Java.
> [!NOTE]
>
> A mesma coisa também pode ser feita usando IDEs populares, como o IntelliJ e Eclipse. Confira nossos documentos semelhantes em [Início Rápido: Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) ou [Início Rápido: Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Aplicativo de exemplo em execução no Azure](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Criar um aplicativo Java

Execute o seguinte comando do Maven no prompt do Cloud Shell para criar um aplicativo chamado `helloworld`:

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Configurar o plug-in do Maven

Para implantar do Maven, use o editor de códigos no Cloud Shell para abrir arquivo de projeto `pom.xml` no diretório `helloworld`. 

```bash
code pom.xml
```

Em seguida, adicione a seguinte definição de plug-in ao elemento `<build>` do arquivo `pom.xml`.

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Linux           -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
    </plugin>
</plugins>
```

O processo de implantação no Serviço de Aplicativo do Azure usa credenciais de conta da CLI do Azure. [Entre com a CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) antes de continuar.

```azurecli
az login
```

Você pode configurar a implantação, execute o comando maven `mvn azure-webapp:config` no Prompt de comando e usar as configurações padrão pressionando **ENTER** até chegar a **Confirmar (S/N)** e, em seguida, pressione **'y'** e a configuração está concluída.

```cmd
~@Azure:~/helloworld$ mvn azure-webapp:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.7.0:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. jre8 [*]
2. java11
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. jre8
3. TOMCAT 8.5 [*]
4. WILDFLY 14
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```

> [!NOTE]
> Neste artigo, estamos trabalhando apenas com aplicativos Java empacotados em arquivos WAR. O plug-in também oferece suporte a aplicativos Web JAR, visite [Implantar um arquivo JAR do Java SE para o Serviço de Aplicativo no Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para experimentá-lo.

Navegue até `pom.xml` novamente para ver se a configuração de plug-in foi atualizada. Você poderá modificar outras configurações do Serviço de Aplicativo diretamente em seu arquivo pom se necessário. Alguns comuns estão listados abaixo:

 Propriedade | Obrigatório | DESCRIÇÃO | Versão
---|---|---|---
`<schemaVersion>` | falso | Especifique a versão do esquema de configuração. Os valores suportados são: `v1`, `v2`. | 1.5.2
`<resourceGroup>` | verdadeiro | Grupo de recursos do Azure para seu aplicativo Web. | 0.1.0+
`<appName>` | verdadeiro | O nome do seu aplicativo Web. | 0.1.0+
[`<region>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) | verdadeiro | Especifica a região onde seu aplicativo Web será hospedado; o valor padrão é **westus**. Todas as regiões válidas na seção [Regiões com suporte](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region). | 0.1.0+
[`<pricingTier>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme##pricingtier) | falso | O tipo de preço do seu aplicativo Web. O valor padrão é **P1V2**.| 0.1.0+
[`<runtime>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting) | verdadeiro | A configuração do ambiente de tempo de execução. Você pode ver os detalhes [aqui](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting). | 0.1.0+
[`<deployment>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting) | verdadeiro | A configuração de implantação. Você pode ver os detalhes [aqui](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting). | 0.1.0+

## <a name="deploy-the-app"></a>Implantar o aplicativo

Implante seu aplicativo Java no Azure usando o seguinte comando:

```bash
mvn package azure-webapp:deploy
```

Após a conclusão da implantação, navegue até o aplicativo implantado usando a URL a seguir no navegador da Web, por exemplo, `http://<webapp>.azurewebsites.net`. 

![Aplicativo de exemplo em execução no Azure](media/quickstart-java/java-hello-world-in-browser.png)

**Parabéns!** Você implantou seu primeiro aplicativo Java no Serviço de Aplicativo no Linux.

## <a name="clean-up-resources"></a>Limpar recursos

Nas etapas anteriores, você criou os recursos do Azure em um grupo de recursos. Se você acha que não precisará desses recursos no futuro, exclua o grupo de recursos executando o seguinte comando no Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Esse comando pode demorar um pouco para ser executado.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo Java Enterprise com o PostgreSQL](tutorial-java-enterprise-postgresql-app.md)

> [!div class="nextstepaction"]
> [Configurar o aplicativo Java](configure-custom-container.md)

> [!div class="nextstepaction"]
> [CI/CD com Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Outros recursos do Azure para desenvolvedores Java](/java/azure/)
