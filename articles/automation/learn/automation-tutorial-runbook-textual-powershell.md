---
title: Criar um runbook do PowerShell na Automação do Azure
description: Este artigo ensina a criar, testar e publicar um runbook simples do PowerShell.
keywords: azure powershell, tutorial de script do powershell, automação do powershell
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: 0e5573634edf29698e7b118487fb2ec4e22e8eec
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169429"
---
# <a name="tutorial-create-a-powershell-runbook"></a>Tutorial: Criar runbook do PowerShell

Este tutorial orienta você durante a criação de um [Runbook do PowerShell](../automation-runbook-types.md#powershell-runbooks) na Automação do Azure. Os runbooks do PowerShell se baseiam no Windows PowerShell. Você pode editar o código do runbook diretamente usando o editor de texto do portal do Azure.

> [!div class="checklist"]
> * Criar um runbook simples do PowerShell
> * Testar e publicar o runbook
> * Executar e acompanhar o status do trabalho do runbook
> * Atualizar o runbook para iniciar uma máquina virtual do Azure com parâmetros de runbook

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](../automation-quickstart-create-account.md) para manter o runbook e se autenticar nos recursos do Azure. Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Já que você para e inicia essa máquina, portanto, ela não deve ser uma VM de produção.
* Se necessário, [importe módulos do Azure](../shared-resources/modules.md) ou [módulos de atualização](../automation-update-azure-modules.md) com base nos cmdlets que você usa.

## <a name="differences-from-powershell-workflow-runbooks"></a>Diferenças de runbooks de Fluxo de Trabalho do PowerShell

Os runbooks do PowerShell têm o mesmo ciclo de vida, os mesmos recursos e o mesmo gerenciamento dos runbooks do fluxo de trabalho do PowerShell. No entanto, há algumas limitações e diferenças entre eles.

| Característica  | Runbooks do PowerShell | Runbooks de Fluxo de Trabalho do PowerShell |
| ------ | ----- | ----- |
| Velocidade | Executam rápido, já que não usam uma etapa de compilação. | Executam mais lentamente. |
| Pontos de verificação | Não dão suporte a pontos de verificação. Um runbook do PowerShell só pode retomar a operação desde o início. | Usam checkpoints, que permitem que uma pasta de trabalho retome a operação de qualquer ponto. |
| Execução de comandos | Dão suporte apenas à execução serial. | Dão suporte à execução em série e à em paralelo.|
| Runspace | Um runspace individual executa tudo em um script. | Um runspace separado pode ser usado para uma atividade, um comando ou um bloco de script. |

Além dessas diferenças, os runbooks do PowerShell apresentam algumas [diferenças sintáticas](/previous-versions/technet-magazine/dn151046(v=msdn.10)) de runbooks de Fluxo de Trabalho do PowerShell.

## <a name="step-1---create-runbook"></a>Etapa 1: criar o runbook

Comece criando um runbook simples que exibe o texto `Hello World`.

1. No portal do Azure, abra sua conta da Automação.

2. Selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.

3. Crie um runbook selecionando **Criar um runbook**.

4. Atribua o nome **MyFirstRunbook-PowerShell** ao runbook.

