---
title: Empacotar um aplicativo do Azure Service Fabric | Microsoft Docs
description: Como empacotar um aplicativo do Service Fabric antes de implantar em um cluster.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: b8e66a9d5bba0c48f15b1ccd3f2d47e5405db792
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60718354"
---
# <a name="package-an-application"></a>Preparar um aplicativo

Este artigo descreve como empacotar um aplicativo do Service Fabric e deixá-lo pronto para implantação.

## <a name="package-layout"></a>Layout do pacote

O manifesto do aplicativo, um ou mais manifestos do serviço e outros arquivos de pacote necessários devem ser organizados em um layout específico para implantação em um cluster do Service Fabric. Os manifestos de exemplo neste artigo precisariam estar organizados na seguinte estrutura de diretórios:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

As pastas são nomeadas para corresponder a atributos **Name** de cada elemento correspondente. Por exemplo, se o manifesto do serviço contiver dois pacotes de códigos com os nomes **MyCodeA** e **MyCodeB**, então, duas pastas com os mesmos nomes conteriam os binários necessários para cada pacote de códigos.

## <a name="use-setupentrypoint"></a>Use o SetupEntryPoint

Cenários típicos de uso do **SetupEntryPoint** quando você precisa executar um executável antes do início do serviço ou você precisa executar uma operação com privilégios elevados. Por exemplo: 

* Configurar e inicializar as variáveis de ambiente que o serviço executável precisa. Isso não é limitado apenas a executáveis gravados por meio de modelos de programação do Service Fabric. Por exemplo, npm.exe precisa de algumas variáveis de ambiente configurados para implantar um aplicativo node.js.
* Configurando o controle de acesso, instalando certificados de segurança.

Para obter mais informações sobre como configurar o **SetupEntryPoint**, consulte [Configurar a política para um ponto de entrada de instalação do serviço](service-fabric-application-runas-security.md)

<a id="Package-App"></a>

## <a name="configure"></a>Configurar

### <a name="build-a-package-by-using-visual-studio"></a>Compilar um pacote usando o Visual Studio

Se você usar o Visual Studio 2015 para criar o seu aplicativo, pode usar o comando Package para criar automaticamente um pacote que corresponda ao layout descrito acima.

Para criar um pacote, clique com o botão direito do mouse no projeto de aplicativo no Gerenciador de Soluções e escolha o comando de Pacote, conforme mostrado abaixo:

![Empacotando um aplicativo no Visual Studio][vs-package-command]

Quando o empacotamento estiver concluído, você poderá encontrar o local do pacote na janela **Saída**. A etapa de empacotamento ocorre automaticamente quando você implanta ou depura seu aplicativo no Visual Studio.

### <a name="build-a-package-by-command-line"></a>Criar um pacote pela linha de comando

Também é possível empacote seu aplicativo de modo programático usando `msbuild.exe`. Nos bastidores, o Visual Studio está sendo executado, de forma que o resultado é o mesmo.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Teste o pacote

