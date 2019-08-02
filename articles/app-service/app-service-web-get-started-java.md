---
title: Criar um aplicativo Web do Java no Windows – Serviço de Aplicativo do Azure
description: Neste início rápido, você implanta seu primeiro Olá, Mundo em Java no Serviço de Aplicativo do Azure no Windows em minutos.
keywords: azure, serviço de aplicativo, aplicativo Web, windows, java, maven, início rápido
services: app-service\web
documentationcenter: ''
author: msangapu-msft
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc
ms.openlocfilehash: 1e25f65cf7ec52b4a58386843a1d2f5796c66e65
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297350"
---
# <a name="quickstart-create-a-java-app-in-app-service"></a>Início Rápido: Criar um aplicativo Java no Serviço de Aplicativo

> [!NOTE]
> Este artigo implanta um aplicativo no Serviço de Aplicativo no Windows. Para implantar o Serviço de Aplicativo no _Linux_, confira [Criar um aplicativo Web Java no Linux](./containers/quickstart-java.md).
>

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches.  Este início rápido mostra como usar a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) com o [Plug-in do Maven para o Serviço de Aplicativo do Azure](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) para implantar um arquivo WAR (Arquivo Web) Java.

> [!NOTE]
> A mesma coisa também pode ser feita usando IDEs populares, como o IntelliJ e Eclipse. Confira nossos documentos semelhantes em [Início Rápido: Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) ou [Início Rápido: Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Aplicativo de exemplo em execução no Azure](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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
    <!-- Deploy to Tomcat in App Service Windows         -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>${SUBSCRIPTION_ID}</subscriptionId>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            <!-- Java Runtime Stack for App Service on Windows-->
            <runtime>
                <os>windows</os>
                <javaVersion>1.8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```

> [!NOTE]
> Neste artigo, estamos trabalhando apenas com aplicativos Java empacotados em arquivos WAR. O plug-in também oferece suporte a aplicativos Web JAR, visite [Implantar um arquivo JAR do Java SE para o Serviço de Aplicativo no Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para experimentá-lo.


Atualize os seguintes espaços reservados na configuração do plug-in:

| Placeholder | DESCRIÇÃO |
| ----------- | ----------- |
| `SUBSCRIPTION_ID` | A ID exclusiva da assinatura em que você deseja implantar seu aplicativo. A ID da assinatura padrão pode ser encontrada no Cloud Shell ou na CLI usando o comando `az account show`. Para todas as assinaturas disponíveis, use o comando `az account list`.|
| `RESOURCEGROUP_NAME` | Nome do novo grupo de recursos no qual criar o aplicativo. Ao colocar todos os recursos para um aplicativo em um grupo, você pode gerenciá-los juntos. Por exemplo, excluir o grupo de recursos excluiria todos os recursos associados ao aplicativo. Atualize esse valor com um novo nome de grupo de recursos exclusivo, por exemplo, *myResourcesGroup*. Você usará esse nome de grupo de recursos para limpar todos os recursos do Azure em uma seção posterior. |
| `WEBAPP_NAME` | O nome do aplicativo fará parte do nome do host do aplicativo quando implantado no Azure (NOME_APLICATIVO_WEB.azurewebsites.net). Atualize esse valor com um nome exclusivo para o novo aplicativo do Serviço de Aplicativo, que hospedará o aplicativo Java, por exemplo, *contoso*. |
| `REGION` | Uma região do Azure na qual o aplicativo está hospedado, por exemplo, *westus2*. Você pode obter uma lista de regiões do Cloud Shell ou da CLI usando o comando `az account list-locations`. |

## <a name="deploy-the-app"></a>Implantar o aplicativo

Implante seu aplicativo Java no Azure usando o seguinte comando:

```bash
mvn package azure-webapp:deploy
```

Após a conclusão da implantação, navegue até o aplicativo implantado usando a URL a seguir no navegador da Web, por exemplo, `http://<webapp>.azurewebsites.net/`.

![Aplicativo de exemplo em execução no Azure](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

**Parabéns!** Você implantou seu primeiro aplicativo Java no Serviço de Aplicativo no Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Recursos do Azure para desenvolvedores Java](/java/azure/)

> [!div class="nextstepaction"]
> [Mapear domínio personalizado](app-service-web-tutorial-custom-domain.md)
