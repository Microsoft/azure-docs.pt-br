---
title: Configurar um ambiente de preparo no Azure Spring Cloud | Microsoft Docs
description: Saiba como usar a implantação azul-verde com o Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 82a8da9d2663b03d89ad0819ec6d918bebaf5f5e
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574695"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Configurar um ambiente de preparo no Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java

Este artigo explica como configurar uma implantação de preparo usando o padrão de implantação azul-verde no Azure Spring Cloud. A implantação azul-verde é um padrão de entrega contínua do Azure DevOps que se baseia em manter uma versão existente (azul) ao vivo, enquanto um novo (verde) é implantado. Este artigo mostra como colocar essa implantação de preparo em produção sem alterar a implantação de produção.

## <a name="prerequisites"></a>Pré-requisitos

* Instância do Azure Spring Cloud no **tipo de preço** *Standard* .
* CLI do Azure [extensão do Azure Spring Cloud](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview)

Este artigo usa um aplicativo criado a partir do inicializador Spring. Se você quiser usar um aplicativo diferente para este exemplo, será necessário fazer uma alteração simples em uma parte voltada ao público do aplicativo para diferenciar a implantação de preparo da produção.

>[!TIP]
> Azure Cloud Shell é um shell interativo gratuito que você pode usar para executar as instruções neste artigo.  Ele tem ferramentas do Azure já instaladas, incluindo as versões mais recentes do git, do JDK, do Maven e do CLI do Azure. Se você estiver conectado à sua assinatura do Azure, inicie o [Azure cloud Shell](https://shell.azure.com).  Para saber mais, confira [visão geral do Azure cloud Shell](../cloud-shell/overview.md).

Para configurar implantações azuis-verdes no Azure Spring Cloud, siga as instruções nas próximas seções.

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão da CLI do Azure

Instale a extensão Azure Spring Cloud para a CLI do Azure usando o seguinte comando:

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-app-and-deployments"></a>Preparar o aplicativo e as implantações
Para compilar o aplicativo, siga estas etapas:
1. Gere o código para o aplicativo de exemplo usando o inicializador Spring com [essa configuração](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client).

2. Baixe o código.
3. Adicione o seguinte arquivo de origem HelloController. java à pasta `\src\main\java\com\example\hellospring\` .
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
az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --is-public
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

Exiba os aplicativos implantados usando os procedimentos a seguir.

1. Acesse sua instância do Azure Spring Cloud na portal do Azure.

1. No painel de navegação à esquerda, abra a folha "aplicativos" para exibir os aplicativos para sua instância de serviço.

    [![Aplicativos-painel](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Você pode clicar em um aplicativo e exibir detalhes.

    [![Aplicativos-visão geral](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. Abra **implantações** para ver todas as implantações do aplicativo. A grade mostra as implantações de produção e de preparo.

    [![Painel de aplicativo/implantações](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Clique na URL para abrir o aplicativo atualmente implantado.
    ![URL implantada](media/spring-cloud-blue-green-staging/running-blue-app.png)
1. Clique em **produção** na coluna **estado** para ver o aplicativo padrão.
    ![Padrão em execução](media/spring-cloud-blue-green-staging/running-default-app.png)
1. Clique em **preparo** na coluna **estado** para ver o aplicativo de preparo.
    ![Preparação em execução](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * Confirme se o ponto de extremidade de teste termina com uma barra (/) para garantir que o arquivo CSS seja carregado corretamente.  
> * Se o navegador exigir que você insira as credenciais de logon para exibir a página, use [Decodificação de URL](https://www.urldecoder.org/) para decodificar o ponto de extremidade de teste. A decodificação de URL retorna uma URL no formato "https:// \<username> : \<password> @ \<cluster-name> . Test.azureapps.Io/gateway/Green".  Use este formulário para acessar seu ponto de extremidade.

>[!NOTE]    
> As configurações do servidor de configuração se aplicam tanto ao ambiente de preparo quanto à produção. Por exemplo, se você definir o caminho do contexto ( `server.servlet.context-path` ) para o gateway de aplicativo no servidor de configuração como *somepath*, o caminho para a implantação verde será alterado para "https:// \<username> : \<password> @ \<cluster-name> . Test.azureapps.Io/gateway/Green/somepath/...".
 
 Se você visitar seu gateway de aplicativo voltado para o público neste ponto, verá a página antiga sem a nova alteração.

## <a name="set-the-green-deployment-as-the-production-environment"></a>Definir a implantação verde como o ambiente de produção

1. Depois de verificar sua alteração no ambiente de preparo, você pode enviá-la por push para produção. Na página  / **implantações** de aplicativos, selecione o aplicativo atualmente em `Production` .

1. Clique nas reticências após o **status de registro** da implantação verde e defina a compilação de preparo para produção. 

   [![Definir produção como preparo](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Agora, a URL do aplicativo deve exibir suas alterações.

   ![Preparo agora na implantação](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> Depois de definir a implantação verde como o ambiente de produção, a implantação anterior torna-se a implantação de preparo.

## <a name="modify-the-staging-deployment"></a>Modificar a implantação de preparo

Se você não estiver satisfeito com sua alteração, poderá modificar o código do aplicativo, criar um novo pacote jar e carregá-lo em sua implantação verde usando o CLI do Azure.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Excluir a implantação de preparo

Para excluir a implantação de preparo da porta do Azure, acesse a página de implantação de preparo e, em seguida, selecione o botão **excluir** .

Como alternativa, exclua a implantação de preparo do CLI do Azure executando o seguinte comando:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Próximas etapas

* [CI/CD para o Azure Spring Cloud](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-cicd?branch=pr-en-us-142929&pivots=programming-language-java)
