---
title: Como criar um aplicativo de contêiner do Azure Service Fabric | Microsoft Docs
description: Crie seu primeiro aplicativo de contêiner do Windows no Azure Service Fabric. Compile uma imagem do Docker com o seu aplicativo Python, envie a imagem por push para um registro de contêiner, compile e implante um aplicativo de contêiner do Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: jpconnock
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/25/2019
ms.author: aljo
ms.openlocfilehash: 70dc458e341024797761262cd9a4fd1b3eb23ec3
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359783"
---
# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Como criar seu primeiro aplicativo de contêiner do Service Fabric no Windows

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Executar um aplicativo existente em um contêiner do Windows em um cluster do Service Fabric não requer alterações no seu aplicativo. Este artigo mostra como criar uma imagem do Docker que contém um aplicativo Web do Python [Flask](http://flask.pocoo.org/) e como implantá-lo em um cluster do Service Fabric em execução no seu computador local. Você também compartilhará seu aplicativo em contêineres pelo [Registro de Contêiner do Azure](/azure/container-registry/). Este artigo pressupõe uma compreensão básica sobre o Docker. Saiba mais sobre o Docker lendo a [Visão geral de Docker](https://docs.docker.com/engine/understanding-docker/).

> [!NOTE]
> Este artigo se aplica a um ambiente de desenvolvimento do Windows.  O tempo de execução do cluster do Service Fabric e o tempo de execução do Docker devem estar em execução no mesmo sistema operacional.  Você não pode executar contêineres do Windows em um cluster do Linux.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Um computador de desenvolvimento executando:
  * Visual Studio 2015 ou Visual Studio 2019.
  * [Ferramentas e SDK do Service Fabric](service-fabric-get-started.md).
  *  Docker para Windows. [Obter Docker CE para o Windows (estável)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Depois de instalar e iniciar o Docker, clique no ícone de bandeja e selecione **Alternar para contêineres do Windows**. Essa etapa é necessária para executar imagens do Docker com base no Windows.

* Um cluster do Windows com três ou mais nós em execução no Windows Server com contêineres. 

  Neste artigo, a versão (build) do Windows Server com contêineres em execução em nós do cluster deve corresponder em seu computador de desenvolvimento. Isso ocorre porque você compila a imagem do docker em seu computador de desenvolvimento e há restrições de compatibilidade entre versões do sistema operacional do contêiner e o sistema operacional do host no qual ele foi implantado. Para obter mais informações, consulte [Compatibilidade do sistema operacional do contêiner e do sistema operacional do host do Windows Server](#windows-server-container-os-and-host-os-compatibility). 
  
Para determinar a versão do Windows Server com contêineres que você precisa para seu cluster, execute o comando `ver` em um prompt de comando do Windows no computador de desenvolvimento:

* Se a versão contém *x.x.14323.x*, selecione *WindowsServer 2016-Datacenter-with-Containers* para o sistema operacional ao [criar um cluster](service-fabric-cluster-creation-via-portal.md).
  * Se a versão contém *x.x.16299.x*, selecione *WindowsServerSemiAnnual Datacenter-Core-1709-with-Containers* para o sistema operacional ao [criar um cluster](service-fabric-cluster-creation-via-portal.md).

* Um registro no Registro de Contêiner do Azure - [Crie um registro de contêiner](../container-registry/container-registry-get-started-portal.md) em sua assinatura do Azure.

> [!NOTE]
> A implantação de contêineres em um cluster do Service Fabric em execução no Windows 10 tem suporte.  Consulte [este artigo](service-fabric-how-to-debug-windows-containers.md) para obter informações sobre como configurar o Windows 10 para executar contêineres do Windows.
>   

> [!NOTE]
> O Service Fabric versões 6.2 e posteriores dá suporte para implantação de contêineres em clusters em execução no Windows Server versão 1709.  
> 

## <a name="define-the-docker-container"></a>Defina o contêiner Docker

Crie uma imagem com base na [imagem do Python](https://hub.docker.com/_/python/) localizada no Hub do Docker.

Especifique o contêiner do Docker em um Dockerfile. O Dockerfile é composto por instruções para configurar o ambiente do seu contêiner, carregar o aplicativo que você deseja executar e mapear portas. O Dockerfile é a entrada para o comando `docker build`, que cria a imagem.

Crie um diretório vazio e crie o arquivo *Dockerfile* (sem extensão de arquivo). Adicione o seguinte ao *Dockerfile* e salve as alterações:

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Leia a [referência do Dockerfile](https://docs.docker.com/engine/reference/builder/) para saber mais informações.

## <a name="create-a-basic-web-application"></a>Criar um aplicativo Web básico
Crie um aplicativo Web Flask que escuta a porta 80 retornar `Hello World!`. No mesmo diretório, crie o arquivo *requirements.txt*. Adicione o seguinte e salve as alterações:
```
Flask
```

Crie também o arquivo *app.py* e adicione o seguinte snippet:

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello():

    return 'Hello World!'


if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

<a id="Build-Containers"></a>
## <a name="build-the-image"></a>Criar a imagem
Execute o comando `docker build` para criar a imagem que executa o seu aplicativo web. Abra uma janela do PowerShell e acesse o diretório que contém o Dockerfile. Execute o seguinte comando:

```
docker build -t helloworldapp .
```

Esse comando cria a nova imagem usando as instruções no seu Dockerfile de nomeando (marcação -t) a imagem `helloworldapp`. Para criar uma imagem de contêiner, primeiro a imagem base é baixada do Hub do Docker ao qual o aplicativo foi adicionado. 

Depois de concluir o comando de compilação, execute o comando `docker images` para ver informações sobre a nova imagem:

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>Executar o aplicativo localmente
Verifique a imagem localmente antes de enviá-la ao registro de contêiner. 

Executar o aplicativo:

```
docker run -d --name my-web-site helloworldapp
```

*name* fornece um nome para o contêiner em execução (em vez da ID do contêiner).

Depois que o contêiner iniciar, localize seu endereço IP para que você pode se conectar ao seu contêiner em execução em um navegador:
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

Se esse comando não retorna nada, execute o seguinte comando e inspecione o elemento **NetworkSettings**->**Networks** para ver o endereço IP:
```
docker inspect my-web-site
```

Conectar-se ao contêiner em execução. Abra um navegador da Web apontando para o endereço IP retornado, por exemplo, "\/http:/172.31.194.61". Você deve ver o cabeçalho "Olá, Mundo!" ser exibido no navegador.

Para interromper o contêiner, execute:

```
docker stop my-web-site
```

Exclua o contêiner do seu computador de desenvolvimento:

```
docker rm my-web-site
```

<a id="Push-Containers"></a>
## <a name="push-the-image-to-the-container-registry"></a>Enviar a imagem para o registro de contêiner

Depois de verificar que o contêiner é executado na máquina de desenvolvimento, envie a imagem para seu registro no Registro de Contêiner do Azure.

Execute ``docker login`` para entrar no registro de contêiner com suas [credenciais de registro](../container-registry/container-registry-authentication.md).

O seguinte exemplo passa a ID e senha de uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md) do Azure Active Directory. Por exemplo, você pode atribuir uma entidade de serviço ao registro para um cenário de automação. Ou, você pode entrar usando o nome de usuário e a senha do registro.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

O comando a seguir cria uma marca ou alias imagem, com um caminho totalmente qualificado para o registro. Este exemplo coloca a imagem no namespace ```samples``` para evitar confusão na raiz do registro.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Enviar a imagem para o eu registro de contêiner:

```
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="create-the-containerized-service-in-visual-studio"></a>Crie o serviço em contêineres no Visual Studio
As ferramentas e o SDK do Service Fabric oferecem um modelo de serviço para ajudar você a criar um aplicativo em contêineres.

1. Inicie o Visual Studio. Selecione **Arquivo** > **Novo** > **Projeto**.
2. Selecione **Aplicativo do Service Fabric**, nomeie-o como "MyFirstContainer" e clique em **OK**.
3. Selecione **Contêiner** na lista de **modelos de serviço**.
4. Em **Nome da imagem** insira "myregistry.azurecr.io/samples/helloworldapp", a imagem é enviada para o seu repositório de contêiner.
5. Dê um nome ao seu serviço e clique em **OK**.

## <a name="configure-communication"></a>Configurar a comunicação
O serviço em contêineres precisa de um ponto de extremidade para comunicação. Adicione um elemento `Endpoint` com o protocolo, a porta e o tipo ao arquivo ServiceManifest.xml. Neste exemplo, uma porta fixa 8081 é usada. Se nenhuma porta for especificada, uma porta aleatória do intervalo de portas de aplicativo é escolhida. 

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```
> [!NOTE]
> Endpoints adicionais para um serviço podem ser adicionados declarando elementos EndPoint adicionais com valores de propriedade aplicáveis. Cada porta só pode declarar um valor de protocolo.

Definindo um ponto de extremidade, o Service Fabric publica o ponto de extremidade para o Serviço de nomeação. Outros serviços em execução no cluster podem resolver este contêiner. Você também pode executar a comunicação de contêiner para contêiner usando o [proxy reverso](service-fabric-reverseproxy.md). A comunicação é realizada fornecendo a porta de escuta de proxy reverso HTTP e o nome dos serviços com os quais você deseja se comunicar como variáveis de ambiente.

O serviço está ouvindo em uma porta específica (8081 neste exemplo). Quando o aplicativo for implantado para um cluster no Azure, o cluster e o aplicativo executam atrás de um balanceador de carga do Azure. A porta do aplicativo deve estar aberta no balanceador de carga do Microsoft Azure para que o tráfego de entrada pode obter por meio para o serviço.  Você pode abrir essa porta no balanceador de carga do Microsoft Azure usando um [script do PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) ou o [portal do Azure](https://portal.azure.com).

## <a name="configure-and-set-environment-variables"></a>Configurar e definir as variáveis de ambiente
Variáveis de ambiente podem ser especificadas para cada pacote de códigos no manifesto do serviço. Esse recurso está disponível para todos os serviços, independentemente de eles serem implantados como contêineres ou processos ou executáveis convidados. Você pode substituir valores de variáveis de ambiente no manifesto do aplicativo ou especificá-los durante a implantação como parâmetros de aplicativo.

O snippet XML do manifesto do serviço a seguir mostra um exemplo de como especificar variáveis de ambiente para um pacote de códigos:
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

Essas variáveis de ambiente podem ser substituídas no manifesto do aplicativo:

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <EnvironmentOverrides CodePackageRef="FrontendService.Code">
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>Configurar mapeamento de porta para host e descoberta de contêiner para contêiner
Configure uma porta de host usada para se comunicar com o contêiner. A associação de porta mapeia a porta na qual o serviço está escutando dentro do contêiner para uma porta no host. Adicionar um elemento `PortBinding` no elemento `ContainerHostPolicies` do arquivo ApplicationManifest.xml. Para este artigo, `ContainerPort` é 80 (o contêiner expõe a porta 80, conforme especificado no Dockerfile) e `EndpointRef` é "Guest1TypeEndpoint" (o ponto de extremidade definido anteriormente no manifesto do serviço). As solicitações de entrada para o serviço na porta 8081 são mapeadas para a porta 80 no contêiner.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```
> [!NOTE]
> PortBindings adicionais para um serviço podem ser adicionados, declarando elementos PortBinding adicionais com valores de propriedade aplicável.

## <a name="configure-container-registry-authentication"></a>Definir autenticação do registro de contêiner

Configurar a autenticação de registro de contêiner adicionando `RepositoryCredentials` a `ContainerHostPolicies` do arquivo ApplicationManifest.xml. Adicione a conta e a senha para o registro de contêiner myregistry.azurecr.io, o que permite ao serviço baixar a imagem de contêiner do repositório.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

É recomendável que você criptografe a senha do repositório usando um certificado de criptografia que é implantado em todos os nós do cluster. Quando o Service Fabric implanta o pacote de serviço para o cluster, o certificado de criptografia é usado para descriptografar o texto cifrado. O cmdlet Invoke-ServiceFabricEncryptText é usado para criar o texto cifrado para a senha, que é adicionado ao arquivo ApplicationManifest.xml.

O script a seguir cria um novo certificado autoassinado e o exporta para um arquivo PFX. O certificado é importado para um cofre de chaves existente e, em seguida, implantado para o cluster do Service Fabric.

```powershell
# Variables.
$certpwd = ConvertTo-SecureString -String "Pa$$word321!" -Force -AsPlainText
$filepath = "C:\MyCertificates\dataenciphermentcert.pfx"
$subjectname = "dataencipherment"
$vaultname = "mykeyvault"
$certificateName = "dataenciphermentcert"
$groupname="myclustergroup"
$clustername = "mycluster"

$subscriptionId = "subscription ID"

Login-AzAccount

Select-AzSubscription -SubscriptionId $subscriptionId

# Create a self signed cert, export to PFX file.
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject $subjectname -Provider 'Microsoft Enhanced Cryptographic Provider v1.0' `
| Export-PfxCertificate -FilePath $filepath -Password $certpwd

# Import the certificate to an existing key vault. The key vault must be enabled for deployment.
$cer = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certificateName -FilePath $filepath -Password $certpwd

Set-AzKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $groupname -EnabledForDeployment
Add-AzServiceFabricApplicationCertificate -ResourceGroupName $groupname -Name $clustername -SecretIdentifier $cer.SecretId
```
Criptografar a senha usando o cmdlet [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps).

```powershell
$text = "=P==/==/=8=/=+u4lyOB=+=nWzEeRfF="
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint $cer.Thumbprint -Text $text -StoreLocation Local -StoreName My
```

Substitua a senha com o texto codificado retornado pelo cmdlet [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps) e defina `PasswordEncrypted` como "true".

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

### <a name="configure-cluster-wide-credentials"></a>Configurar credenciais de todo o cluster

Começando com o tempo de execução 6.3, o Service Fabric permite que você configure credenciais de todo o cluster que podem ser usadas como credenciais do repositório padrão por aplicativos.

Você pode habilitar ou desabilitar o recurso adicionando o atributo `UseDefaultRepositoryCredentials` a `ContainerHostPolicies` em ApplicationManifest.xml com um valor de `true` ou `false`.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

O Service Fabric, em seguida, usa as credenciais do repositório padrão que você pode especificar em ClusterManifest na seção `Hosting`.  Se `UseDefaultRepositoryCredentials` for `true`, o Service Fabric lerá os seguintes valores de ClusterManifest:

* DefaultContainerRepositoryAccountName (cadeia de caracteres)
* DefaultContainerRepositoryPassword (cadeia de caracteres)
* IsDefaultContainerRepositoryPasswordEncrypted (booliano)
* DefaultContainerRepositoryPasswordType (cadeia de caracteres) – com suporte a partir do tempo de execução 6.4

Veja um exemplo do que você pode adicionar dentro da seção `Hosting` no arquivo ClusterManifestTemplate.json. A `Hosting` seção pode ser adicionada na criação do cluster ou posteriormente em uma atualização de configuração. Para obter mais informações, consulte [Alterar configurações de cluster do Azure Service Fabric](service-fabric-cluster-fabric-settings.md) e [Gerenciar segredos de aplicativo do Azure Service Fabric](service-fabric-application-secret-management.md)

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="configure-isolation-mode"></a>Configurar o modo de isolamento
O Windows dá suporte a dois modos de isolamento para contêineres: processo e Hyper-V. Com o modo de isolamento de processo, todos os contêineres em execução no mesmo computador host compartilham o kernel com o host. Com o modo de isolamento do Hyper-V, os kernels são isolados entre cada contêiner do Hyper-V e o host do contêiner. O modo de isolamento é especificado no elemento `ContainerHostPolicies` no arquivo de manifesto do aplicativo. Os modos de isolamento que podem ser especificados são `process`, `hyperv` e `default`. O padrão é o modo de isolamento de processo nos hosts do Windows Server. Em hosts do Windows 10, somente o modo de isolamento do Hyper-V tem suporte, assim o contêiner é executado no modo de isolamento do Hyper-V, independentemente de sua configuração de modo de isolamento. O snippet a seguir mostra como o modo de isolamento é especificado no arquivo de manifesto do aplicativo.

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```
   > [!NOTE]
   > O modo de isolamento do Hyper-v está disponível nas SKUs do Azure Ev3 e Dv3 que têm suporte para virtualização aninhada. 
   >
   >

## <a name="configure-resource-governance"></a>Configurar governança de recursos
A [governança de recursos](service-fabric-resource-governance.md) restringe os recursos que o contêiner pode usar no host. O elemento `ResourceGovernancePolicy`, especificado no manifesto do aplicativo, é usado para declarar os limites de recurso para um pacote de códigos de serviço. Limites de recursos podem ser definidos para os seguintes recursos: Memory, MemorySwap, CpuShares (peso relativo da CPU), MemoryReservationInMB, BlkioWeight (peso relativo de BlockIO). Neste exemplo, o pacote de serviço Guest1Pkg obtém um núcleo nos nós de cluster em que ele é colocado. Os limites de memória são absolutos e, portanto, o pacote de códigos é limitado a 1024 MB de memória (e a uma reserva de garantia reversível da mesma). Os pacotes de códigos (contêineres ou processos) não podem alocar mais memória do que esse limite. A tentativa de fazer isso resultará em uma exceção de memória insuficiente. Para que a imposição do limite de recursos funcione, todos os pacotes de códigos em um pacote de serviço devem ter limites de memória especificados.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```
## <a name="configure-docker-healthcheck"></a>Configurar o HEALTHCHECK do Docker 

Iniciando a versão 6.1, o Service Fabric integra automaticamente os eventos do [HEALTHCHECK do Docker](https://docs.docker.com/engine/reference/builder/#healthcheck) em seu relatório de integridade do sistema. Isso significa que, se o contêiner tiver o **HEALTHCHECK** habilitado, o Service Fabric relatará a integridade sempre que o status de integridade do contêiner for alterado conforme relatado pelo Docker. Um relatório de integridade **OK** será exibido no [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) quando o *health_status* for *íntegro* e um **AVISO** aparecerá quando o *health_status* for *não íntegro*. 

A partir da versão de atualização mais recente do v 6.4, você tem a opção de especificar que as avaliações do Docker HEALTHCHECK devem ser relatadas como um erro. Se essa opção estiver habilitada, um relatório de integridade **OK** será exibido quando *health_status* estiver *íntegro* e o **erro** será exibido quando *health_status* não estiver *íntegro*.

A instrução do **HEALTHCHECK** apontando para a verificação real que é executada para monitorar a integridade do contêiner deve estar presente no Dockerfile usado ao gerar a imagem de contêiner.

![HealthCheckHealthy][3]

![HealthCheckUnhealthyApp][4]

![HealthCheckUnhealthyDsp][5]

Você pode configurar o comportamento do **HEALTHCHECK** para cada contêiner especificando as opções do **HealthConfig** como parte do **ContainerHostPolicies** no ApplicationManifest.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true"
              RestartContainerOnUnhealthyDockerHealthStatus="false" 
              TreatContainerUnhealthyStatusAsError="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
Por padrão, *IncludeDockerHealthStatusInSystemHealthReport* é definido **como true**, *RestartContainerOnUnhealthyDockerHealthStatus* é definido como **false**e *TreatContainerUnhealthyStatusAsError* é definido como **false** . 

Se o *RestartContainerOnUnhealthyDockerHealthStatus* for definido como **true**, um contêiner relatando repetidamente um estado não íntegro será reiniciado (possivelmente em outros nós).

Se *TreatContainerUnhealthyStatusAsError* for definido como **true**, os relatórios de integridade de **erro** serão exibidos quando o *health_status* do contêiner não estiver *íntegro*.

Se você deseja desabilitar a integração do **HEALTHCHECK** para todo o cluster do Service Fabric, precisará definir o [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) como **false**.

## <a name="deploy-the-container-application"></a>Como implantar o aplicativo de contêiner
Salve todas as suas alterações e compile o aplicativo. Para publicar o seu aplicativo, clique o botão direito do mouse em **MyFirstContainer** no Gerenciador de Soluções e selecione **Publicar**.

Em **Ponto de Extremidade de Conexão**, insira o ponto de extremidade de gerenciamento para o cluster. Por exemplo, "containercluster.westus2.cloudapp.azure.com:19000". Você pode encontrar o ponto de extremidade de conexão do cliente na guia de Visão geral para o cluster no [portal do Azure](https://portal.azure.com).

Clique em **Publicar**.

O [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) é uma ferramenta baseada na Web para inspecionar e gerenciar aplicativos e nós em um cluster do Service Fabric. Abra um navegador e navegue até http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ e siga a implantação do aplicativo. O aplicativo é implantado, mas fica em estado de erro até que a imagem seja baixada nos nós de cluster (o que pode levar algum tempo, dependendo do tamanho da imagem): ![Erro][1]

O aplicativo está pronto quando está com o estado ```Ready```: ![Pronto][2]

Abra um navegador e navegue até http://containercluster.westus2.cloudapp.azure.com:8081. Você deve ver o cabeçalho "Olá, Mundo!" ser exibido no navegador.

## <a name="clean-up"></a>Limpar

Você continua a incorrer em encargos enquanto o cluster estiver em execução, considere [excluir o cluster](service-fabric-cluster-delete.md).

Depois que você enviar a imagem para o registro de contêiner, você pode excluir a imagem local do seu computador de desenvolvimento:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="windows-server-container-os-and-host-os-compatibility"></a>Compatibilidade do sistema operacional do contêiner e do sistema operacional do host do Windows Server

Os contêineres do Windows Server não são compatíveis em todas as versões de um sistema operacional do host. Por exemplo:
 
- Os contêineres do Windows Server criados usando o Windows Server versão 1709 não funcionam em um host executando o Windows Server versão 2016. 
- Os contêineres do Windows Server criados usando o Windows Server 2016 funcionam no modo de isolamento do Hyper-V somente em um host que executa o Windows Server versão 1709. 
- Com contêineres do Windows Server criados usando o Windows Server 2016, pode ser necessário garantir que a revisão do sistema operacional do contêiner e sistema operacional do host são as mesmas durante a execução no modo de isolamento do processo em um host executando o Windows Server 2016.
 
Para obter mais informações, consulte [Compatibilidade de versão de contêiner do Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

Considere a compatibilidade do sistema operacional do host e do sistema operacional do contêiner ao compilar e implantar contêineres para o cluster do Service Fabric. Por exemplo:

- Verifique se que você implanta os contêineres com um sistema operacional compatível com o sistema operacional nos nós do cluster.
- Certifique-se de que o modo de isolamento especificado para o aplicativo de contêiner é consistente com o suporte para o sistema operacional do contêiner no nó onde ele está sendo implantado.
- Considere como as atualizações do sistema operacional para os nós do cluster ou contêineres podem afetar sua compatibilidade. 

Recomendamos as seguintes práticas para certificar-se de que os contêineres são implantados corretamente no cluster do Service Fabric:

- Use a marcação de imagem explícita com as imagens do Docker para especificar a versão do sistema operacional do Windows Server a partir da qual um contêiner é criado. 
- Use a [marcação do SO](#specify-os-build-specific-container-images) em seu arquivo de manifesto do aplicativo para certificar-se de que seu aplicativo é compatível com versões e atualizações diferentes do Windows Server.

> [!NOTE]
> Com o Service Fabric versão 6.2 e posterior, você pode implantar contêineres baseados no Windows Server 2016 localmente em um host do Windows 10. No Windows 10, os contêineres são executados no modo de isolamento do Hyper-V, independentemente do modo de isolamento definido no manifesto do aplicativo. Para obter mais informações, consulte [Configurar o modo de isolamento](#configure-isolation-mode).   
>
 
## <a name="specify-os-build-specific-container-images"></a>Especifique a compilação do sistema operacional das imagens de contêiner específicas 

Os contêineres do Windows Server podem não ser compatíveis entre diferentes versões do sistema operacional. Por exemplo, os contêineres do Windows Server criados usando o Windows Server 2016 não funcionam na versão 1709 do Windows Server no modo de isolamento do processo. Portanto, se os nós do cluster forem atualizados para a versão mais recente, os serviços de contêiner criados com versões anteriores do sistema operacional poderão falhar. Para contornar isso com a versão 6.1 do tempo de execução e mais recente, o Service Fabric oferece suporte à especificação de várias imagens do sistema operacional por contêiner e marca-as com as versões de compilação do sistema operacional no manifesto do aplicativo. Você pode obter a versão do sistema operacional executando `winver` em um prompt de comando do Windows. Atualize os manifestos do aplicativo e especificar as substituições de imagem por versão do sistema operacional antes de atualizar o sistema operacional nos nós. O snippet de código a seguir mostra como especificar várias imagens de contêiner no manifesto do aplicativo, **ApplicationManifest.xml**:


```xml
      <ContainerHostPolicies> 
         <ImageOverrides> 
           <Image Name="myregistry.azurecr.io/samples/helloworldappDefault" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1701" Os="14393" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1709" Os="16299" /> 
         </ImageOverrides> 
      </ContainerHostPolicies> 
```
A versão de compilação para o WIndows Server 2016 é 14393 e a versão de compilação para a versão 1709 do Windows Server é 16299. O manifesto do serviço continua a especificar apenas uma imagem por serviço de contêiner, como mostra o seguinte:

```xml
<ContainerHost>
    <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName> 
</ContainerHost>
```

   > [!NOTE]
   > Os recursos de marcação da versão de compilação do sistema operacional só estão disponíveis para o Service Fabric no Windows
   >

Se o sistema operacional subjacente na VM é a compilação 16299 (versão 1709), o Service Fabric escolherá a imagem do contêiner correspondente a essa versão do Windows Server. Se uma imagem de contêiner sem marcas também for fornecida junto com as imagens de contêiner marcadas no manifesto do aplicativo, o Service Fabric tratará a imagem não marcada como uma que funciona entre versões diferentes. Marque as imagens de contêiner explicitamente para evitar problemas durante as atualizações.

A imagem de contêiner sem marcas funcionará como substituição da fornecida em ServiceManifest. Portanto, a imagem myregistry.azurecr.io/samples/helloworldappDefault"imagem" substituirá ImageName "myregistry.azurecr.io/samples/helloworldapp" no ServiceManifest.

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Exemplo completo de manifestos de serviço e aplicativo do Service Fabric
Aqui estão os manifestos de aplicativo e serviço completos usados neste artigo.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->    
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to
           listen. Please note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this
         application type is created. You can also create one or more instances of service type using the
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="configure-time-interval-before-container-is-force-terminated"></a>Configurar o intervalo de tempo antes do contêiner ser forçado a terminar

Você pode configurar um intervalo de tempo para a execução aguardar antes do contêiner ser removido após a exclusão do serviço (ou um movimento para outro nó) ter iniciado. Configurar o intervalo de tempo envia o comando `docker stop <time in seconds>` para o contêiner.  Para obter mais detalhes, consulte [parar docker](https://docs.docker.com/engine/reference/commandline/stop/). O intervalo de tempo de espera é especificado na seção `Hosting`. A `Hosting` seção pode ser adicionada na criação do cluster ou posteriormente em uma atualização de configuração. O snippet de manifesto do cluster a seguir mostra como definir o intervalo de espera:

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
    }
]
```
O intervalo de tempo padrão é definido para 10 segundos. Como essa configuração é dinâmica, uma configuração somente atualiza no cluster que atualiza no tempo limite. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Configurar a execução para remover as imagens de contêiner não utilizadas

Você pode configurar o cluster do Service Fabric para remover as imagens de contêiner não utilizadas do nó. Essa configuração permite que o espaço em disco seja recapturado se houver imagens de contêiner demais no nó. Para habilitar esse recurso, atualize a seção [Hosting](service-fabric-cluster-fabric-settings.md#hosting) no manifesto do cluster, conforme mostrado no seguinte trecho: 


```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "microsoft/windowsservercore|microsoft/nanoserver|microsoft/dotnet-frameworku|..."
          }
          ...
          }
        ]
    } 
]
```

Para as imagens que não devem ser excluídas, você pode especificá-las no parâmetro `ContainerImagesToSkip`.  


## <a name="configure-container-image-download-time"></a>Configurar o tempo de download de imagem de contêiner

O tempo de execução do Service Fabric aloca 20 minutos para baixar e extrair as imagens de contêiner, o que funciona para a maioria das imagens de contêiner. Para imagens grandes, ou quando a conexão de rede estiver lenta, talvez seja necessário aumentar o tempo de espera antes de cancelar o download e a extração da imagem. Esse tempo limite é definido usando o atributo **ContainerImageDownloadTimeout** na seção **Hospedagem** do manifesto do cluster, conforme mostrado no snippet de código a seguir:

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
    }
]
```


## <a name="set-container-retention-policy"></a>Definir política de retenção de contêiner

Para ajudar a diagnosticar as falhas de inicialização do contêiner, o Service Fabric (versão 6.1 ou superiores) oferece suporte à retenção de contêineres que encerraram ou falharam na inicialização. Essa política pode ser definida no arquivo **ApplicationManifest.xml** conforme mostrado no snippet de código a seguir:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

A configuração **ContainersRetentionCount** especifica o número de contêineres mantidos quando ocorre uma falha. Se um valor negativo for especificado, todos os contêineres com falha serão mantidos. Quando o atributo **ContainersRetentionCount** não for especificado, nenhum contêiner será retido. O atributo **ContainersRetentionCount** também oferece suporte a Parâmetros de Aplicativo para que os usuários possam especificar valores diferentes para clusters de teste e produção. Use restrições de posicionamento para direcionar o serviço de contêiner para um determinado nó quando esses recursos forem usados com a finalidade de impedir que o serviço de contêiner se mova para outros nós. Todos os contêineres retidos usando esse recurso devem ser removidos manualmente.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>Iniciar o daemon do Docker com argumentos personalizados

Com a versão 6.2, e superiores, do tempo de execução do Service Fabric, você pode iniciar o daemon do Docker com argumentos personalizados. Ao especificar argumentos personalizados, o Service Fabric não passa outro argumento para o mecanismo do docker, com exceção do argumento `--pidfile`. Portanto, não passe `--pidfile` como um argumento. Além disso, o argumento deve manter a escuta do daemon do docker no pipe de nome padrão no Windows (ou um soquete de domínio do unix no Linux) para o Service Fabric se comunicar com o Daemon. Os argumentos personalizados são passados no manifesto do cluster na seção **Hospedagem** em **ContainerServiceArguments** conforme mostrado no snippet de código a seguir: 
 

```json
"fabricSettings": [
    ...,
    { 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
    } 
]
```

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre como executar [contêineres no Service Fabric](service-fabric-containers-overview.md).
* Leia o tutorial [Como implantar um aplicativo .NET em um contêiner](service-fabric-host-app-in-a-container.md).
* Leia mais sobre o [ciclo de vida do aplicativo](service-fabric-application-lifecycle.md) do Service Fabric.
* Confira os [exemplos de código do Service Fabric](https://github.com/Azure-Samples/service-fabric-containers) no GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[4]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[5]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
