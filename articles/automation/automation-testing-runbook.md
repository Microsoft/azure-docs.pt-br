---
title: "Testando um runbook na Automação do Azure | Microsoft Docs"
description: "Antes de publicar um runbook na Automação do Azure, você pode testá-lo para garantir que ele funcione conforme o esperado.  Este artigo descreve como testar um runbook e exibir sua saída."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 7f7db785-52c0-4613-aa12-b02fd32a5182
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/12/2016
ms.author: magoedte;bwren
ms.openlocfilehash: 2c811cc8c8277e9840babcf1043cde44238b8661
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="testing-a-runbook-in-azure-automation"></a>Testando um runbook na Automação do Azure
Quando você testa um runbook, a [Versão de rascunho](automation-creating-importing-runbook.md#publishing-a-runbook) é executada e as ações que ela realiza são concluídas. Nenhum histórico de trabalho é criado, mas os fluxos [Saída](automation-runbook-output-and-messages.md#output-stream) e [Aviso e Erro](automation-runbook-output-and-messages.md#message-streams) são exibidos no Painel da saída de Teste. As mensagens para o [Fluxo Detalhado](automation-runbook-output-and-messages.md#message-streams) serão exibidas no Painel de Saída somente se a [variável $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) for definida para Continuar.

Mesmo que a versão de rascunho esteja sendo executada, o runbook ainda executa o fluxo de trabalho normalmente e executa qualquer ação em relação aos recursos do ambiente. Por esse motivo, você deve testar apenas runbooks em recursos de não produção.

O procedimento para testar cada [tipo de runbook](automation-runbook-types.md) é o mesmo e não há nenhuma diferença entre testar o editor de texto e o editor gráfico no Portal do Azure.  

## <a name="to-test-a-runbook-in-the-azure-portal"></a>Para testar um runbook no Portal do Azure
Você pode trabalhar com qualquer [tipo de runbook](automation-runbook-types.md) no Portal do Azure.

1. Abra a versão de Rascunho do runbook no [editor de texto](automation-edit-textual-runbook.md) ou no [editor gráfico](automation-graphical-authoring-intro.md).
2. Clique no botão **Testar** para abrir a folha de Teste.
3. Se o runbook tiver parâmetros, eles serão listados no painel à esquerda, em que você pode fornecer valores a serem usados para o teste.
4. Se você quiser executar o teste em um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), altere **Configurações da Execução** para **Hybrid Worker** e selecione o nome do grupo de destino.  Caso contrário, mantenha o padrão **Azure** para executar o teste na nuvem.
5. Clique no botão **Iniciar** para iniciar a origem.
6. Se o runbook for um [Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) ou [Gráfico](automation-runbook-types.md#graphical-runbooks),então, você poderá interrompê-lo ou suspendê-lo enquanto ele está sendo testado com os botões abaixo do Painel de Saída. Quando você suspende o runbook, ele conclui a atividade atual antes de ser suspenso. Após o runbook ser suspenso, você pode interrompê-lo ou reiniciá-lo.
7. Inspecione a saída do runbook no painel de saída.

## <a name="next-steps"></a>Próximas etapas
* Para saber como criar ou importar um runbook, veja [Criando ou importando um runbook na Automação do Azure](automation-creating-importing-runbook.md)
* Para saber mais sobre a Criação Gráfica, veja [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)
* Para começar a usar os runbooks de fluxo de trabalho do PowerShell, confira [Meu primeiro runbook de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre como configurar runbooks para retornar mensagens de status e erros, incluindo práticas recomendadas, confira [Saída e mensagens de runbook na Automação do Azure](automation-runbook-output-and-messages.md)

