---
title: Usando scripts do Windows PowerShell para publicar em ambientes de desenvolvimento e teste |Microsoft Docs
description: Saiba como usar scripts do Windows PowerShell do Visual Studio para publicar em ambientes de teste e desenvolvimento.
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 5fff1301-5469-4d97-be88-c85c30f837c1
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: 92753860ec820172e46f483831eb0c1cf1acb038
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-windows-powershell-scripts-to-publish-to-dev-and-test-environments"></a>Usando scripts do Windows PowerShell para publicar para ambientes de desenvolvimento e teste
Quando você cria um aplicativo Web no Visual Studio, pode gerar um script do Windows PowerShell que pode usar posteriormente para automatizar a publicação de seu site no Azure como um aplicativo Web no Serviço de Aplicativo do Azure ou em uma máquina virtual. Você pode editar e estender o script do Windows PowerShell no editor do Visual Studio para atender às suas necessidades ou integrar o script a scripts de compilação, teste e publicação existentes.

Usando esses scripts, você pode provisionar versões personalizadas (também conhecidas como ambientes de desenvolvimento e teste) do seu site para uso temporário. Por exemplo, você pode configurar uma versão específica do seu site em uma máquina virtual do Azure ou no slot de preparo em um site para executar um conjunto de testes, reproduzir um bug, testar uma correção de bug, avaliar uma alteração de proposta ou configurar um ambiente personalizado para uma demonstração ou apresentação. Depois de criar um script que publica seu projeto, você pode recriar ambientes idênticos ao executar novamente o script conforme necessário ou executar o script com sua própria compilação do aplicativo Web para criar um ambiente personalizado de teste.

