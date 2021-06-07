---
title: Depurar Azure Functions do PowerShell localmente
description: Saiba como depurar funções do PowerShell ao executar localmente.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: a668024db126c82f96756555aba513b77f7d7366
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422953"
---
# <a name="debug-powershell-azure-functions-locally"></a>Depurar Azure Functions do PowerShell localmente

Azure Functions permite desenvolver suas funções como scripts do PowerShell.

Você pode depurar suas funções do PowerShell localmente como faria com qualquer script do PowerShell usando as seguintes ferramentas de desenvolvimento padrão:

* [Visual Studio Code](https://code.visualstudio.com/): editor de texto gratuito, leve e de software livre da Microsoft com a extensão do PowerShell que oferece uma experiência de desenvolvimento completa do PowerShell.
* Um console do PowerShell: depurar usando os mesmos comandos que você usaria para depurar qualquer outro processo do PowerShell.

O [Azure Functions Core Tools](functions-run-local.md) dá suporte à depuração local de Azure functions, incluindo o PowerShell functions.

## <a name="example-function-app"></a>Exemplo de aplicativo de funções

O aplicativo de funções usado neste artigo tem uma única função disparada por HTTP e tem os seguintes arquivos:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Esse aplicativo de funções é semelhante ao que você obtém quando conclui o [início rápido do PowerShell](./create-first-function-vs-code-powershell.md).

O código de função `run.ps1` é semelhante ao seguinte script:

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>Definir o ponto de anexo

Para depurar qualquer função do PowerShell, a função precisa parar para que o depurador seja anexado. O `Wait-Debugger` cmdlet para a execução e aguarda o depurador.

>[!NOTE]
>Ao usar o PowerShell 7, você não precisa adicionar a `Wait-Debugger` chamada em seu código.

Tudo o que você precisa fazer é adicionar uma chamada ao `Wait-Debugger` cmdlet logo acima da `if` instrução, da seguinte maneira:

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

A depuração começa na `if` instrução. 

Com `Wait-Debugger` o no local, agora você pode depurar as funções usando o Visual Studio Code ou um console do PowerShell.

## <a name="debug-in-visual-studio-code"></a>Depurar no Visual Studio Code

Para depurar as funções do PowerShell no Visual Studio Code, você deve ter o seguinte instalado:

* [Extensão do PowerShell para Visual Studio Code](/powershell/scripting/components/vscode/using-vscode)
* [Extensão do Azure Functions para Visual Studio Code](./create-first-function-cli-powershell.md)
* [PowerShell Core 6,2 ou superior](/powershell/scripting/install/installing-powershell-core-on-windows)

Depois de instalar essas dependências, carregue um projeto de funções do PowerShell existente ou [crie seu primeiro projeto de funções do PowerShell](./create-first-function-vs-code-powershell.md).

>[!NOTE]
> Se o seu projeto não tiver os arquivos de configuração necessários, você será solicitado a adicioná-los.

### <a name="set-the-powershell-version"></a>Definir a versão do PowerShell

O PowerShell Core é instalado lado a lado com o Windows PowerShell. Defina o PowerShell Core como a versão do PowerShell a ser usada com a extensão do PowerShell para Visual Studio Code.

1. Pressione F1 para exibir o palete de comandos e procure por `Session` .

1. Escolha o **menu do PowerShell: mostrar sessão**.

1. Se a **sessão atual** não for o **PowerShell Core 6**, escolha **alternar para: PowerShell Core 6**.

Quando você tiver um arquivo do PowerShell aberto, verá a versão exibida em verde na parte inferior direita da janela. A seleção desse texto também exibe o menu sessão. Para saber mais, consulte [escolhendo uma versão do PowerShell para usar com a extensão](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Iniciar o aplicativo de funções

Verifique se `Wait-Debugger` está definido na função em que você deseja anexar o depurador.  Com `Wait-Debugger` o adicionado, você pode depurar seu aplicativo de funções usando Visual Studio Code.

Escolha o painel **depurar** e **anexe à função do PowerShell**.

![depurador](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Você também pode pressionar a tecla F5 para iniciar a depuração.

A operação iniciar depuração realiza as seguintes tarefas:

* É executado `func extensions install` no terminal para instalar qualquer Azure Functions extensões exigidas pelo seu aplicativo de funções.
* É executado `func host start` no terminal para iniciar o aplicativo de funções no host do functions.
* Anexe o depurador do PowerShell ao runspace do PowerShell dentro do tempo de execução do functions.

>[!NOTE]
> Você precisa garantir que PSWorkerInProcConcurrencyUpperBound esteja definido como 1 para garantir a experiência de depuração correta em Visual Studio Code. Esse é o padrão.

Com seu aplicativo de funções em execução, você precisa de um console do PowerShell separado para chamar a função disparada por HTTP.

Nesse caso, o console do PowerShell é o cliente do. O `Invoke-RestMethod` é usado para disparar a função.

Em um console do PowerShell, execute o seguinte comando:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Você observará que uma resposta não é retornada imediatamente. Isso porque `Wait-Debugger` anexou o depurador e a execução do PowerShell entrou no modo de interrupção assim que possível. Isso ocorre devido ao [conceito de BreakAll](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), que é explicado posteriormente. Depois que você pressiona o `continue` botão, o depurador agora é interrompido na linha logo após `Wait-Debugger` .

Neste ponto, o depurador é anexado e você pode fazer todas as operações normais do depurador. Para obter mais informações sobre como usar o depurador no Visual Studio Code, consulte [a documentação oficial](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Depois de continuar e chamar totalmente o script, você observará que:

* O console do PowerShell que fez o `Invoke-RestMethod` retornou um resultado
* O console integrado do PowerShell no Visual Studio Code está aguardando a execução de um script

Posteriormente, quando você invoca a mesma função, o depurador na extensão do PowerShell é interrompido logo após o `Wait-Debugger` .

## <a name="debugging-in-a-powershell-console"></a>Depuração em um console do PowerShell

>[!NOTE]
> Esta seção pressupõe que você leu o [Azure Functions Core Tools docs](functions-run-local.md) e saberá como usar o `func host start` comando para iniciar seu aplicativo de funções.

Abra um console `cd` do no diretório do seu aplicativo de funções e execute o seguinte comando:

```sh
func host start
```

Com o aplicativo de funções em execução e o `Wait-Debugger` no local, você pode anexar ao processo. Você precisa de mais dois consoles do PowerShell.

Um dos consoles atua como o cliente. A partir disso, você chama `Invoke-RestMethod` para disparar a função. Por exemplo, você pode executar o seguinte comando:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Você observará que ele não retorna uma resposta, que é um resultado do `Wait-Debugger` . O runspace do PowerShell agora está aguardando a anexação de um depurador. Vamos colocar isso em anexo.

No outro console do PowerShell, execute o seguinte comando:

```powershell
Get-PSHostProcessInfo
```

Esse cmdlet retorna uma tabela parecida com a seguinte saída:

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

Anote o `ProcessId` para o item na tabela com o as `ProcessName` `dotnet` . Esse processo é seu aplicativo de funções.

Em seguida, execute o seguinte trecho:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Depois de iniciado, o depurador é interrompido e mostra algo semelhante à seguinte saída:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Neste ponto, você está parado em um pontos de interrupção no [depurador do PowerShell](/powershell/module/microsoft.powershell.core/about/about_debuggers). A partir daqui, você pode fazer todas as operações de depuração usuais, depurar, entrar, continuar, encerrar e outras. Para ver o conjunto completo de comandos de depuração disponíveis no console do, execute `h` os `?` comandos ou.

Você também pode definir pontos de interrupção nesse nível com o `Set-PSBreakpoint` cmdlet.

Depois de continuar e chamar totalmente o script, você observará que:

* O console do PowerShell em que você executou `Invoke-RestMethod` agora retornou um resultado.
* O console do PowerShell em que você executou `Debug-Runspace` está aguardando a execução de um script.

Você pode invocar a mesma função novamente (usando, `Invoke-RestMethod` por exemplo) e o depurador é interrompido logo após o `Wait-Debugger` comando.

## <a name="considerations-for-debugging"></a>Considerações sobre depuração

Tenha em mente os seguintes problemas ao depurar seu código de funções.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` pode fazer com que o depurador quebre em um local inesperado

A extensão do PowerShell usa `Debug-Runspace` , que, por sua vez, depende do recurso do PowerShell `BreakAll` . Esse recurso informa ao PowerShell para parar no primeiro comando que é executado. Esse comportamento oferece a oportunidade de definir pontos de interrupção dentro do runspace depurado.

O tempo de execução de Azure Functions executa alguns comandos antes de realmente invocar o `run.ps1` script, portanto, é possível que o depurador acabe a interrupção dentro do `Microsoft.Azure.Functions.PowerShellWorker.psm1` ou `Microsoft.Azure.Functions.PowerShellWorker.psd1` .

Se essa interrupção ocorrer, execute o `continue` `c` comando ou para ignorar esse ponto de interrupção. Em seguida, você pára no ponto de interrupção esperado.

## <a name="troubleshooting"></a>Solução de problemas

Se você tiver dificuldades durante a depuração, deverá verificar o seguinte:

| Verificação | Ação |
|------|------|
| Executar `func --version` do terminal. Se você receber um erro que `func` não pode ser encontrado, as ferramentas principais (func.exe) podem estar ausentes da `path` variável local.| [Reinstale as ferramentas principais](functions-run-local.md#v2).|  
| No Visual Studio Code, o terminal padrão precisa ter acesso ao func.exe. Verifique se você não está usando um terminal padrão que não tem as ferramentas principais instaladas, como o subsistema do Windows para Linux (WSL).  | Defina o shell padrão em Visual Studio Code para o PowerShell 7 (recomendado) ou o Windows PowerShell 5,1.|
  

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como desenvolver funções usando o PowerShell, confira [Azure Functions guia do desenvolvedor do PowerShell](functions-reference-powershell.md).
