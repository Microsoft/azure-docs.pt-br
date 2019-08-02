---
title: Tarefas de inicialização comuns para Serviços de Nuvem | Microsoft Docs
description: Oferece alguns exemplos de tarefas de inicialização comuns que talvez você queira executar na função Web ou função de trabalho de seus serviços de nuvem.
services: cloud-services
documentationcenter: ''
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: gwallace
ms.openlocfilehash: 2eb299ad841444a3100eac207b225d5377959f85
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358963"
---
# <a name="common-cloud-service-startup-tasks"></a>Tarefas de inicialização comuns do Serviço de Nuvem
Este artigo oferece alguns exemplos de tarefas de inicialização comuns que talvez você queira executar no serviço de nuvem. Você pode usar as tarefas de inicialização para executar operações antes do início de uma função. As operações que talvez você queira executar incluem a instalação de um componente, o registro de componentes COM, a configuração de chaves do registro ou o início de um processo de longa duração. 

Confira [este artigo](cloud-services-startup-tasks.md) para entender o funcionamento das tarefas de inicialização e, especificamente, como criar as entradas que definem uma tarefa de inicialização.

> [!NOTE]
> As tarefas de inicialização não são aplicáveis às Máquinas Virtuais, apenas às funções Web e de Trabalho do Serviço de Nuvem.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Definir variáveis de ambiente antes de iniciar uma função
Se você precisar de variáveis de ambiente definidas para uma tarefa específica, use o elemento [Ambiente] dentro do elemento [Tarefa].

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

As variáveis também podem usar um [valor válido do Azure XPath](cloud-services-role-config-xpath.md) para fazer referência a algo sobre a implantação. Em vez de usar o atributo `value` , defina um elemento filho [RoleInstanceValue] .

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Configurar a inicialização do IIS com AppCmd.exe
A ferramenta de linha de comando [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) pode ser usada para gerenciar as configurações do IIS na inicialização no Azure. *AppCmd.exe* oferece acesso de linha de comando conveniente às definições de configuração para uso nas tarefas de inicialização no Azure. Com *AppCmd.exe*, as configurações do site podem ser adicionadas, modificadas ou removidas para aplicativos e sites.

No entanto, há algumas coisas que merecem atenção no uso de *AppCmd.exe* como uma tarefa de inicialização:

* As tarefas de inicialização podem ser executadas mais de uma vez entre as reinicializações. Por exemplo, quando uma função é reciclada.
* Se uma ação *AppCmd.exe* for executada mais de uma vez, poderá gerar um erro. Por exemplo, a tentativa de adicionar uma seção a *Web.config* duas vezes pode gerar um erro.
* As tarefas de inicialização falharão caso retornem um código de saída diferente de zero ou **errorlevel**. Por exemplo, quando *AppCmd.exe* gera um erro.

É uma prática recomendada verificar **errorlevel** depois de chamar *AppCmd.exe*, o que é fácil se você encapsula a chamada a *AppCmd.exe* com um arquivo *.cmd*. Se você detectar uma resposta **errorlevel** conhecida, poderá ignorá-la ou passá-la novamente.

O errorlevel retornado por *AppCmd.exe* é listado no arquivo winerror.h e também pode ser visto no [MSDN](/windows/desktop/Debug/system-error-codes--0-499-).

### <a name="example-of-managing-the-error-level"></a>Exemplo de gerenciamento de nível de erro
Este exemplo adiciona uma seção e uma entrada de compactação para JSON para o arquivo *Web.config* , com tratamento de erros e registro em log.

