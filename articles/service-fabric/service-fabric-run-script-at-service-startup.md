---
title: Executar um script quando um serviço do Azure Service Fabric iniciar | Microsoft Docs
description: Saiba como configurar uma política para um ponto de entrada de configuração de serviço do Service Fabric e executar um script no momento de inicialização do serviço.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: 76be814e0dd4c054fc3a873716dbfe395eeeb2dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837770"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Executar um script de inicialização do serviço como uma conta de usuário ou sistema local
Antes de um arquivo executável do serviço do Service Fabric iniciar, pode ser necessário executar um trabalho de configuração ou instalação.  Por exemplo, configurar as variáveis de ambiente. Você pode especificar um script a ser executado antes que o executável do serviço seja iniciado no manifesto do serviço para o serviço. Ao configurar uma política de RunAs para o ponto de entrada de configuração de serviço você pode alterar a conta em que o arquivo do executável funciona.  Um ponto de entrada de instalação separado permite executar a configuração de privilégio elevado por um curto período, de modo que o executável do host de serviço não precise ser executado com privilégios elevados por um longo período.

Conforme descrito no ponto de entrada de configuração (**SetupEntryPoint** no [manifesto do serviço](service-fabric-application-and-service-manifests.md)) é um ponto de entrada privilegiado que, por padrão, executa as mesmas credenciais do Service Fabric (normalmente a conta *NetworkService*) antes de qualquer ponto de entrada. O executável especificado pelo **EntryPoint** normalmente é o host de serviço de execução longa. O executável **EntryPoint** é executado depois que o executável **SetupEntryPoint** é encerrado com êxito. O processo resultante é monitorado e reiniciado, começando novamente com **SetupEntryPoint**, caso ele termine ou falhe. 

## <a name="configure-the-service-setup-entry-point"></a>Configurar um ponto de entrada de instalação do serviço
A seguir está um exemplo de manifesto do serviço simples para um serviço se estado que especifica um script de instalação *MySetup.bat* no serviço **SetupEntryPoint**.  **Argumentos** é usado para passar os argumentos para o script quando ele é executado.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Configurar a política para um ponto de entrada de instalação do serviço
Por padrão, o executável de ponto de entrada de instalação do serviço é executado com as mesmas credenciais do Service Fabric (normalmente a conta *NetworkService*).  No manifesto do aplicativo, você pode alterar as permissões de segurança para executar o script de inicialização em uma conta de sistema local ou uma conta de administrador.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Configurar a política usando uma conta do sistema local
O exemplo de manifesto de aplicativo a seguir mostra como configurar o ponto de entrada de configuração de serviço para executar na conta de administrador do usuário (SetupAdminUser).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

Primeiro, crie uma seção **Principals** com um nome de usuário SetupAdminUser. A conta de suário SetupAdminUser é membro do grupo do sistema Administradores.

Em seguida, na seção **ServiceManifestImport**, configure uma política para aplicar essa entidade de segurança ao **SetupEntryPoint**. Essa política informa ao Service Fabric que quando o arquivo **MySetup.bat** for executado ele deve ser executado como SetupAdminUser (com privilégios de administrador). Considerando que você *não* aplicou uma política ao ponto de entrada principal, o código em **MyServiceHost.exe** será executado na conta do sistema **NetworkService**. Essa é a conta padrão que todos os pontos de entrada de serviço são executados como.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Configurar a política usando contas do sistema local
Em geral, é preferível executar o script de inicialização usando uma conta do sistema local em vez de uma conta de administrador. A execução da política RunAs como membro do grupo Administradores geralmente não funciona bem, porque os computadores têm o UAC (Controle de Acesso de Usuário) habilitado por padrão. Em tais casos, a recomendação é executar o SetupEntryPoint como LocalSystem em vez de um usuário local adicionado ao grupo Administradores. O exemplo a seguir mostra a definição de SetupEntryPoint para ser executado como LocalSystem:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> Para clusters do Linux, para executar um serviço ou o ponto de entrada de instalação como **raiz**, é possível especificar o **AccountType** como **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Executar um script de instalação a partir do ponto de entrada
Agora, adicione uma script de inicialização ao projeto para ser executado com privilégios de administrador. 

