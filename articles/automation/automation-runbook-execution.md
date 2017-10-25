---
title: "Execução de runbook na Automação do Azure | Microsoft Docs"
description: "Descreve os detalhes de como um runbook na Automação do Azure é processado."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: d10c8ce2-2c0b-4ea7-ba3c-d20e09b2c9ca
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/17/2017
ms.author: bwren
ms.openlocfilehash: 6f627140e6c5583636f7979889f74e489fe66496
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="runbook-execution-in-azure-automation"></a>Execução de runbook na Automação do Azure
Quando você inicia um runbook na Automação do Azure, um trabalho é criado. Um trabalho é uma instância única de execução de um runbook. Um trabalhador da Automação do Azure é atribuído para executar cada tarefa. Enquanto os trabalhadores são compartilhados por várias contas do Azure, os trabalhos de diferentes contas de automação ficam isolados uns dos outros. Você não tem controle sobre qual trabalhador atende a solicitação do seu trabalho.  Um único runbook pode ter vários trabalhos em execução ao mesmo tempo. Quando você exibe a lista de runbooks no Portal do Azure, ela lista o status de todos os trabalhos iniciados para cada runbook. Você pode exibir a lista de trabalhos para cada runbook a fim de acompanhar o status de cada um. Para obter uma descrição das opções de status de trabalho diferentes, confira [Status de trabalho](#job-statuses).

O diagrama a seguir mostra o ciclo de vida de um trabalho de runbook para [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) e [runbooks de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Status de trabalho - Fluxo de trabalho do PowerShell](./media/automation-runbook-execution/job-statuses.png)

O diagrama a seguir mostra o ciclo de vida de um trabalho de runbook para [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks).

![Status de trabalho - Script do PowerShell](./media/automation-runbook-execution/job-statuses-script.png)

Os trabalhos têm acesso aos recursos do Azure fazendo uma conexão à sua assinatura do Azure. Eles só têm acesso aos recursos no seu data center se estes puderem ser acessados da nuvem pública.

## <a name="job-statuses"></a>Status de trabalho
A tabela a seguir descreve os diferentes status possíveis para um trabalho.

| Status | Descrição |
|:--- |:--- |
| Concluído |Operação concluída com sucesso. |
| Falha |Para [runbooks gráficos e de fluxo de trabalho do PowerShell](automation-runbook-types.md), o runbook falhou ao compilar.  Para [runbooks de Script do PowerShell](automation-runbook-types.md), o runbook falhou ao iniciar ou o trabalho encontrou uma exceção. |
| Erro, aguardando recursos |O trabalho falhou porque atingiu o limite de [fração justa](#fairshare) três vezes e iniciou do mesmo ponto de verificação ou desde o início do runbook em cada uma das vezes. |
| Em fila |O trabalho está aguardando recursos de um trabalho do Automation ficar disponível para que ele possa ser iniciado. |
| Iniciando |O trabalho foi atribuído a um trabalho, e o sistema está iniciando-o. |
| Continuando |O sistema está reiniciando o trabalho depois que ele foi suspenso. |
| Executando |O trabalho está em execução. |
| Executando, aguardando recursos |O trabalho foi descarregado, pois atingiu o limite de [fração justa](#fairshare) . Ele será retomado em breve do seu último ponto de verificação. |
| Parada |O trabalho foi interrompido pelo usuário antes de ser concluído. |
| Parando |O sistema está no processo de interromper o trabalho. |
| Suspenso |O trabalho foi suspenso pelo usuário, pelo sistema ou por um comando no runbook. Um trabalho suspenso pode ser iniciado novamente e retomará do seu último ponto de verificação ou desde o início do runbook, se ele não tiver pontos de verificação. O runbook só será suspenso pelo sistema quando ocorrer uma exceção. Por padrão, ErrorActionPreference é definido como **Continuar** o que significa que o trabalho continua a ser executado no caso de um erro. Se essa variável de preferência for definida como **Parar**, o trabalho será suspenso no caso de um erro.  Aplica-se a somente [runbooks gráficos e de fluxo de trabalho do PowerShell](automation-runbook-types.md) . |
| Suspensão |O sistema está tentando suspender o trabalho por solicitação do usuário. O runbook precisa atingir seu próximo ponto de verificação antes de poder ser suspenso. Se já passou de seu último ponto de verificação, ele será concluído antes de ser suspenso.  Aplica-se a somente [runbooks gráficos e de fluxo de trabalho do PowerShell](automation-runbook-types.md) . |

## <a name="viewing-job-status-from-the-azure-portal"></a>Exibindo o status do trabalho no portal do Azure
Você pode exibir um status resumido de todos os trabalhos de runbook ou analisar os detalhes de um trabalho específico de runbook no portal do Azure ou configurando a integração com seu espaço de trabalho de análise do Log Analytics do OMS (Microsoft Operations Management Suite) para encaminhar fluxos e status do trabalho do runbook.  Para saber mais sobre a integração com o Log Analytics do OMS, confira [Encaminhar status e fluxos de trabalho da Automação para o Log Analytics (OMS)](automation-manage-send-joblogs-log-analytics.md).  

### <a name="automation-runbook-jobs-summary"></a>Resumo dos trabalhos de runbook da Automação
À direita da Conta de automação selecionada, você pode ver um resumo de todos os trabalhos de runbook para uma Conta de automação selecionada no bloco **Estatísticas de Trabalho**.<br><br> ![Bloco Estatísticas de Trabalho](./media/automation-runbook-execution/automation-account-job-status-summary.png).<br> Esse bloco exibe a contagem e a representação gráfica do status do trabalho para todos os trabalhos executados.  

Um clique no bloco exibe a folha **Trabalhos**, que inclui uma lista resumida de todos os trabalhos executados, com status, execução do trabalho e as horas de início e de conclusão.<br><br> ![Folha Trabalhos da Conta de automação](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)<br><br>  Você pode filtrar a lista de trabalhos selecionando **Filtrar trabalhos** e filtrar por um runbook ou status do trabalho específico ou, na lista suspensa, pelo intervalo de data/hora de pesquisa.<br><br> ![Filtrar por status do trabalho](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Como alternativa, você pode exibir detalhes de resumo do trabalho para um runbook específico selecionando esse runbook na folha **Runbooks** folha da sua Conta de automação e selecionando o bloco **Trabalhos**.  Isso exibe a folha **Trabalhos** e, nela você pode clicar no registro do trabalho para exibir seus detalhes e sua saída.<br><br> ![Folha Trabalhos da Conta de automação](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)<br> 

### <a name="job-summary"></a>Resumo do trabalho
Você pode exibir uma lista de todos os trabalhos que foram criados para um determinado runbook e o status mais recente. Você pode filtrar essa lista pelo status do trabalho e o intervalo de datas para a última alteração no trabalho. Para exibir as informações detalhadas e sua saída, clique no nome de um trabalho. A exibição detalhada do trabalho inclui os valores para os parâmetros de runbook que foram fornecidos para esse trabalho.

Você pode usar as etapas a seguir para exibir os trabalhos de um runbook.

1. No Portal do Azure, selecione **Automação** e, em seguida, selecione no nome de uma Conta de automação.
2. No hub, selecione **Runbooks** e, em seguida, na folha **Runbooks**, selecione um runbook da lista.
3. Na folha do runbook selecionado, clique no bloco **Trabalhos**.
4. Clique em um dos trabalhos na lista e, na folha de detalhes do trabalho do runbook, você pode exibir seus detalhes e sua saída.

## <a name="retrieving-job-status-using-windows-powershell"></a>Recuperando o status do trabalho usando o Windows PowerShell
Você pode usar o [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) para recuperar os trabalhos criados para um runbook e os detalhes de um trabalho específico. Se você inicia um runbook com o Windows PowerShell usando [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx), ele retorna o trabalho resultante. Use [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx)Output para obter a saída de um trabalho.

Os comandos de exemplo a seguir recuperam o último trabalho para um exemplo de runbook e exibe seu status, os valores fornecidos para os parâmetros de runbook e a saída do trabalho.

    $job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
    –RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
    $job.Status
    $job.JobParameters
    Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output

## <a name="fair-share"></a>fração justa
Para compartilhar recursos entre todos os runbooks na nuvem, a Automação do Azure descarregará temporariamente qualquer trabalho depois que ele for executado por três horas.  Durante esse tempo, os trabalhos para [runbooks com base no PowerShell](automation-runbook-types.md#powershell-runbooks) são interrompidos e não serão reiniciados.  O status do trabalho mostrará **Parado**.  Esse tipo de runbook é sempre retomado desde o início, já que não dá suporte a pontos de verificação.  

[Os runbooks baseados em Fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) serão retomados do seu último [ponto de verificação](https://docs.microsoft.com/system-center/sma/overview-powershell-workflows#bk_Checkpoints).  Depois de ser executado por três horas, o trabalho do runbook será suspenso pelo serviço e seu status mostrará **Em execução, aguardando recursos**.  Quando uma área restrita ficar disponível, o runbook será reiniciado automaticamente pelo Serviço de automação e será retomado do último ponto de verificação.  Este é o comportamento normal do fluxo de trabalho do PowerShell para suspender/reiniciar.  Se o runbook exceder novamente as três horas de tempo de execução, o processo se repetirá mais três vezes.  Após a terceira reinicialização, se o runbook ainda não tiver sido concluído nas três horas, o trabalho do runbook será considerado como uma falha e o status do trabalho será exibido como **Falhou, aguardando recursos**.  Nesse caso, você receberá a seguinte exceção com a falha.

*O trabalho não pode continuar a execução porque foi removido repetidamente do mesmo ponto de verificação. Verifique se o Runbook não executa operações demoradas sem persistir o estado.*

Isso serve para proteger o serviço contra runbooks em execução indefinidamente sem conclusão, já que não são capazes alcançar o próximo ponto de verificação sem serem descarregados novamente.

Se o runbook não tiver nenhum ponto de verificação ou o trabalho não atingir o primeiro ponto de verificação antes de ser descarregado, ele será reiniciado desde o início.  

Quando você cria um runbook, verifique se o tempo para executar atividades entre dois pontos de verificação não ultrapassa as três horas. Talvez seja necessário adicionar pontos de verificação ao seu runbook para que não atinja esse limite de três horas ou ultrapasse as operações de execução. Por exemplo, o runbook pode executar uma reindexação em um grande banco de dados SQL. Se essa operação única não for concluída dentro do limite adequado, o trabalho será descarregado e reiniciado do princípio. Nesse caso, você deve dividir a operação de reindexação em várias etapas, como a reindexação de uma tabela de cada vez, e inserir um ponto de verificação após cada operação para que o trabalho possa continuar após a conclusão da última operação.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre os diferentes métodos que podem ser usados para iniciar um runbook na Automação do Azure, confira [Iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md)