As seções relevantes do arquivo [ServiceDefinition.csdef] são mostradas aqui, o que inclui a definição do atributo [executionContext](/previous-versions/azure/reference/gg557552(v=azure.100)#task) como `elevated` para dar a *AppCmd.exe* permissões suficientes para alterar as configurações no arquivo *Web.config*:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

O arquivo em lotes *Startup.cmd* usa *AppCmd.exe* para adicionar uma seção e uma entrada de compactação para JSON ao arquivo *Web.config*. O **errorlevel** esperado de 183 é definido como zero usando o programa de linha de comando VERIFY.EXE. Os errorlevels inesperados são registrados em StartupErrorLog.txt.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in an Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL

REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Add compression for json. ***
%windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
IF %ERRORLEVEL% EQU 183 VERIFY > NUL
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Exit batch file. ***
EXIT /b 0

REM   *** Log error and exit ***
:ErrorExit
REM   Report the date, time, and ERRORLEVEL of the error.
DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT %ERRORLEVEL%
```

## <a name="add-firewall-rules"></a>Adicionar regras de firewall
No Azure, há efetivamente dois firewalls. O primeiro firewall controla conexões entre a máquina virtual e o mundo externo. Esse firewall é controlado pelo elemento [EndPoints] no arquivo [ServiceDefinition.csdef].

O segundo firewall controla conexões entre a máquina virtual e os processos dessa máquina virtual. Esse firewall pode ser controlada pela ferramenta de linha de comando `netsh advfirewall firewall`.

O Azure cria regras de firewall para processos iniciados em suas funções. Por exemplo, quando você inicia um serviço ou um programa, o Azure cria automaticamente as regras de firewall necessárias para permitir que o serviço ser comunique com a Internet. No entanto, se você criar um serviço que é iniciado por um processo fora de sua função (como um serviço COM+ ou uma tarefa agendada do Windows), precisará criar manualmente uma regra de firewall para permitir o acesso a esse serviço. Essas regras de firewall podem ser criadas usando uma tarefa de inicialização.

Uma tarefa de inicialização que cria uma regra de firewall deve ter um [executionContext][tarefa]  **elevado**. Adicione a seguinte tarefa de inicialização ao arquivo [ServiceDefinition.csdef] .

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Para adicionar a regra de firewall, você deverá usar os comandos `netsh advfirewall firewall` adequados no arquivo em lotes de inicialização. Neste exemplo, a tarefa de inicialização exige segurança e criptografia para a porta TCP 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Bloquear um endereço IP específico
Você pode restringir um acesso de função Web do Azure para um conjunto de endereços IP especificados, modificando o IIS arquivo **Web.config**. Você também precisa usar um arquivo de comando que desbloqueie a seção **ipSecurity** do arquivo **applicationHost.config**.

Para desbloquear a seção **ipSecurity** do arquivo **applicationHost. config**, crie um arquivo de comando que é executado no início da função. Crie uma pasta no nível raiz da sua função Web chamada **startup** e, nessa pasta, crie um arquivo em lotes chamado **startup.cmd**. Adicione esse arquivo ao projeto do Visual Studio e defina as propriedades como **Copiar Sempre** para garantir que ele seja incluído no pacote.

Adicione a seguinte tarefa de inicialização ao arquivo [ServiceDefinition.csdef] .

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Adicione este comando ao arquivo **startup.cmd** :

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Essa tarefa faz com que o arquivo em lotes **startup.cmd** seja executado sempre que a função Web for inicializada, garantindo que a seção **ipSecurity** necessária seja desbloqueada.

Por fim, modifique a [seção system.webServer](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) do arquivo **web.config** da sua função Web para adicionar uma lista de endereços IP com acesso concedido, como mostrado neste exemplo:

Esta configuração de exemplo **permite** que todos os IPs acessem o servidor, exceto os dois definidos

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Esta configuração de exemplo **nega** que todos os IPs acessem o servidor, exceto os dois definidos.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## <a name="create-a-powershell-startup-task"></a>Criar uma tarefa de inicialização do PowerShell
Os scripts do Windows PowerShell não podem ser chamados diretamente do arquivo [ServiceDefinition.csdef] , mas podem ser chamados de um arquivo em lotes de inicialização.

O PowerShell (por padrão) não executa scripts não assinados. A menos que você assine seu script, precisará configurar o PowerShell para executar scripts não assinados. Para executar scripts não assinados, **ExecutionPolicy** deve ser definido como **Irrestrito**. A configuração **ExecutionPolicy** que você usa baseia-se na versão do Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Se você estiver usando um SO Convidado que esteja executando o PowerShell 2.0 ou 1.0, poderá impor a execução da versão 2. Se ela não estiver disponível, use a versão 1.

```cmd
REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
IF %ERRORLEVEL% EQU -393216 (
   PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
   PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
)

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Criar arquivos no armazenamento local de uma tarefa de inicialização
Você pode usar um recurso de armazenamento local para armazenar os arquivos criados pela tarefa de inicialização que será acessada posteriormente por seu aplicativo.

Para criar o recurso de armazenamento local, adicione uma seção [LocalResources] ao arquivo [ServiceDefinition.csdef] e adicione o elemento filho [LocalStorage]. Dê ao recurso de armazenamento local um nome exclusivo e um tamanho adequado para sua tarefa de inicialização.

Para usar um recurso de armazenamento local em sua tarefa de inicialização, será necessário criar uma variável de ambiente para fazer referência ao local do recurso de armazenamento local. Em seguida, a tarefa de Inicialização e o aplicativo podem ler e gravar arquivos no recurso de armazenamento local.

As seções relevantes do arquivo **ServiceDefinition.csdef** são mostradas aqui:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">
    ...

    <LocalResources>
      <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
    </LocalResources>

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="PathToStartupStorage">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
  </WorkerRole>
</ServiceDefinition>
```

Como um exemplo, esse arquivo em lotes **Startup.cmd** usa a variável de ambiente **PathToStartupStorage** para criar o arquivo **MyTest.txt** no armazenamento local.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Você pode acessar a pasta de armazenamento local do SDK do Azure usando o método [GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)).

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Executar no emulador ou na nuvem
Você pode fazer com que sua tarefa de inicialização execute etapas diferentes quando estiver funcionando na nuvem em comparação a quando estiver no emulador de computação. Por exemplo, convém usar uma cópia atualizada dos dados SQL somente durante a execução no emulador. Ou você talvez queira fazer alguma otimização de desempenho para a nuvem que não seja necessária na execução no emulador.

Essa capacidade de executar ações diferentes no emulador de computação e na nuvem pode ser obtida criando uma variável de ambiente no arquivo [ServiceDefinition.csdef]. Você testa então essa variável de ambiente para um valor em sua tarefa de inicialização.

Para criar a variável de ambiente, adicione o elemento [Variable]/[RoleInstanceValue] e crie um valor XPath de `/RoleEnvironment/Deployment/@emulated`. O valor da variável de ambiente **%ComputeEmulatorRunning%** é `true` na execução no emulador de computação e `false` na execução na nuvem.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">

    ...

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>

  </WorkerRole>
</ServiceDefinition>
```

Agora a tarefa pode verificar a variável de ambiente **%ComputeEmulatorRunning%** para executar ações diferentes com base na função estar em execução na nuvem ou no emulador. A seguir, um script de shell .cmd que verifica essa variável de ambiente.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Detectar se a tarefa já foi executada
A função pode ser reciclada sem uma reinicialização, fazendo com que suas tarefas de inicialização sejam executadas novamente. Não há um sinalizador para indicar se uma tarefa já foi executada na VM de host. Talvez você tenha algumas tarefas onde não importará se elas forem executadas várias vezes. No entanto, você poderá encontrar uma situação em que precisará impedir que uma tarefa seja executada mais de uma vez.

A maneira mais simples de detectar se uma tarefa já foi executada é criar um arquivo na pasta **%TEMP%** quando a tarefa for bem-sucedida e procurá-lo no início da tarefa. A seguir, um script de shell cmd de exemplo que faz isso para você.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%PathToApp1Install%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%PathToApp1Install%\Task1_Success.txt"

) ELSE (
  REM   An error occurred. Log the error and exit with the error code.

  DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
  TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
  ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

  EXIT %ERRORLEVEL%
)

