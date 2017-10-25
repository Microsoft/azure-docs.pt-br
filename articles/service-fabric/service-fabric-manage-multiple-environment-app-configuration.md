---
title: "Gerenciar vários ambientes no Service Fabric | Microsoft Docs"
description: "Os aplicativos do Service Fabric podem ser executados em clusters que variam de tamanho de um computador para milhares de computadores. Em alguns casos, você desejará configurar seu aplicativo de forma diferente para esses ambientes variados. Este artigo aborda como definir parâmetros de aplicativo diferentes por ambiente."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: mikkelhegn
ms.openlocfilehash: 671cc9b0f7b7b37fcf5b052f7e34bc98e66b2838
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-application-parameters-for-multiple-environments"></a>Gerenciar parâmetros do aplicativo para vários ambientes
Você pode criar clusters do Service Fabric usando em qualquer lugar de um a milhares de computadores. Embora os binários de aplicativo possam ser executados sem modificação em um amplo espectro de ambientes, com frequência, você desejará configurar o aplicativo de forma diferente, dependendo do número de computadores em que ele estiver sendo implantado.

Como um exemplo simples, considere a `InstanceCount` para um serviço sem estado. Quando você estiver executando aplicativos no Azure, geralmente, você desejará definir esse parâmetro como o valor especial “-1”. Essa configuração garante que o serviço esteja em execução em cada nó no cluster (ou em todos os nós no tipo de nó, caso você tenha definido uma restrição de posicionamento). No entanto, essa configuração não é adequada para um cluster de um único computador, pois não é possível ter vários processos escutando o mesmo ponto de extremidade em um único computador. Em vez disso, normalmente, você define `InstanceCount` como “1”.

## <a name="specifying-environment-specific-parameters"></a>Especificando parâmetros específicos do ambiente
A solução para esse problema de configuração é um conjunto de serviços padrão parametrizados e arquivos de parâmetros do aplicativo que preencham os valores de parâmetro para um determinado ambiente. Os parâmetros padrão dos serviços e do aplicativo são configurados nos manifestos do aplicativo e do serviço. A definição de esquema dos arquivos ServiceManifest.xml e ApplicationManifest.xml é instalada com o SDK do e as ferramentas do Service Fabric em *C:\Arquivos de Programas\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

### <a name="default-services"></a>Serviços padrão
Os aplicativos do Service Fabric são compostos de uma coleção de instâncias de serviço. Embora seja possível para você criar um aplicativo vazio e, em seguida, criar dinamicamente todas as instâncias de serviço, a maioria dos aplicativos tem um conjunto de serviços principais que sempre deverá ser criado quando o aplicativo for instanciado. Eles são chamados de "serviços padrão". Eles são especificados no manifesto do aplicativo, com espaços reservados para configuração por ambiente incluídos entre colchetes adicionais:

```xml
  <DefaultServices>
      <Service Name="Stateful1">
          <StatefulService
              ServiceTypeName="Stateful1Type"
              TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
              MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

              <UniformInt64Partition
                  PartitionCount="[Stateful1_PartitionCount]"
                  LowKey="-9223372036854775808"
                  HighKey="9223372036854775807"
              />
        </StatefulService>
    </Service>
  </DefaultServices>
```

Cada um dos parâmetros nomeados deve ser definido dentro do elemento Parameters do manifesto do aplicativo:

```xml
    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>
```

O atributo DefaultValue especifica o valor a ser usado na ausência de um parâmetro mais específico para um determinado ambiente.

> [!NOTE]
> Nem todos os parâmetros da instância de serviço são adequados para a configuração por ambiente. No exemplo acima, os valores LowKey e HighKey para o esquema de particionamento do serviço são definidos explicitamente para todas as instâncias do serviço, já que o intervalo de partição é uma função do domínio de dados, não do ambiente.
> 
> 

### <a name="per-environment-service-configuration-settings"></a>Definições de configuração de serviço por ambiente
O [modelo de aplicativo do Service Fabric](service-fabric-application-model.md) permite que os serviços incluam pacotes de configuração com pares de chave e valor personalizados legíveis em tempo de execução. Os valores dessas configurações também podem ser diferenciados pelo ambiente por meio da especificação de uma `ConfigOverride` no manifesto do aplicativo.

Suponha que você tenha a seguinte configuração no arquivo Config\Settings.xml para o serviço `Stateful1`:

```xml
  <Section Name="MyConfigSection">
     <Parameter Name="MaxQueueSize" Value="25" />
  </Section>
```
Para substituir esse valor por um par de aplicativo/ambiente específico, crie uma `ConfigOverride` ao importar o manifesto do serviço no manifesto do aplicativo.

```xml
  <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>
```
Esse parâmetro pode então ser configurado pelo ambiente como mostrado acima. Você pode fazer isso declarando-o na seção de parâmetros do manifesto do aplicativo e especificando valores específicos nos arquivos de parâmetro do aplicativo.

> [!NOTE]
> No caso de definições de configuração de serviço, há três locais onde o valor de uma chave pode ser definido: o pacote de configuração de serviço, o manifesto do aplicativo e o arquivo de parâmetro do aplicativo. O Service Fabric sempre escolherá o arquivo de parâmetro de aplicativo primeiro (se especificado), o manifesto do aplicativo e, por fim, o pacote de configuração.
> 
> 

