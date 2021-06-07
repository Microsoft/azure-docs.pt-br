---
title: Criar um runbook gráfico na Automação do Azure
description: Este artigo ensina a criar, testar e publicar um runbook gráfico simples na Automação do Azure.
keywords: runbook, modelo de runbook, automação de runbook, runbook do azure
services: automation
ms.subservice: process-automation
ms.date: 09/15/2020
ms.topic: tutorial
ms.openlocfilehash: 1b6c02778b0ee790d81c713283e653058c29c153
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92899792"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Tutorial: Criar runbook gráfico

Este tutorial orienta você durante a criação de um [runbook gráfico](../automation-runbook-types.md#graphical-runbooks) na Automação do Azure. Você pode criar e editar runbooks gráficos e runbooks gráficos de Fluxo de Trabalho do PowerShell usando o editor gráfico no portal do Azure. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um runbook gráfico simples
> * Testar e publicar o runbook
> * Executar e acompanhar o status do trabalho do runbook
> * Atualizar o runbook para iniciar uma máquina virtual do Azure com parâmetros de runbook e links condicionais

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free).
* [Conta de automação](../index.yml) para manter o runbook e se autenticar nos recursos do Azure. Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Já que você para e inicia essa máquina, portanto, ela não deve ser uma VM de produção.
* Se necessário, [importe módulos do Azure](../shared-resources/modules.md) ou [módulos de atualização](../automation-update-azure-modules.md) com base nos cmdlets que você usa.

## <a name="step-1---create-runbook"></a>Etapa 1: criar o runbook

Comece criando um runbook simples que exibe o texto `Hello World`.

1. No portal do Azure, abra sua conta da Automação.

    A página da conta de Automação fornece uma exibição rápida dos recursos nessa conta. Você já deve ter alguns ativos. A maioria desses ativos é de módulos incluídos automaticamente em uma nova conta de Automação. Você também deve ter o ativo de credencial associado à sua assinatura.

2. Selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.

3. Crie um runbook selecionando **Criar um runbook**.

4. Atribua o nome **MyFirstRunbook-Graphical** ao runbook.

5. Neste caso, você criará um [runbook gráfico](../automation-graphical-authoring-intro.md). Selecione **Gráfico** para **Tipo de runbook**.

    ![Novo runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)

6. Clique em **criar** para criar o runbook e abrir o editor gráfico.

## <a name="step-2---add-activities"></a>Etapa 2 – Adicionar atividades

O controle Biblioteca no lado esquerdo do editor permite que você selecione as atividades para adicionar ao runbook. Você vai adicionar um cmdlet `Write-Output` para exibir o texto proveniente do runbook.

1. No controle Biblioteca, clique no campo de pesquisa e digite `write-output`. Os resultados da pesquisa são mostrados na imagem a seguir.

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. Role até o final da lista. Clique com o botão direito do mouse em **Write-Output** e selecione **Adicionar à tela**. Como alternativa, você pode clicar nas reticências (...) ao lado do nome do cmdlet e, em seguida, selecionar **Adicionar à tela**.

3. Clique na atividade **Write-Output** na tela. Esta ação abrirá a página Controle da configuração, que permite configurar a atividade.

4. O campo **Rótulo** usa por padrão o nome do cmdlet, mas você pode alterá-lo para algo mais familiar. Altere-o para `Write Hello World to output`.

5. Clique em **Parâmetros** para fornecer valores para os parâmetros do cmdlet.

   Alguns cmdlets têm vários conjuntos de parâmetros e você precisa selecionar o que será usado. Nesse caso, `Write-Output` tem apenas um conjunto de parâmetros.

6. Selecione o parâmetro `InputObject`. Esse é o parâmetro que você usa para especificar o texto a ser enviado ao fluxo de saída.

7. O menu suspenso **Fonte de dados** fornece fontes que podem ser usadas para popular um valor de parâmetro. Nesse menu, selecione **Expressão do PowerShell**.

   Você pode usar a saída dessas fontes como outra atividade, um ativo de Automação ou uma expressão do PowerShell. Neste caso, a saída é apenas `Hello World`. Você pode usar uma expressão do PowerShell e especificar uma cadeia de caracteres.

8. Na caixa **Expressão**, digite `"Hello World"` e, em seguida, clique em **OK** duas vezes para retornar à tela.

9. Salve o runbook clicando em **Salvar**.

## <a name="step-3---test-the-runbook"></a>Etapa 3: testar o runbook