:Finish

REM   Exit normally.
EXIT /B 0
```

## <a name="task-best-practices"></a>Práticas recomendadas para tarefas
A seguir, algumas práticas recomendadas que você deve seguir ao configurar a tarefa para sua função Web ou de trabalho.

### <a name="always-log-startup-activities"></a>Sempre registrar em log as atividades de inicialização
O Visual Studio não fornece um depurador para percorrer arquivos em lotes e, portanto, será bom ter tantos dados sobre a operação de arquivos em lotes quanto possível. O registro em log da saída de arquivos em lotes, **stdout** e **stderr**, pode fornecer informações importantes ao tentar depurar e corrigir arquivos em lotes. Para registrar em log **stdout** e **stderr** para o arquivo StartupLog.txt no diretório apontado pela variável de ambiente **%TEMP%** , adicione o texto `>>  "%TEMP%\\StartupLog.txt" 2>&1` ao final de linhas específicas que deseja registrar em log. Por exemplo, para executar setup.exe no diretório **%PathToApp1Install%** :

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Para simplificar o xml, você pode criar um arquivo wrapper *cmd* que chama todas as tarefas de inicialização, juntamente com o registro em log, e garante que cada tarefa filho compartilhe as mesmas variáveis de ambiente.

Porém, talvez você ache incômodo usar `>> "%TEMP%\StartupLog.txt" 2>&1` ao fim de cada tarefa de inicialização. Você pode impor o log de tarefas criando um invólucro que manipula o registro em log para você. Este wrapper chama o arquivo de lote real que você deseja executar. Nenhuma saída do arquivo de lote de destino será redirecionada para o arquivo *startuplog*.

O exemplo a seguir mostra como redirecionar todas as saídas de um arquivo em lotes de inicialização. Neste exemplo, o arquivo ServerDefinition.csdef cria uma tarefa de inicialização que chama *logwrap.cmd*. *logwrap.cmd* chama *Startup2.cmd*, redirecionando toda a saída para **%TEMP%\\StartupLog.txt**.

ServiceDefinition.cmd:

```xml
<Startup>
    <Task commandLine="logwrap.cmd startup2.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

