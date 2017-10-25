---
title: "Criar um aplicativo de contêiner do Azure Service Fabric no Linux | Microsoft Docs"
description: "Crie seu primeiro aplicativo de contêiner do Linux no Azure Service Fabric.  Crie uma imagem do Docker com o seu aplicativo, envie a imagem para um registro de contêiner por push, crie e implante um aplicativo de contêiner do Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/04/2017
ms.author: ryanwi
ms.openlocfilehash: 3c5a6ec70e1041d43b549c8e5a5416a9a65728bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-service-fabric-container-application-on-linux"></a>Criar seu primeiro aplicativo de contêiner do Service Fabric no Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

A execução de um aplicativo existente em um contêiner do Linux em um cluster do Service Fabric não requer alterações no seu aplicativo. Este artigo mostra como criar uma imagem do Docker que contém um aplicativo de web Python [Flask](http://flask.pocoo.org/) e a implantá-lo em um cluster do Service Fabric.  Você também compartilhará seu aplicativo em contêineres pelo [Registro de Contêiner do Azure](/azure/container-registry/).  Este artigo pressupõe uma compreensão básica sobre o Docker. Saiba mais sobre o Docker lendo a [Visão geral de Docker](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Pré-requisitos
* Um computador de desenvolvimento executando:
  * [Ferramentas e SDK do Service Fabric](service-fabric-get-started-linux.md).
  * [Docker CE para Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [CLI do Service Fabric](service-fabric-cli.md)

* Um registro no Registro de Contêiner do Azure - [Crie um registro de contêiner](../container-registry/container-registry-get-started-portal.md) em sua assinatura do Azure. 

## <a name="define-the-docker-container"></a>Defina o contêiner Docker
Crie uma imagem com base na [imagem do Python](https://hub.docker.com/_/python/) localizada no Hub do Docker. 

Defina o contêiner do Docker em um Dockerfile. O Dockerfile contém instruções para configurar o ambiente do seu contêiner, carregar o aplicativo que você deseja executar e mapear portas. O Dockerfile é a entrada para o comando `docker build`, que cria a imagem. 

Crie um diretório vazio e crie o arquivo *Dockerfile* (sem extensão de arquivo). Adicione o seguinte ao *Dockerfile* e salve as alterações:

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Leia a [referência do Dockerfile](https://docs.docker.com/engine/reference/builder/) para saber mais informações.

## <a name="create-a-simple-web-application"></a>Criar um aplicativo Web simples
Crie um aplicativo Web Flask que escuta a porta 80 retornar "Olá, Mundo!".  No mesmo diretório, crie o arquivo *requirements.txt*.  Adicione o seguinte e salve as alterações:
```
Flask
```

Crie também o arquivo *app.py* e adicione o seguinte:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    
    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="build-the-image"></a>Criar a imagem
Execute o comando `docker build` para criar a imagem que executa o seu aplicativo web. Abra uma janela do PowerShell e navegue até *c:\temp\helloworldapp*. Execute o comando a seguir:

```bash
docker build -t helloworldapp .
```

Esse comando cria a nova imagem usando as instruções no seu Dockerfile de nomeando (-t marcação) a imagem "helloworldapp". A criação de uma imagem puxa a imagem base do Hub do Docker e cria uma nova imagem que adiciona seu aplicativo sobre a imagem base.  

Depois de concluir o comando de compilação, execute o comando `docker images` para ver informações sobre a nova imagem:

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>Executar o aplicativo localmente
Verifique se seu aplicativo em contêineres está sendo executado localmente antes de enviar a ele o registro de contêiner.  

Execute o aplicativo, mapeando a porta 4000 do computador para a porta 80 exposta do contêiner:

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

*name* fornece um nome para o contêiner em execução (em vez da ID do contêiner).

Conectar-se ao contêiner em execução.  Abra um navegador da Web apontando para o endereço IP retornado na porta 4000, por exemplo "http://localhost:4000". Você deve ver o cabeçalho "Olá, Mundo!" ser exibido no navegador.

![Olá, Mundo!][hello-world]

Para interromper o contêiner, execute:

```bash
docker stop my-web-site
```

Exclua o contêiner do seu computador de desenvolvimento:

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>Enviar a imagem para o registro de contêiner
Depois de verificar que o aplicativo é executado no Docker, envie a imagem por push para o registro no Registro de Contêiner do Azure.

Execute `docker login` para fazer logon em seu registro de contêiner as [credenciais de registro](../container-registry/container-registry-authentication.md).

O seguinte exemplo passa a ID e senha de uma [entidade de serviço](../active-directory/active-directory-application-objects.md) do Azure Active Directory. Por exemplo, você pode atribuir uma entidade de serviço ao registro para um cenário de automação.  Ou pode fazer logon usando o nome de usuário e a senha do registro.

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

O comando a seguir cria uma marca ou alias imagem, com um caminho totalmente qualificado para o registro. Este exemplo coloca a imagem no namespace `samples` para evitar confusão na raiz do registro.

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Enviar a imagem para o eu registro de contêiner:

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>Empacotar a imagem do Docker com o Yeoman
O SDK do Service Fabric para Linux inclui um gerador [Yeoman](http://yeoman.io/) que facilita a criação de seu aplicativo e a adição de uma imagem de contêiner. Vamos usar o Yeoman para criar um aplicativo, com um único contêiner do Docker, chamado de *SimpleContainerApp*.

Para criar um aplicativo de contêiner do Service Fabric, abra uma janela do terminal e execute `yo azuresfcontainer`.  

Nomeie seu aplicativo (por exemplo, "mycontainer") e nomeie o serviço de aplicativo (por exemplo, "myservice").

Para o nome da imagem, forneça a URL da imagem de contêiner em um registro de contêiner (por exemplo, "myregistry.azurecr.io/samples/helloworldapp"). 

Como essa imagem tem um ponto de entrada de carga de trabalho-ponto definido, não é necessário especificar explicitamente os comandos de entrada (comandos executados dentro do contêiner, o que manterá o contêiner em execução depois da inicialização). 

Especifique a contagem de instâncias como "1".

![Gerador de Yeoman do Service Fabric para contêineres][sf-yeoman]

## <a name="configure-port-mapping-and-container-repository-authentication"></a>Configurar a autenticação de repositório de contêiner e o mapeamento de porta
O serviço em contêineres precisa de um ponto de extremidade para comunicação.  Agora, você pode adicionar o protocolo, a porta e o tipo a um `Endpoint` no arquivo ServiceManifest.xml, sob a marca ‘Resources’. Para este artigo, o serviço em contêineres escuta na porta 4000: 

```xml

<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
 ```
 
Ao fornecer `UriScheme`, o ponto de extremidade do contêiner é registrado automaticamente no serviço de Nomenclatura do Service Fabric para capacidade de descoberta. Um arquivo de exemplo servicemanifest. XML completo é fornecido no final deste artigo. 

Configure o mapeamento de porta, da porta para o host, do contêiner especificando uma política `PortBinding` no `ContainerHostPolicies` do arquivo ApplicationManifest.xml.  Para este artigo, `ContainerPort` é 80 (o contêiner expõe a porta 80, conforme especificado no Dockerfile) e `EndpointRef` é "myServiceTypeEndpoint" (o ponto de extremidade definido no manifesto do serviço).  As solicitações de entrada para o serviço na porta 4000 são mapeadas para a porta 80 no contêiner.  Se o seu contêiner precisar autenticar com um repositório privado, adicione `RepositoryCredentials`.  Para este artigo, adicione o nome da conta e a senha para o registro de contêiner de myregistry.azurecr.io. Certifique-se de que a política é adicionada sob a marca 'ServiceManifestImport' correspondente ao pacote de serviço certo.

```xml
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0" />
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
   </ServiceManifestImport>
``` 

## <a name="build-and-package-the-service-fabric-application"></a>Compilar e empacotar o aplicativo do Service Fabric
Os modelos Yeoman do Service Fabric incluem um script de compilação para [Gradle](https://gradle.org/), que pode ser usado para compilar o aplicativo no terminal. Para compilar e empacotar o aplicativo, execute o seguinte:

```bash
cd mycontainer
gradle
```

## <a name="deploy-the-application"></a>Implantar o aplicativo
Após a compilação do aplicativo, você pode implantá-lo no cluster local usando a CLI do Service Fabric.

Conectar-se ao cluster local do Service Fabric.

```bash
sfctl cluster select --endpoint http://localhost:19080
```

Use o script de instalação fornecido no modelo para copiar o pacote de aplicativo no repositório de imagens do cluster, registrar o tipo de aplicativo e criar uma instância do aplicativo.

```bash
./install.sh
```

Abra um navegador e navegue até o Service Fabric Explorer em http://localhost:19080/Explorer (substitua localhost pelo IP privado da VM se estiver usando Vagrant no Mac OS X). Expanda o nó Aplicativos e observe que agora há uma entrada para o seu tipo de aplicativo e outra para a primeira instância desse tipo.

Conectar-se ao contêiner em execução.  Abra um navegador da Web apontando para o endereço IP retornado na porta 4000, por exemplo "http://localhost:4000". Você deve ver o cabeçalho "Olá, Mundo!" ser exibido no navegador.

![Olá, Mundo!][hello-world]

## <a name="clean-up"></a>Limpar
Use o script de desinstalação fornecido com o modelo para excluir a instância do aplicativo no cluster de desenvolvimento local e cancelar o registro do tipo de aplicativo.

```bash
./uninstall.sh
```

Depois que você enviar a imagem para o registro de contêiner, você pode excluir a imagem local do seu computador de desenvolvimento:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Exemplo completo de manifestos de serviço e aplicativo do Service Fabric
Aqui estão os manifestos de aplicativo e serviço completos usados neste artigo.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>Adicionando mais serviços a um aplicativo existente

Para adicionar outro serviço de contêiner a um aplicativo já criado usando o yeoman, execute as seguintes etapas:

1. Altere o diretório para a raiz do aplicativo existente.  Por exemplo, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` é o aplicativo criado pelo Yeoman.
2. Execute o `yo azuresfcontainer:AddService`

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>Configurar o intervalo de tempo antes do contêiner ser forçado a terminar

Você pode configurar um intervalo de tempo para a execução aguardar antes do contêiner ser removido após a exclusão do serviço (ou um movimento para outro nó) ter iniciado. Configurar o intervalo de tempo envia o comando `docker stop <time in seconds>` para o contêiner.   Para obter mais detalhes, consulte [parar docker](https://docs.docker.com/engine/reference/commandline/stop/). O intervalo de tempo de espera é especificado na seção `Hosting`. O trecho de manifesto do cluster a seguir mostra como definir o intervalo de espera:

```xml
{
        "name": "Hosting",
        "parameters": [
          {
            "ContainerDeactivationTimeout": "10",
          ...
          }
        ]
}
```
O intervalo de tempo padrão é definido para 10 segundos. Como essa configuração é dinâmica, uma configuração somente atualiza no cluster que atualiza no tempo limite. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Configurar a execução para remover as imagens de contêiner não utilizadas

Você pode configurar o cluster do Service Fabric para remover as imagens de contêiner não utilizadas do nó. Essa configuração permite que o espaço em disco seja recapturado se houver imagens de contêiner demais no nó.  Para habilitar esse recurso, atualize a `Hosting` seção no manifesto do cluster, conforme mostrado no trecho a seguir: 


```xml
{
        "name": "Hosting",
        "parameters": [
          {
            "PruneContainerImages": “True”,
            "ContainerImagesToSkip": "microsoft/windowsservercore|microsoft/nanoserver|…",
          ...
          }
        ]
} 
```

Para as imagens que não devem ser excluídas, você pode especificá-las no parâmetro `ContainerImagesToSkip`. 


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre como executar [contêineres no Service Fabric](service-fabric-containers-overview.md).
* Leia o tutorial [Como implantar um aplicativo .NET em um contêiner](service-fabric-host-app-in-a-container.md).
* Leia mais sobre o [ciclo de vida do aplicativo](service-fabric-application-lifecycle.md) do Service Fabric.
* Confira os [exemplos de código do Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-containers) no GitHub.

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png