### <a name="setting-and-using-environment-variables"></a>Configurando e usando variáveis de ambiente 
Você pode especificar e definir variáveis de ambiente no arquivo ServiceManifest.xml e, em seguida, substituí-las no arquivo ApplicationManifest.xml de acordo com a instância.
O exemplo abaixo mostra duas variáveis de ambiente, uma com um valor definido e a outra é substituída. Você pode usar os parâmetros de aplicativo para definir valores de variáveis de ambiente da mesma forma que eles foram usados para substituições de configuração.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
    <EnvironmentVariables>
      <EnvironmentVariable Name="MyEnvVariable" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentVariables>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
```
Para substituir as variáveis de ambiente no ApplicationManifest.xml, faça referência ao pacote de códigos no ServiceManifest com o elemento `EnvironmentOverrides`.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="MyCode">
      <EnvironmentVariable Name="MyEnvVariable" Value="mydata"/>
    </EnvironmentOverrides>
  </ServiceManifestImport>
 ``` 
 Depois que a instância de serviço nomeada for criada, você poderá acessar as variáveis de ambiente no código. Por exemplo, em C#, você pode fazer o seguinte

```csharp
    string EnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

### <a name="service-fabric-environment-variables"></a>Variáveis de ambiente do Service Fabric
O Service Fabric tem variáveis de ambiente internas definidas para cada instância de serviço. A lista completa de variáveis de ambiente pode ser consultada logo abaixo. Os itens destacados em negrito são os que você utilizará em seu serviço; o restante será usado pelo tempo de execução do Service Fabric. 

* Fabric_ApplicationHostId
* Fabric_ApplicationHostType
* Fabric_ApplicationId
* **Fabric_ApplicationName**
* Fabric_CodePackageInstanceId
* **Fabric_CodePackageName**
* **Fabric_Endpoint_[YourServiceName]TypeEndpoint**
* **Fabric_Folder_App_Log**
* **Fabric_Folder_App_Temp**
* **Fabric_Folder_App_Work**
* **Fabric_Folder_Application**
* Fabric_NodeId
* **Fabric_NodeIPOrFQDN**
* **Fabric_NodeName**
* Fabric_RuntimeConnectionAddress
* Fabric_ServicePackageInstanceId
* Fabric_ServicePackageName
* Fabric_ServicePackageVersionInstance
* FabricPackageFileName

O código abaixo mostra como listar as variáveis de ambiente do Service Fabric
 ```csharp
    foreach (DictionaryEntry de in Environment.GetEnvironmentVariables())
    {
        if (de.Key.ToString().StartsWith("Fabric"))
        {
            Console.WriteLine(" Environment variable {0} = {1}", de.Key, de.Value);
        }
    }
```
Veja abaixo exemplos de variáveis de ambiente para um tipo de aplicativo chamado `GuestExe.Application` com um tipo de serviço chamado `FrontEndService` quando executado no computador de desenvolvimento local.

* **Fabric_ApplicationName = fabric:/GuestExe.Application**
* **Fabric_CodePackageName = Code**
* **Fabric_Endpoint_FrontEndServiceTypeEndpoint = 80**
* **Fabric_NodeIPOrFQDN = localhost**
* **Fabric_NodeName = _Node_2**

### <a name="application-parameter-files"></a>Arquivos de parâmetros de aplicativo
O projeto de aplicativo do Service Fabric pode incluir um ou mais arquivos de parâmetro de aplicativo. Cada um deles define os valores específicos para os parâmetros definidos no manifesto do aplicativo:

```xml
    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="3" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>
```
Por padrão, um novo aplicativo inclui três arquivos de parâmetro de aplicativo, chamados Local.1Node.xml, Local.5Node.xml e Cloud.xml:

![Arquivos de parâmetros de aplicativo no Gerenciador de Soluções][app-parameters-solution-explorer]

Para criar um arquivo de parâmetro, basta copiar e colar um existente e fornecer um novo nome a ele.

## <a name="identifying-environment-specific-parameters-during-deployment"></a>Identificando parâmetros específicos do ambiente durante a implantação
No momento da implantação, você precisa escolher o arquivo de parâmetro adequado para aplicar ao seu aplicativo. Você pode fazer isso por meio da caixa de diálogo Publicar no Visual Studio ou por meio do PowerShell.

### <a name="deploy-from-visual-studio"></a>Implantar com o Visual Studio
Você pode escolher na lista de arquivos de parâmetro disponíveis ao publicar seu aplicativo no Visual Studio.

![Escolher um arquivo de parâmetro na caixa de diálogo Publicar][publishdialog]

### <a name="deploy-from-powershell"></a>Implantar do PowerShell
O script `Deploy-FabricApplication.ps1` do PowerShell incluído no modelo de projeto de aplicativo aceita um perfil de publicação como parâmetro, e o PublishProfile contém uma referência para o arquivo de parâmetros do aplicativo.

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre alguns dos principais conceitos discutidos neste tópico, confira a [visão geral técnica do Service Fabric](service-fabric-technical-overview.md). Para obter informações sobre outras funcionalidades de gerenciamento de aplicativo disponíveis no Visual Studio, confira [Gerenciar seus aplicativos do Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