**logwrap.cmd:**

```cmd
@ECHO OFF

REM   logwrap.cmd calls passed in batch file, redirecting all output to the StartupLog.txt log file.

ECHO [%date% %time%] == START logwrap.cmd ============================================== >> "%TEMP%\StartupLog.txt" 2>&1
ECHO [%date% %time%] Running %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Call the child command batch file, redirecting all output to the StartupLog.txt log file.
START /B /WAIT %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Log the completion of child command.
ECHO [%date% %time%] Done >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (

   REM   No errors occurred. Exit logwrap.cmd normally.
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B 0

) ELSE (

   REM   Log the error.
   ECHO [%date% %time%] An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B %ERRORLEVEL%

)
```

**Startup2.cmd:**

```cmd
@ECHO OFF

REM   This is the batch file where the startup steps should be performed. Because of the
REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
REM   calling batch file.

ECHO [%date% %time%] Some log information about this task
ECHO [%date% %time%] Some more log information about this task

EXIT %ERRORLEVEL%
```

Exemplo de saída no arquivo **Startuplog**:

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> O arquivo **Startuplog** está localizado na pasta *C:\Resources\temp\\{role identifier}\RoleTemp*.
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Definir executionContext adequadamente para tarefas de inicialização
Definir privilégios adequadamente para a tarefa de inicialização. Às vezes, as tarefas de inicialização devem ser executadas com privilégios elevados, mesmo que a função seja executada com privilégios normais.

