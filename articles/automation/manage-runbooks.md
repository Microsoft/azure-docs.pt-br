---
title: Gerenciar runbooks na Automação do Azure
description: Este artigo descreve como gerenciar os runbooks na Automação do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5bb52e0547ed9bc18d67370ffb9db35942212aab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61300251"
---
# <a name="manage-runbooks-in-azure-automation"></a>Gerenciar runbooks na Automação do Azure

Você pode adicionar um runbook à Automação do Azure [criando um novo](#create-a-runbook) ou importando um runbook existente de um arquivo ou da [Galeria de Runbooks](automation-runbook-gallery.md). Este artigo oferece informações sobre a criação e a importação de runbooks de um arquivo.  Você pode obter todos os detalhes sobre o acesso a runbooks e a módulos da comunidade em [Galerias de runbook e de módulos para a Automação do Azure](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Criar um runbook

Você pode criar um novo runbook na Automação do Azure usando um dos portais do Azure ou o Windows PowerShell. Quando o runbook tiver sido criado, você poderá editá-lo usando as informações das [Noções básicas sobre o fluxo de trabalho do PowerShell](automation-powershell-workflow.md) e [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Criar um runbook no portal do Azure

1. No portal do Azure, abra sua conta da Automação.
2. No Hub, selecione **Runbooks** para abrir a lista de runbooks.
3. Clique no botão **Adicionar um runbook** e em **Criar um novo runbook**.
4. Digite um **Nome** para o runbook e selecione seu [Tipo](automation-runbook-types.md). O nome do runbook deve começar com uma letra e pode ter letras, números, sublinhados e traços.
5. Clique em **Criar** para criar o runbook e abrir o editor.

### <a name="create-a-runbook-with-powershell"></a>Criar um runbook com o PowerShell

Você pode usar o cmdlet [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) para criar um [runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) vazio. Use o parâmetro **Type** para especificar um dos quatro tipos de runbook.

Os comandos de exemplo a seguir mostram como criar um novo runbook vazio.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importar um runbook

Você pode criar um novo runbook na Automação do Azure importando um script do PowerShell ou o Fluxo de Trabalho do PowerShell (extensão .ps1), um runbook gráfico exportado (.graphrunbook) ou um script Python 2 (extensão .py).  Você precisa especificar o [tipo de runbook](automation-runbook-types.md) que é criado durante a importação, levando em consideração os aspectos a seguir.

* Um arquivo `.graphrunbook` só pode ser importado para um novo [runbook gráfico](automation-runbook-types.md#graphical-runbooks) e os runbooks gráficos só podem ser criados a partir de um arquivo `.graphrunbook`.
* Um arquivo `.ps1` com um Fluxo de Trabalho do PowerShell só poderá ser importado para um [runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Se o arquivo contiver vários Fluxos de Trabalho do PowerShell, a importação falhará. Você deve salvar cada fluxo de trabalho em seu próprio arquivo e importá-los separadamente.
* Um arquivo `.ps1` que não contém um fluxo de trabalho pode ser importado para um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) ou um [runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Se for importado para um runbook de Fluxo de Trabalho do PowerShell, ele será convertido em um fluxo de trabalho e serão incluídos comentários no runbook especificando as alterações feitas.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Para importar um runbook de um arquivo com o portal do Azure

Você pode usar o procedimento a seguir para importar um arquivo de script para a Automação do Azure.  

> [!NOTE]
> Observe que você só poderá importar um arquivo .ps1 para um runbook do Fluxo de Trabalho do PowerShell usando o portal.

1. No portal do Azure, abra sua conta da Automação.
2. No Hub, selecione **Runbooks** para abrir a lista de runbooks.
3. Clique no botão **Adicionar um runbook** e em **Importar**.
4. Clique em **Arquivo de runbook** para selecionar o arquivo a ser importado
5. Se o campo **Nome** estiver habilitado, você terá a opção de alterá-lo.  O nome do runbook deve começar com uma letra e pode ter letras, números, sublinhados e traços.
6. O [tipo de runbook](automation-runbook-types.md) é selecionado automaticamente, mas você pode alterá-lo depois levar as restrições aplicáveis em consideração. 
7. O novo runbook é exibido na lista de runbooks da Conta de Automação.
8. Será necessário [publicar o runbook](#publish-a-runbook) antes de executá-lo.

> [!NOTE]
> Depois de importar um runbook gráfico ou um runbook gráfico do fluxo de trabalho do PowerShell, você terá a opção de converter para o outro tipo, se desejar. Não é possível converter para um runbook de texto.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Para importar um runbook de um arquivo de script com o Windows PowerShell

Você pode usar o cmdlet [Import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) para importar um arquivo de script como um rascunho do runbook do Fluxo de Trabalho do PowerShell. Se o runbook já existir, a importação falhará a menos que você use o parâmetro *-Force*.

Os comandos de exemplo a seguir mostram como importar um arquivo de script para um runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Testar um runbook

Quando você testa um runbook, a [Versão de rascunho](#publish-a-runbook) é executada e as ações que ela realiza são concluídas. Nenhum histórico de trabalho é criado, mas os fluxos [Saída](automation-runbook-output-and-messages.md#output-stream) e [Aviso e Erro](automation-runbook-output-and-messages.md#message-streams) são exibidos no Painel da saída de Teste. As mensagens para o [Fluxo Detalhado](automation-runbook-output-and-messages.md#message-streams) serão exibidas no Painel de Saída somente se a [variável $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) for definida para Continuar.

Mesmo que a versão de rascunho esteja em execução, o runbook ainda será executado normalmente e executará qualquer ação nos recursos do ambiente. Por esse motivo, você deve testar apenas runbooks nos recursos de não produção.

O procedimento para testar cada [tipo de runbook](automation-runbook-types.md) é o mesmo e não há nenhuma diferença entre testar o editor de texto e o editor gráfico no portal do Azure.  

1. Abra a versão de Rascunho do runbook no [editor de texto](automation-edit-textual-runbook.md) ou no [editor gráfico](automation-graphical-authoring-intro.md).
1. Clique no botão **Testar** para abrir a página de Teste.
1. Se o runbook tiver parâmetros, eles serão listados no painel à esquerda, onde você pode fornecer valores a serem usados para o teste.
1. Se você quiser executar o teste em um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), altere **Configurações da Execução** para **Hybrid Worker** e selecione o nome do grupo de destino.  Caso contrário, mantenha o padrão **Azure** para executar o teste na nuvem.
1. Clique no botão **Iniciar** para iniciar a origem.
1. Se o runbook for um [Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) ou um [Gráfico](automation-runbook-types.md#graphical-runbooks),então, você poderá interrompê-lo ou suspendê-lo enquanto ele está sendo testado com os botões abaixo do Painel de Saída. Quando você suspende o runbook, ele conclui a atividade atual antes de ser suspenso. Após o runbook ser suspenso, você pode interrompê-lo ou reiniciá-lo.
1. Inspecione a saída do runbook no painel de saída.

## <a name="publish-a-runbook"></a>Publicar um runbook

Quando você criar ou importar um novo runbook, deverá publicá-lo antes de poder executá-lo.  Cada runbook na Automação tem um Rascunho e uma versão Publicada. Somente a versão Publicada está disponível para ser executada, e somente a versão de Rascunho pode ser editada. A versão Publicada não é afetada pelas alterações feitas na versão de Rascunho. Quando a versão de Rascunho estiver pronta para ser disponibilizada, você a publicará e ela substituirá a versão Publicada pela versão de Rascunho.

### <a name="azure-portal"></a>Portal do Azure

1. Abra o runbook no portal do Azure.
2. Clique no botão **Editar** .
3. Clique no botão **Publicar** e em **Sim** na mensagem de verificação.

### <a name="powershell"></a>PowerShell

Você pode usar o cmdlet [Publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) para publicar um runbook com o Windows PowerShell. Os comandos de exemplo a seguir mostram como publicar um runbook de exemplo.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como você pode aproveitar a Galeria de Módulos do PowerShell e do Runbook, consulte [Galerias de runbook e de módulos para a Automação do Azure](automation-runbook-gallery.md)
* Para saber mais sobre como editar runbooks do PowerShell e do Fluxo de Trabalho do PowerShell com um editor de texto, consulte [Editando runbooks textuais na Automação do Azure](automation-edit-textual-runbook.md)
* Para saber mais sobre a criação de runbooks Gráficos, veja [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)
