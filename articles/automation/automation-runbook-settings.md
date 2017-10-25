---
title: "Configurações de runbook | Microsoft Docs"
description: "Descreve as definições de configuração de um runbook na Automação do Azure e como alterá-las usando o Portal de Gerenciamento do Azure e o Windows PowerShell."
services: automation
documentationcenter: 
author: eslesar
manager: stevenka
editor: tysonn
ms.assetid: a726f20c-a952-48b8-88ee-36d76aa3ac61
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/11/2016
ms.author: bwren
ms.openlocfilehash: 534ea7e3f2f8e5640db4d351c2bb3245f29b6eec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="runbook-settings"></a>Configurações de runbook
Cada runbook na Automação do Azure tem várias configurações que ajudam a identificá-lo e a alterar seu comportamento de log. Cada uma dessas configurações é descrita abaixo seguida de procedimentos sobre como modificá-las.

## <a name="settings"></a>Configurações
### <a name="name-and-description"></a>Nome e descrição
Você não pode alterar o nome de um runbook após ele ter sido criado. A descrição é opcional e pode ter até 512 caracteres.

### <a name="tags"></a>Marcas
Os rótulos permitem que você atribua diferentes palavras e frases para ajudar a identificar um runbook. Por exemplo, quando você envia um runbook para a [Galeria do PowerShell](https://www.powershellgallery.com/), também define rótulos específicos para identificar em quais categorias o runbook deve estar listado. Você pode especificar vários rótulos para um runbook separando-os com vírgulas.

### <a name="logging"></a>Registro em log
Por padrão, os registros Detalhado e Progresso não são gravados no histórico do trabalho. Você pode alterar as configurações de um runbook específico para registrar logs. Para saber mais sobre esses registros, confira [Saída de Runbook e Mensagens](automation-runbook-output-and-messages.md).

## <a name="changing-runbook-settings"></a>Alterando as configurações de runbook

### <a name="changing-runbook-settings-with-the-azure-portal"></a>Alterando as configurações de runbook com o portal do Azure
Você pode alterar as configurações de um runbook no portal do Azure na folha **Configurações** para o runbook.

1. No portal do Azure, selecione **Automação** e clique no nome de uma conta de automação.
2. Selecione a guia **Runbooks** .
3. Clique no nome de um runbook e você será direcionado para a folha de configurações do runbook. Aqui, você pode especificar ou modificar marcas e a descrição do runbook, configurar definições de rastreamento e registro em log e acessar ferramentas de suporte para ajudar a resolver problemas.     

### <a name="changing-runbook-settings-with-windows-powershell"></a>Alterando as configurações de runbook com o Windows PowerShell
Você pode usar o cmdlet [Set-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603786.aspx) para alterar as configurações de um runbook. Se você quiser especificar várias marcas, pode fornecer uma matriz ou uma única cadeia de caracteres com valores delimitados por vírgula para o parâmetro dos Rótulos. Você pode obter as marcas atuais com o [Get-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603728.aspx).

Os comandos de exemplo a seguir mostram como definir as propriedades de um runbook. Este exemplo adiciona três rótulos aos rótulos existentes e especifica que registros detalhados devem ser registrados em log.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="next-steps"></a>Próximas etapas
* Para aprender a criar e recuperar mensagens de erro e de saída de runbooks, confira [Saída e mensagens de Runbook](automation-runbook-output-and-messages.md) 
* Para entender como adicionar um runbook que já foi desenvolvido pela comunidade ou de outra fonte, ou criar seu próprio runbook, confira [Criando ou importando um runbook](automation-creating-importing-runbook.md) 