Antes de publicar o runbook para disponibilizá-lo na produção, você deve testá-lo para verificar se ele funciona corretamente. Ao testar um runbook, você executa sua versão de rascunho e pode ver a saída dele interativamente.

1. Selecione **Painel Teste** para abrir o painel Teste.

2. Clique em **Iniciar** para iniciar o teste. Essa deve ser a única opção habilitada.

3. Observe que um [trabalho de runbook](../automation-runbook-execution.md) é criado e o status dele é exibido no painel.

   O status do trabalho inicia como `Queued`, indicando que o trabalho está aguardando um trabalho de runbook na nuvem ficar disponível. O status é alterado para `Starting` quando um trabalho (worker) reivindica o trabalho. Por fim, o status se torna `Running` quando o runbook começa a ser executado.

4. Quando o trabalho do runbook é concluído, sua saída é exibida no painel Teste. Nesse caso, você verá `Hello World`.

    ![Saída Olá, Mundo do runbook](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. Feche o Painel de teste para retornar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Etapa 4: publicar e iniciar o runbook

O runbook que você criou ainda está em modo de Rascunho. Ele precisa ser publicado antes de poder ser executado na produção. Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Nesse caso, ainda não há uma versão publicada porque você acabou de criar o runbook.

1. Selecione **Publicar** para publicar o runbook e em **Sim** quando solicitado.

2. Role para a esquerda para exibir o runbook na página Runbooks e observe que o valor de **Status de Criação** está definido para **Publicado**.

3. Role de volta para a direita para visualizar a página para **MyFirstRunbook-Graphical**.

   As opções na parte superior permitem a você iniciar o runbook imediatamente, agendá-lo para iniciar em algum momento no futuro ou criar um [webhook](../automation-webhooks.md) para que ele possa ser iniciado por meio de uma chamada HTTP.

4. Selecione **Iniciar** e em **Sim** quando solicitado para iniciar o runbook.

5. Um painel Trabalho é aberto para o trabalho de runbook criado. Verifique se o campo **Status de trabalho** mostra **Completo**.

6. Clique em **Saída** para abrir a página Saída, em que você pode ver `Hello World` exibido.

7. Feche a página de Saída.

8. Clique em **Todos os Logs** para abrir o painel Fluxos do trabalho do runbook. Você deverá ver apenas `Hello World` no fluxo de saída.

    Observe que o painel Fluxos poderá mostrar outros fluxos de um trabalho de runbook, como Detalhado e Erro, se o runbook gravar neles.

9. Feche os painéis Fluxos e Trabalho para retornar à página MyFirstRunbook-Graphical.

10. Para exibir todos os trabalhos do runbook, selecione **Trabalhos** em **Recursos**. A página Trabalhos lista todos os trabalhos criados pelo seu runbook. Você deve ver apenas um trabalho listado, já que você executou o trabalho apenas uma vez.

11. Clique no nome do trabalho para abrir o mesmo painel Trabalho que você viu ao iniciar o runbook. Use esse painel para exibir os detalhes de qualquer trabalho criado para o runbook.

## <a name="step-5---create-variable-assets"></a>Etapa 5: criar ativos de variável

Você testou e publicou seu runbook, mas, até o momento, ele não faz nada útil para gerenciar recursos do Azure. Antes de configurar o runbook a ser autenticado, você precisa criar uma variável para conter a ID da assinatura, configurar uma atividade para autenticar e, em seguida, referenciar a variável. A inclusão de uma referência no contexto da assinatura facilita o seu trabalho com as várias assinaturas.

1. Copie sua ID de assinatura na opção **Assinaturas** no Painel de navegação.

2. Na página de Contas de Automação, selecione **Variáveis** em **Recursos Compartilhados**.

3. Selecione **Adicionar uma variável**.

4. Na página Nova variável, realize as configurações a seguir nos campos fornecidos.

    * **Nome** – digite `AzureSubscriptionId`.
    * **Valor** – insira sua ID da assinatura.
    * **Tipo** – mantenha a cadeia de caracteres selecionada.
    * **Criptografia** – use o valor padrão.

5. Clique em **Criar** para criar a variável.

## <a name="step-6---add-authentication"></a>Etapa 6 – Adicionar a autenticação

Agora que você tem uma variável para manter a ID da assinatura, você pode configurar o runbook para autenticar usando as credenciais Executar Como da sua assinatura. Faça isso adicionando a conexão Executar Como do Azure como um ativo. Você também deve adicionar o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) e o cmdlet [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) à tela.

