---
title: Introdução aos Serviços de Nuvem do Azure e ao Python | Microsoft Docs
description: Visão geral do uso do Python Tools para Visual Studio para criar serviços de nuvem do Azure, incluindo funções Web e de Trabalho.
services: cloud-services
documentationcenter: python
author: georgewallace
ms.service: cloud-services
ms.devlang: python
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: gwallace
ms.openlocfilehash: 981b1cc4a7adb98ba68ebf3a7673b7116479e704
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68359564"
---
# <a name="python-web-and-worker-roles-with-python-tools-for-visual-studio"></a>Funções Web e de trabalho do Python com Ferramentas Python para Visual Studio

Este artigo oferece uma visão geral do uso das funções Web e de trabalho do Python por meio das [Ferramentas Python para Visual Studio][Python Tools for Visual Studio]. Saiba como usar o Visual Studio para criar e implantar um Serviço de Nuvem básico que usa Python.

## <a name="prerequisites"></a>Pré-requisitos
* [Visual Studio 2013, 2015 ou 2017](https://www.visualstudio.com/)
* [Python Tools for Visual Studio][Python Tools for Visual Studio] (PTVS)
* [SDK Tools do Azure para VS 2013][Azure SDK Tools for VS 2013] ou  
[SDK Tools do Azure para VS 2015][Azure SDK Tools for VS 2015] ou  
[SDK Tools do Azure para VS 2017][Azure SDK Tools for VS 2017]
* [Python 2.7 de 32 bits][Python 2.7 32-bit] ou [Python 3.5 de 32 bits][Python 3.5 32-bit]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>O que são funções Web e de Trabalho do Python?
O Azure fornece três modelos de computação para a execução de aplicativos: [Recurso de aplicativos Web no serviço de Azure app][execution model-web sites], [máquinas virtuais do Azure][execution model-vms]e [serviços de nuvem do Azure][execution model-cloud services]. Todos os três modelos oferecem suporte ao Python. Os Serviços de Nuvem, que incluem as funções Web e de trabalho, fornecem a *PaaS (plataforma como serviço)* . Dentro de um serviço de nuvem, uma função Web fornece um servidor web dos Serviços de Informações da Internet (IIS) dedicado para hospedar aplicativos web de front-end, enquanto uma função de trabalho pode executar tarefas assíncronas, de longa execução ou perpétuas independentes de interação com o usuário ou de entrada.

Para saber mais, confira [O que é um Serviço de Nuvem?].

> [!NOTE]
> *Procurando criar um site simples?*
> Se o seu cenário envolver apenas um front-end de site simples, considere usar um recurso Aplicativos Web leve no Serviço de Aplicativo do Azure. Você pode atualizar facilmente para um serviço de nuvem conforme o site cresce e suas necessidades mudam. Consulte o [Centro de Desenvolvedores do Python](https://azure.microsoft.com/develop/python/) para obter artigos que abordem o desenvolvimento do recurso Aplicativos Web no Serviço de Aplicativo do Azure.
> <br />
> 
> 

## <a name="project-creation"></a>Criação do projeto
No Visual Studio, você pode escolher o **Serviço de Nuvem do Azure** na caixa de diálogo **Novo Projeto**, em **Python**.

![Caixa de diálogo Novo Projeto](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

No assistente do Serviço de Nuvem do Azure, você pode criar novas funções Web e de trabalho.

![Caixa de diálogo de serviço de nuvem do Azure](./media/cloud-services-python-ptvs/new-service-wizard.png)

O modelo de função de trabalho vem com o código de texto clichê para se conectar a uma conta de armazenamento ou ao Barramento de Serviço do Azure.

![Solução de serviço de nuvem](./media/cloud-services-python-ptvs/worker.png)

Você pode adicionar funções Web ou de Trabalho a um serviço de nuvem existente a qualquer momento.  Você pode escolher adicionar projetos existentes em sua solução ou criar projetos novos.

![Adicionar comando de função](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Seu serviço de nuvem pode conter funções implementadas em diferentes idiomas.  Por exemplo, uma função Web do Python pode ser implementada usando o Django, com funções de trabalho do Python ou do C#.  Você pode se comunicar facilmente entre as funções usando filas do Barramento de Serviço ou filas de armazenamento.

## <a name="install-python-on-the-cloud-service"></a>Instalar o Python no serviço de nuvem
> [!WARNING]
> Os scripts de instalação instalados com o Visual Studio (no momento em que este artigo foi atualizado pela última vez) não funcionam. Esta seção descreve uma solução alternativa.
> 
> 

O principal problema com os scripts de instalação é que eles não instalam o python. Primeiro, defina duas [tarefas de inicialização](cloud-services-startup-tasks.md) no arquivo [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef). A primeira tarefa (**PrepPython.ps1**) baixa e instala o tempo de execução do Python. A segunda tarefa (**PipInstaller.ps1**) executa o pip para instalar todas as dependências que possam existir.

Os scripts a seguir foram escritos para o Python 3.5. Se você quiser usar a versão 2. x do python, defina o arquivo da variável **PYTHON2** como **ativado** para as duas tarefas de inicialização e a tarefa de tempo de execução: `<Variable name="PYTHON2" value="<mark>on</mark>" />`.

```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>

  </Task>

</Startup>
```

As variáveis **PYTHON2** e **PYPATH** precisam ser adicionadas à tarefa de inicialização de trabalho. A variável **PYPATH** só será usada se a variável **PYTHON2** estiver definida como **ativada**.

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### <a name="sample-servicedefinitioncsdef"></a>ServiceDefinition.csdef de exemplo
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



Em seguida, crie os arquivos **PrepPython.ps1** e **PipInstaller.ps1** na pasta **./bin** da sua função.

#### <a name="preppythonps1"></a>PrepPython.ps1
Esse script instala o python. Se a variável de ambiente **PYTHON2** estiver definida como **ativada**, o Python 2.7 será instalado; caso contrário, o Python 3.5 será instalado.

```powershell
[Net.ServicePointManager]::SecurityProtocol = "tls12, tls11, tls"
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if python is installed...$nl"
    if ($is_python2) {
        & "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
    }
    else {
        py -V | Out-Null
    }

    if (-not $?) {

        $url = "https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64.exe"
        $outFile = "${env:TEMP}\python-3.5.2-amd64.exe"

        if ($is_python2) {
            $url = "https://www.python.org/ftp/python/2.7.12/python-2.7.12.amd64.msi"
            $outFile = "${env:TEMP}\python-2.7.12.amd64.msi"
        }

        Write-Output "Not found, downloading $url to $outFile$nl"
        Invoke-WebRequest $url -OutFile $outFile
        Write-Output "Installing$nl"

        if ($is_python2) {
            Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
        }
        else {
            Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Already installed"
    }
}
```

#### <a name="pipinstallerps1"></a>PipInstaller.ps1
Esse script chama o pip e instala todas as dependências no arquivo **requirements.txt**. Se a variável de ambiente **PYTHON2** estiver definida como **ativada**, o Python 2.7 será usado; caso contrário, o Python 3.5 será usado.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if requirements.txt exists$nl"
    if (Test-Path ..\requirements.txt) {
        Write-Output "Found. Processing pip$nl"

        if ($is_python2) {
            & "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
        }
        else {
            py -m pip install -r ..\requirements.txt
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Not found$nl"
    }
}
```

#### <a name="modify-launchworkerps1"></a>Modificar LaunchWorker.ps1
> [!NOTE]
> No caso de um projeto de **função de trabalho**, o arquivo **LauncherWorker.ps1** é necessário para executar o arquivo de inicialização. Em um projeto de **função Web**, o arquivo de inicialização é definido nas propriedades do projeto.
> 
> 

O **bin\LaunchWorker.ps1** foi originalmente criado para grande parte do trabalho de preparação, mas ele não funciona. Substitua o conteúdo desse arquivo pelo script a seguir.

Esse script chama o arquivo **worker.py** do seu projeto python. Se a variável de ambiente **PYTHON2** estiver definida como **ativada**, o Python 2.7 será usado; caso contrário, o Python 3.5 será usado.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
    Write-Output "Running worker.py$nl"

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
else
{
    Write-Output "Running (EMULATED) worker.py$nl"

    # Customize to your local dev environment

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
```

#### <a name="pscmd"></a>ps.cmd
Os modelos do Visual Studio devem ter criado um arquivo **ps.cmd** na pasta **./bin**. Esse script de shell chama os scripts do wrapper do PowerShell acima e fornece registros em log com base no nome do wrapper chamado pelo PowerShell. Se esse arquivo não tiver sido criado, veja qual deve ser seu conteúdo. 

```bat
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## <a name="run-locally"></a>Executar localmente
Se você configurar seu projeto de serviço de nuvem como o projeto de inicialização e pressionar F5, o serviço de nuvem será executado no emulador do Azure local.

Embora o PTVS ofereça suporte à inicialização no emulador, a depuração (por exemplo, pontos de interrupção) não funciona.

Para depurar suas funções Web e de Trabalho, você pode configurar o projeto de funções como o projeto de inicialização e depurá-lo.  Você também pode definir vários projetos de inicialização.  Clique com o botão direito do mouse na solução e selecione **Definir Projetos de Inicialização**.

![Propriedades do projeto de inicialização da solução](./media/cloud-services-python-ptvs/startup.png)

## <a name="publish-to-azure"></a>Publicar no Azure
Para publicar, clique com o botão direito do mouse no projeto de serviço de nuvem na solução e selecione **Publicar**.

![Conexão de publicação no Microsoft Azure](./media/cloud-services-python-ptvs/publish-sign-in.png)

Siga o assistente. Se for necessário, habilite a área de trabalho remota. A área de trabalho remota será útil quando você precisar depurar algo.

Quando tiver concluído os ajustes de configurações, clique em **Publicar**.

Algum progresso aparece na janela de saída e, então, você verá a janela Log de Atividades do Microsoft Azure.

![Janela Log de atividade do Microsoft Azure](./media/cloud-services-python-ptvs/publish-activity-log.png)

A implantação levará alguns minutos para ser concluída; em seguida, suas funções Web e/ou de trabalho serão executadas no Azure!

### <a name="investigate-logs"></a>Investigar os logs
Depois que a máquina virtual do serviço de nuvem for iniciada e instalar o Python, você poderá examinar os logs para encontrar alguma mensagem de falha. Esses logs estão localizados na pasta **C:\Resources\Directory\\{função}\LogFiles**. **PrepPython.err.txt** tem pelo menos um erro de quando o script tenta detectar se o Python está instalado e **PipInstaller.err.txt** pode reclamar sobre uma versão desatualizada do pip.

## <a name="next-steps"></a>Próximas etapas
Para obter informações mais detalhadas sobre como usar funções Web e de trabalho nas Ferramentas Python para Visual Studio, consulte a documentação do PTVS:

* [Projetos do serviço de nuvem][Cloud Service Projects]

Para obter mais detalhes sobre o uso dos serviços do Azure por meio das funções Web e de trabalho, como o uso do Armazenamento ou Barramento de Serviço do Azure, consulte os artigos a seguir:

* [Serviço Blob][Blob Service]
* [Serviço Tabela][Table Service]
* [Serviço Fila][Queue Service]
* [Filas do Barramento de Serviço][Service Bus Queues]
* [Tópicos do Barramento de Serviço][Service Bus Topics]

<!--Link references-->

[O que é um Serviço de Nuvem?]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service/overview.md
[execution model-vms]:../virtual-machines/windows/overview.md
[execution model-cloud services]: cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Blob Service]:../storage/blobs/storage-python-how-to-use-blob-storage.md
[Queue Service]: ../storage/queues/storage-python-how-to-use-queue-storage.md
[Table Service]:../cosmos-db/table-storage-how-to-use-python.md
[Service Bus Queues]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md
[Service Bus Topics]: ../service-bus-messaging/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools for Visual Studio]: https://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: https://aka.ms/ptvsdocs
[Cloud Service Projects]: https://docs.microsoft.com/visualstudio/python/python-azure-cloud-service-project-template
[Azure SDK Tools for VS 2013]: https://go.microsoft.com/fwlink/?LinkId=746482
[Azure SDK Tools for VS 2015]: https://go.microsoft.com/fwlink/?LinkId=746481
[Azure SDK Tools for VS 2017]: https://go.microsoft.com/fwlink/?LinkId=746483
[Python 2.7 32-bit]: https://www.python.org/downloads/
[Python 3.5 32-bit]: https://www.python.org/downloads/
