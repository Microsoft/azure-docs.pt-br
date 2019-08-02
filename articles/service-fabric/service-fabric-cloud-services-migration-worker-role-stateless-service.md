---
title: Converter aplicativos dos Serviços de Nuvem do Azure para o Service Fabric | Microsoft Docs
description: Este guia compara as funções de trabalho e Web dos Serviços de Nuvem e os serviços sem estado do Service Fabric para ajudar a migrar dos Serviços de Nuvem para o Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 5880ebb3-8b54-4be8-af4b-95a1bc082603
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: e82abd6a7915123a94b4355e24cb94f13f9693c8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550379"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Guia de conversão de funções de trabalho e Web em serviços sem estado do Service Fabric
Este artigo descreve como migrar suas funções de trabalho e Web dos Serviços de Nuvem para serviços sem estado do Service Fabric. Esse é o caminho mais simples de migração dos Serviços de Nuvem para o Service Fabric, no caso de aplicativos cuja arquitetura geral permanecerá basicamente igual.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Projeto de Serviço de Nuvem para projeto de aplicativo Service Fabric
 Um projeto de Serviço de Nuvem e um projeto de aplicativo Service Fabric têm uma estrutura semelhante, e ambos representam a unidade de implantação de seu aplicativo, ou seja, cada um define o pacote completo que é implantado para executar seu aplicativo. Um projeto de Serviço de Nuvem contém uma ou mais funções de trabalho ou Web. Da mesma forma, um projeto de aplicativo Service Fabric contém um ou mais serviços. 

A diferença é que o projeto de Serviço de Nuvem associa a implantação do aplicativo a uma implantação de VM e, portanto, contém as definições de configuração da VM, enquanto o projeto de aplicativo Service Fabric define apenas um aplicativo que será implantado em um conjunto de máquinas virtuais existentes em um cluster do Service Fabric. O cluster do Service Fabric em si só é implantado uma vez, por meio de um modelo do Resource Manager ou por meio do portal do Azure, e vários aplicativos do Service Fabric podem ser implantados nele.

![Comparação de projeto dos Serviços de Nuvem e do Service Fabric][3]

## <a name="worker-role-to-stateless-service"></a>Função de trabalho para serviço sem estado
Conceitualmente, uma função de trabalho representa uma carga de trabalho sem estado, o que significa que cada instância da carga de trabalho é idêntica e as solicitações podem ser roteadas para qualquer instância a qualquer hora. Não se espera que as instâncias lembrem da solicitação anterior. Estado em que a carga de trabalho opera é gerenciado por um repositório de estado externo, como o armazenamento de tabelas do Azure ou Azure Cosmos DB. No Service Fabric, esse tipo de carga de trabalho é representado por um serviço sem estado. A abordagem mais simples para migrar uma função de trabalho para o Service Fabric pode ser feita pela conversão de um código de função de trabalho em um serviço sem estado.

![Função de trabalho para serviço sem estado][4]

## <a name="web-role-to-stateless-service"></a>Função Web para serviço sem estado
Semelhante à função de trabalho, uma função Web também representa uma carga de trabalho sem estado e, assim, conceitualmente, ele também pode ser mapeado para um serviço sem estado do Service Fabric. No entanto, diferentemente das funções Web, o Service Fabric não dá suporte a IIS. A migração de um aplicativo Web de uma função Web para um serviço sem estado requer primeiro a mudança para uma estrutura Web que pode ser auto-hospedada e não depende de IIS ou System.Web, como o ASP.NET Core 1.

| **Aplicativo** | **Com suporte** | **Caminho de migração** |
| --- | --- | --- |
| Web Forms do ASP.NET |Não |Converter em MVC do ASP.NET Core 1 |
| ASP.NET MVC |Com migração |Atualizar para o ASP.NET Core 1 MVC |
| ASP.NET Web API |Com migração |Usar o servidor auto-hospedado ou o ASP.NET Core 1 |
| ASP.NET Core 1 |Sim |N/D |

## <a name="entry-point-api-and-lifecycle"></a>API de ponto de entrada e ciclo de vida
As APIs de função de trabalho e do Service Fabric oferecem pontos de entrada semelhantes: 

| **Ponto de entrada** | **Função de trabalho** | **Serviço Service Fabric** |
| --- | --- | --- |
| Processando |`Run()` |`RunAsync()` |
| Iniciar VM |`OnStart()` |N/D |
| Parar VM |`OnStop()` |N/D |
| Abrir escuta para solicitações de cliente |N/D |<ul><li> `CreateServiceInstanceListener()` para serviços sem estado</li><li>`CreateServiceReplicaListener()` para serviços com estado</li></ul> |

