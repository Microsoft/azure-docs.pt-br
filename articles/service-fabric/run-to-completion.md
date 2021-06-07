---
title: RunToCompletion semântica no Service Fabric
description: Descreve a semântica RunToCompletion no Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: 6f2f6aa4380fcf6909957118bf682275350ce68c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000259"
---
# <a name="runtocompletion"></a>RunToCompletion

A partir da versão 7,1, Service Fabric dá suporte à semântica **RunToCompletion** para [contêineres][containers-introduction-link] e aplicativos [executáveis de convidado][guest-executables-introduction-link] . Essas semânticas habilitam aplicativos e serviços que concluem uma tarefa e saem, em oposição a, sempre executando aplicativos e serviços.

Antes de prosseguir com este artigo, recomendamos familiarizar-se com o [modelo de aplicativo Service Fabric][application-model-link] e o [modelo de Hospedagem de Service Fabric][hosting-model-link].

> [!NOTE]
> Atualmente, não há suporte para semântica RunToCompletion para serviços gravados usando o modelo de programação [Reliable Services][reliable-services-link] .
 
## <a name="runtocompletion-semantics-and-specification"></a>Semântica e especificação do RunToCompletion
A semântica RunToCompletion pode ser especificada como um **ExecutionPolicy** ao [importar o manifesto][application-and-service-manifests-link]. A política especificada é herdada por todos os CodePackages que compõem o manifesto. O trecho de ApplicationManifest.xml a seguir fornece um exemplo.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy** permite os dois atributos a seguir:
* **Tipo:** **RunToCompletion** atualmente é o único valor permitido para este atributo.
* **Reiniciar:** Esse atributo especifica a política de reinicialização que é aplicada ao CodePackages que inclui o pacote de pacotes, em caso de falha. Uma CodePackage saindo com um **código de saída diferente de zero** é considerada como falha. Os valores permitidos para esse atributo são **OnFailure** e **nunca** com **OnFailure** é o padrão.

Com a política de reinicialização **definida como** onfail, se qualquer CodePackage falhar **(código de saída diferente de zero)**, ele será reiniciado, com as desvantagens entre as falhas repetidas. Com a política de reinicialização definida como **nunca**, se qualquer CodePackage falhar, o status de implantação do DeployedServicePackage será marcado como **com falha** , mas outros CodePackages terão permissão para continuar a execução. Se todos os CodePackages que compõem o pacote de execução para a conclusão bem-sucedida **(código de saída 0)**, o status de implantação do DeployedServicePackage será marcado como **RanToCompletion**. 

## <a name="complete-example-using-runtocompletion-semantics"></a>Exemplo completo usando a semântica RunToCompletion

Vejamos um exemplo completo usando a semântica RunToCompletion.

> [!IMPORTANT]
> O exemplo a seguir pressupõe familiaridade com a criação de [aplicativos de contêiner do Windows usando o Service Fabric e o Docker][containers-getting-started-link].
>
> Este exemplo faz referência a mcr.microsoft.com/windows/nanoserver:1809. Os contêineres do Windows Server não são compatíveis em todas as versões de um sistema operacional do host. Para obter mais informações, consulte [Compatibilidade de versão de contêiner do Windows](/virtualization/windowscontainers/deploy-containers/version-compatibility).

O ServiceManifest.xml a seguir descreve um pacote de pacotes que consiste em dois CodePackages, que representam contêineres. *RunToCompletionCodePackage1* apenas registra em log uma mensagem para **stdout** e sai. *RunToCompletionCodePackage2* executa ping no endereço de loopback por um tempo e, em seguida, sai com um código de saída de **0**, **1** ou **2**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

O ApplicationManifest.xml a seguir descreve um aplicativo baseado no ServiceManifest.xml discutido acima. Ele especifica **RunToCompletion** **ExecutionPolicy** para *WindowsRunToCompletionServicePackage* com uma política de reinicialização de **OnFailure**. Após a ativação do *WindowsRunToCompletionServicePackage*, seu constituinte CodePackages será iniciado. *RunToCompletionCodePackage1* deve sair com êxito na primeira ativação. No entanto, o *RunToCompletionCodePackage2* pode falhar **(código de saída diferente de zero)**. nesse caso, ele será reiniciado, pois a política de reinicialização é **OnFailure**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>Consultando o status de implantação de um DeployedServicePackage
O status de implantação de um DeployedServicePackage pode ser consultado do PowerShell usando [Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] ou do C# usando [FabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync (cadeia de caracteres, Uri, Cadeia de caracteres)][deployed-service-package-fabricclient-link]

## <a name="considerations-when-using-runtocompletion-semantics"></a>Considerações ao usar a semântica RunToCompletion

Os seguintes pontos devem ser indicados para o suporte atual do RunToCompletion.
* Essas semânticas só têm suporte para [contêineres][containers-introduction-link] e aplicativos [executáveis de convidado][guest-executables-introduction-link] .
* Cenários de atualização para aplicativos com semântica RunToCompletion não são permitidos. Os usuários devem excluir e recriar esses aplicativos, se necessário.
* Eventos de failover podem fazer com que o CodePackages seja executado novamente após a conclusão bem-sucedida, no mesmo nó ou em outros nós do cluster. Exemplos de eventos de failover são, reinicializações de nó e Service Fabric atualizações de tempo de execução em um nó.

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
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[fabric-client-link]: /dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: /dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync
