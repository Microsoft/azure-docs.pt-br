---
title: Editar runbooks de texto na Automação do Azure
description: Este artigo informa como usar o editor de texto da Automação do Azure para trabalhar com runbooks do PowerShell e do Fluxo de Trabalho do PowerShell.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8d6b786ffaf309e147de27e8cd8be314a3d8a5fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896979"
---
# <a name="edit-textual-runbooks-in-azure-automation"></a>Editar runbooks de texto na Automação do Azure

É possível usar o editor de texto na Automação do Azure para editar os [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks) e os [runbooks do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Esse editor tem os recursos típicos de outros editores de código, como o IntelliSense. Ele também usa a codificação por cores com recursos especiais adicionais para ajudar você a acessar recursos comuns a runbooks. 

O editor textual inclui um recurso para inserir código para cmdlets, ativos e runbooks filhos em um runbook. Em vez de digitar o código por conta própria, você pode selecionar de uma lista de recursos disponíveis e o editor insere o código apropriado no runbook.

Cada runbook da Automação do Azure tem duas versões, Rascunho e Publicado. Edite a versão de Rascunho do runbook e publique-a para que ela possa ser executada. A versão Publicado não pode ser editada. Para obter mais informações, confira [Publicar um runbook](manage-runbooks.md#publish-a-runbook).

Este artigo oferece as etapas detalhadas para a execução de funções diferentes com este editor. Eles não são aplicáveis a [runbooks gráficos](automation-runbook-types.md#graphical-runbooks). Para trabalhar com esses runbooks, confira [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md).

## <a name="edit-a-runbook-with-the-azure-portal"></a>Editar um runbook com o portal do Azure

1. No portal do Azure, selecione sua conta de Automação.
2. Em **AUTOMAÇÃO DE PROCESSO**, selecione **Runbooks** para abrir a lista de runbooks.
3. Escolha o runbook a ser editado e clique em **Editar**.
4. Edite o runbook.
5. Clique em **Salvar** quando concluir suas edições.
6. Clique em **Publicar** se quiser publicar a última versão de rascunho do runbook.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Inserir um cmdlet em um runbook

1. Na Tela do editor de texto, posicione o cursor onde você deseja colocar o cmdlet.
2. Expanda o nó **Cmdlets** no controle Biblioteca.
3. Expanda o módulo que contém o cmdlet que você deseja usar.
4. Clique com o botão direito do mouse no nome do cmdlet a ser inserido e selecione **Adicionar à tela**. Se o cmdlet tiver mais de um parâmetro definido, o editor adicionará o conjunto padrão. Você também pode expandir o cmdlet para selecionar um conjunto de parâmetros diferente.
5. Observe que o código para o cmdlet é inserido com a lista de parâmetros inteira.
6. Forneça um valor adequado no lugar do valor entre chaves (<>) para os parâmetros obrigatórios. Remova todos os parâmetros que não forem necessários.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Inserir código para um runbook filho em um runbook

1. Na tela do editor de texto, posicione o cursor onde você deseja colocar o código do [runbook filho](automation-child-runbooks.md).
2. Expanda o nó **Runbooks** no controle Biblioteca.
3. Clique com botão direito do mouse no runbook a ser inserido e selecione **Adicionar à tela**.
4. O código do runbook filho é inserido com todos os espaços reservados para os parâmetros de runbook.
5. Substitua os espaços reservados pelos valores adequados para cada parâmetro.

### <a name="insert-an-asset-into-a-runbook"></a>Para inserir um ativo em um runbook

1. No controle da tela do editor de texto, posicione o cursor onde você deseja colocar o código do runbook filho.
2. Expanda o nó **Ativos** no controle Biblioteca.
3. Expanda o nó para o tipo de ativo desejado.
4. Clique com botão direito do mouse no nome do ativo a ser inserido e selecione **Adicionar à tela**. Para os [ativos da variável](./shared-resources/variables.md), selecione **Adicionar "Get Variable" à tela** ou **Adicionar "Set Variable" à tela**, dependendo de você querer obter ou definir a variável.
5. Observe que o código do ativo é inserido no runbook.

## <a name="edit-an-azure-automation-runbook-using-windows-powershell"></a>Editar um runbook da Automação do Azure usando o Windows PowerShell

Para editar um runbook com o Windows PowerShell, use seu editor preferido e salve-o como um arquivo **.ps1**. Você pode usar o cmdlet [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) para recuperar o conteúdo do runbook. Você pode usar o cmdlet [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) para substituir o runbook de rascunho existente pelo modificado.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Para recuperar o conteúdo de um runbook usando o Windows PowerShell

Os comandos de exemplo a seguir mostram como recuperar o script de um runbook e salvá-lo em um arquivo de script. Neste exemplo, a versão de Rascunho é recuperada. Também é possível recuperar a versão Publicado do runbook, embora essa versão não possa ser alterada.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Alterar o conteúdo de um runbook usando o Windows PowerShell

Os seguintes comandos de exemplo mostram como substituir o conteúdo existente de um runbook pelo conteúdo de um arquivo de script. 

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar runbooks na Automação do Azure](manage-runbooks.md).
* [Aprender sobre o Fluxo de Trabalho do PowerShell](automation-powershell-workflow.md).
* [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md).
* [Certificados](./shared-resources/certificates.md).
* [Conexões](automation-connections.md).
* [Credenciais](./shared-resources/credentials.md).
* [Agendas](./shared-resources/schedules.md).
* [Variáveis](./shared-resources/variables.md).
* [Referência de cmdlet do PowerShell](/powershell/module/az.automation).