>[!NOTE]
>Para runbooks do PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` são aliases para `Connect-AzAccount`. Observe que esses aliases não estão disponíveis para seus runbooks gráficos. Um runbook gráfico só pode usar `Connect-AzAccount`ele próprio.

1. Navegue até seu runbook e selecione **Editar** na página MyFirstRunbook-Graphical.

2. Você não precisa mais da entrada `Write Hello World to output`. Basta clicar nas reticências e selecionar **Excluir**.

3. No controle Biblioteca, expanda **ATIVOS** e depois **Conexões**. Adicione `AzureRunAsConnection` à tela selecionando **Adicionar à tela**.

4. Renomeie `AzureRunAsConnection` para `Get Run As Connection`.

5. No controle Biblioteca, digite `Connect-AzAccount` no campo de pesquisa.

6. Adicione `Connect-AzAccount` à tela.

7. Passe o mouse sobre `Get Run As Connection` até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta para `Connect-AzAccount` a fim de formar um link. O runbook começa com `Get Run As Connection` e, em seguida, executa `Connect-AzAccount`.

    ![Criar link entre as atividades](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. Na tela, selecione `Connect-AzAccount`. No painel de controle Configuração, digite **Logon no Azure** no campo **Rótulo**.

9. Clique em **Parâmetros** e a página Configuração do Parâmetro da Atividade aparecerá.

10. O cmdlet `Connect-AzAccount` tem vários conjuntos de parâmetros, portanto, você precisa selecionar um antes de poder fornecer os valores de parâmetro. Clique em **Conjunto de Parâmetros** e selecione o conjunto de parâmetros **ServicePrincipalCertificateWithSubscriptionId**.

11. Os parâmetros desse conjunto de parâmetros são exibidos na página Configuração do Parâmetro da Atividade. Clique em **APPLICATIONID**.

    ![Adicionar parâmetros da conta do Azure](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. Na página Valor do Parâmetro, faça as configurações a seguir e clique em **OK**.

   * **Fonte de dados** – selecione **Saída da atividade**.
   * Lista de fontes de dados – selecione **Obter Conexão de Automação**.
   * **Caminho do campo** – digite `ApplicationId`. Você está especificando o nome da propriedade para o caminho do campo porque a atividade produziu um objeto com várias propriedades.

13. Clique em **CERTIFICATETHUMBPRINT** e, na página Valor do Parâmetro, realize as configurações a seguir e clique em **OK**.

    * **Fonte de dados** – selecione **Saída da atividade**.
    * Lista de fontes de dados – selecione **Obter Conexão de Automação**.
    * **Caminho do campo** – digite `CertificateThumbprint`.

14. Clique em **SERVICEPRINCIPAL** e, na página de Valor do Parâmetro, selecione **ConstantValue** para o campo **Fonte de dados**. Clique na opção **True** e depois em **OK**.

15. Clique em **TENANTID** e realize as configurações a seguir na página Valor do Parâmetro. Quando terminar, clique em **OK** duas vezes.

    * **Fonte de dados** – selecione **Saída da atividade**.
    * Lista de fontes de dados – selecione **Obter Conexão de Automação**.
    * **Caminho do campo** – digite `TenantId`.

16. No controle Biblioteca, digite `Set-AzContext` no campo de pesquisa.

17. Adicione `Set-AzContext` à tela.

18. Na tela, selecione `Set-AzContext`. No painel de controle Configuração, digite `Specify Subscription Id` no campo **Rótulo**.

19. Clique em **Parâmetros** e a página Configuração do Parâmetro da Atividade aparecerá.

20. O cmdlet `Set-AzContext` tem vários conjuntos de parâmetros, portanto, você precisa selecionar um antes de poder fornecer os valores de parâmetro. Clique em **Conjunto de Parâmetros** e selecione **SubscriptionId**.

21. Os parâmetros desse conjunto de parâmetros são exibidos na página Configuração do Parâmetro da Atividade. Clique em **SubscriptionID**.

22. Na página de Valor do Parâmetro, selecione **Ativo de Variável** para o campo **Fonte de dados**, selecione **AzureSubscriptionId** na lista de fontes. Quando terminar, clique em **OK** duas vezes.

23. Passe o mouse sobre `Login to Azure` até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta para `Specify Subscription Id`. Seu runbook deve agora ser semelhante ao mostrado a seguir.

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="Captura de tela do runbook depois de arrastar a seta para 'Especificar a ID da Assinatura'.":::

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Etapa 7: adicionar a atividade para iniciar uma máquina virtual

Agora você precisa adicionar uma atividade `Start-AzVM` para iniciar uma máquina virtual. Você pode escolher qualquer VM em sua assinatura do Azure e, por enquanto, você está codificando o nome dessa assinatura no cmdlet [Start-AzVM](/powershell/module/az.compute/start-azvm).

1. No controle Biblioteca, digite `Start-Az` no campo de pesquisa.

2. Adicione `Start-AzVM` à tela e, em seguida, clique nele e arraste-o para baixo de `Specify Subscription Id`.

3. Passe o mouse sobre `Specify Subscription Id` até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta para `Start-AzVM`.

4. Selecione `Start-AzVM`. Clique em **Parâmetros** e **Conjunto de Parâmetros** para exibir os conjuntos da atividade.

5. Selecione **ResourceGroupNameParameterSetName** para o conjunto de parâmetros. Os campos **ResourceGroupName** e **Nome** têm pontos de exclamação ao lado para indicar que eles são parâmetros obrigatórios. Observe que os dois campos esperam valores de cadeia de caracteres.

6. Selecione **Name**. Escolha **Expressão do PowerShell** para o campo **Fonte de dados**. Para a VM que você usa para iniciar este runbook, digite o nome do computador entre aspas duplas. Clique em **OK**.

7. Selecione **ResourceGroupName**. Use o valor **Expressão do PowerShell** para o campo **Fonte de dados** e digite o nome do grupo de recursos entre aspas duplas. Clique em **OK**.

8. Clique em **Painel de teste** para que você possa testar o runbook.

9. Clique em **Iniciar** para iniciar o teste. Quando for concluído, verifique se a VM foi iniciada. Seu runbook deve agora ser semelhante ao mostrado a seguir.

    ![Saída Start-AzVM do runbook](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Etapa 8 – Adicionar parâmetros de entrada adicionais

Seu runbook atualmente inicia a VM no grupo de recursos que você especificou para o cmdlet `Start-AzVM`. O runbook será mais útil se você especificar o nome e o grupo de recursos quando o runbook for iniciado. Vamos adicionar os parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Abra o editor gráfico clicando em **Editar** na página MyFirstRunbook-Graphical.

2. Selecione **Entrada e saída** e em **Adicionar entrada** para abrir o painel Parâmetro de Entrada do Runbook.

3. Faça as configurações a seguir nos campos fornecidos e clique em **OK**.
   * **Nome** – especifique `VMName`.
   * **Tipo** – mantenha a configuração da cadeia de caracteres.
   * **Obrigatório** – altere o valor para **Sim**.

4. Crie um segundo parâmetro de entrada obrigatório denominado `ResourceGroupName` e clique em **OK** para fechar o painel Entrada e Saída.

    ![Parâmetros de Entrada de Runbook](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. Selecione a atividade `Start-AzVM` e clique em **Parâmetros**.

6. Altere o campo **Fonte de dados** de **Name** para **Entrada do runbook**. Em seguida, selecione **VMName**.

7. Altere o campo **Fonte de dados** de **ResourceGroupName** para a **Entrada do runbook** e selecione **ResourceGroupName**.

    ![Parâmetros Start-AzVM](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Salve o runbook e abra o Painel de teste. Agora você pode fornecer valores para as duas variáveis de entrada usadas no teste.

9. Feche o Painel de teste.

10. Clique em **Publicar** para publicar a nova versão do runbook.

11. Pare a VM que você iniciou anteriormente.

12. Clique em **Iniciar** para iniciar o runbook. Digite os valores de `VMName` e `ResourceGroupName` para a VM que você iniciará.

13. Quando o runbook for concluído, verifique se a VM foi iniciada.

## <a name="step-9---create-a-conditional-link"></a>Etapa 9: criar um link condicional

Agora, você poderá modificar o runbook para que ele tente iniciar a VM apenas se ela ainda não tiver iniciado. Faça isso adicionando um cmdlet [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) que recupera o status de nível de instância da VM. Em seguida, adicione um módulo de código do Fluxo de Trabalho do PowerShell chamado `Get Status` com um snippet de código do PowerShell para determinar se o estado da VM é em execução ou parado. Um link condicional do módulo `Get Status` apenas executa `Start-AzVM` se o estado de execução atual é interrompido. No final deste procedimento, o runbook usa o cmdlet `Write-Output` para gerar uma mensagem para informá-lo se a VM foi iniciada com êxito.

1. Abra o **MyFirstRunbook-Graphical** no editor gráfico.

2. Remova o link entre `Specify Subscription Id` e `Start-AzVM` clicando nele e, em seguida, pressionando **Excluir**.

3. No controle Biblioteca, digite `Get-Az` no campo de pesquisa.

4. Adicione `Get-AzVM` à tela.

5. Selecione `Get-AzVM` e clique em **Conjunto de Parâmetros** para exibir os conjuntos para o cmdlet.

6. Selecione o conjunto de parâmetros **GetVirtualMachineInResourceGroupNameParamSet** . Os campos **ResourceGroupName** e **Nome** têm pontos de exclamação ao lado, indicando que eles especificam parâmetros obrigatórios. Observe que os dois campos esperam valores de cadeia de caracteres.

7. Em **Fonte de dados** para **Nome**, selecione **Entrada do runbook** e, em seguida, **VMName**. Clique em **OK**.

8. Em **Fonte de dados** para **ResourceGroupName**, selecione **Entrada do runbook** e, em seguida, **ResourceGroupName**. Clique em **OK**.

9. Em **Fonte de dados** para **Status**, selecione **Valor constante** e **True**. Clique em **OK**.

10. Crie um link de `Specify Subscription Id` para `Get-AzVM`.

11. No controle Biblioteca, expanda **Controle do Runbook** e adicione **Code** à tela.  

12. Crie um link de `Get-AzVM` para `Code`.  

13. Clique em `Code` e, no painel Configuração, altere o rótulo para **Obter Status**.

14. Selecione `Code` e a página Editor de Código aparecerá.  

15. Cole o seguinte snippet de código a seguir na página do editor.

    ```powershell
    $Statuses = $ActivityOutput['Get-AzVM'].Statuses
    $StatusOut = ""
    foreach ($Status in $Statuses) {
      if($Status.Code -eq "Powerstate/running")
        {$StatusOut = "running"}
      elseif ($Status.Code -eq "Powerstate/deallocated")
        {$StatusOut = "stopped"}
    }
    $StatusOut
    ```

16. Crie um link de `Get Status` para `Start-AzVM`.

    ![Runbook com o módulo de código](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. Selecione o link e no painel de Configuração, altere **Aplicar condição** para **Sim**. Observe que o link torna-se uma linha tracejada, indicando que a atividade de destino é executada apenas se a condição é resolvida como verdadeira.  

18. Para **Expressão de condição**, digite `$ActivityOutput['Get Status'] -eq "Stopped"`. `Start-AzVM` agora só será executado se a VM for interrompida.

19. No controle de biblioteca, expanda **Cmdlets** e **Microsoft.PowerShell.Utility**.

20. Adicione `Write-Output` duas vezes à tela.

21. No primeiro controle `Write-Output`, clique em **Parâmetros** e altere o valor **Rótulo** para **Notificar VM Iniciada**.

22. Para **InputObject**, altere a **Fonte de dados** para a **Expressão do PowerShell** e digite a expressão `$VMName successfully started.`.

23. No segundo controle `Write-Output`, clique em **Parâmetros** e altere o valor **Rótulo** para **Falha ao Notificar VM Iniciada**.

24. Para **InputObject**, altere a **Fonte de dados** para a **Expressão do PowerShell** e digite a expressão `$VMName could not start`.

25. Crie links de `Start-AzVM` para `Notify VM Started` e `Notify VM Start Failed`.

26. Selecione o link para `Notify VM Started` e altere **Aplicar condição** para true.

27. Para **Expressão de condição**, digite `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`. Esse controle `Write-Output` agora só será executado se a VM for iniciada com êxito.

28. Selecione o link para `Notify VM Start Failed` e altere **Aplicar condição** para true.

29. Para o campo **Expressão de condição**, digite `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`. Esse controle `Write-Output` agora só será executado se a VM não for iniciada com êxito. Seu runbook deve ser semelhante à imagem a seguir.

    ![Runbook com Write-Output](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Salve o runbook e abra o Painel de teste.

31. Inicie o runbook com a VM parada e ela deverá ser iniciada.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a criação de gráficos, confira [Criar um runbook gráfico na Automação do Azure](../automation-graphical-authoring-intro.md).
* Para começar a usar os runbooks do PowerShell, confira [Criar um runbook do PowerShell](automation-tutorial-runbook-textual-powershell.md).
* Para começar a usar runbooks de Fluxo de Trabalho do PowerShell, confira [Criar um runbook de Fluxo de Trabalho do PowerShell](automation-tutorial-runbook-textual.md).
* Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation).
