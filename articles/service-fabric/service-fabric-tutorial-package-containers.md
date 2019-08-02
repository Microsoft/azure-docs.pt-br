---
title: Empacotar e implantar contêineres como um aplicativo do Service Fabric no Azure | Microsoft Docs
description: Neste tutorial, você aprende a gerar uma definição de aplicativo do Azure Service Fabric usando o Yeoman e a empacotar o aplicativo.
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: chackdan
editor: suhuruli
tags: servicefabric
keywords: Docker, Contêineres, Microservices, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: b4931b62aaf04db2ee4ff6f4a5e2b96274b8af88
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385046"
---
# <a name="tutorial-package-and-deploy-containers-as-a-service-fabric-application-using-yeoman"></a>Tutorial: Empacotar e implantar contêineres como um aplicativo do Service Fabric usando Yeoman

Este tutorial é a parte dois de uma série. Neste tutorial, uma ferramenta geradora de modelos (Yeoman) é usada para gerar uma definição de aplicativo do Service Fabric. Este aplicativo pode ser usado para implantar contêineres no Service Fabric. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Instalar o Yeoman
> * Criar um pacote de aplicativos usando o Yeoman
> * Definir configurações no pacote de aplicativos para uso com contêineres
> * Compilar o aplicativo
> * Implantar e executar o aplicativo
> * Limpar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

* As imagens de contêiner enviadas por push ao Registro de Contêiner do Azure criadas na [Parte 1](service-fabric-tutorial-create-container-images.md) desta série de tutoriais são usadas.
* O ambiente de desenvolvimento do Linux está [configurado](service-fabric-tutorial-create-container-images.md).

## <a name="install-yeoman"></a>Instalar o Yeoman

O Service Fabric fornece ferramentas de scaffolding para ajudar a criar aplicativos no terminal usando o gerador de modelos Yeoman. Siga as etapas abaixo para garantir que você tem o gerador de modelos Yeoman.

1. Instale nodejs e o NPM em seu computador. Observe que os usuários do Mac OSX precisarão usar o gerenciador de pacotes Homebrew

    ```bash
    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash
    nvm install node 
    ```
2. Instalar o gerador de modelos Yeoman em seu computador com base no NPM

    ```bash
    npm install -g yo
    ```
3. Instalar o gerador de contêineres Yeoman do Service Fabric

    ```bash 
    npm install -g generator-azuresfcontainer
    ```

## <a name="package-a-docker-image-container-with-yeoman"></a>Empacotar um recipiente de imagem do Docker com o Yeoman

1. Para criar um aplicativo de contêiner do Service Fabric, no diretório 'container-tutorial' do repositório clonado, execute o seguinte comando.

    ```bash
    yo azuresfcontainer
    ```
2. Digite "TestContainer" para nomear seu aplicativo
3. Digite "azurevotefront" para nomear o serviço de aplicativo.
4. Forneça o caminho da imagem de contêiner no ACR para o repositório frontend – por exemplo, '\<acrName>.azurecr.io/azure-vote-front:v1'. O campo \<acrName > deve ser igual ao valor que foi usado no tutorial anterior.
5. Pressione Enter para deixar a seção Comandos vazia.
6. Especifique uma contagem de instâncias de "1".

O conteúdo a seguir mostra a entrada e a saída da execução do comando yo:

```bash
? Name your application TestContainer
? Name of the application service: azurevotefront
? Input the Image Name: <acrName>.azurecr.io/azure-vote-front:v1
? Commands:
? Number of instances of guest container application: 1
   create TestContainer/TestContainer/ApplicationManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/ServiceManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/config/Settings.xml
   create TestContainer/TestContainer/azurevotefrontPkg/code/Dummy.txt
   create TestContainer/install.sh
   create TestContainer/uninstall.sh
```

Para adicionar outro serviço de contêiner a um aplicativo já criado usando o Yeoman, execute as seguintes etapas:

1. Altere o nível de um diretório para o diretório **TestContainer**, por exemplo, *. / TestContainer*
2. Execute o `yo azuresfcontainer:AddService`
3. Chame o serviço de 'azurevoteback'
4. Forneça o caminho da imagem de contêiner para Redis - 'alpine:redis'
5. Pressione Enter para deixar a seção Comandos vazia
6. Especifique a contagem de instâncias como "1".

As entradas para adicionar o serviço usado são todas mostradas:

```bash
? Name of the application service: azurevoteback
? Input the Image Name: alpine:redis
? Commands:
? Number of instances of guest container application: 1
   create TestContainer/azurevotebackPkg/ServiceManifest.xml
   create TestContainer/azurevotebackPkg/config/Settings.xml
   create TestContainer/azurevotebackPkg/code/Dummy.txt
```

Para o restante deste tutorial, estamos trabalhando no diretório **TestContainer**. Por exemplo, *./TestContainer/TestContainer*. O conteúdo deste diretório deve ser da seguinte maneira.