### <a name="worker-role"></a>Função de trabalho
```csharp

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### <a name="service-fabric-stateless-service"></a>Serviço sem estado do Service Fabric
```csharp

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

Ambos têm uma substituição de "Executar" principal para começar o processamento. Os serviços do Service Fabric combinam `Run`, `Start` e `Stop` em um único ponto de entrada, `RunAsync`. O serviço deve começar a trabalhar quando `RunAsync` inicia e deve parar de funcionar quando o CancellationToken do método `RunAsync` é sinalizado. 

Há várias diferenças importantes entre o ciclo de vida e a vida útil das funções de trabalho e do Service Fabric:

* **Ciclo de vida:** A maior diferença é que uma função de trabalho é uma VM e então, seu ciclo de vida está ligado à VM, que inclui eventos quando a VM inicia e para. Um serviço do Service Fabric tem um ciclo de vida que é separado do ciclo de vida da VM; portanto, ele não inclui eventos quando a VM ou computador host inicia e para, pois eles não estão relacionados.
* **Tempo de vida:** Uma instância de função de trabalho será reciclada se o `Run` sai do método. O método `RunAsync` em um serviço do Service Fabric, no entanto, pode executar até a conclusão, e a instância do serviço permanecerá ativa. 

O Service Fabric fornece um ponto de entrada de configuração opcional de comunicação para serviços que escutam solicitações de cliente. O RunAsync e o ponto de entrada de comunicação são substituições opcionais nos serviços do Service Fabric. Seu serviço pode optar por escutar somente as solicitações do cliente ou somente executar um loop de processamento, ou ambos, motivo pelo qual o método RunAsync pode sair sem reiniciar a instância do serviço, pois ele pode continuar a escutar solicitações de cliente.

## <a name="application-api-and-environment"></a>API de aplicativo e ambiente
A API do ambiente dos Serviços de Nuvem fornece informações e funcionalidade para a instância atual da VM, bem como informações sobre outras instâncias de função VM. O Service Fabric fornece informações relacionadas ao tempo de execução e algumas informações sobre o nó em que o serviço está sendo executado. 

| **Tarefa de ambiente** | **Serviços de Nuvem** | **Service Fabric** |
| --- | --- | --- |
| Notificação de alteração e Definições de Configuração |`RoleEnvironment` |`CodePackageActivationContext` |
| Armazenamento local |`RoleEnvironment` |`CodePackageActivationContext` |
| Informações de ponto de extremidade |`RoleInstance` <ul><li>Instância atual: `RoleEnvironment.CurrentRoleInstance`</li><li>Outras funções e instância: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` para o endereço do nó atual</li><li>`FabricClient` e `ServicePartitionResolver` para descoberta de ponto de extremidade de serviço</li> |
| Ambiente de emulação |`RoleEnvironment.IsEmulated` |N/D |
| Eventos de alteração simultânea |`RoleEnvironment` |N/D |

## <a name="configuration-settings"></a>Definições de configuração
As definições de configuração nos Serviços de Nuvem são definidas para uma função VM e se aplicam a todas as instâncias dessa função VM. Essas configurações são pares chave-valor definidos nos arquivos ServiceConfiguration.*.cscfg e podem ser acessadas diretamente por meio do RoleEnvironment. No Service Fabric, as configurações se aplicam individualmente a cada serviço e a cada aplicativo, em vez de a uma VM, já que uma máquina virtual pode hospedar vários serviços e aplicativos. Um serviço é composto de três pacotes:

* **Código:** contém os executáveis de serviço, binários, DLLs e outros arquivos de que um serviço precisa para executar.
* **Config:** todos os arquivos de configuração e configurações de um serviço.
* **Dados:** arquivos de dados estáticos associados ao serviço.

Cada um desses pacotes pode ter controle de versão e atualização independentes. De forma semelhante aos Serviços de Nuvem, um pacote de configuração pode ser acessado programaticamente por uma API e há eventos disponíveis para notificar o serviço sobre alterações no pacote de configuração. Um arquivo Settings.xml pode ser usado para configuração de chave-valor e acesso programático semelhante à seção de configurações do aplicativo de um arquivo App.config. No entanto, ao contrário dos Serviços de Nuvem, um pacote de configuração do Service Fabric pode conter arquivos de configuração em qualquer formato, seja XML, JSON, YAML ou formato binário personalizado. 

### <a name="accessing-configuration"></a>Acessando configuração
#### <a name="cloud-services"></a>Serviços de Nuvem
As definições de configuração do ServiceConfiguration.*.cscfg podem ser acessadas por meio do `RoleEnvironment`. Essas configurações estão disponíveis globalmente para todas as instâncias de função na mesma implantação do Serviço de Nuvem.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Cada serviço tem seu próprio pacote de configuração individual. Não há nenhum mecanismo interno para as configurações globais que possa ser acessado por todos os aplicativos em um cluster. Ao usar o arquivo de configuração especial Settings.xml do Service Fabric em um pacote de configuração, os valores em Settings.xml podem ser substituídos no nível do aplicativo, possibilitando definições de configuração no nível do aplicativo.

As definições de configuração são acessadas em cada instância de serviço por meio do `CodePackageActivationContext`do serviço.

```csharp

ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### <a name="configuration-update-events"></a>Eventos de atualização de configuração
#### <a name="cloud-services"></a>Serviços de Nuvem
O evento `RoleEnvironment.Changed` é usado para notificar a todas as instâncias de função quando ocorre uma alteração no ambiente, como uma alteração de configuração. Isso é usado para consumir as atualizações da configuração sem reciclar instâncias de função ou reiniciar um processo de trabalho.

