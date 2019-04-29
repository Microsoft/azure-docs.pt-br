---
title: Referência da Malha do Azure Service Fabric | Microsoft Docs
description: Contém a referência de como usar o plug-in do Maven para a Malha do Azure Service Fabric
services: service-fabric-mesh
keywords: maven, java, cli
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.service: service-fabric-mesh
manager: subramar
ms.openlocfilehash: 08e842f5b91bd0ca5f8e8b2a7866f3f9a689ac28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811624"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Plug-in do Maven para a Malha do Azure Service Fabric

## <a name="prerequisites"></a>Pré-requisitos

- Java SDK
- Maven
- CLI do Azure com extensão de malha
- CLI do Service Fabric

## <a name="goals"></a>Metas

### `azure-sfmesh:init`
- Cria uma `servicefabric` pasta que contém uma `appresources` pasta que tem o `application.yaml` arquivo. 

### `azure-sfmesh:addservice`
- Cria uma pasta dentro da `servicefabric` pasta com o nome do serviço e cria o arquivo YAML do serviço. 

### `azure-sfmesh:addnetwork`
- Gera uma `network` YAML com o nome de rede fornecido na `appresources` pasta 

### `azure-sfmesh:addgateway`
- Gera uma `gateway` YAML com o nome de gateway fornecido na `appresources` pasta 

### `azure-sfmesh:addsecret`
- Gera uma `secret` YAML com o nome de segredo fornecido na `appresources` pasta 

### `azure-sfmesh:addsecretvalue`
- Gera uma `secretvalue` YAML com o segredo fornecido e o nome do valor de segredo na pasta `appresources` 

### `azure-sfmesh:deploy`
- Mescla o yamls do `servicefabric` pasta e cria um modelo do Azure Resource Manager JSON na pasta atual.
- Implanta todos os recursos no ambiente de Malha do Azure Service Fabric 

### `azure-sfmesh:deploytocluster`
- Cria uma pasta (`meshDeploy`) que contém a implantação JSONs gerada a partir de yamls que são aplicáveis para clusters do Service Fabric
- Implanta todos os recursos para o cluster do Service Fabric
 

## <a name="usage"></a>Uso

Para usar o plugin do Maven em seu aplicativo Maven Java, adicione o seguinte trecho de código ao seu arquivo pom.xml:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>Configuração de Domínio

O plug-in do Maven atualmente não dá suporte a configurações comuns de plug-ins do Maven para o Azure.

## <a name="how-to"></a>Instruções

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Inicializar o projeto do Maven para a Malha do Azure Service Fabric
Execute o seguinte comando para criar o arquivo YAML do recurso de aplicativo.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Cria uma pasta chamada `servicefabric->appresources` na sua pasta raiz que contém um aplicativo YAML chamado `app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Adicionar um recurso ao seu aplicativo

#### <a name="add-a-new-network-to-your-application"></a>Adicionar uma nova rede ao seu aplicativo
Executar o comando a seguir para criar um yaml de recursos de rede. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.4/22
```

- Cria uma rede YAML na pasta `servicefabric->appresources` chamado `network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Adicionar um novo serviço para seu aplicativo
Executar o comando abaixo para criar um yaml de serviço. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Cria um serviço YAML na pasta `servicefabric->helloworldservice` nomeado `service_helloworldservice` que referencia `helloworldservicenetwork` e o `helloworldserver` aplicativo
- O serviço deve escutar na porta 8080
- O serviço estaria usando ***helloworldserver:latest*** como sua imagem de contêiner.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Adicionar um novo recurso de gateway para o seu aplicativo
Executar o comando a seguir para criar um yaml de recursos de gateway. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Cria um gateway YAML na pasta `servicefabric->appresources` chamado `gateway_helloworldgateway`
- Referências `helloworldservicelistener` como o ouvinte de serviço está escutando a chamadas desse gateway. Também faz referência a `helloworldservice` como o serviço `helloworldservicenetwork` como a rede e `helloworldserver` como o aplicativo. 
- Escuta solicitações na porta 80

#### <a name="add-a-new-volume-to-your-application"></a>Adicionar um novo serviço para seu aplicativo
Executar o comando a seguir para criar um yaml de recursos. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Cria um volume YAML na pasta `servicefabric->appresources` chamado `volume_vol1`
- Define as propriedades de parâmetros necessários, `volumeAccountKey`, e `volumeShareName` como acima
- Para obter mais informações sobre como fazer referência nesse volume criado, visite o seguinte, [Implantar Aplicativo usando o Volume de Armazenamento de Arquivos](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Adicionar um novo recurso de segredo para o seu aplicativo
Executar o comando a seguir para criar um yaml de recursos. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Cria um YAML secreto na pasta `servicefabric->appresources` chamado `secret_secret1`
- Para obter mais informações sobre como fazer referência a esse segredo criado, visite o seguinte, [Gerenciar Segredos](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Adicionar um novo recurso de segredo para o seu aplicativo
Executar o comando a seguir para criar um yaml de recursos secretvalue. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Crie um secretvalue YAML na pasta `servicefabric->appresources` chamado `secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Executar o aplicativo localmente

Com a ajuda da meta `azure-sfmesh:deploytocluster`, você pode executar o aplicativo localmente usando o comando a seguir:

```cmd
mvn azure-sfmesh:deploytocluster
```

Por padrão essa meta implanta recursos em cluster local. Se você estiver implantando um cluster local, ele pressupõe que você tem um cluster do Service Fabric local em funcionamento. O Cluster do Service Fabric local para os recursos atualmente tem suporte somente no [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Cria JSONs de yamls que são aplicáveis para clusters do Service Fabric
- Em seguida, implanta o ponto de extremidade do Cluster

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Implantar um aplicativo personalizado na Malha do Azure Service Fabric

Com a Ajuda da meta `azure-sfmesh:deploy`, você pode implantar para o Ambiente de Malha do Azure Service Fabric executando o comando a seguir:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Cria um grupo de recursos chamado `todoapprg` se ele não existir.
- Cria um modelo JSON do Azure Resource Manager, mesclando os YAMLs. 
- Implanta o JSON ao Ambiente de Malha do Azure Service Fabric.