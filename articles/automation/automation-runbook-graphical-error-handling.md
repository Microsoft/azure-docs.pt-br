---
title: Tratar erros em runbooks gráficos da Automação do Azure
description: Este artigo informa como implementar a lógica de tratamento de erros em runbooks gráficos.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 24c7aaf08b4d22706bee8f37025b12a656ceaff5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98895893"
---
# <a name="handle-errors-in-graphical-runbooks"></a>Tratar erros em runbooks gráficos

Um princípio de design importante a ser considerado para o runbook gráfico da Automação do Azure é a identificação de problemas que o runbook pode enfrentar durante a execução. Esses problemas podem incluir sucesso, estados de erro esperado e as condições de erro inesperado.

Geralmente, no caso de um erro não fatal com uma atividade de runbook, o Windows PowerShell manipula a atividade processando qualquer atividade que se segue, independentemente do erro. O erro é provavelmente gerar uma exceção, mas a próxima atividade ainda pode ser executado.

Seu runbook gráfico deve incluir código de tratamento de erros para lidar com problemas de execução. Para validar a saída de uma atividade ou tratar um erro, você pode usar uma atividade de código do PowerShell, definir a lógica condicional no link de saída da atividade ou aplicar outro método.

Runbooks gráficos de automação do Azure foram aprimorados com a possibilidade de incluir o tratamento de erros. Agora você pode ativar exceções em erros de não finalização e criar links de erro entre atividades. O processo aprimorado permite ao seu runbook detectar erros e gerenciar condições percebidas ou inesperadas. 

## <a name="powershell-error-types"></a>Tipos de erro do PowerShell

Os tipos de erros do PowerShell que podem ocorrer durante a execução do runbook são erros de encerramento e erros de não encerramento.
 
### <a name="terminating-error"></a>Erro de encerramento

Um erro de encerramento é um erro grave durante a execução que interrompe completamente a execução de um comando ou script. Os exemplos incluem cmdlets inexistentes, erros de sintaxe que impedem a execução de um cmdlet e outros erros fatais.

### <a name="non-terminating-error"></a>Erro de não encerramento

Um erro de não encerramento é um erro não grave que permite que a execução continue apesar da condição de erro. Os exemplos incluem erros operacionais como erros de arquivo não encontrado e problemas de permissões.

## <a name="when-to-use-error-handling"></a>Quando usar o tratamento de erros

Use o tratamento de erros no seu runbook quando uma atividade crítica gerar um erro ou exceção. É importante impedir que a próxima atividade no runbook seja processada e manipular o erro adequadamente. Manipular o erro é particularmente crítico quando seus runbooks dão suporte a uma empresa ou a um processo de operações de serviço.

## <a name="add-error-links"></a>Adicionar links de erro

Para cada atividade que pode produzir um erro, você pode adicionar um link de erro apontando para qualquer outra atividade. A atividade de destino pode ser de qualquer tipo, incluindo atividade de código, invocação de um cmdlet, chamada de outro runbook e assim por diante. A atividade de destino também pode ter links de saída, seja regulares ou de erro. Os links permitem que o runbook implemente a lógica de tratamento de erros complexa sem recorrer a uma atividade de código.

A prática recomendada é criar um runbook dedicado de tratamento de erros com funcionalidade comum, mas não é obrigatória. Por exemplo, considere um runbook que tenta iniciar uma máquina virtual e instalar um aplicativo. Se a VM não iniciar corretamente, ela:

1. envia uma notificação sobre esse problema.
2. Inicia outro runbook que provisiona automaticamente uma nova VM em vez disso.

Uma solução é ter um link de erro no runbook apontando para uma atividade que lida com a etapa um. Por exemplo, o runbook pode conectar o cmdlet `Write-Warning` a uma atividade da etapa dois, como o cmdlet [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook).

Você também pode generalizar esse comportamento para uso em vários runbooks ao colocar essas duas atividades em um runbook de tratamento de erros separado. Antes que o runbook original chame esse runbook de tratamento de erros, ele pode criar uma mensagem personalizada a partir dos dados e depois passá-la como um parâmetro para o runbook de tratamento de erros.

## <a name="turn-exceptions-into-non-terminating-errors"></a>Transformar exceções em erros de não encerramento

Cada atividade em seu runbook tem um parâmetro de configuração que transforma exceções em erros de não encerramento. Por padrão, essa configuração é desabilitada. Recomendamos ativar essa configuração em qualquer atividade em que seu runbook trate erros. Essa configuração garante que o runbook trate os erros de encerramento e de não encerramento na atividade como erros de não encerramento, usando um link de erro.  

Depois de habilitar a configuração, faça com que o runbook crie uma atividade que trata o erro. Se a atividade produzir qualquer erro, os links de erro de saída serão seguidos. Os links regulares não são seguidos, mesmo se a atividade também produzir saída regular.<br><br> ![Exemplo de link de erro de runbook de automação](media/automation-runbook-graphical-error-handling/error-link-example.png)

No exemplo a seguir, um runbook recupera uma variável que contém o nome do computador de uma VM. Em seguida, ele tenta iniciar a VM com a próxima atividade.<br><br> ![Exemplo de tratamento de erro de runbook de automação](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

A atividade `Get-AutomationVariable` e o cmdlet [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) são configurados para converter exceções em erros. Se houver problemas ao obter a variável ou iniciar a VM, o código gera erros.<br><br> ![Configurações de atividade de tratamento de erros de runbook de automação](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png).

Os links de erro fluem dessas atividades para uma única atividade de código `error management`. Essa atividade é configurada com uma expressão simples do PowerShell que usa a palavra-chave `throw` para interromper o processamento, além de `$Error.Exception.Message` para obter a mensagem que descreve a exceção atual.<br><br> ![Exemplo de código de tratamento de erros de runbook de automação](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como resolver erros em runbooks gráficos, confira [Solucionar problemas de runbook](troubleshoot/runbooks.md).
