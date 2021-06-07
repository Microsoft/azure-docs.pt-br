---
title: Configurar um ambiente de preparo no Azure Spring Cloud | Microsoft Docs
description: Saiba como usar a implantação azul-verde com o Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 9b3a5659e91ca90d31500b10526e3e2179d4e7da
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046106"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Configurar um ambiente de preparo no Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java

Este artigo explica como configurar uma implantação de preparo usando o padrão de implantação azul-verde no Azure Spring Cloud. A implantação azul-verde é um padrão de entrega contínua do Azure DevOps que se baseia em manter uma versão existente (azul) ao vivo enquanto um novo (verde) é implantado. Este artigo mostra como colocar essa implantação de preparo em produção sem alterar a implantação de produção.

## <a name="prerequisites"></a>Pré-requisitos

* Instância do Azure Spring Cloud em um tipo de preço Standard
* [Extensão do Azure Spring Cloud](/cli/azure/azure-cli-extensions-overview) para o CLI do Azure

Este artigo usa um aplicativo criado a partir do Spring Initializr. Se você quiser usar um aplicativo diferente para este exemplo, precisará fazer uma alteração simples em uma parte voltada ao público do aplicativo para diferenciar a implantação de preparo da produção.

>[!TIP]
> [Azure cloud Shell](https://shell.azure.com) é um shell interativo gratuito que você pode usar para executar as instruções neste artigo.  Ele tem ferramentas do Azure já instaladas, incluindo as versões mais recentes do git, do JDK, do Maven e do CLI do Azure. Se você estiver conectado à sua assinatura do Azure, inicie sua instância do Cloud Shell. Para saber mais, confira [visão geral do Azure cloud Shell](../cloud-shell/overview.md).

Para configurar a implantação azul-verde no Azure Spring Cloud, siga as instruções nas próximas seções.

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão da CLI do Azure

Instale a extensão Azure Spring Cloud para a CLI do Azure usando o seguinte comando:

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-the-app-and-deployments"></a>Preparar o aplicativo e as implantações
Para compilar o aplicativo, siga estas etapas:

1. Gere o código para o aplicativo de exemplo usando Spring Initializr com [essa configuração](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client).

2. Baixe o código.
3. Adicione o seguinte arquivo de origem HelloController. java à pasta `\src\main\java\com\example\hellospring\` :

   ```java
   package com.example.hellospring; 
   import org.springframework.web.bind.annotation.RestController; 
   import org.springframework.web.bind.annotation.RequestMapping; 

   @RestController 

   public class HelloController { 

   @RequestMapping("/") 

     public String index() { 

         return "Greetings from Azure Spring Cloud!"; 
     } 

   } 
   ```
4. Crie o arquivo. jar:

   ```azurecli
   mvn clean packge -DskipTests
   ```
5. Crie o aplicativo em sua instância do Azure Spring Cloud:

   ```azurecli
   az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --assign-endpoint
   ```
6. Implantar o aplicativo no Azure Spring Cloud:

   ```azurecli
   az spring-cloud app deploy -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
   ```
7. Modifique o código para sua implantação de preparo:

   ```java
   package com.example.hellospring; 
   import org.springframework.web.bind.annotation.RestController; 
   import org.springframework.web.bind.annotation.RequestMapping; 

   @RestController 

   public class HelloController { 

   @RequestMapping("/") 

     public String index() { 

         return "Greetings from Azure Spring Cloud! THIS IS THE GREEN DEPLOYMENT"; 
     } 

   } 
   ```
8. Recompile o arquivo. jar:

   ```azurecli
   mvn clean packge -DskipTests
   ```
9. Crie a implantação verde: 

   ```azurecli
   az spring-cloud app deployment create -n green --app demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar 
   ```

## <a name="view-apps-and-deployments"></a>Exibir aplicativos e implantações

Exiba os aplicativos implantados usando o seguinte procedimento:

1. Acesse sua instância do Azure Spring Cloud na portal do Azure.

1. No painel esquerdo, abra o painel **aplicativos** para exibir aplicativos para sua instância de serviço.

   ![Captura de tela do painel aplicativos abertos.](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Você pode selecionar um aplicativo e exibir detalhes.

   ![Captura de tela de detalhes de um aplicativo.](media/spring-cloud-blue-green-staging/app-overview.png)

1. Abra **implantações** para ver todas as implantações do aplicativo. A grade mostra as implantações de produção e de preparo.

   ![Captura de tela que mostra as implantações de aplicativo listadas.](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Selecione a URL para abrir o aplicativo atualmente implantado.
    
   ![Captura de tela que mostra o U R L para o aplicativo implantado.](media/spring-cloud-blue-green-staging/running-blue-app.png)

1. Selecione **produção** na coluna **estado** para ver o aplicativo padrão.
    
   ![Captura de tela que mostra o U R L para o aplicativo padrão.](media/spring-cloud-blue-green-staging/running-default-app.png)

1. Selecione **preparo** na coluna **estado** para ver o aplicativo de preparo.
    
   ![Captura de tela que mostra o U R L para o aplicativo de preparo.](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * Confirme se o ponto de extremidade de teste termina com uma barra (/) para garantir que o arquivo CSS seja carregado corretamente.  
> * Se o navegador exigir que você insira as credenciais de logon para exibir a página, use [Decodificação de URL](https://www.urldecoder.org/) para decodificar o ponto de extremidade de teste. A decodificação de URL retorna uma URL no formato *https:// \<username> : \<password> @ \<cluster-name> . Test.azureapps.Io/gateway/Green*. Use esse formato para acessar seu ponto de extremidade.

>[!NOTE]    
> As configurações do servidor de configuração se aplicam tanto ao ambiente de preparo quanto ao ambiente de produção. Por exemplo, se você definir o caminho do contexto (*Server. servlet. Context-path*) para seu gateway de aplicativo no servidor de configuração como *somepath*, o caminho para a implantação verde será alterado para *https:// \<username> : \<password> @ \<cluster-name> . Test.azureapps.Io/gateway/Green/somepath/.*..
 
Se você visitar seu gateway de aplicativo voltado para o público neste ponto, verá a página antiga sem a nova alteração.

## <a name="set-the-green-deployment-as-the-production-environment"></a>Definir a implantação verde como o ambiente de produção

1. Depois de verificar sua alteração no ambiente de preparo, você pode enviá-la por push para produção. Na página   >  **implantações** de aplicativos, selecione o aplicativo atualmente em **produção**.

1. Selecione as reticências após o **status de registro** da implantação verde e, em seguida, selecione **definir como produção**. 

   ![Captura de tela que mostra seleções para definir a compilação de preparo para produção.](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Confirme se a URL do aplicativo exibe as alterações.

   ![Captura de tela que mostra o U R L do aplicativo agora em produção.](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> Depois de definir a implantação verde como o ambiente de produção, a implantação anterior torna-se a implantação de preparo.

## <a name="modify-the-staging-deployment"></a>Modificar a implantação de preparo

Se você não estiver satisfeito com sua alteração, poderá modificar o código do aplicativo, criar um novo pacote. jar e carregá-lo em sua implantação verde usando o CLI do Azure:

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Excluir a implantação de preparo

Para excluir a implantação de preparo do portal do Azure, vá para a página de sua implantação de preparo e selecione o botão **excluir** .

Como alternativa, exclua a implantação de preparo do CLI do Azure executando o seguinte comando:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Próximas etapas

* [CI/CD para o Azure Spring Cloud](./spring-cloud-howto-cicd.md?pivots=programming-language-java)