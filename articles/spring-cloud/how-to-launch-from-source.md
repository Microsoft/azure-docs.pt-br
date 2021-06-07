---
title: Como iniciar seu aplicativo Spring Cloud usando o código-fonte
description: Neste início rápido, saiba como iniciar seu aplicativo Azure Spring Cloud diretamente do seu código-fonte
author: MikeDodaro
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: a6710a15bd0637eead0051ebb70a7cdd8bb8aa58
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878823"
---
# <a name="how-to-launch-your-spring-cloud-application-from-source-code"></a>Como iniciar seu aplicativo Spring Cloud usando o código-fonte

**Este artigo aplica-se a:** ✔️ Java

O Azure Spring Cloud habilita aplicativos de microsserviço baseados no Spring Cloud no Azure.

Você pode iniciar aplicativos diretamente do código-fonte Java ou de um JAR predefinido. Este artigo explica os procedimentos de implantação.

Este guia de início rápido explica como:

> [!div class="checklist"]
> * Provisionar uma instância de serviço
> * Definir um servidor de configuração para uma instância
> * Criar um aplicativo de microsserviço localmente
> * Implantar cada microsserviço
> * Atribuir um ponto de extremidade público para seu aplicativo

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, verifique se sua assinatura do Azure possui as dependências necessárias:

1. [Instalar o Git](https://git-scm.com/)
2. [Instalar o JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Instalar o Maven 3.0 ou posterior](https://maven.apache.org/download.cgi)
4. [Instalar a CLI do Azure](/cli/azure/install-azure-cli)
5. [Inscrever-se em uma assinatura do Azure](https://azure.microsoft.com/free/)

> [!TIP]
> O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo.  Ele tem ferramentas comuns do Azure pré-instaladas, incluindo as versões mais recentes do Git, do JDK, do Maven e da CLI do Azure. Se você fez logon na assinatura do Azure, inicie o [Azure Cloud Shell](https://shell.azure.com) em shell.azure.com.  Você pode saber mais sobre o Azure Cloud Shell [lendo nossa documentação](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão da CLI do Azure

Instalar a extensão do Azure Spring Cloud para a CLI do Azure com o seguinte comando

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Provisionar uma instância de serviço usando a CLI do Azure

Faça logon na CLI do Azure e escolha sua assinatura ativa. 

```azurecli
az login
az account list -o table
az account set --subscription
```

Crie um grupo de recursos para conter seu serviço do Azure Spring Cloud. Você pode saber mais sobre os [Grupos de Recursos do Azure](../azure-resource-manager/management/overview.md).

```azurecli
az group create --location eastus --name <resource group name>
```

Execute os comandos a seguir para provisionar uma instância do Azure Spring Cloud. Prepare um nome para seu serviço do Azure Spring Cloud. O nome deve ter entre 4 e 32 caracteres e pode conter apenas letras minúsculas, números e hifens. O primeiro caractere do nome do serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

A instância de serviço levará aproximadamente cinco minutos para ser implantada.

Defina o nome do grupo de recursos padrão e o nome da instância do Azure Spring Cloud usando os seguintes comandos:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-azure-spring-cloud-application"></a>Criar o aplicativo Azure Spring Cloud

O comando a seguir cria um aplicativo Azure Spring Cloud em sua assinatura.  Isso cria um serviço vazio no qual podemos carregar nosso aplicativo.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Implantar seu aplicativo Spring Cloud

Você pode implantar seu aplicativo de um JAR pré-compilado ou de um repositório Gradle ou Maven.  Encontre instruções para cada caso abaixo.

### <a name="deploy-a-built-jar"></a>Implantar um JAR compilado

Para implantar a partir de um JAR compilado em seu computador local, verifique se sua compilação produz um [fat-JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven).

Para implantar o fat-JAR em uma implantação ativa

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
```

Para implantar o fat-JAR em uma implantação específica

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
```

### <a name="deploy-from-source-code"></a>Implantar do código-fonte

O Azure Spring Cloud usa [kpack](https://github.com/pivotal/kpack) para compilar seu projeto.  Você pode usar o CLI do Azure para carregar seu código-fonte, compilar seu projeto usando kpack e implantá-lo no aplicativo de destino.

> [!WARNING]
> O projeto deve produzir apenas um arquivo JAR com uma entrada `main-class` em `MANIFEST.MF` no `target` (para implantações do Maven) ou `build/libs` (para implantações do Gradle).  Vários arquivos JAR com entradas `main-class` farão com que a implantação falhe.

Para projetos Maven/Gradle de módulo único:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

Para projetos Maven/Gradle com vários módulos, repita para cada módulo:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Exibir logs de implantação

Examine os logs de compilação do kpack usando o seguinte comando:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> Os logs do kpack mostrarão apenas a implantação mais recente se essa implantação foi criada a partir da origem usando kpack.

## <a name="assign-a-public-endpoint-to-gateway"></a>Atribuir um ponto de extremidade público ao gateway

1. Abra a página **Painel de Aplicativo**.
2. Selecione o aplicativo `gateway` para mostrar a página **Detalhes do Aplicativo**.
3. Selecione **Atribuir ponto de extremidade** para atribuir um ponto de extremidade público ao gateway. Isso pode levar alguns minutos. 
4. Insira o IP público atribuído em seu navegador para exibir o aplicativo em execução.

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

> [!div class="checklist"]
> * Provisionar uma instância de serviço
> * Definir um servidor de configuração para uma instância
> * Criar um aplicativo de microsserviço localmente
> * Implantar cada microsserviço
> * Editar variáveis de ambiente para aplicativos
> * Atribuir um IP público para seu gateway de aplicativo

> [!div class="nextstepaction"]
> [Logs, métricas, rastreamento do Spring Cloud](spring-cloud-quickstart-logs-metrics-tracing.md)

Mais exemplos estão disponíveis no GitHub: [Exemplos do Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).