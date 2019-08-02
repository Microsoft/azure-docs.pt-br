---
title: Depurar o PowerShell do Azure Functions localmente
description: Entenda como desenvolver funções usando o PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: fc30a2efb21d5b7f3168d9229ec5baf9a7f05eb1
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706420"
---
# <a name="debug-powershell-azure-functions-locally"></a>Depurar o PowerShell do Azure Functions localmente

O Azure Functions permite desenvolver funções como scripts do PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Você pode depurar suas funções do PowerShell localmente, como você faria com qualquer script do PowerShell usando as ferramentas de desenvolvimento padrão a seguir:

* [Visual Studio Code](https://code.visualstudio.com/): Editor de texto gratuitas, leves e código-fonte aberto da Microsoft com a extensão do PowerShell que oferece uma experiência de desenvolvimento completa do PowerShell.
* Um console do PowerShell: Depure usando os mesmos comandos que você usaria para depurar qualquer processo do PowerShell.

[Funções principais ferramentas do Azure](functions-run-local.md) dá suporte à depuração local do Azure Functions, incluindo as funções do PowerShell.

## <a name="example-function-app"></a>Exemplo de aplicativo de função

O aplicativo de função usado neste artigo tem uma única função disparada por HTTP e tem os seguintes arquivos:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Esse aplicativo de função é semelhante ao que você obtém quando você concluir o [início rápido do PowerShell](functions-create-first-function-powershell.md).

O código de função em `run.ps1` se parece com o script a seguir:

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

Para depurar qualquer função do PowerShell, a função precisa interromper o depurador a ser anexado. O `Wait-Debugger` cmdlet interrompe a execução e aguarda o depurador.

Tudo o que você precisa fazer é adicionar uma chamada para o `Wait-Debugger` cmdlet imediatamente acima do `if` instrução, da seguinte maneira:

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

Depuração de US o `if` instrução. 

Com `Wait-Debugger` em vigor, você pode depurar funções usando o Visual Studio Code ou em um console do PowerShell.

## <a name="debug-in-visual-studio-code"></a>Depurar no Visual Studio Code

Para depurar suas funções do PowerShell no Visual Studio Code, você deve ter o seguinte instalado:

* [Extensão do PowerShell para Visual Studio Code](/powershell/scripting/components/vscode/using-vscode)
* [Extensão Azure Functions para Visual Studio Code](functions-create-first-function-vs-code.md)
* [O PowerShell Core 6.2 ou superior](/powershell/scripting/install/installing-powershell-core-on-windows)

Depois de instalar essas dependências, carregar um projeto de funções do PowerShell existente, ou [criar seu primeiro projeto de funções do PowerShell](functions-create-first-function-powershell.md).

>[!NOTE]
> Seu projeto não deve ter os arquivos de configuração necessárias, você precisará adicioná-los.

### <a name="set-the-powershell-version"></a>Defina a versão do PowerShell

O PowerShell Core instala lado a lado com o Windows PowerShell. Defina o PowerShell Core como a versão do PowerShell para usar com a extensão do PowerShell para Visual Studio Code.

1. Pressione F1 para exibir a paleta de comando, em seguida, pesquise `Session`.

1. Escolha **PowerShell: Mostrar Menu de sessão**.

1. Se sua **sessão atual** não está **PowerShell Core 6**, escolha **alternar para: O PowerShell Core 6**.

Quando você tiver um arquivo do PowerShell aberta, você verá a versão exibida em verde na parte inferior direita da janela. Também selecionar esse texto exibe o menu de sessão. Para obter mais informações, consulte o [escolher uma versão do PowerShell para usar com a extensão](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Iniciar o aplicativo de funções

Verifique se que `Wait-Debugger` é definido na função em que você deseja anexar o depurador.  Com `Wait-Debugger` adicionado, você pode depurar seu aplicativo de funções usando o Visual Studio Code.

Escolha o **Debug** painel e, em seguida **anexar a função do PowerShell**.

![Depurador](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Você também pode pressionar a tecla F5 para iniciar a depuração.

O inicie a operação de depuração faz as seguintes tarefas:

* Execuções `func extensions install` no terminal para instalar qualquer extensão de funções do Azure exigido pelo seu aplicativo de funções.
* Execuções `func host start` no terminal para iniciar o aplicativo de funções no host do Functions.
* Anexe o depurador do PowerShell para o espaço de execução do PowerShell no tempo de execução de funções.

Com seu aplicativo de função em execução, você precisa de um console do PowerShell separado para chamar a função disparada por HTTP.

Nesse caso, o console do PowerShell é o cliente. O `Invoke-RestMethod` é usado para disparar a função.

No console do PowerShell, execute o seguinte comando:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Você observará que uma resposta não é retornada imediatamente. Isso ocorre porque `Wait-Debugger` anexou o depurador e o PowerShell execução entrou no modo de interrupção, assim que era possível. Isso é devido a [BreakAll conceito](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), que é explicado mais tarde. Depois de pressionar o `continue` botão, o depurador agora quebra na linha logo após `Wait-Debugger`.

Neste ponto, o depurador é anexado e você pode fazer operações depurador normal. Para obter mais informações sobre como usar o depurador no Visual Studio Code, consulte [a documentação oficial do](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Depois de você continua e invoca totalmente o seu script, você observará que:

* O console do PowerShell que fez o `Invoke-RestMethod` retornou um resultado
* O Console do PowerShell integrado no Visual Studio Code está esperando para ser executado de um script

Posteriormente, quando você invoca a mesma função, o depurador na extensão do PowerShell interrompe logo após o `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Depuração no Console do PowerShell

>[!NOTE]
> Esta seção pressupõe que você leu a [docs as ferramentas básicas do Azure Functions](functions-run-local.md) e saber como usar o `func host start` comando para iniciar seu aplicativo de funções.

Abra um console `cd` no diretório do seu aplicativo de funções e execute o seguinte comando:

```sh
func host start
```

Com o aplicativo de função em execução e o `Wait-Debugger` em vigor, você pode anexar ao processo. Você precisa de dois consoles mais do PowerShell.

Um dos consoles atua como o cliente. A partir disso, você deve chamar `Invoke-RestMethod` para disparar a função. Por exemplo, você pode executar o comando a seguir:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Você observará que ele não retorna uma resposta, que é um resultado do `Wait-Debugger`. O espaço de execução do PowerShell agora está aguardando um depurador seja anexado. Vamos que anexado.

No console do outros PowerShell, execute o seguinte comando:

```powershell
Get-PSHostProcessInfo
```

Esse cmdlet retorna uma tabela que se parece com a seguinte saída:

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

Anote o `ProcessId` para o item na tabela com o `ProcessName` como `dotnet`. Esse processo é o seu aplicativo de funções.

Em seguida, execute o trecho a seguir:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Depois de iniciado, o depurador interrompe e mostra algo parecido com a seguinte saída:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Neste ponto, você ficar parado em um ponto de interrupção na [depurador do PowerShell](/powershell/module/microsoft.powershell.core/about/about_debuggers). A partir daqui, você pode fazer todas as operações de depuração normal, contornar, intervir, continuar, sair, entre outros. Para ver o conjunto completo de comandos de depuração disponíveis no console do, execute as `h` ou `?` comandos.

Você também pode definir pontos de interrupção nesse nível com o `Set-PSBreakpoint` cmdlet.

Depois de você continua e invoca totalmente o seu script, você observará que:

* O console do PowerShell em que você executou `Invoke-RestMethod` agora retornou um resultado.
* O console do PowerShell em que você executou `Debug-Runspace` está esperando para ser executado de um script.

Você pode chamar a mesma função novamente (usando `Invoke-RestMethod` por exemplo) e o depurador interrompe no logo após o `Wait-Debugger` comando.

## <a name="considerations-for-debugging"></a>Considerações sobre a depuração

Tenha em mente os seguintes problemas ao depurar o código de funções.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` pode fazer com que o depurador seja interrompido em um lugar inesperado

Os usos de extensão do PowerShell `Debug-Runspace`, que por sua vez se baseia em do PowerShell `BreakAll` recurso. Esse recurso informa ao PowerShell parar no primeiro comando que é executado. Esse comportamento lhe dá a oportunidade de definir pontos de interrupção em um runspace do depurado.

O tempo de execução do Azure Functions executa alguns comandos antes de realmente invocar seu `run.ps1` script, portanto, é possível que o depurador acaba significativa dentro a `Microsoft.Azure.Functions.PowerShellWorker.psm1` ou `Microsoft.Azure.Functions.PowerShellWorker.psd1`.

Essa interrupção deve ocorrer, execute as `continue` ou `c` comando para ignorar este ponto de interrupção. Você, em seguida, parar no ponto de interrupção esperado.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como desenvolver funções usando o PowerShell, consulte [guia do desenvolvedor do PowerShell do Azure Functions](functions-reference-powershell.md).