No Visual Studio, clique com o botão direito do mouse no projeto de serviço e adicione um novo arquivo chamado *MySetup.bat*.

Em seguida, certifique-se de que o arquivo *MySetup.bat* está incluído no service pack. Por padrão, não é. Selecione o arquivo, clique com o botão direito do mouse para exibir o menu de contexto e escolha **Propriedades**. Na caixa de diálogo Propriedades, verifique se **Copiar para Diretório de Saída** está definido como **Copiar se mais recente**. Consulte a seguinte captura de tela.

![CopyToOutput do Visual Studio para o arquivo em lotes SetupEntryPoint][image1]

Agora, edite o arquivo *MySetup.bat* e adicione os seguintes conjuntos de comando para definir uma variável de ambiente do sistema e um arquivo de texto de saída:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Em seguida, compile e implante a solução em um cluster de desenvolvimento local. Após a inicialização do serviço, conforme mostrado no [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), é possível ver que o arquivo MySetup.bat foi bem-sucedido de duas maneiras. Abra um prompt de comando do PowerShell e digite:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Em seguida, anote o nome do nó no qual o serviço foi implantado e iniciado no [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Por exemplo, nó 2. Em seguida, navegue até a pasta de trabalho de instância do aplicativo para localizar o arquivo out.txt que mostra o valor de **TestVariable**. Por exemplo, se o serviço foi implantado no Nó 2, você pode acessar este caminho até o **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Executar os comandos do PowerShell em um ponto de entrada de instalação
Para executar o PowerShell do ponto **SetupEntryPoint**, execute **PowerShell.exe** em um arquivo em lotes que aponte para um arquivo do PowerShell. Primeiro, adicione um arquivo do PowerShell ao projeto de serviço, por exemplo, **MySetup.ps1**. Não deixe de definir a propriedade *Copiar se for mais recente* para que esse arquivo também seja incluído no pacote de serviço. O exemplo a seguir mostra um exemplo de arquivo em lotes que inicia um arquivo do PowerShell chamado MySetup.ps1, que define uma variável de ambiente do sistema chamada **TestVariable**.

MySetup.bat para iniciar o arquivo do PowerShell:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

No arquivo do PowerShell, adicione o seguinte para definir uma variável de ambiente do sistema:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Por padrão, quando o arquivo em lotes é executado, ele examina a pasta de aplicativo chamada **trabalho** em busca de arquivos. Nesse caso, quando MySetup.bat é executado, queremos que o arquivo MySetup.ps1 seja encontrado na mesma pasta, que é a pasta do **pacote de códigos** do aplicativo. Para alterar essa pasta, defina a pasta de trabalho:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Depurar um script de inicialização localmente usando o redirecionamento de console
De vez em quando, é útil para fins de depuração ver a saída do console da execução de um script de configuração. Você pode definir uma política de redirecionamento no ponto de entrada de configuração no manifesto do serviço, que escreve uma saída para um arquivo. A saída do arquivo é gravada na pasta de aplicativos chamada **log** no nó do cluster no qual o aplicativo é implantado e executado. 

> [!WARNING]
> Nunca use a política de redirecionamento de console em um aplicativo implantado na produção, pois isso pode afetar o failover do aplicativo. *Só* use isso para fins de depuração e de desenvolvimento locais.  
> 
> 

O manifesto do serviço a seguir mostra a configuração de redirecionamento do console com um valor FileRetentionCount:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Se você alterar o arquivo MySetup.ps1 agora para gravar um comando **Echo** , ele gravará o arquivo de saída para fins de depuração:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Depois de depurar seu script, remova imediatamente a política de redirecionamento de console.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre segurança de aplicativo e serviço](service-fabric-application-and-service-security.md)
* [Entenda o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
