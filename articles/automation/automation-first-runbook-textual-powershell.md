---
title: Meu primeiro runbook do PowerShell na Automação do Azure
description: Tutorial que orienta você pela criação, pelos testes e pela publicação de um runbook simples do PowerShell.
keywords: azure powershell, tutorial de script do powershell, automação do powershell
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 11/27/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 10b93e54bc3f13c72889ab7c75b0e4f6e280e7d8
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476734"
---
# <a name="my-first-powershell-runbook"></a>Meu primeiro runbook do PowerShell

> [!div class="op_single_selector"]
> * [Gráfico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Este tutorial orienta você durante a criação de um [Runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) na Automação do Azure. Você começa com um runbook simples, que você testa e publica enquanto aprende a acompanhar o status do trabalho do runbook. Em seguida, você modifica o runbook para gerenciar os recursos do Azure, neste caso, iniciando uma máquina virtual do Azure. Depois, você torna o runbook mais robusto adicionando parâmetros de runbook.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-quickstart-create-account.md) para manter o runbook e se autenticar nos recursos do Azure. Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Você para e inicia essa máquina, portanto, não deve ser uma VM de produção.
* Talvez você precise [atualizar os módulos do Azure](automation-update-azure-modules.md) com base em cmdlets que você usa.

## <a name="create-new-runbook"></a>Criar o novo runbook

Você começa criando um runbook simples que exibe o texto *Olá, Mundo*.

1. No portal do Azure, abra sua conta da Automação.
2. Clique em **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.
3. Crie um novo runbook clicando no botão **+ Adicionar um runbook** e em **Criar um novo runbook**.
4. Atribua o nome *MyFirstRunbook-PowerShell*ao runbook.
5. Neste caso, você criará um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks), portanto, escolha **PowerShell** como o **Tipo de runbook**.
6. Clique em **criar** para criar o runbook e abrir o editor textual.

## <a name="add-code-to-the-runbook"></a>Adicionar código ao runbook

Você pode digitar o código diretamente no runbook, ou você pode selecionar cmdlets, runbooks, e ativos do controle de biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Para este passo a passo, você digita diretamente no runbook.

