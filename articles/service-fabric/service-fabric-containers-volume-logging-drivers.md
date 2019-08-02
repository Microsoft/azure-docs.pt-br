---
title: Driver de Volume dos Arquivos do Azure do Service Fabric (Versão prévia) | Microsoft Docs
description: O Service Fabric oferece suporte a Arquivos do Azure para volumes de backup de seu contêiner. No momento, isso está em visualização.
services: service-fabric
documentationcenter: other
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/10/2018
ms.author: aljo, subramar
ms.openlocfilehash: 09ee729fea952665350aa25c21cdb3d5823b899f
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489891"
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Driver de Volume dos Arquivos do Azure do Service Fabric (Versão prévia)
O plug-in de volume dos Arquivos do Azure é um [Plug-in de volume de Docker](https://docs.docker.com/engine/extend/plugins_volume/) que fornece [Arquivos do Azure](https:///azure/storage/files/storage-files-introduction) com base em volumes para contêineres do Docker. Este plug-in de volume do Docker é empacotado como um aplicativo do Service Fabric que pode ser implantado em clusters do Service Fabric. Sua finalidade é fornecer volumes baseados em arquivos do Azure para outros aplicativos de contêiner Service Fabric implantados no cluster.

> [!NOTE]
> A versão 6.5.516.9494 do plug-in de volume do Azure Files é uma versão de visualização que está disponível neste documento. Como uma versão prévia, **não** tem suporte para uso em ambientes de produção.
>

## <a name="prerequisites"></a>Pré-requisitos
* A versão do Windows do plug-in de volume dos Arquivos do Azure funciona no [Windows Server versão 1709](https:///windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 versão 1709](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) ou apenas em sistemas operacionais posteriores.

* A versão do Linux do plug-in de volume dos Arquivos do Azure funciona em todas as versões de sistema operacional com suporte pelo Service Fabric.

* O plug-in do volume de arquivos do Azure funciona somente no Service Fabric versão 6.2 e mais recente.

* Siga as instruções na [Documentação dos Arquivos do Azure](https:///azure/storage/files/storage-how-to-create-file-share) para criar um compartilhamento de arquivo para o aplicativo de contêiner do Service Fabric para usar como o volume.

* Você precisará do [Powershell com o módulo do Service Fabric](https:///azure/service-fabric/service-fabric-get-started) ou [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) instalado.

* Se você estiver usando contêineres do Hyper-V, os trechos de código a seguir precisam ser adicionados na seção ClusterManifest (cluster local) ou fabricSettings no modelo de Azure Resource Manager (cluster do Azure) ou ClusterConfig. JSON (cluster autônomo).

No ClusterManifest, o seguinte precisa ser adicionado na seção Hospedagem. Neste exemplo, o nome do volume é **sfazurefile** e a porta que ele escuta no cluster é **19100**. Substitua-os pelos valores corretos para o cluster.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

Na seção fabricSettings do modelo de Azure Resource Manager (para implantações do Azure) ou ClusterConfig. JSON (para implantações autônomas), o trecho a seguir precisa ser adicionado. Novamente, substitua o nome do volume e os valores de porta pelos seus próprios.

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```


## <a name="deploy-the-service-fabric-azure-files-application"></a>Implantar o aplicativo dos Arquivos do Azure do Service Fabric

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Usando Azure Resource Manager por meio do script do PowerShell fornecido (recomendado)

Se o cluster for baseado no Azure, é recomendável implantar aplicativos nele usando o modelo de recurso de aplicativo Azure Resource Manager para facilitar o uso e para ajudar a se mover para o modelo de manutenção da infraestrutura como código. Essa abordagem elimina a necessidade de controlar a versão do aplicativo para o driver de volume de arquivos do Azure. Ele também permite que você mantenha modelos de Azure Resource Manager separados para cada sistema operacional com suporte. O script pressupõe que você está implantando a versão mais recente do aplicativo de arquivos do Azure e usa parâmetros para tipo de sistema operacional, ID de assinatura de cluster e grupo de recursos. Você pode baixar o script no [site de download Service Fabric](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip). Observe que isso define automaticamente o ListenPort, que é a porta na qual o plug-in de volume dos arquivos do Azure escuta solicitações do daemon do Docker, até 19100. Você pode alterá-lo adicionando o parâmetro chamado "listenPort". Certifique-se de que a porta não entre em conflito com nenhuma outra porta que o cluster ou seus aplicativos usam.
 

Azure Resource Manager comando de implantação para Windows:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Azure Resource Manager comando de implantação para Linux:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

Depois de executar o script com êxito, você pode pular para a [seção configurando seu aplicativo.](https:////azure/service-fabric/service-fabric-containers-volume-logging-drivers#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Implantação manual para clusters autônomos

O aplicativo Service Fabric que fornece os volumes para seus contêineres pode ser baixado do [site de download do Service Fabric](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.516.9494.zip). O aplicativo pode ser implantado para o cluster por meio do [PowerShell](https:///azure/service-fabric/service-fabric-deploy-remove-applications), [CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle-sfctl) ou [APIs FabricClient](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient).

1. Usando a linha de comando, altere o diretório para o diretório raiz do pacote de aplicativos baixado.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Em seguida, copie o pacote de aplicativos para o repositório de imagens com os valores apropriados para [ApplicationPackagePath] e [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Registrar o tipo de aplicativo

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Crie o aplicativo, pagando atentamente ao valor do parâmetro de aplicativo **listenport** . Esse valor é a porta na qual o plug-in de volume de arquivos do Azure escuta solicitações do daemon do Docker. Verifique se a porta fornecida ao aplicativo corresponde a VolumePluginPorts no ClusterManifest e não está em conflito com nenhuma outra porta que o cluster ou seus aplicativos usam.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.516.9494  -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.516.9494 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> O Windows Server 2016 Datacenter não é compatível com mapeamento de montagens de SMB em contêineres ([só há compatibilidade no Windows Server versão 1709](https:///virtualization/windowscontainers/manage-containers/container-storage)). Essa restrição impede mapeamento do volume de rede e drivers de volume de Arquivos do Azure em versões anteriores à 1709.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>Implantar o aplicativo em um cluster de desenvolvimento local
Siga as etapas de 1-3 a seguir [.](https:////azure/service-fabric/service-fabric-containers-volume-logging-drivers#manual-deployment-for-standalone-clusters)

 A contagem de instâncias de serviço padrão para o aplicativo de plug-in de volume dos Arquivos do Azure é -1, o que significa que há uma instância do serviço implantado em cada nó no cluster. No entanto, ao implantar o aplicativo de plug-in de volume dos Arquivos do Azure em um cluster de desenvolvimento local, a contagem de instâncias de serviço deve ser especificada como 1. Isso pode ser feito por meio do parâmetro de aplicativo **InstanceCount**. Portanto, o comando para criar o aplicativo de plug-in de volume de arquivos do Azure em um cluster de desenvolvimento local é:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.516.9494 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.516.9494 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Configure seus aplicativos para usar o volume
O trecho a seguir mostra como um volume baseado em arquivos do Azure pode ser especificado no arquivo de manifesto do aplicativo do seu aplicativo. O elemento específico de interesse é a marca **Volume**:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
                <DriverOption Name="storageAccountFQDN" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

O nome do driver para o plug-in de volume dos Arquivos do Azure é **sfazurefile**. Esse valor é definido para o atributo **Driver** do elemento de marca de **volume** no manifesto do aplicativo.

Na marca de **volume** no trecho acima, o plug-in de volume de arquivos do Azure requer os seguintes atributos:
- **Source** – Esse é o nome do volume. O usuário pode escolher qualquer nome para seu volume.
- **Destino** -esse atributo é o local para o qual o volume é mapeado dentro do contêiner em execução. Assim, seu destino não pode ser um local já existente em seu contêiner

Conforme exibido nos elementos **DriverOption** no snippet acima, o plug-in do volume de Arquivos do Azure oferece suporte às seguintes opções de driver:
- **shareName** - Nome do compartilhamento de arquivos dos Arquivos do Azure que fornece o volume para o contêiner.
- **storageAccountName** - Nome da conta de armazenamento do Azure que contém o compartilhamento de arquivos dos Arquivos do Azure.
- **storageAccountKey** - Chave de acesso para a conta de armazenamento do Azure que contém o compartilhamento de arquivos dos Arquivos do Azure.
- **storageAccountFQDN** – nome de domínio associado à conta de armazenamento. Se storageAccountFQDN não for especificado, o nome de domínio será formado usando o padrão suffix(.file.core.windows.net) com storageAccountName.  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>Usando seu próprio volume ou o log do driver
O Service Fabric também permite o uso do [volume](https://docs.docker.com/engine/extend/plugins_volume/) personalizado ou drivers de [registro em log](https://docs.docker.com/engine/admin/logging/overview/). Se o driver de volume/log do Docker não estiver instalado em seu cluster, é possível instalá-lo manualmente usando os protocolos RDP/SSH. Você pode executar a instalação usando esses protocolos por meio de um [script de inicialização de conjunto de escala de máquina virtual](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) ou um [script SetupEntryPoint](https:///azure/service-fabric/service-fabric-application-model).

Um exemplo de script para instalar o [driver de volume do Docker para Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) é o seguinte:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Em seus aplicativos, para usar o volume ou a unidade de log que você instalou, é necessário especificar os valores apropriados nos elementos **Volume** e **LogConfig** sob  **ContainerHostPolicies** em seu manifesto de aplicativo.

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

Ao especificar um plug-in do volume, o Service Fabric cria automaticamente o volume usando os parâmetros especificados. A marcação **Fonte** para o elemento **Volume** é o nome do volume e a marcação **Driver** especifica o plug-in do driver do volume. A marcação **Destino** é o local em que a **Fonte** é mapeada no contêiner em execução. Assim, seu destino não pode ser um local já existente em seu contêiner. As opções podem ser especificadas usando a marcação de **DriverOption** como da seguinte maneira:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Os parâmetros do aplicativo são compatíveis com volumes, conforme mostrado no snippet de manifesto anterior (procure `MyStorageVar` para obter um uso de exemplo).

Se um driver de log do Docker for especificado, será necessário implantar agentes (ou contêineres) para tratar os logs no cluster. A marcação **DriverOption** pode ser usada para especificar opções para o driver de log.

## <a name="next-steps"></a>Próximas etapas
* Para ver exemplos de contêiner, incluindo o driver de volume, visite os [Exemplos de contêiner do Service Fabric](https://github.com/Azure-Samples/service-fabric-containers)
* Para implantar contêineres em um cluster do Service Fabric, consulte o artigo [Implantar um contêiner no Service Fabric](service-fabric-deploy-container.md)
