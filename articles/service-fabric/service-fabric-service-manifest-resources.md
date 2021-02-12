---
title: Especificando Service Fabric pontos de extremidade de serviço
description: Como descrever os recursos de ponto de extremidade em um manifesto do serviço, incluindo como configurar pontos de extremidade HTTPS
ms.topic: conceptual
ms.date: 09/16/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 0ed5a4aa8993f52d42b97288cd143e6114ff36ff
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033299"
---
# <a name="specify-resources-in-a-service-manifest"></a>Especificar recursos em um manifesto do serviço
## <a name="overview"></a>Visão geral
Service Fabric aplicativos e serviços são definidos e com controle de versão usando arquivos de manifesto. Para obter uma visão geral de nível mais alto de ServiceManifest.xml e ApplicationManifest.xml, consulte [Service Fabric manifestos de aplicativo e serviço](service-fabric-application-and-service-manifests.md).

O manifesto do serviço permite que os recursos usados pelo serviço sejam declarados ou alterados sem alterar o código compilado. Service Fabric dá suporte à configuração de recursos de ponto de extremidade para o serviço. O acesso aos recursos que são especificados no manifesto do serviço pode ser controlado por meio do SecurityGroup no manifesto do aplicativo. A declaração de recursos permite que esses recursos sejam alterados no momento da implantação, o que significa que o serviço não precisa apresentar um novo mecanismo de configuração. A definição de esquema para o arquivo de ServiceManifest.xml é instalada com o SDK do Service Fabric e ferramentas para *c:\Arquivos de Programas\microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd* e está documentada na [documentação do esquema ServiceFabricServiceModel. xsd](service-fabric-service-model-schema.md).

## <a name="endpoints"></a>Pontos de extremidade
Quando um recurso de ponto de extremidade é definido no manifesto do serviço, o Service Fabric atribui portas do intervalo de portas reservadas do aplicativo quando uma porta não é explicitamente especificada. Por exemplo, examine o ponto de extremidade *ServiceEndpoint1* especificado no snippet de manifesto fornecido após este parágrafo. Além disso, os serviços também podem solicitar uma porta específica em um recurso. As réplicas do serviço em execução em nós diferentes do cluster podem receber números de porta diferentes, enquanto as réplicas do mesmo serviço em execução no mesmo nó compartilham a porta. As réplicas de serviço podem usar essas portas conforme a necessidade para replicação e escuta de solicitações de clientes.

Na ativação de um serviço que especifica um ponto de extremidade HTTPS, Service Fabric definirá a entrada de controle de acesso para a porta, associará o certificado do servidor especificado à porta e também concederá a identidade que o serviço está executando como permissões para a chave privada do certificado. O fluxo de ativação é invocado toda vez que Service Fabric é iniciado ou quando a declaração de certificado do aplicativo é alterada por meio de uma atualização. O certificado do ponto de extremidade também será monitorado quanto a alterações/renovações e as permissões serão reaplicadas periodicamente conforme necessário.

Após o encerramento do serviço, Service Fabric limpará a entrada de controle de acesso do ponto de extremidade e removerá a associação de certificado. No entanto, todas as permissões aplicadas à chave privada do certificado não serão limpas.