1. O seu runbook está vazio no momento. Digite *Write-Output "Olá, Mundo”.* no corpo do script.

   ![Olá Mundo](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  

2. Salve o runbook clicando em **Salvar**.

## <a name="step-3---test-the-runbook"> </a> Testar o runbook

Antes de publicar o runbook para disponibilizá-lo na produção, teste-o para verificar se ele funciona corretamente. Quando você testa um runbook, executa sua versão de **Rascunho** e vê sua saída interativamente.

1. Clique em **Painel de teste** para abrir o Painel de teste.
2. Clique em **Iniciar** para iniciar o teste. Essa deve ser a única opção habilitada.
3. Um [trabalho de runbook](automation-runbook-execution.md) é criado e seu status é exibido.

   O status do trabalho inicia como *Na fila*, indicando que ele está aguardando um trabalho de runbook na nuvem ficar disponível. E ele muda para *Iniciando* quando um trabalhador reivindica o trabalho e para *Executando* quando o runbook realmente começa a ser executado.  

4. Quando o trabalho do runbook é concluído, sua saída é exibida. No seu caso, você deve ver *Olá, Mundo*.

   ![Saída do painel de teste](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  

5. Feche o Painel de teste para retornar à tela.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar o runbook

O runbook criado ainda está em modo de Rascunho. Deve ser publicado antes de poder ser executado na produção.  Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. No seu caso, ainda não há uma versão publicada porque você acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e em **Sim** quando solicitado.
1. Se você rolar para a esquerda para exibir o runbook no painel **Runbooks**, ele mostrará o **Status de Criação** **Publicado**.
1. Role para a direita para exibir o painel de **MyFirstRunbook-PowerShell**.  
   As opções na parte superior nos permitem iniciar o runbook, exibi-lo, agendá-lo para iniciar em algum momento no futuro ou criar um [webhook](automation-webhooks.md) para que ele possa ser iniciado por meio de uma chamada de HTTP.
1. Você quer iniciar o runbook, sendo assim, clique em **Iniciar** e em **OK** quando a página Iniciar Runbook for aberta.
1. Uma página de trabalho é aberto para o trabalho de runbook criado. Você pode fechar esse painel, mas neste caso, deixe-o averto para acompanhar o progresso do trabalho.
1. O status do trabalho é mostrado em **Resumo do trabalho** e corresponde aos status que você viu quando testou o runbook.

   ![Resumo do trabalho](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Assim que o status do runbook mostrar *Concluído*, em **Visão Geral** clique em **Saída**. O painel Saída é aberto e você pode ver seu *Olá, Mundo*.

   ![Saída do trabalho](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Feche a página de Saída.
1. Clique em **Todos os Logs** para abrir o painel Fluxos do trabalho do runbook. Você só deve ver *Olá, Mundo* no fluxo de saída, mas isso pode mostrar outros fluxos de um trabalho do runbook como Detalhado e Erro se o runbook gravar neles.

   ![Todos os Logs](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Feche a página Fluxos e a página Trabalho para retornar à página MyFirstRunbook-PowerShell.
1. Em **Detalhes**, clique em **Trabalhos** para abrir o painel de trabalhos para este runbook. Esta página lista todos os trabalhos criados por esse runbook. Você deve ver apenas um trabalho listado, já que executou o trabalho apenas uma vez.

   ![Lista de trabalhos](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  

1. Você pode clicar nesse trabalho para abrir o mesmo painel do Trabalho exibido ao iniciar o runbook. Esta ação permite que você volte no tempo e veja os detalhes de qualquer trabalho que foi criado para um determinado runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Adicionar a autenticação para gerenciar os recursos do Azure

Você testou e publicou seu runbook, mas, até o momento, ele não faz nada útil. Você gostaria que ele gerencie recursos do Azure. Não é possível fazer isso, embora a menos que você seja autenticado usando uma conexão Executar como que é criada automaticamente quando você cria sua conta de automação. Você usa a conexão executar como com o cmdlet **Connect-AzureRmAccount**. Se você estiver gerenciando recursos entre várias assinaturas, precisará usar o parâmetro **-AzureRmContext** junto com [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process
   
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. Abra o editor de texto clicando em **Editar** na página MyFirstRunbook-PowerShell.
1. Você não precisa da linha **Write-Output**, então vamos continuar e excluí-la.
1. Digite ou copie e cole o seguinte código que manipula a autenticação com sua conta de Automação Executar Como:

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** e **Login-AzureRmAccount** agora são aliases para **Connect-AzureRMAccount**. Se o **Connect-AzureRMAccount** cmdlet não existe, você pode usar **Add-AzureRmAccount** ou **Login-AzureRmAccount**, ou você pode atualizar os módulos na sua automação Conta para as versões mais recentes.

1. Clique em **Painel de teste** para que você possa testar o runbook.
1. Clique em **Iniciar** para iniciar o teste. Quando ele for concluído, você deverá receber uma saída semelhante ao exemplo a seguir, que exibe informações básicas de sua conta. Essa saída confirma que a conta Executar como é válida.

   ![Autenticar](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="add-code-to-start-a-virtual-machine"></a>Adicionar código para iniciar uma máquina virtual

Agora que o seu runbook está se autenticando na sua assinatura do Azure, você pode gerenciar os recursos. Você adiciona um comando para iniciar uma máquina virtual. Você pode escolher qualquer máquina virtual em sua assinatura do Azure e, por enquanto, você codifica esse nome no runbook.

1. Após o *Connect-AzureRmAccount*, digite *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'* fornecendo o nome e o nome do Grupo de Recursos da máquina virtual para iniciar.  

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Salve o runbook e, para que possa testá-lo, clique em **Painel de teste**.
1. Clique em **Iniciar** para iniciar o teste. Quando for concluído, verifique se a máquina virtual foi iniciada.

## <a name="add-an-input-parameter"></a>Adicionar um parâmetro de entrada

No momento, o seu runbook inicia a máquina virtual que você codificou no runbook, mas ele seria mais útil se você especificasse a máquina virtual quando o runbook é iniciado. Agora, você adiciona os parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Adicione parâmetros para *VMName* e *ResourceGroupName* ao runbook e use essas variáveis com o cmdlet **Start-AzureRmVM** como no exemplo a seguir.

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Salve o runbook e abra o Painel de teste. Agora você pode fornecer valores para as duas variáveis de entrada que são usadas no teste.
1. Feche o Painel de teste.
1. Clique em **Publicar** para publicar a nova versão do runbook.
1. Pare a máquina virtual que você iniciou na etapa anterior.
1. Clique em **OK** para iniciar o runbook. Digite o **VMName** e o **ResourceGroupName** da máquina virtual que você iniciará.<br><br> ![Passar parâmetro](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
1. Quando o runbook for concluído, verifique se a máquina virtual foi iniciada.

## <a name="differences-from-powershell-workflow"></a>Diferenças do fluxo de trabalho do PowerShell

Os runbooks do PowerShell têm o mesmo ciclo de vida, os mesmos recursos e o mesmo gerenciamento dos runbooks do fluxo de trabalho do PowerShell, mas há algumas diferenças e limitações:

1. Os runbooks do PowerShell são executados com mais rapidez em comparação aos runbooks do fluxo de trabalho do PowerShell, já que não têm a etapa de compilação.
2. Os runbooks do fluxo de trabalho do PowerShell dão suporte a pontos de verificação, ao uso de pontos de verificação e podem retomar de qualquer ponto no runbook. Os runbooks do PowerShell só podem retomar desde o início.
3. Os runbooks de fluxo de trabalho do PowerShell dão suporte a execução paralela e serial. Os runbooks do PowerShell só podem executar comandos em série.
4. Em um runbook de fluxo de trabalho do PowerShell, uma atividade, um comando ou um script de bloco pode ter seu próprio espaço de execução. Em um runbook do PowerShell, tudo em um script é executado em um único espaço de execução. Também há algumas [diferenças sintáticas](https://technet.microsoft.com/magazine/dn151046.aspx) entre um runbook nativo do PowerShell e um runbook do Fluxo de Trabalho do PowerShell.

## <a name="next-steps"></a>Próximas etapas

* Para começar a usar os runbooks Gráficos, consulte [Meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar a usar runbooks de fluxo de trabalho do PowerShell, veja [Meu primeiro runbook de Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre os tipos de runbook, suas vantagens e limitações, veja [Tipos de runbook da Automação do Azure](automation-runbook-types.md)
* Para saber mais sobre o recurso de suporte de script do PowerShell, veja [Native PowerShell script support in Azure Automation (Suporte a scripts nativos do PowerShell na Automação do Azure)](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