A ferramenta de linha de comando [executionContext][tarefa] define o nível de privilégio da tarefa de inicialização. A utilização de `executionContext="limited"` significa que a tarefa de inicialização tem o mesmo nível de privilégio que a função. A utilização de `executionContext="elevated"` significa que a tarefa de inicialização tem privilégios de administrador, o que permite que a tarefa de inicialização execute tarefas de administrador sem conceder privilégios de administrador à sua função.

Um exemplo de uma tarefa de inicialização que exija privilégios elevados é uma tarefa de inicialização que usa **AppCmd.exe** para configurar o IIS. **AppCmd.exe** requer `executionContext="elevated"`.

### <a name="use-the-appropriate-tasktype"></a>Usar o taskType adequado
A ferramenta de linha de comando [taskType][tarefa] determina a maneira como a tarefa de inicialização é executada. Há três valores: **simples**, **segundo plano** e **primeiro plano**. As tarefas em primeiro e segundo plano são iniciadas de forma assíncrona e as tarefas simples são executadas de forma síncrona, uma de cada vez.

Com as tarefas de inicialização **simples**, você pode definir a ordem na qual as tarefas são executadas pela ordem na qual as tarefas são listadas no arquivo ServiceDefinition.csdef. Se uma tarefa **simples** terminar com um código de saída diferente de zero, o procedimento de inicialização será interrompido e a função não será iniciada.

A diferença entre as tarefas de inicialização em **segundo plano** e em **primeiro plano** é que as tarefas de inicialização em **primeiro plano** mantêm a função em execução até a tarefa em **primeiro plano** ser encerrada. Isso também significa que, se a tarefa em **primeiro plano** congelar ou falhar, a função não será reciclada até a tarefa em **primeiro plano** ser forçada a fechar. Por esse motivo, as tarefas em **segundo plano** são recomendadas para tarefas de inicialização assíncronas, a menos que você precise desse recurso da tarefa em **primeiro plano**.

### <a name="end-batch-files-with-exit-b-0"></a>Encerrar arquivos em lotes com EXIT /B 0
A função só será iniciada se o **errorlevel** de cada uma de suas tarefas de inicialização simples for zero. Nem todos os programas definem o **errorlevel** (código de saída) corretamente e, portanto, o arquivo em lotes deverá terminar com um `EXIT /B 0` se tudo tiver sido executado corretamente.

Um `EXIT /B 0` ausente no final de um arquivo em lotes de inicialização é uma causa comum de funções que não são iniciadas.

> [!NOTE]
> Observei que arquivos em lotes aninhados às vezes travarm ao usar o parâmetro `/B`. Convém verificar se esse problema de suspensão não acontece se outro arquivo em lotes chama o arquivo em lotes atual, como quando você usa o [wrapper de log](#always-log-startup-activities). Você pode omitir o parâmetro `/B` nesse caso.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Esperar que tarefas de inicialização sejam executadas mais de uma vez
Nem todas as reciclagens de função incluem uma reinicialização, mas todas as reciclagens incluem a execução de todas as tarefas de inicialização. Isso significa que deve ser possível executar as tarefas de inicialização várias vezes entre as reinicializações sem problemas. Isso é discutido na [seção anterior](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Usar o armazenamento local para armazenar arquivos que devem ser acessados na função
Se você quiser copiar ou criar um arquivo enquanto sua tarefa de inicialização estiver acessível para sua função, esse arquivo deverá ser colocado no armazenamento local. Confira a [seção anterior](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Próximas etapas
Examine o [modelo de serviço e o pacote da nuvem](cloud-services-model-and-package.md)

Saiba mais sobre o funcionamento de [Tarefas](cloud-services-startup-tasks.md) .

[Crie e implante](cloud-services-how-to-create-deploy-portal.md) seu pacote de serviço de nuvem.

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tarefa]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Ambiente]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[EndPoints]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
