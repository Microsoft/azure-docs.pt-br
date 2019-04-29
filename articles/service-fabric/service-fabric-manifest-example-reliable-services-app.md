---
title: Exemplos de manifesto do aplicativo Reliable Services do Azure Service Fabric | Microsoft Docs
description: Saiba como definir as configurações do aplicativo e do manifesto do serviço para um aplicativo Reliable Services do Service Fabric.
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
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6c4c8f0ee6aa12c58e02f71b42312cd6872076aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60719132"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Exemplos de aplicativo de Reliable Services e manifesto do serviço
A seguir estão exemplos dos manifestos de aplicativo e serviço para um aplicativo do Service Fabric com um front-end Web do ASP.NET Core e um back-end com estado. O objetivo desses exemplos é mostrar quais configurações estão disponíveis e como usá-las. Esses manifestos de aplicativo e serviços se baseiam em manifestos [Início Rápido do Service Fabric .NET](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/).

Os seguintes recursos são mostrados:

|Manifesto|Recursos|
|---|---|
|[Manifesto do aplicativo](#application-manifest)| [governança de recursos](service-fabric-resource-governance.md), [executar um serviço como uma conta do administrador local](service-fabric-application-runas-security.md), [aplicar uma política padrão a todos os pacotes de código de serviço](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages), [criar entidades de usuário e grupo](service-fabric-application-runas-security.md), compartilhar um pacote de dados entre instâncias de serviço, [substituir pontos de extremidade de serviço](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|Manifesto do serviço FrontEndService| [Executar um script na inicialização do serviço](service-fabric-run-script-at-service-startup.md), [definir um ponto de extremidade HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|Manifesto do serviço BackEndService| [Declarar um pacote de configuração](service-fabric-application-and-service-manifests.md), [declarar um pacote de dados](service-fabric-application-and-service-manifests.md), [configurar um ponto de extremidade](service-fabric-service-manifest-resources.md)| 

Veja [Elementos do manifesto de aplicativo](#application-manifest-elements), [Elementos de manifesto de serviço VotingWeb](#votingweb-service-manifest-elements) e [Elementos de manifesto de serviço VotingData](#votingdata-service-manifest-elements) para obter mais informações sobre elementos específicos de XML.

## <a name="application-manifest"></a>Manifesto do aplicativo

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="Memory" DefaultValue="4084" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="Port" DefaultValue="8081" />
    <Parameter Name="Protocol" DefaultValue="tcp" />
    <Parameter Name="Type" DefaultValue="internal" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <!-- Override endpoints declared in the service manifest. -->
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="DataEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
      </Endpoints>
    </ResourceOverrides>

    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[Memory]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[Memory]" BlockIOWeight="[BlockIOWeight]" 
                                MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" 
                                MemorySwapInMB="[MemorySwapInMB]"/>

      <!-- Share the data package across multiple instances of the VotingData service-->
      <PackageSharingPolicy PackageRef="VotingDataPkg.Data"/>

      <!-- Give read rights on the "DataEndpoint" endpoint to the Customer2 account.-->
      <SecurityAccessPolicy GrantRights="Read" PrincipalRef="Customer2" ResourceRef="DataEndpoint" ResourceType="Endpoint"/>         
    </Policies>
  </ServiceManifestImport>
  
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Run the setup entry point (defined in the imported service manifest) as the SetupAdminUser account 
      (declared in the following Principals section). -->
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      
    </Policies>

  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <!-- Define users and groups required to run the services and access resources.  Principals are used in the Policies section(s). -->
  <Principals>
    <!-- Declare a set of groups as security principals, which can be referenced in policies. Groups are useful if there are multiple users 
    for different service entry points and they need to have certain common privileges that are available at the group level. -->
    <Groups>
      <!-- Create a group that has administrator privileges. -->
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <!-- Declare a user and add the user to the Administrators system group. The SetupAdminUser account is used to run the 
      setup entry point of the VotingWebPkg code package (described in the preceding Policies section).-->
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
      <!-- Create a user. Local user accounts are created on the machines where the application is deployed. By default, these accounts 
      do not have the same names as those specified here. Instead, they are dynamically generated and have random passwords. -->
      <User Name="Customer1" >
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as a local user with the specified account name and password. Local user accounts are created on the machines 
      where the application is deployed. -->
      <User Name="Customer2" AccountType="LocalUser" AccountName="Customer1" Password="MyPassword">
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as NetworkService. -->
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <!-- Policies applied at the application level. -->
  <Policies>
    <!-- Specify a default user account for all code packages that don’t have a specific RunAsPolicy defined in 
    the ServiceManifestImport section(s). -->
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
    
  </Policies>
</ApplicationManifest>

```

## <a name="votingweb-service-manifest"></a>Manifesto do serviço VotingWeb

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <!-- A privileged entry point that by default runs with the same credentials as Service Fabric (typically the NetworkService account) before 
    any other entry point. Use the setup entry point to set system environment variables, give the account running the service (NETWORK SERVICE, by default) 
    access to a certificate's private key, or perform other setup tasks. In the application manifest, you can change the security permissions to run the startup script 
    under a local system account or an administrator account.  -->
    <SetupEntryPoint>
      <ExeHost>
        <!-- The setup script to run. -->
        <Program>Setup.bat</Program>
        <!-- Pass arguments to the script when it runs.-->
        <Arguments>MyValue</Arguments>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
        <!-- Warning! Do not use console redirection in a production application, only use it for local development and debugging. Redirects console output from the startup
        script to an output file in the application folder called "log" on the cluster node where the application is deployed and run. Also set the number of output files
        to retain and the maximum file size (in KB). -->
        <ConsoleRedirection FileRetentionCount="10" FileMaxSizeInKb="20480"/>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- Configure a HTTPS endpoint on port 443. This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

## <a name="votingdata-service-manifest"></a>Manifesto do serviço VotingData

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingDataPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="VotingDataType"  HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingData.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Declares a folder, named by the Name attribute, under PackageRoot that contains a Settings.xml file. This file contains sections of user-defined, 
  key-value pair settings that the process can read back at run time. During an upgrade, if only the ConfigPackage version has changed, 
  then the running process is not restarted. Instead, a callback notifies the process that configuration settings have changed so they can be reloaded dynamically. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Declares a folder, named by the Name attribute, under PackageRoot which contains static data files to be consumed by the process at run time. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- Define an internal (used for intra-application communication) TCP endpoint. Since no port is specified, one is created and assigned dynamically
           to the service.-->
      <Endpoint Name="DataEndpoint" Protocol="tcp" Type="Internal" />
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

### <a name="resourceoverrides-element"></a>Elemento ResourceOverrides
Especifica substituições de recurso para pontos de extremidade declarados em recursos do manifesto do serviço. Para obter mais informações, veja [Elemento ResourceOverrides](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Elemento Endpoints
Os pontos de extremidade a substituir. Para obter mais informações, confira [Elemento Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Elemento Endpoint
O ponto de extremidade, declarado no manifesto do serviço, a ser substituído. Para obter mais informações, confira [Elemento Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Elemento Policies
Descreve as políticas (associação de ponto de extremidade, compartilhamento de pacotes, executar como e acesso de segurança) a serem aplicadas no manifesto do serviço importado. Para obter mais informações, confira [Elemento Policies](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>Elemento ServicePackageResourceGovernancePolicy
Define a política de controle de recursos que é aplicada no nível de todo o pacote de serviço. Para obter mais informações, confira [Elemento ServicePackageResourceGovernancePolicy](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>Elemento ResourceGovernancePolicy
Especifica limites de recurso para codepackage. Para obter mais informações, confira [Elemento ResourceGovernancePolicy](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>Elemento PackageSharingPolicy
Indica se um pacote de códigos, configurações ou dados deve ser compartilhado entre instâncias de serviço do mesmo tipo de serviço. Para obter mais informações, veja [Elemento PackageSharingPolicy](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>Elemento SecurityAccessPolicy
Concede permissões de acesso a uma entidade em um recurso (como um ponto de extremidade) definido em um manifesto do serviço. Normalmente, é muito útil controlar e restringir o acesso dos serviços para recursos diferentes a fim de minimizar riscos de segurança. Isso é especialmente importante quando o aplicativo é criado a partir de uma coleção de serviços de um marketplace que são criados por desenvolvedores diferentes. Para obter mais informações, veja [Elemento SecurityAccessPolicy](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>Elemento RunAsPolicy
Especifica o usuário local ou a conta do sistema local usado por um pacote de código de serviço para execução. As contas de domínio têm suporte em implantações do Windows Server em que o Active Directory do Azure está disponível. Por padrão, os aplicativos são executados na conta sob a qual o processo Fabric.exe está sendo executado. Os aplicativos também podem ser executados como outras contas, que devem ser declaradas na seção Entidades de segurança. Se você aplicar uma política RunAs a um serviço e o manifesto do serviço declarar recursos de ponto de extremidade com o protocolo HTTP, será necessário também especificar uma SecurityAccessPolicy para assegurar que as portas alocadas a esses pontos de extremidade sejam corretamente listadas no controle de acesso para a conta de usuário RunAs na qual o serviço é executado. Para um ponto de extremidade HTTPS, você também precisa definir um EndpointBindingPolicy para indicar o nome do certificado para retornar ao cliente. Para obter mais informações, veja [Elemento RunAsPolicy](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>Elemento DefaultServices
Declara as instâncias de serviço que são criadas automaticamente sempre que um aplicativo é instanciado em relação a esse tipo de aplicativo. Para obter mais informações, confira [Elemento DefaultServices](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Elemento Service
Declara a criação automática de um serviço quando o aplicativo é instanciado. Para obter mais informações, confira [Elemento Service](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>Elemento StatefulService
Define um serviço com estado. Para obter mais informações, veja [Elemento StatefulService](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>Elemento StatelessService
Define um serviço sem estado. Para obter mais informações, confira [Elemento StatelessService](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Elemento Principals
Descreve as entidades de segurança (usuários, grupos) necessárias para o aplicativo executar serviços e recursos de segurança. As entidades de segurança são referenciadas nas seções de políticas. Para obter mais informações, veja [Elemento Principal](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="groups-element"></a>Elemento Groups
Declara um conjunto de grupos como entidades de segurança que podem ser referenciadas em políticas. Grupos serão úteis se houver vários usuários para pontos de entrada de serviço diferentes e eles precisarem de privilégios comuns disponíveis no nível do grupo. Para obter mais informações, veja [Elemento Groups](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Elemento Group
Declara um grupo como uma entidade de segurança que pode ser referenciada em políticas. Para obter mais informações, veja [Elemento Group](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Elemento Membership
 Para obter mais informações, veja [Elemento Membership](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>Elemento SystemGroup
 Para obter mais informações, veja [Elemento SystemGroup](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)

### <a name="users-element"></a>Elemento Users
Declara um conjunto de usuários como entidades de segurança que podem ser referenciadas em políticas. Para obter mais informações, veja [Elemento Users](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Elemento User
Declara um usuário como uma entidade de segurança que pode ser referenciada em políticas. Para obter mais informações, veja [Elemento User](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>Elemento MemberOf
Os usuários podem ser adicionados a um grupo de associação existente, para que possam herdar todas as propriedades e configurações de segurança de tal grupo de associação. O grupo de associação pode ser usado para proteger os recursos externos que precisam ser acessados por diferentes serviços ou o mesmo serviço (em um computador diferente). Para obter mais informações, veja [Elemento MemberOf](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>Elemento SystemGroup
O grupo do sistema ao qual adicionar o usuário.  O grupo do sistema deve ser definido na seção Groups. Para obter mais informações, veja [Elemento SystemGroup](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="group-element"></a>Elemento Group
O grupo ao qual adicionar o usuário.  O grupo deve ser definido na seção Groups. Para obter mais informações, veja [Elemento Group](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Elemento Policies
Descreve as políticas (coleta de log, executar como padrão, integridade e acesso de segurança) a serem aplicadas no nível do aplicativo. Para obter mais informações, confira [Elemento Policies](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>Elemento DefaultRunAsPolicy
Especifique uma conta de usuário padrão para todos os pacotes de código de serviço que não tenham um RunAsPolicy específico definido na seção ServiceManifestImport. Para obter mais informações, veja [Elemento DefaultRunAsPolicy](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>Elementos de manifesto de serviço VotingWeb
### <a name="servicemanifest-element"></a>Elemento ServiceManifest
Descreve declarativamente o tipo de serviço e a versão. Ele lista o código que pode ser atualizado independentemente, a configuração e os pacotes de código que compõem um pacote de serviço para dar suporte a um ou mais tipos de serviço. Recursos, configurações de diagnóstico e metadados de serviço, como o tipo de serviço, as propriedades de integridade e as métricas de balanceamento de carga, também são especificados. Para obter mais informações, confira [Elemento ServiceManifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento ServiceTypes
Define os tipos de serviço com suporte por um CodePackage no manifesto. Quando um serviço é instanciado em relação a um desses tipos de serviço, todos os pacotes de código declarados nesse manifesto são ativados com a execução de seus pontos de entrada. Os tipos de serviço são declarados no nível do manifesto e não no nível do pacote de código. Para obter mais informações, confira [Elemento ServiceTypes](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Elemento StatelessServiceType
Descreve um tipo de serviço sem estado. Para obter mais informações, confira [Elemento StatelessServiceType](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento CodePackage
Descreve um pacote de código que dá suporte a um tipo de serviço definido. Quando um serviço é instanciado em relação a um desses tipos de serviço, todos os pacotes de código declarados nesse manifesto são ativados com a execução de seus pontos de entrada. Os processos resultantes devem registrar os tipos de serviço com suporte no tempo de execução. Quando há vários pacotes de código, eles serão todos ativados sempre que o sistema procurar por qualquer um dos tipos de serviço declarados. Para obter mais informações, confira [Elemento CodePackage](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>Elemento SetupEntryPoint
Um ponto de entrada privilegiado que por padrão é executado com as mesmas credenciais do que o Service Fabric (normalmente, a conta NETWORKSERVICE) antes de qualquer outro ponto de entrada. O executável especificado pelo EntryPoint normalmente é o host de serviço de longa duração. A presença de um ponto de entrada de instalação separado evita a necessidade de executar o host de serviço com altos privilégios por longos períodos de tempo. Para obter mais informações, veja [Elemento SetupEntryPoint](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Elemento ExeHost
 Para obter mais informações, veja [Elemento ExeHost](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Elemento Program
O nome do executável.  Por exemplo, "MySetup.bat" ou "MyServiceHost.exe". Para obter mais informações, veja [elemento de programa](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Elemento Arguments
 Para obter mais informações, veja [Elemento Arguments](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Elemento WorkingFolder
O diretório de trabalho para o processo no pacote de códigos no nó de cluster em que o aplicativo é implantado. Você pode especificar três valores: Work (o padrão), CodePackage ou CodeBase. CodeBase especifica que o diretório de trabalho está definido para o diretório no qual o EXE é definido no pacote de código. CodePackage define o diretório de trabalho para ser a raiz do pacote de códigos, independentemente do local em que o EXE é definido no diretório do pacote de códigos. Trabalho define o diretório de trabalho para uma pasta exclusiva criada no nó.  Essa pasta é a mesma para toda a instância do aplicativo. Por padrão, o diretório de trabalho de todos os processos no aplicativo é definido para a pasta de trabalho do aplicativo. Esse é o local em que os processos podem gravar os dados. Não é recomendável gravar dados no pacote de códigos ou base de código, uma vez que essas pastas podem ser compartilhadas entre instâncias diferentes do aplicativo e ser excluídas. Para obter mais informações, veja [Elemento WorkingFolder](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>Elemento ConsoleRedirection

> [!WARNING]
> Não use redirecionamento de console em um aplicativo de produção, use-o apenas para desenvolvimento local e depuração. Redireciona a saída do console do script de inicialização para um arquivo de saída na pasta do aplicativo chamada "log" no nó de cluster em que o aplicativo é implantado e executado. Para obter mais informações, veja [Elemento ConsoleRedirection](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>Elemento EntryPoint
O executável especificado pelo EntryPoint normalmente é o host de serviço de longa duração. A presença de um ponto de entrada de instalação separado evita a necessidade de executar o host de serviço com altos privilégios por longos períodos de tempo. O executável especificado pelo EntryPoint é executado depois que o SetupEntryPoint é encerrado com êxito. O processo resultante será monitorado e reiniciado (começando novamente com SetupEntryPoint) se ele terminar ou falhar. Para obter mais informações, confira [Elemento EntryPoint](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Elemento ExeHost
 Para obter mais informações, veja [Elemento ExeHost](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>Elemento ConfigPackage
Declara uma pasta nomeada pelo atributo Name, em PackageRoot, que contém um arquivo Settings.xml. Esse arquivo contém seções de configurações de par chave-valor, definido pelo usuário, que o processo pode ler de volta no tempo de execução. Durante a atualização, se apenas a versão do ConfigPackage tiver sido alterada, o processo de execução não será reiniciado. Em vez disso, um retorno de chamada notifica o processo de que as definições de configuração foram alteradas para que possam ser recarregadas dinamicamente. Para obter mais informações, confira [Elemento ConfigPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Elemento Resources
Descreve os recursos usados por esse serviço, que podem ser declarados sem modificar o código compilado e alterados quando o serviço é implantado. O acesso a esses recursos é controlado pelas seções Entidades de segurança e Políticas do manifesto do aplicativo. Para obter mais informações, confira [Elemento Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento Endpoints
Define pontos de extremidade para o serviço. Para obter mais informações, confira [Elemento Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento Endpoint
O ponto de extremidade, declarado no manifesto do serviço, a ser substituído. Para obter mais informações, confira [Elemento Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>Elementos do manifesto do serviço VotingData
### <a name="servicemanifest-element"></a>Elemento ServiceManifest
Descreve declarativamente o tipo de serviço e a versão. Ele lista o código que pode ser atualizado independentemente, a configuração e os pacotes de código que compõem um pacote de serviço para dar suporte a um ou mais tipos de serviço. Recursos, configurações de diagnóstico e metadados de serviço, como o tipo de serviço, as propriedades de integridade e as métricas de balanceamento de carga, também são especificados. Para obter mais informações, confira [Elemento ServiceManifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento ServiceTypes
Define os tipos de serviço com suporte por um CodePackage no manifesto. Quando um serviço é instanciado em relação a um desses tipos de serviço, todos os pacotes de código declarados nesse manifesto são ativados com a execução de seus pontos de entrada. Os tipos de serviço são declarados no nível do manifesto e não no nível do pacote de código. Para obter mais informações, confira [Elemento ServiceTypes](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>Elemento StatefulServiceType
Descreve um tipo de serviço com estado. Para obter mais informações, veja [Elemento StatefulServiceType](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento CodePackage
Descreve um pacote de código que dá suporte a um tipo de serviço definido. Quando um serviço é instanciado em relação a um desses tipos de serviço, todos os pacotes de código declarados nesse manifesto são ativados com a execução de seus pontos de entrada. Os processos resultantes devem registrar os tipos de serviço com suporte no tempo de execução. Quando há vários pacotes de código, eles serão todos ativados sempre que o sistema procurar por qualquer um dos tipos de serviço declarados. Para obter mais informações, confira [Elemento CodePackage](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Elemento EntryPoint
O executável especificado pelo EntryPoint normalmente é o host de serviço de longa duração. A presença de um ponto de entrada de instalação separado evita a necessidade de executar o host de serviço com altos privilégios por longos períodos de tempo. O executável especificado pelo EntryPoint é executado depois que o SetupEntryPoint é encerrado com êxito. O processo resultante será monitorado e reiniciado (começando novamente com SetupEntryPoint) se ele terminar ou falhar. Para obter mais informações, confira [Elemento EntryPoint](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Elemento ExeHost
 Para obter mais informações, veja [Elemento ExeHost](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Elemento Program
O nome do executável.  Por exemplo, "MySetup.bat" ou "MyServiceHost.exe". Para obter mais informações, veja [elemento de programa](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Elemento WorkingFolder
O diretório de trabalho para o processo no pacote de códigos no nó de cluster em que o aplicativo é implantado. Você pode especificar três valores: Work (o padrão), CodePackage ou CodeBase. CodeBase especifica que o diretório de trabalho está definido para o diretório no qual o EXE é definido no pacote de código. CodePackage define o diretório de trabalho para ser a raiz do pacote de códigos, independentemente do local em que o EXE é definido no diretório do pacote de códigos. Trabalho define o diretório de trabalho para uma pasta exclusiva criada no nó.  Essa pasta é a mesma para toda a instância do aplicativo. Por padrão, o diretório de trabalho de todos os processos no aplicativo é definido para a pasta de trabalho do aplicativo. Esse é o local em que os processos podem gravar os dados. Não é recomendável gravar dados no pacote de códigos ou base de código, uma vez que essas pastas podem ser compartilhadas entre instâncias diferentes do aplicativo e ser excluídas. Para obter mais informações, veja [Elemento WorkingFolder](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>Elemento ConfigPackage
Declara uma pasta nomeada pelo atributo Name, em PackageRoot, que contém um arquivo Settings.xml. Esse arquivo contém seções de configurações de par chave-valor, definido pelo usuário, que o processo pode ler de volta no tempo de execução. Durante a atualização, se apenas a versão do ConfigPackage tiver sido alterada, o processo de execução não será reiniciado. Em vez disso, um retorno de chamada notifica o processo de que as definições de configuração foram alteradas para que possam ser recarregadas dinamicamente. Para obter mais informações, confira [Elemento ConfigPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Elemento DataPackage
Declara uma pasta, nomeada pelo atributo Name, em PackageRoot que contém os arquivos de dados estáticos a serem consumidos pelo processo no tempo de execução. O Service Fabric reciclará todos os EXEs e DLLHOSTs especificados nos pacotes de host e de suporte quando um dos pacotes de dados listados no manifesto do serviço forem atualizados. Para obter mais informações, confira [Elemento DataPackage](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Elemento Resources
Descreve os recursos usados por esse serviço, que podem ser declarados sem modificar o código compilado e alterados quando o serviço é implantado. O acesso a esses recursos é controlado pelas seções Entidades de segurança e Políticas do manifesto do aplicativo. Para obter mais informações, confira [Elemento Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento Endpoints
Define pontos de extremidade para o serviço. Para obter mais informações, confira [Elemento Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento Endpoint
O ponto de extremidade, declarado no manifesto do serviço, a ser substituído. Para obter mais informações, confira [Elemento Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

