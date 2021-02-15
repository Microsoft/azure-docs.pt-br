---
title: Inicializador CodePackages em Service Fabric
description: Descreve o inicializador CodePackages no Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 3be079b97c2660437344f88203fdda06cc6d6740
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86258972"
---
# <a name="initializer-codepackages"></a>Initializer CodePackages

A partir da versão 7,1, Service Fabric dá suporte ao **inicializador CodePackages** para [contêineres][containers-introduction-link] e aplicativos [executáveis de convidado][guest-executables-introduction-link] . O inicializador CodePackages fornece a oportunidade de executar inicializações no escopo do pacote de pacotes antes que outros CodePackages comecem a execução. Sua relação com um pacote de pacotes é análoga ao que um [SetupEntryPoint][setup-entry-point-link] é para um CodePackage.

Antes de prosseguir com este artigo, recomendamos familiarizar-se com o [modelo de aplicativo Service Fabric][application-model-link] e o [modelo de Hospedagem de Service Fabric][hosting-model-link].

> [!NOTE]
> No momento, não há suporte para inicializadores CodePackages para serviços gravados usando o modelo de programação [Reliable Services][reliable-services-link] .
 
## <a name="semantics"></a>Semântica

Espera-se que um inicializador CodePackage seja executado para **concluir a conclusão com êxito (código de saída 0)**. Um inicializador com falha CodePackage é reiniciado até que seja concluído com êxito. Vários inicializadores CodePackages são permitidos e são executados para a **conclusão bem-sucedida**, **sequencialmente**, **em uma ordem especificada** antes de outros CodePackages no pacote iniciar a execução.

## <a name="specifying-initializer-codepackages"></a>Especificando o inicializador CodePackages
Você pode marcar um CodePackage como um inicializador definindo o atributo do **inicializador** como **true** no manifesto. Quando há vários CodePackages de inicializador, sua ordem de execução pode ser especificada por meio do atributo **ExecOrder** . **ExecOrder** deve ser um inteiro não negativo e só é válido para o inicializador CodePackages. O inicializador CodePackages com valores inferiores de **ExecOrder** são executados primeiro. Se **ExecOrder** não for especificado para um inicializador CodePackage, um valor padrão de 0 será assumido. A ordem de execução relativa do inicializador CodePackages com o mesmo valor de **ExecOrder** não está especificada.

O trecho de código do manifesto a seguir descreve três CodePackages dois dos quais são marcados como inicializadores. Quando esse pacote de pacotes é ativado, o *InitCodePackage0* é executado primeiro, pois tem o menor valor de **ExecOrder**. Após a conclusão bem-sucedida (código de saída 0) de *InitCodePackage0*, *InitCodePackage1* será executado. Finalmente, após a conclusão bem-sucedida de *InitCodePackage1*, *WorkloadCodePackage* será executado.

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>Exemplo completo usando inicializador CodePackages

Vejamos um exemplo completo usando o inicializador CodePackages.

> [!IMPORTANT]
> O exemplo a seguir pressupõe familiaridade com a criação de [aplicativos de contêiner do Windows usando o Service Fabric e o Docker][containers-getting-started-link].
>
> Este exemplo faz referência a mcr.microsoft.com/windows/nanoserver:1809. Os contêineres do Windows Server não são compatíveis em todas as versões de um sistema operacional do host. Para obter mais informações, consulte [Compatibilidade de versão de contêiner do Windows](/virtualization/windowscontainers/deploy-containers/version-compatibility).

A ServiceManifest.xml a seguir baseia-se no trecho de código do manifesto descrito anteriormente. *InitCodePackage0*, *InitCodePackage1* e *WorkloadCodePackage* são CodePackages que representam contêineres. Após a ativação, *InitCodePackage0* é executado primeiro. Ele registra uma mensagem em um arquivo e sai. Em seguida, *InitCodePackage1* é executado, que também registra uma mensagem em um arquivo e sai. Por fim, o *WorkloadCodePackage* começa a execução. Ele também registra uma mensagem em um arquivo, gera o conteúdo do arquivo para **stdout** e, em seguida, executa pings para sempre.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

O ApplicationManifest.xml a seguir descreve um aplicativo baseado no ServiceManifest.xml discutido acima. Observe que ele especifica a mesma montagem de **volume** para todos os contêineres, ou seja, **C:\WorkspaceOnHost** é montado em **C:\WorkspaceOnContainer** em todos os três contêineres. O efeito líquido é que todos os contêineres gravam no mesmo arquivo de log na ordem em que são ativados.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
Depois que o pacote de pacotes for ativado com êxito, o conteúdo de **C:\WorkspaceOnHost\log.txt** deverá ser o seguinte.

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir para obter informações relacionadas.

* [Service Fabric e contêineres.][containers-introduction-link]
* [Executáveis Service Fabric e convidado.][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