```csharp

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### <a name="service-fabric"></a>Service Fabric
Cada um dos três tipos de pacote em um serviço, Código, Configuração e Dados, tem eventos que notificam uma instância de serviço quando um pacote é atualizado, adicionado ou removido. Um serviço pode conter vários pacotes de cada tipo. Por exemplo, um serviço pode ter vários pacotes de configuração, cada um deles com controle de versão e atualização individuais. 

Esses eventos estão disponíveis para consumir alterações nos pacotes de serviço sem reiniciar a instância do serviço.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Tarefas de inicialização
As tarefas de inicialização são ações executadas antes de um aplicativo ser iniciado. Uma tarefa de inicialização normalmente é usada para executar scripts de instalação usando privilégios elevados. Os Serviços de Nuvem e o Service Fabric dão suporte a tarefas de inicialização. A principal diferença é que nos Serviços de Nuvem, uma tarefa de inicialização está vinculada a uma VM porque faz parte de uma instância de função, enquanto que no Service Fabric uma tarefa de inicialização está associada a um serviço, que não está vinculado a nenhuma máquina virtual específica.

| Service Fabric | Serviços de Nuvem |
| --- | --- |
| Configuração local |ServiceDefinition.csdef |
| Privilégios |"limitados" ou "elevados" |
| Sequenciamento |"simples", "em segundo plano", "primeiro plano" |

### <a name="cloud-services"></a>Serviços de Nuvem
Nos Serviços de Nuvem, um ponto de entrada de inicialização é configurado por função em ServiceDefinition.csdef. 

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### <a name="service-fabric"></a>Service Fabric
No Service Fabric, um ponto de entrada de inicialização é configurado por serviço em ServiceManifest.xml:

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## <a name="a-note-about-development-environment"></a>Uma observação sobre o ambiente de desenvolvimento
Os Serviços de Nuvem e o Service Fabric são integrados com o Visual Studio com modelos de projeto e suporte para depuração, configuração e implantação localmente e no Azure. Os Serviços de Nuvem e o Service Fabric também fornecem um ambiente de tempo de execução de desenvolvimento local. A diferença é que enquanto o tempo de execução do desenvolvimento do Serviço de Nuvem emula o ambiente do Azure no qual ele é executado, o Service Fabric não usa um emulador; ele usa o tempo de execução do Service Fabric completo. O ambiente do Service Fabric executado em sua máquina de desenvolvimento local é o mesmo ambiente executado na produção.

## <a name="next-steps"></a>Próximas etapas
Leia mais sobre os Reliable Services do Service Fabric e as diferenças fundamentais entre os Serviços de Nuvem e a arquitetura de aplicativos Service Fabric para entender como tirar proveito do conjunto completo de recursos do Service Fabric.

* [Introdução aos Reliable Services do Service Fabric](service-fabric-reliable-services-quick-start.md)
* [Guia conceitual sobre as diferenças entre os Serviços de Nuvem e o Service Fabric](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
