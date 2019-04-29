---
title: Executar um serviço do Azure Service Fabric nas contas de sistema e de segurança local | Microsoft Docs
description: Saiba como executar um aplicativo do Service Fabric nas contas de sistema e de segurança local.  Criar entidades de segurança e aplique a política Run-As para executar com segurança seus serviços.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: aljo
ms.openlocfilehash: 28cd1162d7cae2b3a16062bdf18a2971e1f05aad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621166"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Executar um serviço como uma conta de usuário ou conta de sistema local
Usando o Azure Service Fabric, é possível proteger aplicativos em execução no cluster em contas de usuário diferentes. Por padrão, os aplicativos de Service Fabric são executados na conta sob a qual o processo Fabric.exe está sendo executado. O Service Fabric também fornece a capacidade de executar aplicativos em um usuário local ou conta do sistema. Os tipos de conta do sistema local com suporte são **LocalUser**, **NetworkService**, **LocalService** e **LocalSystem**.  Se você estiver executando o Service Fabric em um cluster autônomo do Windows, será possível executar um serviço em [Contas de domínio do Active Directory](service-fabric-run-service-as-ad-user-or-group.md) ou [Contas de serviço gerenciado de grupo](service-fabric-run-service-as-gmsa.md).

No manifesto do aplicativo, você define as contas de usuário necessárias para executar serviços ou recursos seguros na seção **Entidades de segurança**. É possível também pode definir e criar grupos de usuários para que um ou mais usuários possam ser gerenciados em conjunto. Isso é útil quando houver vários usuários para diferentes pontos de entrada de serviço e eles precisarem de privilégios comuns disponíveis no nível de grupo.  Os usuários são referenciados em uma política RunAs, que é aplicada a um serviço específico ou a todos os serviços no aplicativo. 

Por padrão, a política RunAs é aplicada ao ponto de entrada principal.  Também é possível aplicar uma política RunAs ao ponto de entrada de configuração, caso precise [executar determinadas operações de configuração de privilégio elevado em uma conta do sistema](service-fabric-run-script-at-service-startup.md), ou em ambos os pontos de entrada de configuração e principal.  

> [!NOTE] 
> Se você aplicar uma política RunAs a um serviço e o manifesto do serviço declarar recursos de ponto de extremidade com o protocolo HTTP, você deverá especificar um **SecurityAccessPolicy**.  Para obter mais informações, consulte [Atribuir uma política de acesso de segurança a pontos de extremidade HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Executar um serviço como um usuário local
É possível criar um usuário local que pode ser usado para ajudar a proteger um serviço dentro do aplicativo. Quando um tipo de conta **LocalUser** é especificado na seção Entidades do manifesto do aplicativo, o Service Fabric cria contas de usuário local em máquinas nas quais o aplicativo foi implantado. Por padrão, essas contas não têm os mesmos nomes especificados no manifesto do aplicativo (por exemplo, *Customer3* no exemplo de manifesto do aplicativo a seguir). Em vez disso, eles são gerados dinamicamente e têm senhas aleatórias.

Na seção **RunAsPolicy** de um **ServiceManifestImport**, especifique a conta de usuário da seção **Entidades de segurança** para executar o pacote de códigos de serviço.  O exemplo a seguir mostra como criar um usuário local e aplicar uma política RunAs ao ponto de entrada principal:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>Criar um grupo de usuários local
Você pode criar grupos de usuários e adicionar um ou mais usuários ao grupo. Isso será útil se houverem vários usuários para pontos de entrada de serviço diferentes e eles precisarem de privilégios comuns disponíveis no nível do grupo. O exemplo de manifesto do aplicativo a seguir mostra um grupo local nomeado *LocalAdminGroup* que tem privilégios de administrador. Dois usuários, *Customer1* e *Customer2*, tornam-se membros desse grupo local. Na seção **ServiceManifestImport**, uma política RunAs é aplicada para executar o *Stateful1Pkg* pacote de códigos *Customer2*.  Outra política RunAs é aplicada para executar o pacote de códigos *Web1Pkg* como *Customer1*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Groups>
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <User Name="Customer1">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <User Name="Customer2">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Aplicar uma política padrão a todos os pacotes de códigos de serviço
Use a seção **DefaultRunAsPolicy** para especificar uma conta de usuário padrão para todos os pacotes de códigos que não tenham uma **RunAsPolicy** específica definida. Quando a maioria dos pacotes de códigos especificados no manifesto do serviço usado por um aplicativo precisar ser executada com o mesmo usuário, o aplicativo poderá definir apenas uma política RunAs padrão nessa conta de usuário. O exemplo a seguir especifica que quando um pacote de códigos não tiver uma **RunAsPolicy** especificada, ele deverá executar no usuário **MyDefaultAccount** especificado na seção de entidades de segurança.  Os tipos de conta com suporte são LocalUser, NetworkService, LocalSystem e LocalService.  Se estiver usando um usuário ou serviço local, especifique também o nome da conta e senha.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Depurar um pacote de códigos localmente usando redirecionamento de console
Ocasionalmente, é útil para fins de depuração ver a saída do console de um serviço em execução. É possível definir uma política de redirecionamento do console no ponto de entrada no manifesto do serviço, que grava a saída em um arquivo. A saída do arquivo é gravada na pasta de aplicativos chamada **log** no nó do cluster no qual o aplicativo é implantado e executado. 

> [!WARNING]
> Nunca use a política de redirecionamento de console em um aplicativo implantado na produção, pois isso pode afetar o failover do aplicativo. *Só* use isso para fins de depuração e de desenvolvimento locais.  
> 
> 

O exemplo de manifesto de serviço a seguir mostra a habilitação do redirecionamento do console com um valor FileRetentionCount:

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
* [Entenda o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
