---
title: "Implantar um executável existente ao Service Fabric do Azure | Microsoft Docs"
description: "Instruções passo a passo sobre como empacotar um aplicativo existente como um executável de convidado, para que ele possa ser implantado em um cluster do Service Fabric"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/02/2017
ms.author: mfussell;mikhegn
ms.openlocfilehash: a8579c66cbfb0968a3659316aa5f03b798f4e332
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-guest-executable-to-service-fabric"></a>Implantar um executável convidado à Service Fabric
Você pode executar qualquer tipo de código, como Node.js, Java ou C++ no Service Fabric como um serviço. O Service Fabric se refere a esses tipos de serviço como executáveis convidados.

Os executáveis convidados são tratados pela Service Fabric como serviços sem monitoração de estado. Como resultado, eles são colocados em nós em um cluster, com base na disponibilidade e em outras métricas. Este artigo descreve como empacotar e implantar um executável convidado em um cluster do Service Fabric usando o Visual Studio ou um utilitário de linha de comando.

Neste artigo, abordaremos as etapas para empacotar um convidado executável e implantá-lo no Service Fabric.  

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Benefícios de executar um executável convidado no Service Fabric
Há várias vantagens de executar um convidado executável em um cluster de Service Fabric:

* Alta disponibilidade. Os aplicativos que são executados no Service Fabric estão no modo de alta disponibilidade. O Service Fabric garante que as instâncias de um aplicativo estão em execução.
* Monitoramento de integridade. O monitoramento de integridade do Service Fabric detecta se um aplicativo está em execução e fornece informações de diagnóstico se houver uma falha.   
* Gerenciamento do ciclo de vida do aplicativo. Além de oferecer atualizações sem tempo de inatividade, o Service Fabric fornecerá reversão automática da versão anterior, se houver um evento de integridade deficiente durante uma atualização.    
* Densidade. Você pode executar vários aplicativos no cluster, o que elimina a necessidade um hardware próprio para a execução de cada aplicativo.
* Capacidade de descoberta: usando o REST, você pode chamar o serviço Nomenclatura do Service Fabric para localizar outros serviços no cluster. 

