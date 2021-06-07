---
title: Criar um runbook de Fluxo de Trabalho do PowerShell na Automação do Azure
description: Este artigo ensina a criar, testar e publicar um runbook simples do Fluxo de Trabalho do PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: e0247d1d113f7fb0bf23116b3473f73b78a58c80
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167593"
---
# <a name="tutorial-create-a-powershell-workflow-runbook"></a>Tutorial: Criar runbook de Fluxo de Trabalho do PowerShell

Este tutorial orienta você durante a criação de um [runbook de Fluxo de Trabalho do PowerShell](../automation-runbook-types.md#powershell-workflow-runbooks) na Automação do Azure. Os runbooks do Fluxo de Trabalho do PowerShell são runbooks de texto baseados no Fluxo de Trabalho do Windows PowerShell. Você pode criar e editar o código do runbook diretamente usando o editor de texto do portal do Azure. 

> [!div class="checklist"]
> * Criar um runbook de Fluxo de Trabalho simples do PowerShell
> * Testar e publicar o runbook
> * Executar e acompanhar o status do trabalho do runbook
> * Atualizar o runbook para iniciar uma máquina virtual do Azure com parâmetros de runbook

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](../index.yml) para manter o runbook e se autenticar nos recursos do Azure. Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Já que você para e inicia essa máquina, portanto, ela não deve ser uma VM de produção.

## <a name="step-1---create-new-runbook"></a>Etapa 1: criar o novo runbook

Comece criando um runbook simples que exibe o texto `Hello World`.

1. No portal do Azure, abra sua conta da Automação.

   A página da conta de Automação fornece uma exibição rápida dos recursos nessa conta. Você já deve ter alguns ativos. A maioria desses ativos é de módulos incluídos automaticamente em uma nova conta de Automação. Você também deve ter o ativo de credencial associado à sua assinatura.
 
2. Selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.

3. Crie um runbook selecionando **Criar um runbook**.

4. Atribua o nome **MyFirstRunbook-Workflow** ao runbook.

