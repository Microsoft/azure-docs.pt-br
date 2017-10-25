---
title: "Meu primeiro runbook do PowerShell na Automação do Azure | Microsoft Docs"
description: "Tutorial que orienta você pela criação, pelos testes e pela publicação de um runbook simples do PowerShell."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
keywords: "azure powershell, tutorial de script do powershell, automação do powershell"
ms.assetid: a43b395a-e740-41a3-ae62-40eac9d0ec00
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/31/2017
ms.author: magoedte;sngun
ms.openlocfilehash: 2645d149df1f337700fb627aaff99e869fc8b8e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="my-first-powershell-runbook"></a>Meu primeiro runbook do PowerShell

> [!div class="op_single_selector"]
> * [Gráfico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Este tutorial orienta você durante a criação de um [Runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) na Automação do Azure. Começaremos com um runbook simples, que testaremos e publicaremos enquanto explicamos como acompanhar o status do trabalho do runbook. Em seguida, modificamos o runbook para gerenciar os recursos do Azure, neste caso, iniciando uma máquina virtual do Azure. Depois, tornaremos o runbook mais robusto adicionando parâmetros de runbook.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará do seguinte:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para manter o runbook e se autenticar nos recursos do Azure.  Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Paramos e iniciamos o computador, portanto, ele não deve ser uma VM de produção.

## <a name="step-1---create-new-runbook"></a>Etapa 1: criar o novo runbook
Começaremos criando um runbook simples que exibe o texto *Olá mundo*.

1. No portal do Azure, abra sua conta da Automação.  
   A página da conta de Automação fornece uma exibição rápida dos recursos nessa conta. Você já deve ter alguns ativos. A maioria deles são os módulos que são incluídos automaticamente em uma nova conta de Automação. Você também deve ter o ativo de credencial que é mencionado nos [pré-requisitos](#prerequisites).
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.<br><br> ![RunbooksControl](media/automation-first-runbook-textual-powershell/runbooks-control-tiles.png)  
3. Crie um novo runbook clicando no botão **Adicionar um runbook** e em **Criar um novo runbook**.
4. Atribua o nome *MyFirstRunbook-PowerShell*ao runbook.
5. Neste caso, vamos criar um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks), portanto, escolha **PowerShell** como o **Tipo de runbook**.<br><br> ![Runbook Type](media/automation-first-runbook-textual-powershell/automation-runbook-type.png)  
6. Clique em **criar** para criar o runbook e abrir o editor textual.

## <a name="step-2---add-code-to-the-runbook"></a>Etapa 2 - adicionar código ao runbook
Você pode digitar o código diretamente no runbook, ou você pode selecionar cmdlets, runbooks, e ativos do controle de biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Para este passo a passo, digitaremos diretamente no runbook.

1. Nosso runbook está vazio no momento. Digite *Write-Output "Hello World”.* no corpo do script.<br><br> ![Olá mundo](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. Salve o runbook clicando em **Salvar**.<br><br> ![Botão Salvar](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-save.png)  

## <a name="step-3---test-the-runbook"></a>Etapa 3: testar o runbook
Antes que publicamos o runbook para disponibilizá-lo na produção, queremos testá-lo para garantir que ele funciona corretamente. Quando você testa um runbook, executa sua versão de **Rascunho** e vê sua saída interativamente.

1. Clique em **Painel de teste** para abrir o Painel de teste.<br><br> ![Test Pane](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-test.png)  
2. Clique em **Iniciar** para iniciar o teste. Essa deve ser a única opção habilitada.
3. Um [trabalho de runbook](automation-runbook-execution.md) é criado e seu status é exibido.  
   O status do trabalho inicia como *Na fila* , indicando que ele está aguardando um trabalho de runbook na nuvem ficar disponível. Mudará para *Iniciando* quando um trabalhador reivindicar o trabalho, em seguida, para *Executando* quando o runbook realmente começar a ser executado.  
4. Quando o trabalho do runbook é concluído, sua saída é exibida. Em nosso caso, deveremos ver *Olá mundo*.<br><br> ![Saída do painel de teste](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
5. Feche o Painel de teste para retornar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Etapa 4: publicar e iniciar o runbook
O runbook que criamos ainda está em Modo de rascunho. Precisamos publicá-lo antes que possamos executá-lo na produção.  Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho.  Em nosso caso, não temos uma versão Publicada ainda porque que acabamos de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e em **Sim** quando solicitado.<br><br> ![Botão Publicar](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-publish.png)  
2. Se você rolar para a esquerda para exibir o runbook no painel **Runbooks** agora, ele mostrará um **Status de Criação** de **Publicado**.
3. Role para a direita para exibir o painel de **MyFirstRunbook-PowerShell**.  
   As opções na parte superior nos permitem iniciar o runbook, exibi-lo, agendá-lo para iniciar em algum momento no futuro ou criar um [webhook](automation-webhooks.md) para que ele possa ser iniciado por meio de uma chamada de HTTP.
4. Nós queremos iniciar o runbook, sendo assim, clique em **Iniciar** e em **OK** quando a folha Iniciar Runbook for aberta.<br><br> ![Botão Iniciar](media/automation-first-runbook-textual-powershell/automation-runbook-controls-start.png)<br>    
5. Um painel de trabalho é aberto para o trabalho de runbook criado. Podemos fechar esse painel, mas nesse caso, o deixaremos aberto para que possamos acompanhar o progresso do trabalho.
6. O status do trabalho é mostrado em **Resumo do trabalho** e corresponde aos status que vimos quando testamos o runbook.<br><br> ![Resumo do trabalho](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)<br>  
7. Assim que o status do runbook mostrar *Concluído*, clique em **Saída**. O painel Saída é aberto e podemos ver nosso *Olá Mundo*.<br><br> ![Saída do trabalho](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)<br> 
8. Feche o painel Saída.
9. Clique em **Todos os Logs** para abrir o painel Fluxos do trabalho do runbook. Devemos ver apenas *Olá Mundo* no fluxo de saída, mas isso pode mostrar outros fluxos de um trabalho do runbook como Detalhado e Erro se o runbook gravar neles.<br><br> ![Todos os Logs](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)<br>   
10. Feche o painel Fluxos e o painel Trabalho para retornar ao painel MyFirstRunbook-PowerShell.
11. Clique em **Trabalhos** para abrir o painel de trabalhos para este runbook. Ele lista todos os trabalhos criados por esse runbook. Devemos ver apenas um trabalho listado, já que executamos o trabalho apenas uma vez.<br><br> ![Lista de trabalhos](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  
12. Você pode clicar neste trabalho para abrir o mesmo painel Trabalho que exibimos quando iniciamos o runbook. Isso permite que você volte no tempo e veja os detalhes de qualquer trabalho que foi criado para um determinado runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Etapa 5: adicionar autenticação para gerenciar recursos do Azure
Testamos e publicamos nosso runbook, mas até o momento ele não faz nada útil. Gostaríamos que ele gerenciasse recursos do Azure. No entanto, ele não será capaz de fazer isso a menos que o autentiquemos usando as credenciais mencionadas nos [pré-requisitos](#prerequisites). Fazemos isso com o cmdlet **Add-AzureRmAccount** .

1. Abra o editor de texto clicando em **Editar** no painel MyFirstRunbook-PowerShell.<br><br> ![Editar runbook](media/automation-first-runbook-textual-powershell/automation-runbook-controls-edit.png)<br>   
2. Já não precisamos da linha **Write-Output** , então vamos continuar e excluí-la.
3. Digite ou copie e cole o seguinte código que manipula a autenticação com sua conta de Automação Executar Como:
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
   <br>
4. Clique no **Painel de teste** para que possamos testar o runbook.
5. Clique em **Iniciar** para iniciar o teste. Quando ele for concluído, você deverá receber uma saída semelhante ao exemplo a seguir, que exibe informações básicas de sua conta. Isso confirma que a credencial é válida.<br><br> ![Autenticar](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Etapa 6: adicionar código para iniciar uma máquina virtual
Agora que nosso runbook está se autenticando em nossa assinatura do Azure, podemos gerenciar recursos. Adicionamos um comando para iniciar uma máquina virtual. Você pode escolher qualquer máquina virtual em sua assinatura do Azure e, por enquanto, codificaremos esse nome no runbook.

1. Depois de *Add-AzureRmAccount*, digite *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'*, fornecendo o nome da máquina virtual a iniciar e o nome do Grupo de Recursos dessa máquina virtual.  
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```
   <br>
2. Salve o runbook e, para que possamos testá-lo, clique em **Painel de teste** .
3. Clique em **Iniciar** para iniciar o teste. Quando for concluído, verifique se a máquina virtual foi iniciada.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Etapa 7: adicionar um parâmetro de entrada ao runbook
No momento, nosso runbook inicia a máquina virtual que codificamos no runbook, mas ele seria mais útil se especificássemos a máquina virtual quando o runbook é iniciado. Agora adicionaremos parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Adicione parâmetros para *VMName* e *ResourceGroupName* ao runbook e use essas variáveis com o cmdlet **Start-AzureRmVM** como no exemplo abaixo.  
   
   ```
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```
   <br>
2. Salve o runbook e abra o Painel de teste. Agora você pode fornecer valores para as duas variáveis de entrada que são usadas no teste.
3. Feche o Painel de teste.
4. Clique em **Publicar** para publicar a nova versão do runbook.
5. Pare a máquina virtual que você iniciou na etapa anterior.
6. Clique em **Iniciar** para iniciar o runbook. Digite o **VMName** e o **ResourceGroupName** da máquina virtual que você iniciará.<br><br> ![Passar parâmetro](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
7. Quando o runbook for concluído, verifique se a máquina virtual foi iniciada.

## <a name="differences-from-powershell-workflow"></a>Diferenças do fluxo de trabalho do PowerShell
Os runbooks do PowerShell têm o mesmo ciclo de vida, os mesmos recursos e o mesmo gerenciamento dos runbooks do fluxo de trabalho do PowerShell, mas há algumas diferenças e limitações:

1. Os runbooks do PowerShell são executados com mais rapidez em comparação aos runbooks do fluxo de trabalho do PowerShell, já que não têm a etapa de compilação.
2. Os runbooks do fluxo de trabalho do PowerShell dão suporte a pontos de verificação, ao uso de pontos de verificação e podem retomar de qualquer ponto no runbook, enquanto os runbooks do PowerShell só podem retomar desde o início.
3. Os runbooks do fluxo de trabalho do PowerShell dão suporte à execução em paralelo e serial, enquanto os runbooks do PowerShell só podem executar comandos de forma serial.
4. Em um runbook do fluxo de trabalho do PowerShell, uma atividade, um comando ou um bloco de script pode ter seu próprio espaço de execução, enquanto em um runbook do PowerShell, tudo em um script é executado em um único espaço de execução. Também há algumas [diferenças sintáticas](https://technet.microsoft.com/magazine/dn151046.aspx) entre um runbook nativo do PowerShell e um runbook do Fluxo de Trabalho do PowerShell.

## <a name="next-steps"></a>Próximas etapas
* Para começar a usar runbooks gráficos, veja [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar a usar runbooks de fluxo de trabalho do PowerShell, veja [Meu primeiro runbook de Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre os tipos de runbook, suas vantagens e limitações, veja [Tipos de runbook da Automação do Azure](automation-runbook-types.md)
* Para saber mais sobre o recurso de suporte de script do PowerShell, veja [Native PowerShell script support in Azure Automation (Suporte a scripts nativos do PowerShell na Automação do Azure)](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

