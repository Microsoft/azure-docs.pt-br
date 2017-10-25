---
title: "Editando runbooks de texto na Automação do Azure"
description: "Este artigo fornece procedimentos diferentes para trabalhar com runbooks do PowerShell e do Fluxo de Trabalho do PowerShell na Automação do Azure usando o editor de texto."
services: automation
documentationcenter: 
author: eslesar
manager: stevenka
editor: tysonn
ms.assetid: 6f5b48fb-6f30-4e99-9e14-9061b5554b08
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: magoedte;bwren
ms.openlocfilehash: ae36342ab0f42c364dedd4107a59f5b0ffc20a0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Editando runbooks de texto na Automação do Azure
O editor de texto na Automação do Azure pode ser usado para editar os [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks) e os [runbooks do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Ele tem os recursos típicos de outros editores de código, como o intellisense e a codificação de cores, com recursos especiais adicionais para ajudar a acessar os recursos comuns aos runbooks.  Este artigo oferece as etapas detalhadas para a execução de funções diferentes com este editor.

O editor de texto inclui um recurso para inserir código para runbooks de atividades, de ativos e filhos em um runbook. Em vez de digitar o código por conta própria, você poderá selecionar de uma lista de recursos disponíveis e ter o código apropriado inserido no runbook.

Cada runbook da Automação do Azure tem duas versões, Rascunho e Publicado. Edite a versão de Rascunho do runbook e publique-a para que ela possa ser executada. A versão Publicado não pode ser editada. Para saber mais, consulte [Publicando um runbook](automation-creating-importing-runbook.md#publishing-a-runbook) .

Para trabalhar com [Runbooks Gráficos](automation-runbook-types.md#graphical-runbooks), consulte [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Para editar um runbook com o portal do Azure
Use o procedimento a seguir para abrir um runbook para edição no editor de texto.

1. No portal do Azure, selecione sua conta de automação.
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.
3. Clique no nome do runbook que deseja editar e clique no botão **Editar** .
4. Execute a edição necessária.
5. Clique em **Salvar** quando concluir suas edições.
6. Clique em **Publicar** se quiser que a última versão de rascunho do runbook seja publicada.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Para inserir um cmdlet em um runbook
1. Na Tela do editor de texto, posicione o cursor onde você deseja colocar o cmdlet.
2. Expanda o nó **Cmdlets** no controle Biblioteca.
3. Expanda o módulo que contém o cmdlet que você deseja usar.
4. Clique com botão direito do mouse no cmdlet a ser inserido e selecione **Adicionar à tela**.  Se o cmdlet tiver mais de um parâmetro definido, o conjunto padrão será adicionado.  Você também pode expandir o cmdlet para selecionar um conjunto de parâmetros diferente.
5. O código para o cmdlet é inserido com a lista de parâmetros inteira.
6. Forneça um valor adequado no lugar do tipo de dados entre chaves <> para todos os parâmetros obrigatórios.  Remova todos os parâmetros que não sejam necessários.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Para inserir código para um runbook filho em um runbook
1. Na Tela do editor de texto, posicione o cursor onde você deseja colocar o código do [runbook filho](automation-child-runbooks.md).
2. Expanda o nó **Runbooks** no controle Biblioteca.
3. Clique com botão direito do mouse no runbook a ser inserido e selecione **Adicionar à tela**.
4. O código do runbook filho é inserido com todos os espaços reservados para os parâmetros de runbook.
5. Substitua os espaços reservados pelos valores adequados para cada parâmetro.

### <a name="to-insert-an-asset-into-a-runbook"></a>Para inserir um ativo em um runbook
1. Na Tela do editor de texto, posicione o cursor onde você deseja colocar o código do runbook filho.
2. Expanda o nó **Ativos** no controle Biblioteca.
3. Expanda o nó para o tipo de ativo desejado.
4. Clique com botão direito do mouse no ativo a ser inserido e selecione **Adicionar à tela**.  Para os [ativos da variável](automation-variables.md), selecione **Adicionar "Get Variable" à tela** ou **Adicionar "Set Variable" à tela**, dependendo de você querer obter ou definir a variável.
5. O código do ativo é inserido no runbook.

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Para editar um runbook com o portal do Azure
Use o procedimento a seguir para abrir um runbook para edição no editor de texto.

1. No portal do Azure, selecione **Automação** e clique no nome de uma conta de automação.
2. Selecione a guia **Runbooks** .
3. Clique no nome do runbook que deseja editar e então selecione a guia **Autor** .
4. Clique no botão **Editar** na parte inferior da tela.
5. Execute a edição necessária.
6. Clique em **Salvar** quando concluir suas edições.
7. Clique em **Publicar** se quiser que a última versão de rascunho do runbook seja publicada.

### <a name="to-insert-an-activity-into-a-runbook"></a>Para inserir uma atividade em um Runbook
1. Na Tela do editor de texto, posicione o cursor onde você deseja colocar a atividade.
2. Na parte inferior da tela, clique em **Inserir** e **Atividade**.
3. Na coluna **Módulo de Integração** , selecione o módulo que contém a atividade.
4. No painel **Atividade** , selecione uma atividade.
5. Na coluna **Descrição** , observe a descrição da atividade. Opcionalmente, você pode clicar em Exibir ajuda detalhada para iniciar a ajuda para a atividade no navegador.
6. Clique na seta à direita.  Se a atividade tiver parâmetros, eles serão listados para sua informação.
7. Clique no botão de seleção.  O código para executar a atividade será inserido no runbook.
8. Se a atividade exigir parâmetros, forneça um valor adequado no lugar do tipo de dados entre chaves <>.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Para inserir código para um runbook filho em um runbook
1. Na Tela do editor de texto, posicione o cursor onde você deseja colocar o [runbook filho](automation-child-runbooks.md).
2. Na parte inferior da tela, clique em **Inserir** e **Runbook**.
3. Selecione o runbook a ser inserido na coluna cetral e clique na seta para a direita.
4. Se o runbook tiver parâmetros, eles serão listados para sua informação.
5. Clique no botão de seleção.  O código para executar o runbook selecionado será inserido no runbook atual.
6. Se o runbook exigir parâmetros, forneça um valor adequado no lugar do tipo de dados entre chaves <>.

### <a name="to-insert-an-asset-into-a-runbook"></a>Para inserir um ativo em um runbook
1. Na Tela do editor de texto, posicione o cursor onde você deseja colocar a atividade para recuperar o ativo.
2. Na parte inferior da tela, clique em **Inserir** e **Configuração**.
3. Na coluna **Ação de Configuração** , selecione a ação desejada.
4. Selecione os ativos disponíveis na coluna central.
5. Clique no botão de seleção.  O código para obter ou definir o ativo será inserido no runbook.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Para editar um runbook da Automação do Azure usando o Windows PowerShell
Para editar um runbook com o Windows PowerShell, use seu editor preferido e salve-o como um arquivo .ps1. Você pode usar o cmdlet [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) para recuperar o conteúdo do runbook, em seguida, o cmdlet [Set-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) para substituir o runbook de rascunho existente pelo modificado.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Para recuperar o conteúdo de um runbook usando o Windows PowerShell
Os comandos de exemplo a seguir mostram como recuperar o script de um runbook e salvá-lo em um arquivo de script. Neste exemplo, a versão de Rascunho é recuperada. Também é possível recuperar a versão Publicado do runbook, embora essa versão não possa ser alterada.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    $runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
    $runbookContent = $runbookDefinition.Content

    Out-File -InputObject $runbookContent -FilePath $scriptPath

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Para alterar o conteúdo de um runbook usando o Windows PowerShell
Os seguintes comandos de exemplo mostram como substituir o conteúdo existente de um runbook pelo conteúdo de um arquivo de script. Observe que esse é o mesmo procedimento de exemplo mostrado em [Para importar um runbook de um arquivo de script com o Windows PowerShell](automation-creating-importing-runbook.md).

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## <a name="related-articles"></a>Artigos relacionados
* [Criando ou importando um runbook na Automação do Azure](automation-creating-importing-runbook.md)
* [Aprendendo o fluxo de trabalho do PowerShell](automation-powershell-workflow.md)
* [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)
* [Certificados](automation-certificates.md)
* [Conexões](automation-connections.md)
* [Credenciais](automation-credentials.md)
* [Agendas](automation-schedules.md)
* [Variáveis](automation-variables.md)