```bash
$ ls
ApplicationManifest.xml azurevotefrontPkg azurevotebackPkg
```

## <a name="configure-the-application-manifest-with-credentials-for-azure-container-registry"></a>Configurar o manifesto do aplicativo com as credenciais para o Registro de Contêiner do Azure

Para que o Service Fabric efetuar pull das imagens de contêiner do Registro de Contêiner do Azure, é necessário fornecer as credenciais no **ApplicationManifest.xml**.

Entre na instância do ACR. Use o comando **az acr login** para concluir a operação. Forneça o nome exclusivo fornecido para o Registro de contêiner quando ele foi criado.

```bash
az acr login --name <acrName>
```

O comando retorna uma mensagem de **Logon bem-sucedido** quando é concluído.

Em seguida, execute o seguinte comando para obter a senha do Registro de contêiner. Essa senha é usada pelo Service Fabric para autenticar-se com o ACR para efetuar pull das imagens de contêiner.

```bash
az acr credential show -n <acrName> --query passwords[0].value
```

No **ApplicationManifest.xml**, adicione o snippet de código no elemento **ServiceManifestImport** para o serviço de front-end. Insira seu **acrName** para o campo **AccountName** e a senha retornada do comando anterior é usada para o campo **Senha**. Um **ApplicationManifest.xml** completo é fornecido no final deste documento.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="<acrName>" Password="<password>" PasswordEncrypted="false"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Configurar o mapeamento de porta para porta do host de comunicação e do contêiner

### <a name="configure-communication-port"></a>Configurar a porta de comunicação

Configure um ponto de extremidade HTTP para que os clientes possam se comunicar com o serviço. Abra o arquivo *./TestContainer/azurevotefrontPkg/ServiceManifest.xml* e declare um recurso de ponto de extremidade no elemento **ServiceManifest**.  Adicione protocolo, porta e nome. Para este tutorial, o serviço escuta na porta 80. O snippet a seguir está sob a marca *ServiceManifest* no recurso.

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```

Da mesma forma, modifique o Manifesto do serviço para o serviço de back-end. Abra o *./TestContainer/azurevotebackPkg/ServiceManifest.xml* e declare um recurso de ponto de extremidade no elemento **ServiceManifest**. Para este tutorial, o padrão de redis de 6379 é mantido. O snippet a seguir está sob a marca *ServiceManifest* no recurso.

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
  </Endpoints>
</Resources>
```

Ao fornecer o **UriScheme**, o ponto de extremidade do contêiner é registrado automaticamente no serviço de nomenclatura do Service Fabric para capacidade de descoberta. Um arquivo de exemplo ServiceManifest.xml completo para o serviço de back-end é fornecido no final deste artigo como um exemplo.

### <a name="map-container-ports-to-a-service"></a>Mapear portas de contêiner para um serviço

Para expor os contêineres no cluster, também é necessário criar uma associação de porta no 'ApplicationManifest.xml'. A política **PortBinding** referencia os **Pontos de extremidade** definidos nos arquivos **ServiceManifest.xml**. Solicitações de entrada para esses pontos de extremidade são mapeadas para as portas de contêiner abertas e limitadas aqui. No arquivo **ApplicationManifest.xml**, adicione o código a seguir para associar a porta 80 e 6379 aos pontos de extremidade. Um **ApplicationManifest.xml** completo está disponível no final deste documento.

```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
</ContainerHostPolicies>
```

```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
</ContainerHostPolicies>
```

### <a name="add-a-dns-name-to-the-backend-service"></a>Adicionar um nome DNS para o serviço de back-end

Para o Service Fabric atribuir esse nome DNS ao serviço de back-end, o nome precisa ser especificado no **ApplicationManifest.xml**. Adicione o atributo **ServiceDnsName** ao elemento **Serviço** conforme mostrado:

```xml
<Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
  <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
    <SingletonPartition/>
  </StatelessService>
</Service>
```

O serviço de front-end lê uma variável de ambiente para saber o nome DNS da instância do Redis. Essa variável de ambiente já está definida no Dockerfile que foi usado para gerar a imagem do Docker e nenhuma ação precisa ser tomada aqui.

```Dockerfile
ENV REDIS redisbackend.testapp
```

O snippet de código a seguir ilustra como o código Python front-end seleciona a variável de ambiente descrita no Dockerfile. Nenhuma ação precisa ser tomada aqui.

```python
# Get DNS Name
redis_server = os.environ['REDIS']

# Connect to the Redis store
r = redis.StrictRedis(host=redis_server, port=6379, db=0)
```

Neste ponto no tutorial, o modelo para um aplicativo de Pacote de serviços está disponível para implantação em um cluster. No tutorial subsequente, esse aplicativo será implantado e executado em um cluster do Service Fabric.

## <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric

Para implantar o aplicativo no Azure, é necessário que um cluster do Service Fabric execute o aplicativo. Os comandos a seguir criam um cluster de cinco nós no Azure.  Os comandos também criam um certificado autoassinado, adicionam-no a um cofre de chaves e baixam o certificado localmente como um arquivo PEM. O novo certificado é usado para proteger o cluster quando ele é implantado e é usado para autenticar clientes.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName="containertestcluster" 
ClusterName="containertestcluster" 
Location="eastus" 
Password="q6D7nN%6ck@6" 
Subject="containertestcluster.eastus.cloudapp.azure.com" 
VaultName="containertestvault" 
VmPassword="Mypa$$word!321"
VmUserName="sfadminuser"

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  
# The certificate is downloaded locally as a PEM file.
az sf cluster create --resource-group $ResourceGroupName --location $Location \ 
--certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject \ 
--cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName \ 
--vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> O serviço de front-end da Web está configurado para escutar o tráfego de entrada na porta 80. Por padrão, a porta 80 está aberta no seu cluster de VMs e no Azure Load Balancer.
>

Para obter mais informações sobre como criar seu próprio cluster, consulte [Criar um cluster do Service Fabric no Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

## <a name="build-and-deploy-the-application-to-the-cluster"></a>Criar e implantar o aplicativo no cluster

É possível implantar o aplicativo no cluster do Azure usando a CLI do Service Fabric. Se a CLI do Service Fabric não estiver instalada em seu computador, siga as instruções [aqui](service-fabric-get-started-linux.md#set-up-the-service-fabric-cli) para instalá-la.

Conectar-se ao cluster do Service Fabric no Azure. Substitua o ponto de extremidade de exemplo pelos seus próprios. O ponto de extremidade deve ser uma URL completa semelhante à mostrada abaixo.  O arquivo PEM é o certificado autoassinado anteriormente.

```bash
sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
```

Use o script de instalação fornecido no diretório **TestContainer** para copiar o pacote de aplicativos para o repositório de imagens do cluster, registrar o tipo de aplicativo e criar uma instância do aplicativo.

```bash
./install.sh
```

Abra um navegador e navegue até o Service Fabric Explorer em http:\//containertestcluster.eastus.cloudapp.azure.com:19080/Explorer. Expanda o nó Aplicativos e observe que há uma entrada para o seu tipo de aplicativo e outra para a instância.

![Service Fabric Explorer][sfx]

Para se conectar ao aplicativo em execução, abra um navegador da Web e acesse a URL do cluster – por exemplo, http:\//containertestcluster.eastus.cloudapp.azure.com:80. Você deve ver o aplicativo de votação na interface do usuário da Web.

![votingapp][votingapp]

## <a name="clean-up"></a>Limpar

Use o script de desinstalação fornecido com o modelo para excluir a instância do aplicativo no cluster e cancelar o registro do tipo de aplicativo. Esse comando leva algum tempo para limpar a instância e o comando 'install.sh' não pode ser executado imediatamente após esse script.

```bash
./uninstall.sh
```

## <a name="examples-of-completed-manifests"></a>Exemplos de manifestos concluídos

### <a name="applicationmanifestxml"></a>ApplicationManifest.xml

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ApplicationManifest ApplicationTypeName="TestContainerType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotefrontPkg" ServiceManifestVersion="1.0.0"/>
    <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myaccountname" Password="<password>" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
    </ContainerHostPolicies>
        </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotebackPkg" ServiceManifestVersion="1.0.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
          <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
        </ContainerHostPolicies>
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="azurevotefront">
      <StatelessService ServiceTypeName="azurevotefrontType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
    <Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
      <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

### <a name="front-end-servicemanifestxml"></a>ServiceManifest.xml de front-end

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotefrontPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotefrontType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>

   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>acrName.azurecr.io/azure-vote-front:v1</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables>
      </EnvironmentVariables>
   </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>

 </ServiceManifest>
```

### <a name="redis-servicemanifestxml"></a>ServiceManifest.xml do Redis

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotebackPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotebackType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>

   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>alpine:redis</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables>
      </EnvironmentVariables>
   </CodePackage>
     <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, vários contêineres foram empacotados em um aplicativo do Service Fabric usando o Yeoman. Este aplicativo foi implantado e executado em um cluster do Service Fabric. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Instalar o Yeoman
> * Criar um pacote de aplicativos usando o Yeoman
> * Definir configurações no pacote de aplicativos para uso com contêineres
> * Compilar o aplicativo
> * Implantar e executar o aplicativo
> * Limpar o aplicativo

Passe para o próximo tutorial para saber mais sobre o failover e o dimensionamento do aplicativo no Service Fabric.

> [!div class="nextstepaction"]
> [Saiba mais sobre aplicativos de failover e de dimensionamento](service-fabric-tutorial-containers-failover.md)

[votingapp]: ./media/service-fabric-tutorial-deploy-run-containers/votingapp.png
[sfx]: ./media/service-fabric-tutorial-deploy-run-containers/containerspackagetutorialsfx.png