## <a name="samples"></a>Exemplos
* [Exemplo de empacotamento e implantação de um executável convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exemplo de dois executáveis convidados (C# e nodejs) se comunicando por meio do Serviço de nomenclatura usando REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Visão geral do aplicativo e dos arquivos de manifesto do serviço
Como parte da implantação de um executável convidado, é útil entender o modelo de implantação e empacotamento do Service Fabric conforme descrito no [modelo de aplicativo](service-fabric-application-model.md). O modelo de empacotamento do Service Fabric depende de dois arquivos XML: o aplicativo e o manifesto do serviço. A definição de esquema dos arquivos ApplicationManifest.xml e ServiceManifest.xml é instalada com o SDK do Service Fabric em *C:\Arquivos de Programas\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifesto do aplicativo** O manifesto do aplicativo é usado para descrever o aplicativo. Ele lista os serviços que o compõem e os outros parâmetros usados para definir como um ou mais serviços devem ser implantados, como o número de instâncias.

  No Service Fabric, um aplicativo é uma unidade de implantação e atualização. Um aplicativo pode ser atualizado como uma única unidade em que falhas e reversões potencias são gerenciadas. O Service Fabric garante que o processo de atualização seja completamente bem-sucedido ou, se a atualização falhar, ele não deixará o aplicativo em um estado desconhecido/instável.
* **Manifesto do serviço** O manifesto do serviço descreve os componentes de um serviço. Ele inclui dados como o nome e o tipo do serviço e seu código e configuração. O manifesto do serviço também inclui alguns parâmetros adicionais que podem ser usados para configurar o serviço após sua implantação.

## <a name="application-package-file-structure"></a>Estrutura de arquivos do pacote de aplicativos
Para implantar um aplicativo no Service Fabric, o aplicativo deve seguir uma estrutura de diretórios predefinida. A seguir está um exemplo dessa estrutura.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

O ApplicationPackageRoot contém o arquivo ApplicationManifest.xml que define o aplicativo. Um subdiretório para cada serviço incluído no aplicativo é usado para conter todos os artefatos que o serviço requer. Esses subdiretórios são ServiceManifest.xml e, normalmente, o seguinte:

* *Código*. Esse diretório contém o código de serviço.
* *Config*. Esse diretório contém o arquivo Settings.xml (e outros arquivos, se necessário) que o serviço pode acessar no tempo de execução para recuperar definições de configuração específicas.
* *Dados*. Esse é um diretório adicional para armazenar dados locais adicionais que podem ser necessário para o serviço. Os dados devem ser usados para armazenar somente dados efêmeros. O Service Fabric não copia nem replica alterações ao diretório de dados se o serviço precisar ser realocado (por exemplo, durante o failover).

> [!NOTE]
> Você não precisa criar os diretórios `config` e `data` se não precisar deles.
>
>

## <a name="package-an-existing-executable"></a>Empacotar um executável existente
Ao empacotar um executável convidado, você pode optar por usar um modelo de projeto do Visual Studio ou [criar o pacote de aplicativos manualmente](#manually). Usando o Visual Studio, a estrutura do pacote de aplicativos e os arquivos de manifesto são criados pelo novo modelo de projeto para você.

> [!TIP]
> A maneira mais fácil de empacotar um executável existente do Windows em um serviço é usar o Visual Studio e, no Linux, usar o Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Usar o Visual Studio para empacotar e implantar um executável existente
O Visual Studio fornece um modelo de serviço do Service Fabric para ajudar você a implantar um executável convidado em um cluster do Service Fabric.

1. Escolha **Arquivo** > **Novo Projeto** e crie um aplicativo de Service Fabric.
2. Escolha **Executável Convidado** como o modelo de serviço.
3. Clique em **Procurar** para selecionar a pasta com o executável e preencha o restante dos parâmetros para criar o serviço.
   * *Comportamento do Pacote de Código*. Pode ser definido para copiar todo o conteúdo da pasta para o Projeto do Visual Studio, o que será útil se o executável não mudar. Se você espera que o executável mude e se quiser a capacidade de obter novas compilações dinamicamente, poderá optar por vincular para a pasta. Você pode usar pastas vinculadas ao criar o projeto de aplicativo no Visual Studio. Isso cria um vínculo com o local de origem de dentro do projeto, tornando possível atualizar o executável convidado em seu destino de origem. Essas atualizações se tornam parte do pacote de aplicativo no build.
   * *Programa* especifica o executável que deve ser executado para iniciar o serviço.
   * *Argumentos* especificam os argumentos que devem ser passados para o executável. Pode ser uma lista de parâmetros com argumentos.
   * *WorkingFolder* especifica o diretório de trabalho para o processo que será iniciado. Você pode especificar três valores:
     * `CodeBase` especifica que o diretório de trabalho será definido no diretório de código no pacote de aplicativos (diretório `Code` mostrado na estrutura de arquivos anterior).
     * `CodePackage` especifica que o diretório de trabalho será definido como a raiz do pacote de aplicativos (`GuestService1Pkg` mostrado na estrutura de arquivos anterior).
     * `Work` especifica que os arquivos são colocados em um subdiretório chamado trabalho.
4. Dê um nome ao seu serviço e clique em **OK**.
5. Se seu serviço precisar de um ponto de extremidade para comunicação, você é possível adicionar o protocolo, a porta e o tipo ao arquivo ServiceManifest.xml. Por exemplo: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Agora você pode usar o pacote e publicar a ação em seu cluster local ao depurar a solução no Visual Studio. Quando estiver pronto, você poderá publicar o aplicativo em um cluster remoto ou fazer check-in da solução para o controle do código-fonte.
7. Vá para o final deste artigo para conferir como exibir seu serviço executável de convidado em execução no Service Fabric Explorer.

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Usar o Yeoman para empacotar e implantar um executável existente no Linux

O procedimento para criar e implantar um executável convidado no Linux é igual à implantação de um aplicativo csharp ou java.

1. Em um terminal, digite `yo azuresfguest`.
2. Nome do seu aplicativo.
3. Nomeie o serviço e forneça os detalhes, incluindo o caminho do executável e os parâmetros com os quais ele deve ser invocado.

O Yeoman cria um pacote de aplicativos com os devidos arquivos de aplicativo e manifesto juntamente com a instalação e desinstalação dos scripts.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Empacotar e implantar manualmente um executável existente
O processo de empacotar manualmente um executável convidado baseia-se nas seguintes etapas gerais:

1. Criar a estrutura de diretórios do pacote.
2. Adicionar os arquivos de configuração e código do aplicativo.
3. Editar o arquivo de manifesto do serviço.
4. Editar o arquivo de manifesto do aplicativo.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Criar a estrutura de diretórios do pacote
Você pode iniciar criando a estrutura de diretório, conforme descrito na seção anterior, "Estrutura de arquivo de pacote de aplicativo".

### <a name="add-the-applications-code-and-configuration-files"></a>Adicionar os arquivos de configuração e código do aplicativo
Depois de criar a estrutura de diretório, você pode adicionar os arquivos de configuração e código do aplicativo aos diretórios de code e config. Também é possível criar diretórios adicionais ou subdiretórios nos diretórios code ou config.

O Service Fabric faz uma `xcopy` do conteúdo do diretório raiz do aplicativo, portanto, não há estrutura predefinida a usar que não criar dois diretórios principais, código e configurações. (Você pode escolher nomes diferentes, se desejar. Mais detalhes estão na próxima seção.)

> [!NOTE]
> Inclua todos os arquivos e dependências de que o aplicativo precisa. O Service Fabric copia o conteúdo do pacote de aplicativos em todos os nós do cluster nos quais os serviços do aplicativo serão implantados. O pacote deve conter todos os códigos que o aplicativo precisa para ser executado. Não presuma que as dependências já estão instaladas.
>
>

### <a name="edit-the-service-manifest-file"></a>Editar o arquivo de manifesto do serviço
A próxima etapa é editar o arquivo de manifesto do serviço para incluir as seguintes informações:

* O nome da fila do tipo de serviço. Esse é um ID que o Service Fabric usa para identificar um serviço.
* O comando a ser usado para iniciar o aplicativo (ExeHost).
* Qualquer script que precisa ser executado para configurar o aplicativo (SetupEntrypoint).

Segue um exemplo de um arquivo `ServiceManifest.xml` :

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

As seções a seguir repassam as diferente partes do arquivo que você precisa atualizar.

#### <a name="update-servicetypes"></a>Atualizar ServiceTypes
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Você pode escolher qualquer nome que desejar para `ServiceTypeName`. O valor é usado no arquivo `ApplicationManifest.xml` para identificar o serviço.
* Especifique `UseImplicitHost="true"`. Esse atributo informa ao Service Fabric que o serviço se baseia em um aplicativo independente, de modo tudo o que o Service Fabric precisa fazer é iniciá-lo como um processo e monitorar a integridade.

#### <a name="update-codepackage"></a>Atualizar CodePackage
O elemento CodePackage especifica o local (e a versão) do código do serviço.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

O elemento `Name` é usado para especificar o nome do diretório no pacote de aplicativos que contém o código do serviço. `CodePackage` também tem o atributo `version`. Isso pode ser usado para especificar a versão do código e também pode ser usado para atualizar o código do serviço usando a infraestrutura de gerenciamento de ciclo de vida de aplicativo no Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Opcional: atualizar SetupEntrypoint
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
O elemento SetupEntryPoint é usado para especificar qualquer executável ou arquivo em lotes que precise ser executado antes da inicialização do código do serviço. É uma etapa opcional, de modo que não precisa ser incluída se não houver necessidade de inicialização. O SetupEntrypoint é executado toda vez que o serviço é reiniciado.

Há apenas um SetupEntryPoint, de modo que os scripts de instalação precisarão ser agrupados em um único arquivo em lotes se a instalação do aplicativo exigir vários scripts. O SetupEntryPoint pode executar qualquer tipo de arquivo: arquivos executáveis, arquivos em lotes e cmdlets do PowerShell. Para obter mais detalhes, confira [Configure SetupEntryPoint](service-fabric-application-runas-security.md)(Configurar SetupEntryPoint).

No exemplo anterior, o SetupEntryPoint executa um arquivo em lote chamado `LaunchConfig.cmd`, que está localizado no subdiretório `scripts` do diretório de código (supondo que o elemento WorkingFolder esteja definido como CodeBase).

#### <a name="update-entrypoint"></a>Atualizar EntryPoint
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

O elemento `EntryPoint` no arquivo de manifesto do serviço é usado para especificar como iniciar o serviço. O elemento `ExeHost` especifica o executável e os argumentos que devem ser usados para iniciar o serviço.

* `Program` especifica o nome do executável que deve iniciar o serviço.
* `Arguments` especifica os argumentos que devem ser passados para o executável. Pode ser uma lista de parâmetros com argumentos.
* `WorkingFolder` especifica o diretório de trabalho para o processo que será iniciado. Você pode especificar três valores:
  * `CodeBase` especifica que o diretório de trabalho será definido no diretório de código no pacote de aplicativos (diretório `Code` na estrutura de arquivos anterior).
  * `CodePackage` especifica que o diretório de trabalho será definido como a raiz do pacote de aplicativos (`GuestService1Pkg` na estrutura de arquivos anterior).
    * `Work` especifica que os arquivos são colocados em um subdiretório chamado trabalho.

A WorkingFolder é útil para definir o diretório de trabalho correto, de modo que os caminhos relativos possam ser usados pelo aplicativo ou pelos scripts de inicialização.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Atualizar pontos de extremidade e registrar com o Serviço de Nomenclatura para comunicação
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
No exemplo anterior, o elemento `Endpoint` especifica os pontos de extremidade nos quais o aplicativo pode escutar. Neste exemplo, o aplicativo Node.js escuta ao http na porta 3000.

Além disso, você pode solicitar ao Service Fabric para publicar este ponto de extremidade no Serviço de Cadastramento, de modo que outros serviços possam descobrir o endereço do ponto de extremidade deste serviço. Isso permite que você se comunique entre os serviços que são executáveis de convidado.
O endereço do ponto de extremidade publicado está no formato `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme` e `PathSuffix` são atributos opcionais. `IPAddressOrFQDN` é o Endereço IP ou o nome de domínio totalmente qualificado do nó em que esse executável é colocado e calculado para você.

No exemplo a seguir, quando o serviço for implantado no Service Fabric Explorer, você verá um ponto de extremidade semelhante ao `http://10.1.4.92:3000/myapp/` publicado para a instância de serviço. Se esta for um computador local, você verá `http://localhost:3000/myapp/`.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Você pode usar esses endereços com o [proxy reverso](service-fabric-reverseproxy.md) para se comunicar entre os serviços.

### <a name="edit-the-application-manifest-file"></a>Editar o arquivo de manifesto do aplicativo
Depois de configurar o arquivo `Servicemanifest.xml`, você deve fazer algumas alterações ao arquivo `ApplicationManifest.xml` para garantir o uso do tipo e do nome de serviço corretos.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
No elemento `ServiceManifestImport` , você pode especificar um ou mais serviços para incluí-los no aplicativo. Os serviços são referenciados com `ServiceManifestName`, que especifica o nome do diretório em que o arquivo `ServiceManifest.xml` está localizado.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Configurar registro em log
Para executáveis convidados, é útil poder ver os logs do console para descobrir se o aplicativo e os scripts de configuração mostram algum erro.
O redirecionamento do console pode ser configurado no arquivo `ServiceManifest.xml` usando o elemento `ConsoleRedirection`.

> [!WARNING]
> Nunca use a política de redirecionamento de console em um aplicativo implantado na produção, pois isso pode afetar o failover do aplicativo. *Só* use isso para fins de depuração e de desenvolvimento locais.  
>
>

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

`ConsoleRedirection` pode ser usado para redirecionar a saída do console (stdout e stderr) para um diretório de trabalho. Isso possibilita verificar se não há erros durante a instalação ou execução do aplicativo no cluster do Service Fabric.

O `FileRetentionCount` determina quantos arquivos são salvos no diretório de trabalho. Um valor de 5, por exemplo, significa que os arquivos de log das cinco execução anteriores são armazenados no diretório de trabalho.

`FileMaxSizeInKb` especifica o tamanho máximo dos arquivos de log.

Arquivos de log são salvos em um dos diretórios de trabalho do serviço. Para determinar o local em que os arquivos estão localizados, use o Service Fabric Explorer para determinar em qual nó o serviço está sendo executado e qual diretório de trabalho está sendo usado. Esse processo é abordado mais adiante neste artigo.

## <a name="deployment"></a>Implantação
A última etapa será [implantar seu aplicativo](service-fabric-deploy-remove-applications.md). O script do PowerShell a seguir mostra como implantar seu aplicativo no cluster de desenvolvimento local e iniciar um novo serviço do Service Fabric.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [Compacte o pacote](service-fabric-package-apps.md#compress-a-package) antes de copiar no armazenamento de imagens, se o pacote for grande ou tiver muitos arquivos. Leia mais [aqui](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

Um serviço do Service Fabric pode ser implantado em várias "configurações". Por exemplo, pode ser implantado como uma ou várias instâncias, ou pode ser implantado de forma que haja uma instância do serviço em cada nó do cluster do Service Fabric.

O parâmetro `InstanceCount` do cmdlet `New-ServiceFabricService` é usado para especificar quantas instâncias do serviço devem ser iniciadas no cluster do Service Fabric. Você pode definir o valor de `InstanceCount` dependendo do tipo de aplicativo que está implantando. Os dois cenários mais comuns são:

* `InstanceCount = "1"`. Neste caso, somente uma instância do serviço é implantada no cluster. O agendador do Service Fabric determina em qual nó o serviço será implantado.
* `InstanceCount ="-1"`. Neste caso, uma instância do serviço é implantada em cada nó do cluster do Service Fabric. O resultado é ter uma (e apenas uma) instância do serviço para cada nó no cluster.

Essa é uma configuração útil para aplicativos de front-end (por exemplo, um ponto de extremidade REST), pois os aplicativos cliente precisam se “conectar” a qualquer um dos nós do cluster para usar o ponto de extremidade. Essa configuração também pode ser usada quando, por exemplo, todos os nós do cluster do Service Fabric estiverem conectados a um balanceador de carga. O tráfego do cliente então pode ser distribuído entre o serviço está em execução em todos os nós no cluster.

## <a name="check-your-running-application"></a>Verificar seu aplicativo em execução
No Gerenciador do Service Fabric, identifique o nó em que o serviço está em execução. Neste exemplo, ele está executando no Node1:

![Nó em que o serviço está em execução](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Se navegar até o nó e procurar o aplicativo, você verá as informações essenciais do nó, incluindo sua localização no disco.

![Local no disco](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Se navegar até o diretório usando o Gerenciador de Servidores, você poderá localizar o diretório de trabalho e a pasta de log do serviço, como mostra a seguinte captura de tela: 

![Local do log](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como empacotar um executável convidado e implantá-lo à Service Fabric. Consulte os seguintes artigos para tarefas e informações relacionadas.

* [Amostra de empacotamento e implantação de um executável convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), incluindo um link para o pré-lançamento da ferramenta de empacotamento
* [Exemplo de dois executáveis convidados (C# e nodejs) se comunicando por meio do Serviço de nomenclatura usando REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)
* [Implantar vários executáveis de convidado](service-fabric-deploy-multiple-apps.md)
* [Criar seu primeiro aplicativo do Service Fabric usando o Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
