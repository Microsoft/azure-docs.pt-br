---
title: Implantar um aplicativo Node.js que usa MongoDB para o Azure Service Fabric | Microsoft Docs
description: Instruções passo a passo sobre como empacotar executáveis convidados para implantar em um cluster do Service Fabric do Azure
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: chackdan
editor: ''
ms.assetid: b76bb756-c1ba-49f9-9666-e9807cf8f92f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 69df9eff85d96c9cc6ca7fa1d3aabd2c54fae416
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583852"
---
# <a name="deploy-multiple-guest-executables"></a>Implantar vários executáveis de convidado
Este artigo mostra como empacotar e implantar vários executáveis de convidado no Azure Service Fabric. Para criar e implantar um pacote de Service Fabric único, leia como [implantar um executável de convidado no Service Fabric](service-fabric-deploy-existing-app.md).

Embora este passo a passo mostre como implantar um aplicativo com um front-end Node.js que usa MongoDB como o repositório de dados, você pode aplicar as etapas a qualquer aplicativo que tenha dependências de outro aplicativo.   

Você pode usar o Visual Studio para gerar o pacote de aplicativos que contém vários executáveis de convidado. Veja [Uso do Visual Studio para empacotar um aplicativo existente](service-fabric-deploy-existing-app.md). Depois de adicionar o primeiro executável de convidado, clique com o botão direito do mouse no projeto de aplicativo e selecione **Adicionar -> Novo serviço do Service Fabric** para adicionar o segundo projeto do executável de convidado à solução. Observação: Se você optar por vincular a origem no projeto do Visual Studio, criando a solução do Visual Studio, garantirá que seu pacote de aplicativo será atualizado com as alterações na fonte. 

## <a name="samples"></a>Exemplos
* [Exemplo de empacotamento e implantação de um executável convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exemplo de dois executáveis convidados (C# e nodejs) se comunicando por meio do Serviço de nomenclatura usando REST](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Empacotar manualmente o aplicativo executável de vários convidados
Como alternativa, você pode empacotar manualmente o executável de convidado. Para o empacotamento manual, este artigo usa a ferramenta de empacotamento do Service Fabric, que está disponível em [https://aka.ms/servicefabricpacktool](https://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Como empacotar o aplicativo Node.js
Este artigo pressupõe que o Node.js não esteja instalado em nós do cluster do Service Fabric. Como consequência, você precisa adicionar o Node.exe ao diretório raiz de seu aplicativo de nó antes do empacotamento. A estrutura de diretórios do aplicativo Node.js (usando a estrutura da Web Express e o mecanismo de modelagem Jade) deve ser semelhante ao mostrado abaixo:

```
|-- NodeApplication
    |-- bin
        |-- www
    |-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
    |-- public
        |-- images
        |-- etc.
    |-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

Como uma próxima etapa, crie um pacote de aplicativos para o aplicativo Node.js. O código a seguir cria um pacote de aplicativos do Service Fabric que contém o aplicativo Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Veja a seguir uma descrição dos parâmetros que estão sendo usados:

* **/source** : aponta para o diretório do aplicativo que deve ser empacotado.
* **/target** : define o diretório no qual o pacote deve ser criado. Esse diretório deve ser diferente do diretório de origem.
* **/appname** : define o nome do aplicativo existente. É importante entender que isso significa o nome do serviço no manifesto, e não o nome do aplicativo do Service Fabric.
* **/exe** define o arquivo executável que o Service Fabric deve iniciar, neste caso `node.exe`.
* **/ma** : define o argumento usado para iniciar o arquivo executável. Como o Node.js não está instalado, o Service Fabric precisa iniciar o servidor Web do Node.js executando `node.exe bin/www`.  `/ma:'bin/www'` diz à ferramenta de empacotamento para usar `bin/www` como argumento para node.exe.
* **/AppType** : define o nome do tipo de aplicativo do Service Fabric.

Se você navegar até o diretório especificado no parâmetro /target, será possível ver que a ferramenta criou um pacote totalmente funcional do Service Fabric, como mostrado abaixo:

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Agora, o ServiceManifest.xml gerado tem uma seção que descreve como o servidor Web do Node.js deve ser iniciado, como mostrado no snippet de código a seguir:

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
Neste exemplo, o servidor Web do Node.js escuta a porta 3000, portanto, você precisa atualizar as informações do ponto de extremidade no arquivo ServiceManifest.xml, como mostrado abaixo.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Como empacotar o aplicativo MongoDB
Agora que você empacotou o aplicativo Node.js, prossiga e empacote o MongoDB. Como mencionado anteriormente, as etapas que você percorrer agora não são específicas do Node.js e MongoDB. Na verdade, elas se aplicam a todos os aplicativos que devem ser empacotados juntamente como um único aplicativo do Service Fabric.  

Para empacotar o MongoDB, convém empacotar os arquivos Mongod.exe e Mongo.exe. Os dois binários estão localizados no diretório `bin` do diretório de instalação do MongoDB. A estrutura de diretórios é parecida com o seguinte.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
O Service Fabric precisa iniciar o MongoDB com um comando semelhante ao mostrado abaixo e, portanto, você precisa usar o parâmetro `/ma` ao empacotar o MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Os dados não serão preservados no caso de uma falha de nó se você colocar o diretório de dados do MongoDB no diretório local do nó. Você deve usar o armazenamento durável ou implementar um conjunto de réplicas do MongoDB para evitar a perda de dados.  
>
>

No PowerShell ou no shell de comando, executamos a ferramenta de empacotamento com os seguintes parâmetros:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Para adicionar o MongoDB ao seu pacote de aplicativos do Service Fabric é necessário garantir que o parâmetro /target aponte para o mesmo diretório que já contém o manifesto do aplicativo com o aplicativo Node.js. Também é preciso garantir que você esteja usando o mesmo nome de ApplicationType.

Vamos navegar até o diretório e examinar o que a ferramenta criou.

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Como você pode ver, a ferramenta adicionou uma nova pasta, MongoDB, ao diretório que contém os binários do MongoDB. Se você abrir o arquivo `ApplicationManifest.xml` , poderá ver que agora o pacote contém o aplicativo Node.js e o MongoDB. O código a seguir mostra o conteúdo do manifesto do aplicativo.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>Publicar o aplicativo
A última etapa é publicar o aplicativo no cluster do Service Fabric local usando os scripts do PowerShell abaixo:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Depois que o aplicativo é publicado com êxito para o cluster local, você pode acessar o aplicativo Node. js na porta inserida no manifesto do serviço de aplicativo do Node. js - por exemplo, http:\//localhost:3000.

Neste tutorial, você viu como empacotar facilmente dois aplicativos existentes como um único aplicativo do Service Fabric. Você também aprendeu como implantá-lo no Service Fabric para que ele possa beneficiar dos recursos do Service Fabric, como alta disponibilidade e integração do sistema de integridade.


## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Adicionar mais executáveis convidados a um aplicativo existente usando o Yeoman no Linux

Para adicionar outro serviço a um aplicativo já criado usando `yo`, execute as seguintes etapas: 
1. Altere o diretório para a raiz do aplicativo existente.  Por exemplo, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` é o aplicativo criado pelo Yeoman.
2. Execute `yo azuresfguest:AddService` e forneça os detalhes necessários.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre como implantar contêineres com a [Visão geral de contêineres e do Service Fabric](service-fabric-containers-overview.md)
* [Exemplo de empacotamento e implantação de um executável convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exemplo de dois executáveis convidados (C# e nodejs) se comunicando por meio do Serviço de nomenclatura usando REST](https://github.com/Azure-Samples/service-fabric-containers)
