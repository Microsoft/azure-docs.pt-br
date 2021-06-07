---
title: Início Rápido – Criar e enviar imagens de contêiner Java por push para o Registro de Contêiner do Azure usando Maven e Jib
description: Crie um aplicativo Java em contêineres e envie-o por push para o Registro de Contêiner do Azure usando o plug-in Maven Jib.
author: KarlErickson
ms.author: karler
ms.date: 02/26/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
- mode-api
ms.openlocfilehash: 4d805458d90c73de879a9b87d5b08c98a8f1a250
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537303"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Início Rápido: Criar e enviar imagens de contêiner Java por push para o Registro de Contêiner do Azure

Este início rápido mostra como criar um aplicativo Java em contêineres e enviá-lo por push para o Registro de Contêiner do Azure usando o plug-in Maven Jib. O uso do Maven e do Jib é um exemplo de como usar ferramentas de desenvolvedor para interagir com um Registro de Contêiner do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure; se ainda não tiver uma assinatura do Azure, você poderá ativar o [benefício de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para uma [conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial).
* A[CLI (interface de linha de comando) do Azure](/cli/azure/overview).
* Um JDK (Java Development Kit) com suporte. Para obter mais informações sobre os JDKs disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.
* Ferramenta de build do [Maven](http://maven.apache.org) do Apache (versão 3 ou mais recente).
* Um cliente [Git](https://git-scm.com).
* Um cliente do [Docker](https://www.docker.com).
* O [auxiliar de credencial do Docker do ACR](https://github.com/Azure/acr-docker-credential-helper).

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Criar o aplicativo Web de Introdução ao Spring Boot no Docker

As etapas a seguir mostram como compilar um aplicativo Web Spring Boot e testá-lo localmente.

1. No prompt de comando, use o comando a seguir para clonar o exemplo de projeto [Introdução ao Spring Boot no Docker](https://github.com/spring-guides/gs-spring-boot-docker).

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Altere o diretório para o projeto concluído.

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Use o Maven para compilar e executar o aplicativo de exemplo.

   ```bash
   mvn package spring-boot:run
   ```

1. Teste o aplicativo Web navegando até `http://localhost:8080` ou com o seguinte comando `curl`:

   ```bash
   curl http://localhost:8080
   ```

Você verá a seguinte mensagem exibida: **Olá, mundo do Docker**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Criar um Registro de Contêiner do Azure usando a CLI do Azure

Em seguida, você criará um grupo de recursos do Azure e seu ACR usando as seguintes etapas:

1. Faça logon na conta do Azure usando o seguinte comando:

   ```azurecli
   az login
   ```

1. Especifique a assinatura do Azure a ser usada:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Crie um grupo de recursos para os recursos do Azure usados neste tutorial. No comando a seguir, substitua os espaços reservados pelo nome do seu recurso e uma localização como `eastus`.

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Crie um Registro de Contêiner do Azure privado no grupo de recursos usando o comando a seguir. Substitua os espaços reservados pelos valores reais. O tutorial envia o aplicativo de exemplo para esse registro como uma imagem de Docker em etapas posteriores.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Enviar por push seu aplicativo para o registro de contêiner via Jib

Por fim, você atualizará sua configuração de projeto e usará o prompt de comando para criar e implantar sua imagem.

> [!NOTE]
> Para fazer logon no Registro de Contêiner do Azure que você acabou de criar, será necessário ter o daemon do Docker em execução. Para instalar o Docker em seu computador, [aqui está a documentação oficial do Docker](https://docs.docker.com/install/).

1. Faça logon no Registro de Contêiner do Azure por meio da CLI do Azure usando o comando a seguir. Substitua o espaço reservado pelo seu nome do Registro.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   O comando `az configure` define o nome do Registro padrão a ser usado com os comandos `az acr`.

1. Navegue até o diretório do projeto completo de seu aplicativo Spring Boot (por exemplo, "*C:\SpringBoot\gs-spring-boot-docker\complete*" ou " */users/robert/SpringBoot/gs-spring-boot-docker/complete*"), e abra o arquivo *pom.xml* com um editor de texto.

1. Atualize a coleção `<properties>` no arquivo *pom.xml* no XML a seguir. Substitua o espaço reservado pelo nome do Registro e adicione uma propriedade `<jib-maven-plugin.version>` com o valor `2.2.0` ou uma versão mais recente do [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin).

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
   </properties>
   ```

1. Atualize a coleção `<plugins>` no arquivo de *pom.xml* para que o elemento `<plugin>` a contenha e a entrada para o `jib-maven-plugin`, conforme mostrado no exemplo a seguir. Observe que estamos usando uma imagem base do MCR (Registro de Contêiner da Microsoft): `mcr.microsoft.com/java/jdk:8-zulu-alpine`, que contém um JDK oficialmente compatível para o Azure. Para outras imagens base do MCR com JDKs oficialmente compatíveis, confira [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless) e [Java SE JDK e Maven](https://hub.docker.com/_/microsoft-java-maven).

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Navegue para o diretório do projeto completo de seu aplicativo Spring Boot, execute o seguinte comando para compilar a imagem e envie por push a imagem para o registro:

   ```bash
   az acr login && mvn compile jib:build
   ```

> [!NOTE]
>
> Por motivos de segurança, a credencial criada por `az acr login` é válida somente por 1 hora. Se você receber um erro *401 Não autorizado*, poderá executar o comando `az acr login -n <your registry name>` outra vez para se autenticar novamente.

## <a name="verify-your-container-image"></a>Verificar sua imagem de contêiner

Parabéns! Agora você tem o build do aplicativo Java em contêineres no JDK com suporte do Azure enviado por push para seu ACR. Agora você pode testar a imagem implantando-a no Serviço de Aplicativo do Azure ou efetuando pull dela para o local com comando (substituindo o espaço reservado):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker
```

## <a name="next-steps"></a>Próximas etapas

Para outras versões das imagens base oficiais do Java com suporte da Microsoft, confira:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK e Maven](https://hub.docker.com/_/microsoft-java-maven)

Para saber mais sobre o Spring e o Azure, continue no Spring no Centro de Documentação do Azure.

> [!div class="nextstepaction"]
> [Spring no Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Recursos adicionais

Para saber mais, consulte os recursos a seguir:

* [Azure para desenvolvedores Java](/azure/java)
* [Como trabalhar com o Java e o Azure DevOps](/azure/devops/java)
* [Introdução ao Spring Boot no Docker](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [Implantar um aplicativo Spring Boot no Serviço de Aplicativo do Azure](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-on-linux#configure-maven-to-build-image-to-your-azure-container-registry)
* [Usando uma imagem personalizada do Docker para o Aplicativo Web do Azure no Linux](../app-service/tutorial-custom-container.md)