5. Neste caso, você criará um [runbook do PowerShell](../automation-runbook-types.md#powershell-runbooks). Para o **Tipo de Runbook**, selecione **PowerShell**.

6. Clique em **criar** para criar o runbook e abrir o editor textual.

## <a name="step-2---add-code-to-the-runbook"></a>Etapa 2 - adicionar código ao runbook

Você pode digitar o código diretamente no runbook, ou você pode selecionar cmdlets, runbooks, e ativos do controle de biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Para este tutorial, você digitará o código diretamente no runbook.

1. Seu runbook está vazio no momento. Digite `Write-Output "Hello World"` no corpo do script.

   ![Olá, Mundo](../media/automation-tutorial-runbook-textual-powershell/automation-helloworld.png)

2. Salve o runbook clicando em **Salvar**.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a> Etapa 3 – testar o runbook

Antes de publicar o runbook para disponibilizá-lo na produção, você deve testá-lo para verificar se ele funciona corretamente. Ao testar um runbook, você executa sua versão de rascunho e pode ver a saída dele interativamente.

1. Clique em **Painel de teste** para abrir o Painel de teste.

2. Clique em **Iniciar** para iniciar o teste. Essa deve ser a única opção habilitada.

3. Observe que um [trabalho de runbook](../automation-runbook-execution.md) é criado e o status dele é exibido no painel.

   O status do trabalho inicia como Na fila, indicando que o trabalho está aguardando um trabalho de runbook na nuvem ficar disponível. O status é alterado para Iniciando quando um trabalho reivindica o trabalho. Por fim, o status se torna Em execução quando o runbook começa a ser executado.

4. Quando o trabalho do runbook é concluído, sua saída é exibida no painel Teste. Nesse caso, você verá `Hello World`.

   ![Saída do painel de teste](../media/automation-tutorial-runbook-textual-powershell/automation-testpane-output.png)

5. Feche o Painel de teste para retornar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Etapa 4: publicar e iniciar o runbook

O runbook que você criou ainda está em modo de Rascunho. Ele precisa ser publicado antes de poder ser executado na produção. Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Nesse caso, ainda não há uma versão publicada porque você acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e em **Sim** quando solicitado.

2. Role para a esquerda para exibir o runbook na página Runbooks e observe que o valor de **Status de Criação** está definido para **Publicado**.

3. Role para a direita para exibir a página **MyFirstRunbook-PowerShell**.
   
   As opções na parte superior permitem a você iniciar o runbook imediatamente, agendá-lo para iniciar em algum momento no futuro ou criar um [webhook](../automation-webhooks.md) para que ele possa ser iniciado por meio de uma chamada HTTP.

4. Selecione **Iniciar** e em **Sim** quando solicitado para iniciar o runbook. 

5. Um painel Trabalho é aberto para o trabalho de runbook criado. Embora você possa fechar esse painel, mas neste caso, deixe-o aberto para acompanhar o progresso do trabalho. O status do trabalho é mostrado em **Resumo do Trabalho** e os status possíveis são descritos para testar o runbook.

   ![Resumo do trabalho](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

6. Quando o status do runbook mostrar Concluído, clique em **Saída** para abrir a página Saída, onde você pode ver `Hello World` exibido.

   ![Saída do trabalho](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

7. Feche a página de Saída.

8. Clique em **Todos os Logs** para abrir o painel Fluxos do trabalho do runbook. Você deverá ver apenas `Hello World` no fluxo de saída.

    Observe que o painel Fluxos poderá mostrar outros fluxos de um trabalho de runbook, como Detalhado e Erro, se o runbook gravar neles.

   ![Todos os Logs](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

9. Feche o painel Fluxos e o painel Trabalho para retornar à página MyFirstRunbook-PowerShell.

10. Em **Detalhes**, clique em **Trabalhos** para abrir o painel Trabalhos para este runbook. Esta página lista todos os trabalhos criados pelo seu runbook. Você deve ver apenas um trabalho listado, já que você executou o trabalho apenas uma vez.

   ![Lista de trabalhos](../media/automation-tutorial-runbook-textual-powershell/runbook-control-job-tile.png)

11. Clique no nome do trabalho para abrir o mesmo painel Trabalho que você viu ao iniciar o runbook. Use esse painel para exibir os detalhes de qualquer trabalho criado para o runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Etapa 5: adicionar autenticação para gerenciar recursos do Azure

Você testou e publicou seu runbook, mas, até o momento, ele não faz nada útil. Você gostaria que ele gerencie recursos do Azure. Para fazer isso, o runbook deve ser capaz de autenticar usando a conta Executar Como que foi criada automaticamente quando você criou sua conta de Automação.

Conforme mostrado no exemplo abaixo, a conexão Executar Como é feita com o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Se você estiver gerenciando recursos entre várias assinaturas, precisará usar o parâmetro `AzContext` junto com [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext).

> [!NOTE]
> Para runbooks do PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` são aliases para `Connect-AzAccount`. Você pode usar esses cmdlets ou pode [atualizar seus módulos](../automation-update-azure-modules.md) em sua conta de Automação para as versões mais recentes. Talvez você precise atualizar os módulos mesmo que você tenha acabado de criar uma conta de Automação.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```

1. Abra o editor de texto clicando em **Editar** na página MyFirstRunbook-PowerShell.

2. Você não precisa mais da linha `Write-Output`. Basta excluí-la.

3. Digite ou copie e cole o código a seguir, que manipula a autenticação com sua conta de Automação Executar Como.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

4. Clique em **Painel de teste** para que você possa testar o runbook.

5. Clique em **Iniciar** para iniciar o teste. Quando ele for concluído, você deverá ver uma saída semelhante ao exemplo a seguir, que exibe informações básicas de sua conta. Essa saída confirma que a conta Executar como é válida.

   ![Authenticate](../media/automation-tutorial-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Etapa 6: adicionar código para iniciar uma máquina virtual

Agora que o seu runbook está se autenticando na sua assinatura do Azure, você pode gerenciar os recursos. Vamos adicionar um comando para iniciar uma máquina virtual. Você pode escolher qualquer máquina virtual em sua assinatura do Azure e apenas codificar esse nome no runbook.

1. Para o script de runbook, adicione o cmdlet [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) para iniciar a máquina virtual. Como mostrado abaixo, o cmdlet inicia uma máquina virtual com o nome `VMName` e com um grupo de recursos denominado `ResourceGroupName`.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

2. Salve o runbook e, para que possa testá-lo, clique em **Painel de teste**.

3. Clique em **Iniciar** para iniciar o teste. Quando for concluído, verifique se a máquina virtual foi iniciada.

## <a name="step-7---add-an-input-parameter"></a>Etapa 7 – Adicionar um parâmetro de entrada

Atualmente, o runbook inicia a máquina virtual que você codificou no runbook. O runbook será mais útil se você especificar a máquina virtual quando o runbook for iniciado. Vamos adicionar os parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. No editor de texto, modifique o cmdlet `Start-AzVM` para usar variáveis para os parâmetros `VMName` e `ResourceGroupName`. 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

2. Salve o runbook e abra o Painel de teste. Agora você pode fornecer valores para as duas variáveis de entrada usadas no teste.

3. Feche o Painel de teste.

4. Clique em **Publicar** para publicar a nova versão do runbook.

5. Pare a máquina virtual que você iniciou anteriormente.

6. Clique em **Iniciar** para iniciar o runbook. 

7. Digite os valores para **VMNAME** e **RESOURCEGROUPNAME** para a máquina virtual que você iniciará e clique em **OK**.

    ![Passar Parâmetro](../media/automation-tutorial-runbook-textual-powershell/automation-pass-params.png)

8. Quando o runbook for concluído, verifique se a máquina virtual foi iniciada.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, confira a [Documentação do PowerShell](/powershell/scripting/overview).
* Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation).
* Para começar a usar os runbooks gráficos, confira [Criar um runbook gráfico](automation-tutorial-runbook-graphical.md).
* Para começar a usar runbooks de Fluxo de Trabalho do PowerShell, confira [Criar um runbook de Fluxo de Trabalho do PowerShell](automation-tutorial-runbook-textual.md).
* Para saber mais sobre os tipos de runbook, suas vantagens e limitações, confira [Tipos de runbook da Automação do Azure](../automation-runbook-types.md).
* Para saber mais sobre o recurso de suporte de script do PowerShell, confira [Native PowerShell script support in Azure Automation (Suporte a scripts nativos do PowerShell na Automação do Azure)](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
