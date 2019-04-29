---
title: Exemplos de manifesto do aplicativo de contêiner do Azure Service Fabric | Microsoft Docs
description: Saiba como definir as configurações do aplicativo e do manifesto do serviço para um aplicativo do Service Fabric de vários contêineres.
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 622e6f7552d91cdb9ccf3668c302496c68a5920f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60719236"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Exemplos de aplicativo multicontêiner e manifesto do serviço
A seguir estão exemplos dos manifestos do serviço e do aplicativo para um aplicativo do Service Fabric de vários contêineres. O objetivo desses exemplos é mostrar quais configurações estão disponíveis e como usá-las. Esses manifestos do serviço e do aplicativo se baseiam nos manifestos de [Exemplo de contêiner do Windows Server 2016](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows).

Os seguintes recursos são mostrados:

|Manifesto|Recursos|
|---|---|
|[Manifesto do aplicativo](#application-manifest)| [substituir variáveis de ambiente](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [configurar o mapeamento de porta para host do contêiner](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery), [configurar a autenticação de registro de contêiner](service-fabric-get-started-containers.md#configure-container-registry-authentication), [governança de recursos](service-fabric-resource-governance.md), [definir o modo de isolamento](service-fabric-get-started-containers.md#configure-isolation-mode), [especificar imagens de contêiner de build específicas do sistema operacional](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[Manifesto do serviço FrontEndService](#frontendservice-service-manifest)| [definir variáveis de ambiente](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [configurar um ponto de extremidade](service-fabric-get-started-containers.md#configure-communication), passar comandos para o contêiner, [importar um certificado para um contêiner](service-fabric-securing-containers.md)| 
|[Manifesto do serviço BackEndService](#backendservice-service-manifest)|[definir variáveis de ambiente](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [configurar um ponto de extremidade](service-fabric-get-started-containers.md#configure-communication), [configurar o driver de volume](service-fabric-containers-volume-logging-drivers.md)| 

Confira [Elementos do manifesto do aplicativo](#application-manifest-elements), [Elementos do manifesto do serviço FrontEndService](#frontendservice-service-manifest-elements) e [Elementos do manifesto do serviço BackEndService](#backendservice-service-manifest-elements) para obter mais informações sobre elementos XML específicos.

## <a name="application-manifest"></a>Manifesto do aplicativo

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="BackEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="FrontEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="MaximumIOps" DefaultValue="20"/>
    <Parameter Name="MemoryFront" DefaultValue="4084" />
    <Parameter Name="MemoryBack" DefaultValue="2048" />
    <Parameter Name="CertThumbprint" DefaultValue=""/>
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="BackEndServicePkg" ServiceManifestVersion="1.0.0" />    
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryFront]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[MemoryFront]" BlockIOWeight="[BlockIOWeight]" MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" MemorySwapInMB="[MemorySwapInMB]"/>
      
      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
        
        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>
        
        <!-- This binds the port the container is listening on (8905 in this sample) to an endpoint resource named "BackEndServiceTypeEndpoint", which is defined in the service manifest.  -->
        <PortBinding ContainerPort="8905" EndpointRef="BackEndServiceTypeEndpoint"/>
        
        <!-- Configure the Azure Files volume plugin.  Bind the source folder on the host VM or a remote share to the destination folder within the running container. -->
        <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
          <!-- Driver options to be passed to driver. The Azure Files volume plugin supports the following driver options:
            shareName (the Azure Files file share that provides the volume for the container), storageAccountName (the Azure storage account
            that contains the Azure Files file share), storageAccountKey (Access key for the Azure storage account that contains the Azure Files file share).
            These three driver options are required. -->
          <DriverOption Name="shareName" Value="" />
          <DriverOption Name="storageAccountName" Value="MY-STORAGE-ACCOUNT-NAME" />
          <DriverOption Name="storageAccountKey" Value="MY-STORAGE-ACCOUNT-KEY" />
        </Volume>
        
        <!-- Windows Server containers may not be compatible across different versions of the OS.  You can specify multiple OS images per container and tag 
        them with the build versions of the OS. Get the build version of the OS by running "winver" at a Windows command prompt. -->
        <ImageOverrides>
          <!-- If the underlying OS is build version 16299 (Windows Server version 1709), Service Fabric picks the container image tagged with Os="16299". -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_1709" Os="16299" />
          
          <!-- An untagged container image is assumed to work across all versions of the OS and overrides the image specified in the service manifest. -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_default" />          
        </ImageOverrides>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <!-- Policies to be applied to the imported service manifest. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    
    <!-- This enables you to provide different values for environment variables when creating a FrontEndService
         Theses environment variables are declared in the FrontEndServiceType service manifest-->
    <EnvironmentOverrides CodePackageRef="Code">
      <EnvironmentVariable Name="BackendServiceName" Value="Container.Application/BackEndService"/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentOverrides>
    
    <!-- This policy maps the  port of the container (80) to the endpoint declared in the service, 
         FrontEndServiceTypeEndpoint which is exposed as port 80 on the host-->    
    <Policies>

      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryBack]"/>

      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">

        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>

        <!-- Binds an endpoint resource (declared in the service manifest) to the exposed container port. -->
        <PortBinding ContainerPort="80" EndpointRef="FrontEndServiceTypeEndpoint"/>

        <!-- Import a certificate into the container.  The certificate must be installed in the LocalMachine store of all the cluster nodes.
          When the application starts, the runtime reads the certificate and generates a PFX file and password (on Windows) or a PEM file (on Linux).
          The PFX file and password are accessible in the container using the Certificates_ServicePackageName_CodePackageName_CertName_PFX and 
          Certificates_ServicePackageName_CodePackageName_CertName_Password environment variables. The PEM file is accessible in the container using the 
          Certificates_ServicePackageName_CodePackageName_CertName_PEM and Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey environment variables.-->
        <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[CertThumbprint]" />

        <!-- If the certificate is already in PFX or PEM form, you can create a data package inside your application and reference that certificate here. -->
        <CertificateRef Name="MyCert2" DataPackageRef="Data" DataPackageVersion="1.0.0" RelativePath="MyCert2.PFX" Password="ENCRYPTED-PASSWORD" IsPasswordEncrypted="true"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
        
    <Service Name="FrontEndService" >
      <StatelessService ServiceTypeName="FrontEndServiceType" InstanceCount="[FrontEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
        <Service Name="BackEndService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="BackEndServiceType" InstanceCount="[BackEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="frontendservice-service-manifest"></a>Manifesto do serviço FrontEndService

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="FrontEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricfrontendservice:v1</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container or exe.  These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="IsContainer" Value=""/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Data package is the contents of the Data directory under PackageRoot that contains an 
       independently-updateable and versioned static data that's consumed by the process at runtime. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme and port 80 -->
      <Endpoint Name="FrontEndServiceTypeEndpoint" UriScheme="http" Port="80"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="backendservice-service-manifest"></a>Manifesto do serviço BackEndService

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="BackEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricbackendservice:v1</ImageName>
        
        <!-- Pass comma delimited commands to your container. -->
        <Commands> dotnet, myproc.dll, 5 </Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container. These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the host port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme. In this case since no port is specified, one is created and assigned dynamically
           to the service. This dynamically assigned host port is mapped to the container port (8905 in this sample),
            which was specified in the application manifest.-->
      <Endpoint Name="BackEndServiceTypeEndpoint" UriScheme="http" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="application-manifest-elements"></a>Elementos do manifesto do aplicativo
### <a name="applicationmanifest-element"></a>Elemento ApplicationManifest
Descreve declarativamente o tipo de aplicativo e a versão. Um ou mais manifestos de serviço dos serviços membros são referenciados para compor um tipo de aplicativo. As definições de configuração dos serviços membros podem ser substituídas usando configurações do aplicativo com parâmetros. Padrão de serviços, modelos de serviço, entidades de segurança, políticas, configuração de diagnóstico e certificados também podem ser declarados no nível do aplicativo. Para obter mais informações, confira [Elemento ApplicationManifest](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Elemento Parameters
Declara os parâmetros que são usados no manifesto do aplicativo. O valor desses parâmetros pode ser fornecido quando o aplicativo é instanciado e pode ser usado para substituir as definições de configuração do serviço ou aplicativo. Para obter mais informações, confira [Elemento Parameters](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Elemento Parameter
Um parâmetro de aplicativo a ser usado no manifesto. O valor do parâmetro pode ser alterado durante a instanciação do aplicativo, ou, se nenhum valor for fornecido, o valor padrão será usado. Para obter mais informações, confira [Elemento Parameter](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>ServiceManifestImport
Importa um manifesto do serviço criado pelo desenvolvedor do serviço. Um manifesto do serviço deve ser importado para cada serviço membro do aplicativo. As substituições e políticas de configuração podem ser declaradas para o manifesto de serviço. Para obter mais informações, confira [Elemento ServiceManifestImport](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>Elemento ServiceManifestRef
Importa o manifesto do serviço por referência. Atualmente, o arquivo de manifesto do serviço (ServiceManifest.xml) deve estar presente no pacote da build. Para obter mais informações, confira [Elemento ServiceManifestRef](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="policies-element"></a>Elemento Policies
Descreve as políticas (associação de ponto de extremidade, compartilhamento de pacotes, executar como e acesso de segurança) a serem aplicadas no manifesto do serviço importado. Para obter mais informações, confira [Elemento Policies](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>Elemento ServicePackageResourceGovernancePolicy
Define a política de controle de recursos que é aplicada no nível de todo o pacote de serviço. Para obter mais informações, confira [Elemento ServicePackageResourceGovernancePolicy](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>Elemento ResourceGovernancePolicy
Especifica limites de recurso para um pacote de códigos. Para obter mais informações, confira [Elemento ResourceGovernancePolicy](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>Elemento ContainerHostPolicies
Especifica políticas para a ativação de hosts de contêiner. Para obter mais informações, confira [Elemento ContainerHostPolicies](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>Elemento RepositoryCredentials
Credenciais para o repositório de imagens de contêiner de onde as imagens devem ser retiradas. Para obter mais informações, confira [Elemento RepositoryCredentials](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>Elemento PortBinding
Especifica qual recurso do ponto de extremidade associar à porta do contêiner exposto. Para obter mais informações, confira [Elemento PortBinding](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Elemento Volume
Especifica o volume a ser associado ao contêiner. Para obter mais informações, confira [Elemento Volume](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>Elemento DriverOption
Opções de driver a serem transmitidas para o driver. Para obter mais informações, confira [Elemento DriverOption](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>Elemento ImageOverrides
Os contêineres do Windows Server podem não ser compatíveis entre diferentes versões do sistema operacional.  Você pode especificar várias imagens de sistema operacional por contêiner e marcá-las com as versões de build do sistema operacional. Obtenha a versão do sistema operacional executando "winver" em um prompt de comando do Windows. Se o sistema operacional subjacente tiver a versão de build 16299 (versão 1709 do Windows Server), o Service Fabric escolherá a imagem de contêiner marcada com Os="16299". Será assumido que a imagem de contêiner não marcada funcionará em todas as versões do sistema operacional e substituirá a imagem especificada no manifesto do serviço. Para obter mais informações, confira [Elemento ImageOverrides](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Elemento de imagem
Imagem de contêiner que corresponde ao número da versão do build do sistema operacional a ser inicializado. Se o atributo OS não for especificado, será assumido que a imagem de contêiner funcionará em todas as versões do sistema operacional e substituirá a imagem especificada no manifesto do serviço. Para obter mais informações, confira [Elemento Image](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)

### <a name="environmentoverrides-element"></a>Elemento EnvironmentOverrides
 Para obter mais informações, confira [Elemento EnvironmentOverrides](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>Elemento EnvironmentVariable
Variável Environment. Para obter mais informações, confira [Elemento EnvironmentVariable](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="certificateref-element"></a>Elemento CertificateRef
Especifica informações sobre um X509 certificado que deve ser exposto ao ambiente do contêiner. O certificado deve ser instalado no repositório LocalMachine de todos os nós do cluster.
Quando o aplicativo é iniciado, o tempo de execução lê o certificado e gera um arquivo PFX e uma senha (no Windows) ou um arquivo PEM (no Linux).
O arquivo PFX e a senha são acessíveis no contêiner usando as variáveis de ambiente Certificates_ServicePackageName_CodePackageName_CertName_PFX e Certificates_ServicePackageName_CodePackageName_CertName_Password. O arquivo PEM é acessível no contêiner usando as variáveis de ambiente Certificates_ServicePackageName_CodePackageName_CertName_PEM e Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey. Para obter mais informações, confira [Elemento CertificateRef](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>Elemento DefaultServices
Declara as instâncias de serviço que são criadas automaticamente sempre que um aplicativo é instanciado em relação a esse tipo de aplicativo. Para obter mais informações, confira [Elemento DefaultServices](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Elemento Service
Declara a criação automática de um serviço quando o aplicativo é instanciado. Para obter mais informações, confira [Elemento Service](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statelessservice-element"></a>Elemento StatelessService
Define um serviço sem estado. Para obter mais informações, confira [Elemento StatelessService](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>Elementos do manifesto do serviço FrontEndService
### <a name="servicemanifest-element"></a>Elemento ServiceManifest
Descreve declarativamente o tipo de serviço e a versão. Ele lista o código que pode ser atualizado independentemente, a configuração e os pacotes de código que compõem um pacote de serviço para dar suporte a um ou mais tipos de serviço. Recursos, configurações de diagnóstico e metadados de serviço, como o tipo de serviço, as propriedades de integridade e as métricas de balanceamento de carga, também são especificados. Para obter mais informações, confira [Elemento ServiceManifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento ServiceTypes
Define os tipos de serviço com suporte por um CodePackage no manifesto. Quando um serviço é instanciado em relação a um desses tipos de serviço, todos os pacotes de código declarados nesse manifesto são ativados com a execução de seus pontos de entrada. Os tipos de serviço são declarados no nível do manifesto e não no nível do pacote de código. Para obter mais informações, confira [Elemento ServiceTypes](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Elemento StatelessServiceType
Descreve um tipo de serviço sem estado. Para obter mais informações, confira [Elemento StatelessServiceType](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento CodePackage
Descreve um pacote de código que dá suporte a um tipo de serviço definido. Quando um serviço é instanciado em relação a um desses tipos de serviço, todos os pacotes de código declarados nesse manifesto são ativados com a execução de seus pontos de entrada. Os processos resultantes devem registrar os tipos de serviço com suporte no tempo de execução. Quando há vários pacotes de código, eles serão todos ativados sempre que o sistema procurar por qualquer um dos tipos de serviço declarados. Para obter mais informações, confira [Elemento CodePackage](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Elemento EntryPoint
O executável especificado pelo EntryPoint normalmente é o host de serviço de longa duração. A presença de um ponto de entrada de instalação separado evita a necessidade de executar o host de serviço com altos privilégios por longos períodos de tempo. O executável especificado pelo EntryPoint é executado depois que o SetupEntryPoint é encerrado com êxito. O processo resultante será monitorado e reiniciado (começando novamente com SetupEntryPoint) se ele terminar ou falhar. Para obter mais informações, confira [Elemento EntryPoint](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>Elemento ContainerHost
 Para obter mais informações, confira [Elemento ContainerHost](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>Elemento ImageName
O repositório e a imagem no https://hub.docker.com ou Registro de Contêiner do Azure. Para obter mais informações, confira [Elemento ImageName](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>Elemento EnvironmentVariables
Passe as variáveis de ambiente para seu contêiner ou exe.  Para obter mais informações, confira [Elemento EnvironmentVariables](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Elemento EnvironmentVariable
Variável Environment. Para obter mais informações, confira [Elemento EnvironmentVariable](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>Elemento ConfigPackage
Declara uma pasta nomeada pelo atributo Name, que contém um arquivo Settings.xml. Esse arquivo contém seções de configurações de par chave-valor, definido pelo usuário, que o processo pode ler de volta no tempo de execução. Durante a atualização, se apenas a versão do ConfigPackage tiver sido alterada, o processo de execução não será reiniciado. Em vez disso, um retorno de chamada notifica o processo de que as definições de configuração foram alteradas para que possam ser recarregadas dinamicamente. Para obter mais informações, confira [Elemento ConfigPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Elemento DataPackage
Declara uma pasta nomeada pelo atributo Name, que contém arquivos de dados estáticos. O Service Fabric reciclará todos os EXEs e DLLHOSTs especificados nos pacotes de host e de suporte quando um dos pacotes de dados listados no manifesto do serviço forem atualizados. Para obter mais informações, confira [Elemento DataPackage](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Elemento Resources
Descreve os recursos usados por esse serviço, que podem ser declarados sem modificar o código compilado e alterados quando o serviço é implantado. O acesso a esses recursos é controlado pelas seções Entidades de segurança e Políticas do manifesto do aplicativo. Para obter mais informações, confira [Elemento Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento Endpoints
Define pontos de extremidade para o serviço. Para obter mais informações, confira [Elemento Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento Endpoint
Para obter mais informações, confira [Elemento Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>Elementos do manifesto do serviço BackEndService
### <a name="servicemanifest-element"></a>Elemento ServiceManifest
Descreve declarativamente o tipo de serviço e a versão. Ele lista o código que pode ser atualizado independentemente, a configuração e os pacotes de código que compõem um pacote de serviço para dar suporte a um ou mais tipos de serviço. Recursos, configurações de diagnóstico e metadados de serviço, como o tipo de serviço, as propriedades de integridade e as métricas de balanceamento de carga, também são especificados. Para obter mais informações, confira [Elemento ServiceManifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento ServiceTypes
Define os tipos de serviço com suporte por um CodePackage no manifesto. Quando um serviço é instanciado em relação a um desses tipos de serviço, todos os pacotes de código declarados nesse manifesto são ativados com a execução de seus pontos de entrada. Os tipos de serviço são declarados no nível do manifesto e não no nível do pacote de código. Para obter mais informações, confira [Elemento ServiceTypes](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Elemento StatelessServiceType
Descreve um tipo de serviço sem estado. Para obter mais informações, confira [Elemento StatelessServiceType](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento CodePackage
Descreve um pacote de código que dá suporte a um tipo de serviço definido. Quando um serviço é instanciado em relação a um desses tipos de serviço, todos os pacotes de código declarados nesse manifesto são ativados com a execução de seus pontos de entrada. Os processos resultantes devem registrar os tipos de serviço com suporte no tempo de execução. Quando há vários pacotes de código, eles serão todos ativados sempre que o sistema procurar por qualquer um dos tipos de serviço declarados. Para obter mais informações, confira [Elemento CodePackage](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Elemento EntryPoint
O executável especificado pelo EntryPoint normalmente é o host de serviço de longa duração. A presença de um ponto de entrada de instalação separado evita a necessidade de executar o host de serviço com altos privilégios por longos períodos de tempo. O executável especificado pelo EntryPoint é executado depois que o SetupEntryPoint é encerrado com êxito. O processo resultante será monitorado e reiniciado (começando novamente com SetupEntryPoint) se ele terminar ou falhar. Para obter mais informações, confira [Elemento EntryPoint](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>Elemento ContainerHost
Para obter mais informações, confira [Elemento ContainerHost](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>Elemento ImageName
O repositório e a imagem no https://hub.docker.com ou Registro de Contêiner do Azure. Para obter mais informações, confira [Elemento ImageName](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Elemento Commands
Lista delimitada por vírgulas de comandos para o contêiner. Para obter mais informações, confira [Elemento Commands](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>Elemento EnvironmentVariables
Passe as variáveis de ambiente para seu contêiner ou exe.  Para obter mais informações, confira [Elemento EnvironmentVariables](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Elemento EnvironmentVariable
Variável Environment. Para obter mais informações, confira [Elemento EnvironmentVariable](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>Elemento ConfigPackage
Declara uma pasta nomeada pelo atributo Name, que contém um arquivo Settings.xml. Esse arquivo contém seções de configurações de par chave-valor, definido pelo usuário, que o processo pode ler de volta no tempo de execução. Durante a atualização, se apenas a versão do ConfigPackage tiver sido alterada, o processo de execução não será reiniciado. Em vez disso, um retorno de chamada notifica o processo de que as definições de configuração foram alteradas para que possam ser recarregadas dinamicamente. Para obter mais informações, confira [Elemento ConfigPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Elemento Resources
Descreve os recursos usados por esse serviço, que podem ser declarados sem modificar o código compilado e alterados quando o serviço é implantado. O acesso a esses recursos é controlado pelas seções Entidades de segurança e Políticas do manifesto do aplicativo. Para obter mais informações, confira [Elemento Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento Endpoints
Define pontos de extremidade para o serviço. Para obter mais informações, confira [Elemento Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento Endpoint
 Para obter mais informações, confira [Elemento Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

