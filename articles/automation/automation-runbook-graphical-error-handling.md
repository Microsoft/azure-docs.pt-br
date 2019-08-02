---
title: Tratamento de erros em runbooks gráficos na Automação do Azure
description: Este artigo descreve como implementar a lógica de tratamento de erros em runbooks gráficos na Automação do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ed6620333382a1e43ee1b38a009c91e8d7038233
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476944"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Tratamento de erros em runbooks gráficos na Automação do Azure

Um objeto de design runbook chave a considerar é identificar problemas diferentes que pode ter um runbook. Esses problemas podem incluir sucesso, estados de erro esperado e as condições de erro inesperado.

Runbooks deve incluir o tratamento de erros. Para validar a saída de uma atividade ou manipular um erro com runbooks gráficos, você pode usar uma atividade de código do Windows PowerShell, definem lógica condicional no link de saída da atividade ou aplicar outro método.          

Geralmente, se houver um erro não fatal que ocorre com uma atividade de runbook, qualquer atividade que segue é processada, independentemente do erro. O erro é provavelmente gerar uma exceção, mas a próxima atividade ainda pode ser executado. Essa é a maneira que o PowerShell foi projetado para lidar com erros.    

Os tipos de erros do PowerShell que podem ocorrer durante a execução são de finalização ou não fatais. As diferenças entre os erros de finalização e finalização não são da seguinte maneira:

* **Erro de finalização**: um erro grave durante a execução que interrompe o comando (ou a execução do script) completamente. Os exemplos incluem cmdlets inexistentes, erros de sintaxe que impedem a execução de um cmdlet ou outros erros fatais.

* **Erro não fatal**: um erro não grave que permite que a execução continue, apesar da falha. Os exemplos incluem erros operacionais como erros de arquivo não encontrado e problemas de permissões.

Runbooks gráficos de automação do Azure foram aprimorados com a capacidade de incluir o tratamento de erro. Agora você pode ativar exceções em erros de não finalização e criar links de erro entre atividades. Esse processo permite que um autor de runbook capture erros e gerencie condições percebidas ou inesperadas.  

## <a name="when-to-use-error-handling"></a>Quando usar o tratamento de erros

Sempre que houver uma atividade essencial que gera um erro ou exceção, é importante para evitar a próxima atividade no runbook do processamento e tratar o erro adequadamente. Isso é particularmente crítico quando seus runbooks dão suporte a uma empresa ou a um processo de operações de serviço.

Para cada atividade que pode produzir um erro, o autor do runbook pode adicionar um link de erro apontando para qualquer outra atividade. A atividade de destino pode ser de qualquer tipo, incluindo atividades de código, invocação de um cmdlet, chamada de outro runbook e assim por diante.

Além disso, a atividade de destino também pode ter links de saída. Esses links são links regulares ou erro. Isso significa que o autor de runbook pode implementar a lógica de tratamento de erros complexa sem recorrer a uma atividade de código. A prática recomendada é criar um runbook dedicado de tratamento de erros com funcionalidade comum, mas não é obrigatório. Atividade não é a única opção de código de lógica de tratamento de erros no PowerShell.  

Por exemplo, considere um runbook que tenta iniciar uma VM e instalar um aplicativo. Se a máquina virtual não iniciar corretamente, ele executa duas ações:

1. Ele envia uma notificação sobre esse problema.
2. Ele inicia outro runbook que provisiona automaticamente uma nova VM em vez disso.

Uma solução é ter um link de erro apontando para uma atividade que alças etapa um. Por exemplo, você pode conectar o **Write-Warning** cmdlet para uma atividade para a etapa 2, como o **AzureRmAutomationRunbook início** cmdlet.

Você também pode generalizar esse comportamento para uso em vários runbooks ao colocar essas duas atividades em um runbook de tratamento de erros separado e seguir as orientações sugeridas anteriormente. Antes de chamar esse runbook de tratamento de erros, você pode criar uma mensagem personalizada com base nos dados no runbook original e, em seguida, passá-la como um parâmetro para o runbook de tratamento de erros.

## <a name="how-to-use-error-handling"></a>Como usar o tratamento de erros

Cada atividade tem um parâmetro de configuração que transforma exceções em erros de não finalização. Por padrão, essa configuração é desabilitada. Recomendamos que você habilite essa configuração em qualquer atividade em que você deseja manipular erros.  

Habilitando a configuração, você garante que erros de não finalização e finalização na atividade sejam tratados como erros de não finalização e possam ser manipulados com um link de erro.  

Depois de definir essa configuração, você cria uma atividade que manipulará o erro. Se uma atividade produzir erros, os links de saída de erro serão seguidos e os links regulares não o serão, mesmo que uma atividade também produza saída regular.<br><br> ![Exemplo de link de erro de runbook de automação](media/automation-runbook-graphical-error-handling/error-link-example.png)

No exemplo a seguir, um runbook recupera uma variável que contém o nome do computador de uma máquina virtual. Em seguida, ele tenta iniciar a máquina virtual com a próxima atividade.<br><br> ![Exemplo de tratamento de erro de runbook de automação](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

A atividade **Get-AutomationVariable** e **Start-AzureRmVm** são configurados para converter exceções em erros. Se houver problemas ao obter a variável ou iniciar a VM, serão gerados erros.<br><br> ![Configurações de atividade de tratamento de erros de runbook de automação](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

Links de erro de fluxo dessas atividades em uma única **gerenciamento erro** atividade (uma atividade code). Essa atividade é configurada com uma expressão simple do PowerShell que usa o *lançar* palavra-chave para parar o processamento, juntamente com *$Error.Exception.Message* para obter a mensagem que descreve a exceção atual.<br><br> ![Exemplo de código de tratamento de erros de runbook de automação](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre links e tipos de link em runbooks gráficos, confira [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md#links-and-workflow).

* Para saber mais sobre a execução de runbooks, como monitorar trabalhos de runbook e outros detalhes técnicos, confira [Acompanhar um trabalho de runbook](automation-runbook-execution.md).