5. Nesse caso, você vai criar um [runbook de Fluxo de Trabalho do PowerShell](../automation-runbook-types.md#powershell-workflow-runbooks). Para **Tipo de runbook**, selecione **Fluxo de Trabalho do PowerShell**.

6. Clique em **criar** para criar o runbook e abrir o editor textual.

## <a name="step-2---add-code-to-the-runbook"></a>Etapa 2 - adicionar código ao runbook

Você pode digitar o código diretamente no runbook ou selecionar cmdlets, runbooks e ativos do controle de biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Para este tutorial, você digita o código diretamente no runbook.

1. No momento, seu runbook está vazio, com apenas a palavra-chave `Workflow` necessária, o nome do seu runbook e as chaves que encerrarão o fluxo de trabalho inteiro.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

2. Digite `Write-Output "Hello World"` entre as chaves.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

3. Salve o runbook clicando em **Salvar**.

## <a name="step-3---test-the-runbook"></a>Etapa 3: testar o runbook

Antes de publicar o runbook para disponibilizá-lo na produção, você deve testá-lo para verificar se ele funciona corretamente. Ao testar um runbook, você executa sua versão de rascunho e pode ver a saída dele interativamente.

1. Selecione **Painel Teste** para abrir o painel Teste.

2. Para iniciar o teste, clique em **Iniciar**, que é a única opção habilitada.

3. Observe que um [trabalho de runbook](../automation-runbook-execution.md) é criado e o status dele é exibido no painel.

   O status do trabalho inicia como Na fila, indicando que o trabalho está aguardando um trabalho de runbook na nuvem ficar disponível. O status é alterado para Iniciando quando um trabalho reivindica o trabalho. Por fim, o status se torna Em execução quando o runbook começa a ser executado.

4. Quando o trabalho do runbook é concluído, sua saída é exibida no painel Teste. Nesse caso, você verá `Hello World`.

   ![Olá, Mundo](../media/automation-tutorial-runbook-textual/test-output-hello-world.png)

5. Feche o Painel de teste para retornar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Etapa 4: publicar e iniciar o runbook

O runbook que você criou ainda está em modo de Rascunho. Você precisa publicá-lo antes que possa executá-lo na produção. Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Nesse caso, ainda não há uma versão publicada porque você acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e em **Sim** quando solicitado.

2. Role para a esquerda para exibir o runbook na página **Runbooks** e observe que o campo **Status de Criação** está definido para **Publicado**.

3. Role de volta para a direita para exibir a página de **MyFirstRunbook-Workflow**.

   As opções na parte superior permitem a você iniciar o runbook imediatamente, agendá-lo para iniciar em algum momento no futuro ou criar um [webhook](../automation-webhooks.md) para que ele possa ser iniciado por meio de uma chamada HTTP.

4. Selecione **Iniciar** e em **Sim** quando solicitado para iniciar o runbook.

   ![Iniciar runbook](../media/automation-tutorial-runbook-textual/automation-runbook-controls-start.png)

5. Um painel Trabalho é aberto para o trabalho de runbook criado. Nesse caso, deixe o painel aberto para que você possa ver o progresso do trabalho.

6. Observe que o status do trabalho é mostrado em **Resumo do Trabalho**. Esse status corresponde aos status que você viu ao testar o runbook.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-jobsummary.png" alt-text="Captura de tela do painel Trabalho do runbook, mostrando a seção Resumo do Trabalho realçada.":::

7. Assim que o status do runbook mostrar Concluído, clique em **Saída**. A página Saída é aberta e nela você pode ver sua mensagem de `Hello World`.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-outputtile.png" alt-text="Captura de tela do painel Trabalho do runbook, mostrando o botão Saída realçado.":::

8. Feche a página de Saída.

9. Clique em **Todos os Logs** para abrir o painel Fluxos do trabalho do runbook. Você deverá ver apenas `Hello World` no fluxo de saída. Observe que o painel Fluxos poderá mostrar outros fluxos de um trabalho de runbook, como Detalhado e Erro, se o runbook gravar neles.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-alllogstile.png" alt-text="Captura de tela do painel Trabalho do runbook, mostrando o botão Todos os Logs realçado.":::

10. Feche o painel Fluxos e o painel Trabalho para retornar à página MyFirstRunbook.

11. Em **Recursos**, clique em **Trabalhos** para abrir o painel Trabalhos para este runbook. Esta página lista todos os trabalhos criados pelo seu runbook. Você deve ver apenas um trabalho listado, já que você executou o trabalho apenas uma vez.

   ![Trabalhos](../media/automation-tutorial-runbook-textual/runbook-control-job-tile.png)

12. Clique no nome do trabalho para abrir o mesmo painel Trabalho que você viu ao iniciar o runbook. Use esse painel para exibir os detalhes de qualquer trabalho criado para o runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Etapa 5: adicionar autenticação para gerenciar recursos do Azure

Você testou e publicou seu runbook, mas, até o momento, ele não faz nada útil. Você gostaria que ele gerencie recursos do Azure. Ele não pode fazer isso, a menos que seja autenticado usando as credenciais da assinatura. A autenticação usa o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

>[!NOTE]
>Para runbooks do PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` são aliases para `Connect-AzAccount`. Você pode usar esses cmdlets ou pode [atualizar seus módulos](../automation-update-azure-modules.md) em sua conta de Automação para as versões mais recentes. Talvez você precise atualizar os módulos mesmo que você tenha acabado de criar uma conta de Automação.

1. Navegue até a página MyFirstRunbook-Workflow e abra o editor de texto clicando em **Editar**.

2. Exclua a linha `Write-Output`.

3. Posicione o cursor em uma linha em branco entre as chaves.

4. Digite ou copie e cole o código a seguir, que manipula a autenticação com sua conta de Automação Executar Como.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

5. Clique em **Painel de teste** para que você possa testar o runbook.

6. Clique em **Iniciar** para iniciar o teste. Quando ele for concluído, você deverá ver uma saída semelhante ao exemplo a seguir, que exibe informações básicas de sua conta. Essa ação confirma que a credencial é válida.

   ![Authenticate](../media/automation-tutorial-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Etapa 6: adicionar código para iniciar uma máquina virtual

Agora que o seu runbook está se autenticando na sua assinatura do Azure, você pode gerenciar os recursos. Vamos adicionar um comando para iniciar uma máquina virtual. Você pode escolher qualquer VM em sua assinatura do Azure e, por enquanto, codificará esse nome no runbook. Se você estiver gerenciando recursos entre várias assinaturas, precisará usar o parâmetro `AzContext` junto com o cmdlet [Get-AzContext](/powershell/module/az.accounts/get-azcontext).

1. Forneça o nome e o nome do grupo de recursos da VM a ser iniciada inserindo uma chamada para o cmdlet [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM), conforme mostrado abaixo. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

2. Salve o runbook e, para que possa testá-lo, clique em **Painel de teste**.

3. Clique em **Iniciar** para iniciar o teste. Quando for concluído, verifique se a VM foi iniciada.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Etapa 7: adicionar um parâmetro de entrada ao runbook

Atualmente, o runbook inicia a VM que você codificou no runbook. Ele será mais útil se você puder especificar a VM quando o runbook for iniciado. Vamos adicionar os parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Adicione variáveis para os parâmetros `VMName` e `ResourceGroupName` ao runbook e use as variáveis com o cmdlet `Start-AzVM`, conforme mostrado abaixo.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Salve o runbook e abra o Painel de teste. Agora você pode fornecer valores para as duas variáveis de entrada que estão no teste.

3. Feche o Painel de teste.

4. Clique em **Publicar** para publicar a nova versão do runbook.

5. Pare a VM que você iniciou.

6. Clique em **Iniciar** para iniciar o runbook. 

7. Digite os valores para **VMNAME** e **RESOURCEGROUPNAME** da VM que você iniciará.

   ![Iniciar Runbook](../media/automation-tutorial-runbook-textual/automation-pass-params.png)

8. Quando o runbook for concluído, verifique se a VM foi iniciada.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, confira a [Documentação do PowerShell](/powershell/scripting/overview).
* Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation).
* Para começar a usar os runbooks gráficos, confira [Criar um runbook gráfico](automation-tutorial-runbook-graphical.md).
* Para começar a usar os runbooks do PowerShell, confira [Criar um runbook do PowerShell](automation-tutorial-runbook-textual-powershell.md).
* Para aprender mais sobre os tipos de runbook, suas vantagens e limitações, confira [Tipos de runbook da Automação do Azure](../automation-runbook-types.md).
* Para obter mais informações sobre o recurso de suporte de script do PowerShell, confira [Suporte a scripts nativos do PowerShell na Automação do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