> [!WARNING] 
> As portas estáticas de design não devem se sobrepor ao intervalo de portas de aplicativo especificado em ClusterManifest. Se você especificar uma porta estática, atribua-a fora do intervalo de portas do aplicativo, caso contrário, isso resultará em conflitos de porta. Com a versão 6.5 CU2, emitiremos um **aviso de integridade** quando detectarmos esse conflito, mas permitirá que a implantação continue em sincronia com o comportamento 6,5 enviado. No entanto, poderemos impedir a implantação do aplicativo nas próximas versões principais.
>
> Com a versão 7,0, emiteremos um **aviso de integridade** quando detectamos que o uso do intervalo de portas do aplicativo vai além de HostingConfig:: ApplicationPortExhaustThresholdPercentage (padrão 80%).
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Se houver vários pacotes de código em um único pacote de serviço, o pacote de código também precisará ser referenciado na seção **Endpoints**.  Por exemplo, se **ServiceEndpoint2a** e **ServiceEndpoint2b** são pontos de extremidade no mesmo pacote de serviço fazendo referência a pacotes de códigos diferentes, o pacote de código correspondente a cada ponto de extremidade é esclarecido da seguinte maneira:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Consulte [Configurando o Reliable Services com estado](service-fabric-reliable-services-configuration.md) para ler mais sobre como fazer referência a pontos de extremidade por meio do arquivo de configurações do pacote de configuração (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Exemplo: especificando um ponto de extremidade HTTP para o serviço
O manifesto do serviço a seguir define um recurso de ponto de extremidade TCP e dois recursos de ponto de extremidade HTTP no elemento &lt;Recursos&gt;.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
      <Endpoint Name="ServiceEndpoint4" Protocol="https" Port="14023"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Exemplo: especificando um ponto de extremidade HTTPS para o serviço
O protocolo HTTPS fornece autenticação de servidor e também é usado para criptografar a comunicação cliente-servidor. Para habilitar o HTTPS em seu serviço do Service Fabric, especifique o protocolo na seção *Recursos -> Pontos de Extremidade -> Ponto de Extremidade* do manifesto do serviço, conforme mostrado anteriormente para o ponto de extremidade *ServiceEndpoint3*.

> [!NOTE]
> O protocolo de um serviço não pode ser alterado durante a atualização do aplicativo. Se ele for alterado durante a atualização, será uma alteração significativa.
> 

> [!WARNING] 
> Ao usar HTTPS, não use a mesma porta e o certificado de instâncias de serviço diferente (independentemente do aplicativo) implantados no mesmo nó. O upgrade de dois serviços diferentes usando a mesma porta em instâncias de aplicativo diferentes resultará em uma falha de upgrade. Para obter mais informações, consulte [Atualizando vários aplicativos com pontos de extremidade HTTPS](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Aqui está um exemplo de ApplicationManifest demonstrando a configuração necessária para um ponto de extremidade HTTPS. O certificado de servidor/ponto de extremidade pode ser declarado pela impressão digital ou pelo nome comum da entidade, e um valor deve ser fornecido. O EndpointRef é uma referência a EndpointResource no manifesto e cujo protocolo deve ter sido definido para o protocolo ' https '. Você pode adicionar mais de um EndpointCertificate.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint3"/>
      <EndpointBindingPolicy CertificateRef="SslCertByCN" EndpointRef="ServiceEndpoint4"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="SslCertByTP" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
    <EndpointCertificate Name="SslCertByCN" X509FindType="FindBySubjectName" X509FindValue="ServiceFabric-EndpointCertificateBinding-Test" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

Para clusters do Linux, o **MEU** armazenar padroniza para a pasta **/var/lib/sfcerts**.

Para obter um exemplo de um aplicativo completo que utiliza um ponto de extremidade HTTPS, consulte [Adicionar um ponto de extremidade https a um serviço de front-end da API Web ASP.NET Core usando o Kestrel](./service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest).

## <a name="port-acling-for-http-endpoints"></a>Porta atuação para pontos de extremidade HTTP
Service Fabric automaticamente os pontos de extremidade de HTTP (S) de ACL especificados por padrão. Ele **não** executará atuação automática se um ponto de extremidade não tiver um [SecurityAccessPolicy](service-fabric-assign-policy-to-endpoint.md) associado a ele e Service Fabric estiver configurado para ser executado usando uma conta com privilégios de administrador.

## <a name="overriding-endpoints-in-servicemanifestxml"></a>Substituição dos pontos de extremidade em ServiceManifest.xml

No ApplicationManifest, adicione uma seção ResourceOverrides, que será um irmão à seção ConfigOverrides. Nessa seção, é possível especificar a substituição da seção Pontos de Extremidade na seção de recursos especificada no Manifesto do serviço. A substituição de pontos de extremidade é compatível com o runtime 5.7.217/SDK 2.7.217 e superior.

Para substituir o ponto de extremidade no inmanifest usando Applicationparameters, altere o ApplicationManifest da seguinte forma:

Na seção ServiceManifestImport, adicione uma nova seção "ResourceOverrides".

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

Nos Parâmetros, adicione o seguinte:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Ao implantar o aplicativo, você pode passar esses valores como Applicationparameters.  Por exemplo:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Observação: se o valor fornecido para um determinado ApplicationParameter estiver vazio, voltaremos ao valor padrão fornecido no manifesto para o EndpointName correspondente.

Por exemplo:

Se estiver no ServiceManifest especificado

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

Suponha que o valor de Port1 e Protocol1 para parâmetros de aplicativo seja nulo ou vazio. A porta será decidida pelo ServicePortal e o protocolo será TCP.

Vamos supor que você especifica um valor incorreto. Digamos, para a porta, que você especificou um valor de cadeia de caracteres "foo" em vez de um int.  New-ServiceFabricApplication comando falhará com um erro: `The override parameter with name 'ServiceEndpoint1' attribute 'Port1' in section 'ResourceOverrides' is invalid. The value specified is 'Foo' and required is 'int'.`

## <a name="next-steps"></a>Próximas etapas

Este artigo explicou como definir pontos de extremidade no manifesto do serviço Service Fabric. Para obter exemplos mais detalhados, consulte:

> [!div class="nextstepaction"]
> [Exemplos de aplicativo e manifesto do serviço](service-fabric-manifest-examples.md)

Para obter um passo a passo de empacotamento e implantação de um aplicativo existente em um Cluster Service Fabric, consulte:

> [!div class="nextstepaction"]
> [Empacotar e implantar um executável existente no Service Fabric](service-fabric-deploy-existing-app.md)