## <a name="what-you-need"></a>O que você precisa
* SDK 2.3 do Azure ou posterior. Para saber mais, consulte [Downloads do Visual Studio](http://go.microsoft.com/fwlink/?LinkID=624384) .

Você não precisa do SDK do Azure para gerar scripts para projetos Web. Esse recurso é para projetos Web, não as funções Web nos serviços de nuvem.

* Azure PowerShell 0.7.4 ou posterior. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter mais informações.
* [Windows PowerShell 3.0](http://go.microsoft.com/?linkid=9811175) ou posterior.

## <a name="additional-tools"></a>Ferramentas adicionais
Estão disponíveis ferramentas e recursos adicionais para trabalhar com o PowerShell no Visual Studio para desenvolvimento do Azure. Consulte [Ferramentas do PowerShell para Visual Studio](http://go.microsoft.com/fwlink/?LinkId=404012).

## <a name="generating-the-publish-scripts"></a>Gerando scripts de publicação
Você pode gerar scripts de publicação para uma máquina virtual que hospeda seu site ao criar um novo projeto seguindo [estas instruções](virtual-machines/windows/classic/web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Você também pode [gerar scripts de publicação para aplicativos Web no Serviço de Aplicativo do Azure](app-service/app-service-web-get-started-dotnet.md).

## <a name="scripts-that-visual-studio-generates"></a>Scripts gerados pelo Visual Studio
O Visual Studio gera uma pasta de nível de solução chamada **PublishScripts** que contém dois arquivos do Windows PowerShell, um script de publicação para sua máquina virtual ou site e um módulo que contém funções que você pode usar nos scripts. O Visual Studio também gera um arquivo no formato JSON que especifica os detalhes do projeto que você está implantando.

### <a name="windows-powershell-publish-script"></a>Script de publicação do Windows PowerShell
O script de publicação contém etapas específicas de publicação para implantar em um site ou em uma máquina virtual. O Visual Studio fornece sintaxe colorida para o desenvolvimento do Windows PowerShell. Ajuda para as funções estão disponíveis e você pode editar livremente as funções no script para atender às necessidades de mudanças.

### <a name="windows-powershell-module"></a>Módulo do Windows PowerShell
O módulo do Windows PowerShell que o Visual Studio gera contém funções que o script de publicação usa. Essas são funções do Azure PowerShell e não se destinam a serem modificadas. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter mais informações.

### <a name="json-configuration-file"></a>Arquivo de configuração JSON
O arquivo JSON é criado na pasta **Configurações** pasta e contém dados de configuração que especificam exatamente quais recursos implantar no Azure. O nome do arquivo que o Visual Studio gera é project-name-WAWS-dev.json se você tiver criado um site, ou o projeto name-VM-dev.json se você tiver criado uma máquina virtual. Aqui está um exemplo de um arquivo de configuração JSON é gerado quando você cria um site. A maior parte dos valores é autoexplicativa. O nome do site é gerado pelo Azure, portanto não pode coincidir com o nome do projeto.

```json
{
    "environmentSettings": {
        "webSite": {
            "name": "WebApplication26632",
            "location": "West US"
        },
        "databases": [{
            "connectionStringName": "DefaultConnection",
            "databaseName": "WebApplication26632_db",
            "serverName": "YourDatabaseServerName",
            "user": "sqluser2",
            "password": "",
            "edition": "",
            "size": "",
            "collation": "",
            "location": "West US"
        }]
    }
}
```
Quando você cria uma máquina virtual, o arquivo de configuração JSON é semelhante ao seguinte. Observe que um serviço de nuvem é criado como um contêiner para a máquina virtual. A máquina virtual contém os pontos de extremidade comuns para acesso à Web via HTTP e HTTPS, bem como pontos de extremidade para a implantação da Web, o que lhe permite publicar o site de seu computador local, área de trabalho remota e Windows PowerShell.

```json
{
    "environmentSettings": {
        "cloudService": {
            "name": "myusernamevm1",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myusernamevm1",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201403.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [{
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [{
            "connectionStringName": "",
            "databaseName": "",
            "serverName": "",
            "user": "",
            "password": ""
        }],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

Você pode editar a configuração JSON para alterar o que acontece quando você executa scripts de publicação. As seções `cloudService` e `virtualMachine` são necessárias, mas você pode excluir a seção `databases` se não precisar dela. As propriedades vazias no arquivo de configuração padrão que o Visual Studio gera são opcionais; aquelas que têm valores no arquivo de configuração padrão são obrigatórias.

Se você tiver um site com vários ambientes de implantação (conhecidos como slots) em vez de um único site de produção no Azure, pode incluir o nome do slot no nome do site no arquivo de configuração JSON. Por exemplo, se você tiver um site chamado **mysite** e um slot para ele chamado **test**, o URI é mysite-test.cloudapp.net, mas o nome correto a ser usado no arquivo de configuração é mysite(test). Você só pode fazer isso se o site e os slots já existirem em sua assinatura. Se não existirem, crie o site executando o script sem especificar o slot e depois crie o slot no [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885)e execute o script com o nome do site modificado. Para obter mais informações sobre os slots de implantação para aplicativos Web, consulte [Configurar ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure](app-service/web-sites-staged-publishing.md).

## <a name="how-to-run-the-publish-scripts"></a>Como gerar scripts de publicação
Se você nunca executou um script do Windows PowerShell antes, deve primeiro definir a política de execução para habilitar a execução dos scripts. Esse é um recurso de segurança para impedir que usuários executem scripts do Windows PowerShell se estiverem vulneráveis a malware ou vírus que envolvem a execução de scripts.

### <a name="run-the-script"></a>Execute o script
1. Crie o pacote de implantação na Web para seu projeto. Um pacote de implantação na Web é um arquivo compactado (arquivo .zip) que contêm arquivos que você deseja copiar em seu site ou máquina virtual. Você pode criar pacotes de implantação na Web no Visual Studio para qualquer aplicativo Web.

![Criar pacote de implantação na Web](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

Consulte [Como criar um pacote de implantação na Web no Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx). Você também pode automatizar a criação de seu pacote de implantação na Web, conforme descrito na seção **Personalizando e estendendo scripts de publicação** mais adiante neste tópico.

1. No **Gerenciador de Soluções**, abra o menu de contexto do script e, em seguida, escolha **Abrir com o ISE do Windows PowerShell**.
2. Se esta for a primeira vez que você executa scripts do Windows PowerShell neste computador, abra uma janela de prompt de comando com privilégios de administrador e digite o seguinte comando:

    ```powershell
    Set-ExecutionPolicy RemoteSigned
    ```

3. Entre no Azure usando o comando a seguir.

    ```powershell
    Add-AzureAccount
    ```

    Quando solicitado, forneça seu nome de usuário e senha.

    Observe que quando você automatiza o script, esse método para fornecer credenciais do Azure não funciona. Em vez disso, você deve usar o arquivo .publishsettings para fornecer credenciais. Uma vez apenas, você usa o comando **Get-AzurePublishSettingsFile** para baixar o arquivo do Azure e depois usa **Import-AzurePublishSettingsFile** para importar o arquivo. Para saber mais, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

4. (Opcional) Se você quiser criar recursos do Azure, como máquina virtual, banco de dados e site sem publicar o aplicativo Web, use o comando **Publish-WebApplication.ps1** com o argumento **-Configuration**, definido para o arquivo de configuração JSON. Essa linha de comando usa o arquivo de configuração JSON para determinar quais recursos criar. Como ela usa configurações padrão para outros argumentos de linha de comando, ela cria os recursos, mas não publica seu aplicativo Web. A opção –Verbose fornece mais informações sobre o que está acontecendo.

    ```powershell
    Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json
    ```

5. Use o comando **Publish-WebApplication.ps1** conforme mostrado em um dos exemplos a seguir para invocar o script e publicar seu aplicativo Web. Se você precisar substituir as configurações padrão de qualquer um dos outros argumentos, como o nome da assinatura, nome do pacote de publicação, credenciais de máquina virtual ou credenciais do servidor de banco de dados, você pode especificar esses parâmetros. Use a opção **– Verbose** para obter mais informações sobre o andamento do processo de publicação.

    ```powershell
    Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
    –SubscriptionName Contoso `
    -WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
    -DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
    -Verbose
    ```

    Se você estiver criando uma máquina virtual, o comando é semelhante ao seguinte. Este exemplo também mostra como especificar credenciais para vários bancos de dados. Para as máquinas virtuais que esses scripts criam, o certificado SSL não é de uma autoridade raiz confiável. Portanto, você precisa usar a opção **–AllowUntrusted** .

    ```powershell
    Publish-WebApplication.ps1 `
    -Configuration C:\Path\ADVM-VM-test.json `
    -SubscriptionName Contoso `
    -WebDeployPackage C:\Path\ADVM.zip `
    -AllowUntrusted `
    -VMPassword @{name = "vmUserName"; password = "YourPasswordHere"} `
    -DatabaseServerPassword @{Name="server1";Password="adminPassword1"}, @{Name="server2";Password="adminPassword2"} `
    -Verbose
    ```

    O script pode criar bancos de dados, mas não cria servidores de banco de dados. Para criar um servidor de banco de dados, você pode usar a função **New-AzureSqlDatabaseServer** no módulo do Azure.

## <a name="customizing-and-extending-the-publish-scripts"></a>Personalizando e estendendo scripts de publicação
Você pode personalizar o script de publicação e o arquivo de configuração JSON. As funções no módulo do Windows PowerShell **AzureWebAppPublishModule.psm1** não se destinam a serem modificadas. Se você quiser apenas especificar um banco de dados diferente ou alterar algumas das propriedades da máquina virtual, edite o arquivo de configuração JSON. Se quiser estender a funcionalidade do script para automatizar a criação e testar o projeto, você pode implementar stubs de função em **Publish-WebApplication.ps1**.

Para automatizar a criação de seu projeto, adicione o código que chama o MSBuild para `New-WebDeployPackage` conforme mostrado neste exemplo de código. O caminho para o comando MSBuild é diferente dependendo da versão do Visual Studio que você instalou. Para obter o caminho correto, você pode usar a função **Get-MSBuildCmd**, conforme mostrado neste exemplo.

### <a name="to-automate-building-your-project"></a>Para automatizar a criação de seu projeto
1. Adicione o parâmetro `$ProjectFile` à seção de parâmetros globais.

    ```powershell
    [Parameter(Mandatory = $false)]
    [ValidateScript({Test-Path $_ -PathType Leaf})]
    [String]
    $ProjectFile,
    ```

2. Copie a função `Get-MSBuildCmd` em seu arquivo de script.

    ```powershell
    function Get-MSBuildCmd
    {
            process
    {

                $path =  Get-ChildItem "HKLM:\SOFTWARE\Microsoft\MSBuild\ToolsVersions\" |
                                    Sort-Object {[double]$_.PSChildName} -Descending |
                                    Select-Object -First 1 |
                                    Get-ItemProperty -Name MSBuildToolsPath |
                                    Select -ExpandProperty MSBuildToolsPath

                $path = (Join-Path -Path $path -ChildPath 'msbuild.exe')

            return Get-Item $path
        }
    }
    ```

3. Substitua `New-WebDeployPackage` pelo seguinte código e substitua os espaços reservados na construção de linha `$msbuildCmd`. Esse código é para o Visual Studio 2015. Se você estiver usando o Visual Studio 2013, altere a propriedade **VisualStudioVersion** abaixo para `12.0`.

    ```powershell
    function New-WebDeployPackage
    {
        #Write a function to build and package your web application
    ```

    Para criar seu aplicativo Web, use o MsBuild.exe. Para obter ajuda, consulte a Referência de linha de comando de MSBuild em: [http://go.microsoft.com/fwlink/?LinkId=391339](http://go.microsoft.com/fwlink/?LinkId=391339)

    ```powershell
    Write-VerboseWithTime 'Build-WebDeployPackage: Start'

    $msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=14.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory

    Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
    ```

### <a name="start-execution-of-the-build-command"></a>Iniciar a execução do comando de build

```powershell
$job = Start-Process cmd.exe -ArgumentList('/C "' + $msbuildCmd + '"') -WindowStyle Normal -Wait -PassThru

if ($job.ExitCode -ne 0) {
    throw('MsBuild exited with an error. ExitCode:' + $job.ExitCode)
}

#Obtain the project name
$projectName = (Get-Item $ProjectFile).BaseName

#Construct the path to web deploy zip package
$DeployPackageDir =  '.\MSBuildOutputPath\_PublishedWebsites\{0}_Package\{0}.zip' -f $projectName

#Get the full path for the web deploy zip package. This is required for MSDeploy to work
$WebDeployPackage = Resolve-Path –LiteralPath $DeployPackageDir

Write-VerboseWithTime 'Build-WebDeployPackage: End'

return $WebDeployPackage
}
```

1. Chame a função `New-WebDeployPackage` antes dessa linha: `$Config = Read-ConfigFile $Configuration` para aplicativos Web ou `$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)` para máquinas virtuais.

    ```powershell
    if($ProjectFile)
    {
    $WebDeployPackage = New-WebDeployPackage
    }
    ```

2. Invoque o script personalizado da linha de comando usando a passagem do argumento `$Project`  como no seguinte exemplo de linha de comando.

    ```powershell
    .\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
    -ProjectFile ..\WebApplication5\WebApplication5.csproj `
    -VMPassword @{Name="VMUser";Password="Test.123"} `
    -AllowUntrusted `
    -Verbose
    ```

    Para automatizar o teste do seu aplicativo, adicione código ao `Test-WebApplication`. Certifique-se de cancelar os comentários das linhas em **Publish-WebApplication.ps1** no qual essas funções são chamadas. Se você não fornecer uma implementação, poderá compilar manualmente seu projeto com o Visual Studio e depois executar o script de publicação para publicar no Azure.

## <a name="publishing-function-summary"></a>Resumo da função de publicação
Para obter ajuda para as funções que você pode usar no prompt de comando do Windows PowerShell, use o comando `Get-Help function-name`. A Ajuda inclui exemplos e ajuda de parâmetro. O mesmo texto da Ajuda está também nos arquivos de origem de script **AzureWebAppPublishModule.psm1** e **Publish-WebApplication.ps1**. O script e a ajuda estão localizados em seu idioma do Visual Studio.

**AzureWebAppPublishModule**

| Nome da função | Descrição |
| --- | --- |
| Add-AzureSQLDatabase |Cria um novo banco de dados SQL do Azure. |
| Add-AzureSQLDatabases |Cria os bancos de dados de SQL do Azure com base nos valores no arquivo de configuração JSON que o Visual Studio gera. |
| Add-AzureVM |Cria uma máquina virtual do Azure e retorna a URL da VM implantada. A função configura os pré-requisitos e, em seguida, chama a função **New-AzureVM** (módulo do Azure) para criar uma nova máquina virtual. |
| Add-AzureVMEndpoints |Adiciona novos pontos de extremidade de entrada a uma máquina virtual e retorna a máquina virtual com o novo ponto de extremidade. |
| Add-AzureVMStorage |Cria uma nova conta de armazenamento do Azure na assinatura atual. O nome da conta começa com "devtest", seguido por uma cadeia de caracteres alfanumérica exclusiva. A função retorna o nome da nova conta de armazenamento. Você deve especificar um local ou um grupo de afinidades para a nova conta de armazenamento. |
| Add-AzureWebsite |Cria um site com nome e local especificados. Essa função chama a função **New-AzureWebsite** no módulo do Azure. Se a assinatura ainda não incluir um site com o nome especificado, essa função criará o site e retornará um objeto de site. Caso contrário, retornará `$null`. |
| Assinatura de backup |Salva a assinatura do Azure atual na variável `$Script:originalSubscription` no escopo de script. Essa função salva a assinatura do Azure atual (conforme obtidas pelo `Get-AzureSubscription -Current`) e sua conta de armazenamento e a assinatura é alterada por esse script (armazenado na variável `$UserSpecifiedSubscription`) e sua conta de armazenamento, no escopo de script. Ao salvar os valores, você pode usar uma função, como `Restore-Subscription`, para restaurar a assinatura atual original em a conta de armazenamento ao status atual, se o status atual foi alterado. |
| Find-AzureVM |Obtém a máquina virtual especificada do Azure. |
| Format-DevTestMessageWithTime |Anexa data e hora a uma mensagem. Essa função destina-se a mensagens gravadas em fluxos de erro e detalhes. |
| Get-AzureSQLDatabaseConnectionString |Monta uma cadeia de conexão para se conectar a um banco de dados SQL do Azure. |
| Get-AzureVMStorage |Retorna o nome da primeira conta de armazenamento com o nome padrão "devtest*" (não diferencia maiúsculas de minúsculas) no local especificado ou grupo de afinidades. Se a conta de armazenamento "devtest*" não corresponder ao local ou grupo de afinidades, a função a ignorará. Você deve especificar um local ou um grupo de afinidades. |
| Get-MSDeployCmd |Retorna um comando para executar a ferramenta MsDeploy.exe. |
| New-AzureVMEnvironment |Encontra ou cria uma máquina virtual na assinatura que corresponde aos valores no arquivo de configuração JSON. |
| Publish-WebPackage |Usa MsDeploy.exe e um arquivo .zip para implantar recursos em um site. Essa função não gera nenhuma saída. Se a chamada para MSDeploy.exe falhar, a função gerará uma exceção. Para obter uma saída mais detalhada, use a opção **-Verbose** . |
| Publish-WebPackageToVM |Verifica os valores de parâmetro e, em seguida, chama a função **Publish-WebPackage** . |
| Read-ConfigFile |Valida o arquivo de configuração JSON e retorna uma tabela de hash de valores selecionados. |
| Restore-Subscription |Redefine a assinatura atual à assinatura original. |
| Test-AzureModule |Retorna `$true` se a versão do módulo do Azure instalado for 0.7.4 ou posterior. Retorna `$false` se o módulo não estiver instalado ou for uma versão anterior. Essa função não tem parâmetros. |
| Test-AzureModuleVersion |Retorna `$true` se a versão do módulo do Azure for 0.7.4 ou posterior. Retorna `$false` se o módulo não estiver instalado ou for uma versão anterior. Essa função não tem parâmetros. |
| Test-HttpsUrl |Converte a URL de entrada em um objeto System.Uri. Retorna `$True` se a URL for absoluta e seu esquema for https. Retorna `$false` se a URL for relativa, seu esquema não for HTTPS ou a cadeia de caracteres de entrada não puder ser convertida em URL. |
| Test-Member |Retorna `$true` se uma propriedade ou método for um membro do objeto. Caso contrário, retorna `$false`. |
| Write-ErrorWithTime |Grava uma mensagem de erro prefixada com a hora atual. Essa função chama a função **Format-DevTestMessageWithTime** para prefixar a hora antes de gravar a mensagem no fluxo de erro. |
| Write-HostWithTime |Grava uma mensagem no programa host (**Write-Host**) prefixada com a hora atual. O efeito da gravação no programa host varia. A maioria dos programas que hospeda o Windows PowerShell grava essas mensagens na saída padrão. |
| Write-VerboseWithTime |Grava uma mensagem detalhada prefixada com a hora atual. Como a mensagem chama **Write-Verbose**, ela será exibida somente quando o script for executado com o parâmetro **Detalhado** ou quando a preferência **VerbosePreference** é definida como **Continuar**. |

**Publish-WebApplication**

| Nome da função | Descrição |
| --- | --- |
| New-AzureWebApplicationEnvironment |Cria recursos do Azure, como um site ou uma máquina virtual. |
| New-WebDeployPackage |Essa função não está implementada. Você pode adicionar comandos nessa função para compilar o projeto. |
| Publish-AzureWebApplication |Publicar um aplicativo Web no Azure. |
| Publish-WebApplication |Cria e implanta aplicativos Web, máquinas virtuais, bancos de dados SQL e contas de armazenamento para um projeto Web do Visual Studio. |
| Test-WebApplication |Essa função não está implementada. Você pode adicionar comandos a essa função para testar o aplicativo. |

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre os scripts do PowerShell ao ler [Criando scripts com o Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx) e confira outros scripts do Azure PowerShell no [Script Center](https://azure.microsoft.com/documentation/scripts/).