Você pode verificar a estrutura do pacote localmente por meio do PowerShell usando o comando [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) .
Esse comando verifica se há problemas de análise de manifesto e também todas as referências. Observe que esse comando só verifica a correção estrutural de diretórios e arquivos no pacote.
Ele não verificará nenhum código ou conteúdo do pacote de dados além de verificar se todos os arquivos necessários estão presentes.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Esse erro mostra que o arquivo *MySetup.bat* referenciado no manifesto do serviço **SetupEntryPoint** está ausente do pacote de código. Depois de adicionar o arquivo que está faltando, a verificação do aplicativo é aprovada:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
True
```

Se seu aplicativo tiver [parâmetros do aplicativo](service-fabric-manage-multiple-environment-app-configuration.md) definidos, você poderá passá-los em [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) para a validação correta.

Se você souber o cluster em que o aplicativo será implantado, é recomendado passar o parâmetro `ImageStoreConnectionString`. Nesse caso, o pacote também é validado para as versões anteriores do aplicativo que já estão em execução no cluster. Por exemplo, a validação pode detectar se um pacote com a mesma versão mas conteúdo diferente já foi implantado.  

Depois que o aplicativo for empacotado corretamente e passar pela validação, considere compactar o pacote para operações de implantação mais rápidas.

## <a name="compress-a-package"></a>Compactar um pacote

Quando um pacote é grande ou tem vários arquivos, você pode compactá-lo para uma implantação mais rápida. A compactação reduz o número de arquivos e o tamanho do pacote.
Para um pacote de aplicativos compactados, [carregar o pacote de aplicativos](service-fabric-deploy-remove-applications.md#upload-the-application-package) pode levar mais tempo se comparado ao carregamento do pacote não compactado, especialmente se a compactação for feita como parte da cópia. Com compactação, o [registro](service-fabric-deploy-remove-applications.md#register-the-application-package) e o [cancelamento do registro do tipo de aplicativo](service-fabric-deploy-remove-applications.md#unregister-an-application-type) são mais rápidos.

O mecanismo de implantação é o mesmo para pacotes compactados e não compactados. Se o pacote for compactado ele será armazenado como tal no repositório de imagens do cluster e será descompactado no nó antes que o aplicativo seja executado.
A compactação substitui o pacote do Service Fabric válido pela versão compactada. A pasta deve aceitar permissões de gravação. A execução da compactação em um pacote já compactado não produz nenhuma alteração.

Você pode compactar um pacote executando o comando do PowerShell [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) com a opção `CompressPackage`. Você pode descompactar o pacote com o mesmo comando, usando a opção `UncompressPackage`.

O comando a seguir compacta o pacote sem copiá-lo para o repositório de imagens. Você pode copiar um pacote compactado para um ou mais clusters do Service Fabric, conforme necessário, usando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) sem o sinalizador `SkipCopy`.
O pacote agora inclui arquivos compactados para os pacotes `code`, `config` e `data`. O manifesto do aplicativo e os manifestos do serviço não estão compactados porque são necessários para muitas operações internas. Por exemplo, compartilhamento de pacotes, extração de nome e versão do tipo de aplicativo para determinadas validações, todos precisam acessar os manifestos. Compactar os manifestos tornaria essas operações ineficientes.

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

Como alternativa, você pode compactar e copiar o pacote com [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) em uma única etapa.
Se o pacote for grande, forneça um tempo limite suficientemente longo para que haja tempo tanto para a compactação do pacote quanto para o upload para o cluster.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Internamente, o Service Fabric computa somas de verificação para os pacotes de aplicativos para validação. Ao usar a compactação, as somas de verificação são calculadas nas versões compactadas de cada pacote. Gerar um novo CEP do mesmo pacote de aplicativos cria somas de verificação diferentes. Para evitar erros de validação, use [provisionamento diff](service-fabric-application-upgrade-advanced.md). Com essa opção, não inclua os pacotes inalterados na nova versão. Em vez disso, referencie-os diretamente do novo manifesto do serviço.

Se o provisionamento diff não for uma opção e você tiver que incluir os pacotes, gere novas versões para os pacotes `code`, `config` e `data` para evitar a incompatibilidade da soma de verificação. Gerar novas versões para pacotes inalterados é necessário quando um pacote compactado é usado, independentemente se a versão anterior usa a compactação ou não.

O pacote agora é empacotado corretamente, validado e compactado (se necessário), para que esteja pronto para [implantação](service-fabric-deploy-remove-applications.md) em um ou mais clusters do Service Fabric.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Compactar pacotes durante a implantação usando o Visual Studio

Você pode instruir o Visual Studio para compactar os pacotes em implantação, adicionando o elemento `CopyPackageParameters` em seu perfil de publicação e definir o atributo `CompressPackage` como `true`.

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Criar um sfpkg

A partir da versão 6.1, o Service Fabric permite provisionamento de um repositório externo.
Com essa opção, o pacote de aplicativos não precisa ser copiado para o repositório de imagens. Em vez disso, você pode criar um `sfpkg` e carregá-lo em um repositório externo e, em seguida, fornecer o URI de download para o Service Fabric durante o provisionamento. O mesmo pacote pode ser provisionado para vários clusters. Provisionar do armazenamento externo economiza o tempo necessário para copiar o pacote para cada cluster.

O `sfpkg` é um arquivo zip que contém o pacote de aplicativos inicial e tem a extensão ".sfpkg".
Dentro do zip, o pacote de aplicativos pode estar compactado ou não. A compactação do pacote de aplicativos dentro do zip é feita nos níveis de código, configuração e pacote de dados, como [mencionado anteriormente](service-fabric-package-apps.md#compress-a-package).

Para criar um `sfpkg`, comece com uma pasta que contém o pacote de aplicativos original, compactado ou não. Em seguida, use qualquer utilitário para compactar a pasta com a extensão ".sfpkg". Por exemplo, use [ZipFile.CreateFromDirectory](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

O `sfpkg` deve ser carregado para o repositório externo fora da banda, fora do Service Fabric. O repositório externo pode ser qualquer repositório que exponha um ponto de extremidade do REST http ou https. Durante o provisionamento, o Service Fabric executa uma operação GET para fazer o download do pacote de aplicativos `sfpkg`, portanto o repositório deve permitir acesso de leitura para o pacote.

Para provisionar o pacote, use o provisionamento o externo, o que requer o URI de download e as informações de tipo de aplicativo.

>[!NOTE]
> O provisionamento com base no caminho relativo do repositório de imagens no momento não oferece suporte a arquivos `sfpkg`. Portanto, o `sfpkg` não deve ser copiado para o repositório de imagens.

## <a name="next-steps"></a>Próximas etapas

[Implantar e remover aplicativos][10] descreve como usar o PowerShell para gerenciar instâncias do aplicativo

[Gerenciamento de parâmetros do aplicativo para vários ambientes][11] descreve como configurar os parâmetros e variáveis de ambiente para diferentes instâncias do aplicativo.

[Configurar políticas de segurança para seu aplicativo][12] descreve como executar serviços sob políticas de segurança para restringir o acesso.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md