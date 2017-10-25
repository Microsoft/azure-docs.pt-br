---
title: "Meu primeiro runbook gráfico na Automação do Azure | Microsoft Docs"
description: "Tutorial que orienta você pela criação, teste e publicação de um runbook gráfico simples."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
keywords: "runbook, modelo de runbook, automação de runbook, runbook do azure"
ms.assetid: dcb88f19-ed2b-4372-9724-6625cd287c8a
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/17/2017
ms.author: magoedte;bwren
ms.openlocfilehash: b8accf16c43b7a01233742161e68db6f831a2d30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="my-first-graphical-runbook"></a>O meu primeiro runbook gráfico

> [!div class="op_single_selector"]
> * [Gráfico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Este tutorial orienta você durante a criação de um [runbook gráfico](automation-runbook-types.md#graphical-runbooks) na Automação do Azure.  Começamos com um runbook simples, que é testado e publicado enquanto explicamos como acompanhar o status do trabalho do runbook.  Em seguida, modificamos o runbook para gerenciar os recursos do Azure, neste caso, iniciando uma máquina virtual do Azure.  Em seguida, concluímos o tutorial, tornando o runbook mais robusto adicionando parâmetros de runbook e links condicionais.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará do seguinte:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para manter o runbook e se autenticar nos recursos do Azure.  Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Paramos e iniciamos o computador, portanto, ele não deve ser uma VM de produção.

## <a name="step-1---create-runbook"></a>Etapa 1: criar o runbook
Começamos criando um runbook simples que exibe o texto *Olá mundo*.

1. No portal do Azure, abra sua conta da Automação.  
   A página da conta de Automação fornece uma exibição rápida dos recursos nessa conta.  Você já deve ter alguns ativos.  A maioria deles são os módulos que são incluídos automaticamente em uma nova conta de Automação.  Você também deve ter o ativo de credencial que é mencionado nos [pré-requisitos](#prerequisites).
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.<br> ![Controle de runbooks](media/automation-first-runbook-graphical/runbooks-resources-tile.png)
3. Crie um novo runbook clicando no botão **Adicionar um runbook** e em **Criar um novo runbook**.
4. Atribua o nome *MyFirstRunbook-Graphical*ao runbook.
5. Neste caso, criaremos um [runbook gráfico](automation-graphical-authoring-intro.md), portanto, escolha **Gráfico** para **Tipo de runbook**.<br> ![Novo runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Clique em **criar** para criar o runbook e abrir o editor gráfico.

## <a name="step-2---add-activities-to-the-runbook"></a>Etapa 2: adicionar atividades ao runbook
O controle Biblioteca no lado esquerdo do editor permite que você selecione as atividades para adicionar ao runbook.  Adicionaremos um cmdlet **Write-Output** para exibir nosso o texto a partir do runbook.

1. No controle Biblioteca, clique na caixa de texto de pesquisa e digite **Write-Output**.  Os resultados da pesquisa serão exibidos abaixo. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
2. Role até o final da lista.  Você pode clicar com o botão direito do mouse em **Write-Output** e selecionar **Adicionar à tela** ou clicar na elipse ao lado do cmdlet, em seguida, selecionar **Adicionar à tela**.
3. Clique na atividade **Write-Output** na tela.  Isso abrirá a folha Controle da configuração, que permite a você configurar a atividade.
4. O **Rótulo** usa por padrão o nome do cmdlet, mas podemos alterá-lo para algo mais amigável. Altere-o para *Write Hello World to output*.
5. Clique em **Parâmetros** para fornecer valores para os parâmetros do cmdlet.  
   Alguns cmdlets têm vários conjuntos de parâmetros e você precisa selecionar o que será usado. Nesse caso, **Write-Output** tem apenas um conjunto de parâmetros, portanto, não é necessário selecionar um. <br> ![Propriedades de Write-Output](media/automation-first-runbook-graphical/write-output-properties-b.png)
6. Selecione o parâmetro **InputObject** .  Esse é o parâmetro em que especificamos o texto a ser enviado ao fluxo de saída.
7. No menu suspenso **Fonte de dados**, selecione **Expressão do PowerShell**.  O menu suspenso **Fonte de dados** fornece diferentes fontes que podem ser usadas para popular um valor de parâmetro.  
   Você pode usar a saída dessas fontes como outra atividade, um ativo de Automação ou uma expressão do PowerShell.  Nesse caso, queremos apenas exibir o texto *Olá mundo*. Podemos usar uma expressão do PowerShell e especificar uma cadeia de caracteres.
8. Na caixa **Expressão**, digite *"Olá mundo"* e, em seguida, clique em **OK** duas vezes para retornar à tela.<br> ![PowerShell Expression](media/automation-first-runbook-graphical/expression-hello-world.png)
9. Salve o runbook clicando em **Salvar**.<br> ![Salvar runbook](media/automation-first-runbook-graphical/runbook-toolbar-save-revised20165.png)

## <a name="step-3---test-the-runbook"></a>Etapa 3: testar o runbook
Antes que publicamos o runbook para disponibilizá-lo na produção, queremos testá-lo para garantir que ele funciona corretamente.  Quando você testa um runbook, executa sua versão de **Rascunho** e vê sua saída interativamente.

1. Clique em **Painel de teste** para abrir a folha Teste.<br> ![Painel de teste](media/automation-first-runbook-graphical/runbook-toolbar-test-revised20165.png)
2. Clique em **Iniciar** para iniciar o teste.  Essa deve ser a única opção habilitada.
3. Um [trabalho de runbook](automation-runbook-execution.md) é criado e seu status é exibido no painel.  
   O status do trabalho inicia como *Na fila* , indicando que ele está aguardando um trabalho de runbook na nuvem ficar disponível.  Em seguida, ele muda para *Iniciando* quando um trabalhador reivindicar o trabalho e para *Executando* quando o runbook realmente começar a ser executado.  
4. Quando o trabalho do runbook é concluído, sua saída é exibida. Em nosso caso, deveremos ver *Olá mundo*.<br> ![Olá mundo](media/automation-first-runbook-graphical/runbook-test-results.png)
5. Feche a folha Teste para retornar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Etapa 4: publicar e iniciar o runbook
O runbook que criamos ainda está em Modo de rascunho. Precisamos publicá-lo antes que possamos executá-lo na produção.  Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho.  Em nosso caso, não temos uma versão Publicada ainda porque que acabamos de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e em **Sim** quando solicitado.<br> ![Publicar](media/automation-first-runbook-graphical/runbook-toolbar-publish-revised20166.png)
2. Se você rolar para a esquerda para exibir o runbook na folha **Runbooks**, ele mostrará um **Status de Criação** **Publicado**.
3. Role para a direita para exibir a folha para **MyFirstRunbook**.  
   As opções na parte superior nos permitem iniciar o runbook, agendá-lo para iniciar em algum momento no futuro ou criar um [webhook](automation-webhooks.md) para que ele possa ser iniciado por meio de uma chamada de HTTP.
4. Queremos apenas iniciar o runbook, então clique em **Iniciar** e em **Sim** quando solicitado.<br> ![Iniciar runbook](media/automation-first-runbook-graphical/runbook-controls-start-revised20165.png)
5. Uma folha de trabalho será aberto para o trabalho de runbook criado.  Podemos fechar essa folha, mas, neste caso, deixaremos aberta para que possamos acompanhar o progresso do trabalho.
6. O status do trabalho é mostrado em **Resumo do trabalho** e corresponde aos status que vimos quando testamos o runbook.<br> ![Resumo do trabalho](media/automation-first-runbook-graphical/runbook-job-summary.png)
7. Assim que o status do runbook mostrar *Concluído*, clique em **Saída**. A folha **Saída** é aberta e podemos ver nosso *Olá Mundo* no painel.<br> ![Resumo do trabalho](media/automation-first-runbook-graphical/runbook-job-output.png)  
8. Feche a folha Saída.
9. Clique em **Todos os Logs** para abrir a folha Fluxos do trabalho do runbook.  Devemos ver apenas *Olá Mundo* no fluxo de saída, mas isso pode mostrar outros fluxos de um trabalho do runbook como Detalhado e Erro se o runbook gravar neles.<br> ![Resumo do trabalho](media/automation-first-runbook-graphical/runbook-job-AllLogs.png)
10. Feche as folhas Todos os Logs e Trabalho para retornar para a folha MyFirstRunbook.
11. Clique em **Trabalhos** para abrir a folha Trabalhos desse runbook.  Ele lista todos os trabalhos criados por esse runbook. Devemos ver apenas um trabalho listado, já que executamos o trabalho apenas uma vez.<br> ![Trabalhos](media/automation-first-runbook-graphical/runbook-control-jobs.png)
12. Você pode clicar neste trabalho para abrir o mesmo painel Trabalho que exibimos quando iniciamos o runbook.  Isso permite que você volte no tempo e veja os detalhes de qualquer trabalho que foi criado para um determinado runbook.

## <a name="step-5---create-variable-assets"></a>Etapa 5: criar ativos de variável
Testamos e publicamos nosso runbook, mas até o momento ele não faz nada útil. Gostaríamos que ele gerenciasse recursos do Azure.  Antes de configurarmos o runbook a ser autenticado, criaremos uma variável para conter a ID de assinatura e usá-la como referência depois que configurarmos a atividade a ser autenticada na etapa 6 abaixo.  A inclusão de uma referência no contexto da assinatura facilita o seu trabalho entre as várias assinaturas.  Antes de prosseguir, copie a ID de assinatura na opção Assinaturas do Painel de navegação.  

1. Na folha Contas de Automação, clique no bloco **Ativos** e a folha **Ativos** será aberta.
2. Na folha Ativos, clique no bloco **Variáveis** .
3. Na folha Variáveis, clique em **Adicionar uma variável**.<br>![Variável de Automação](media/automation-first-runbook-graphical/create-new-subscriptionid-variable.png)
4. Na folha Nova variável, na caixa **Nome**, digite **AzureSubscriptionId** e na caixa **Valor**, insira sua ID de Assinatura.  Mantenha a *cadeia de caracteres* para o **Tipo** e o valor padrão para a **Criptografia**.  
5. Clique em **Criar** para criar a variável.  

## <a name="step-6---add-authentication-to-manage-azure-resources"></a>Etapa 6: adicionar a autenticação para gerenciar os recursos do Azure
Agora que temos uma variável para conter a ID de assinatura, podemos configurar nosso runbook para autenticar usando as credenciais Executar Como que são referenciadas nos [pré-requisitos](#prerequisites).  Fazemos isso adicionando a conexão do Azure Executar Como **Ativos** e o cmdlet **Add-AzureRMAccount** à tela.  

1. Abra o editor gráfico clicando em **Editar** na folha MyFirstRunbook.<br> ![Editar runbook](media/automation-first-runbook-graphical/runbook-controls-edit-revised20165.png)
2. Não precisamos mais de **Write Olá, Mundo to output** e, portanto, clique nele com o botão direito do mouse e selecione **Excluir**.
3. No controle de Biblioteca, expanda **Conexões** e adicione **AzureRunAsConnection** à tela selecionando **Adicionar à tela**.
4. Na tela, selecione **AzureRunAsConnection** e no painel de controle da Configuração, digite **Obter Conexão Executar Como** na caixa de texto **Rótulo**.  Essa é a conexão
5. No controle Biblioteca, digite **Add-AzureRmAccount** na caixa de texto de pesquisa.
6. Adicione **Adicione-AzureRmAccount** à tela.<br> ![AzureRMAccount adicionar](media/automation-first-runbook-graphical/search-powershell-cmdlet-addazurermaccount.png)
7. Passe o mouse sobre **Obter Conexão Executar Como** até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta para **Add-AzureRmAccount**.  A seta que você criou é um *link*.  O runbook inicia com **Obter Conexão Executar Como** e executa **Add-AzureRmAccount**.<br> ![Criar link entre as atividades](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
8. Na tela, selecione **Add-AzureRmAccount** e no tipo de painel de controle de configuração **Fazer Logon no Azure** na caixa de texto de **Rótulo**.
9. Clique em **Parâmetros** e a folha Configuração do Parâmetro da Atividade aparecerá.
10. **Add-AzureRmAccount** tem vários conjuntos de parâmetros, portanto, precisamos selecionar um antes de podermos fornecer os valores do parâmetro.  Clique em **Conjunto de Parâmetros** e selecione o conjunto de parâmetros **ServicePrincipalCertificatewithSubscriptionId**.
11. Depois de selecionar o conjunto de parâmetros, os parâmetros serão exibidos na folha Configuração do Parâmetro da Atividade.  Clique em **APPLICATIONID**.<br> ![Adicionar parâmetros da conta do Azure RM](media/automation-first-runbook-graphical/add-azurermaccount-params.png)
12. Na folha Valor do Parâmetro, selecione **Saída da atividade** para a **Fonte de dados**, selecione **Obter Conexão Executar Como** da lista, na caixa de texto **Caminho do campo**, escreva **ApplicationId** e então clique em **OK**.  Estamos especificando o nome da propriedade para o caminho de Campo porque a atividade produziu um objeto com várias propriedades.
13. Clique em **CERTIFICATETHUMBPRINT** e na folha de Valor do Parâmetro, selecione **Saída de atividade** para a **Fonte de dados**.  Selecione **Obter Conexão Executar Como** da lista; na caixa de texto **Caminho de campo**, escreva **CertificateThumbprint** e clique em **OK**.
14. Clique em **SERVICEPRINCIPAL**, e na folha de Valor do Parâmetro, selecione **ConstantValue** para a **Fonte de dados**, clique na opção **True**, e em seguida, clique em **OK**.
15. Clique em **TENANTID** e na folha de Valor do Parâmetro, selecione **Saída de atividade** para a **Fonte de dados**.  Selecione **Obter Conexão Executar Como** da lista; na caixa de texto **Caminho de campo**, escreva **TenantId** e, em seguida, clique em **OK** duas vezes.  
16. No controle Biblioteca, digite **Set-AzureRmContext** na caixa de texto de pesquisa.
17. Adicionar **Set-AzureRmContext** à tela.
18. Na tela, selecione **Set-AzureRmContext** e no painel de controle da Configuração, digite **Especificar ID de Assinatura** na caixa de texto **Rótulo**.
19. Clique em **Parâmetros** e a folha Configuração do Parâmetro da Atividade aparecerá.
20. **Set-AzureRmContext** tem vários conjuntos de parâmetros, portanto, precisamos selecionar um antes de podermos fornecer os valores do parâmetro.  Clique em **Conjunto de Parâmetros** e selecione o conjunto de parâmetros **SubscriptionId**.  
21. Depois de selecionar o conjunto de parâmetros, os parâmetros serão exibidos na folha Configuração do Parâmetro da Atividade.  Clique em **SubscriptionID**
22. Na folha Valor do Parâmetro, selecione **Ativo de Variável** para a **Fonte de dados**, selecione **AzureSubscriptionId** na lista e clique em **OK** duas vezes.   
23. Passe o mouse sobre **Login no Azure** até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta para **Especificar Id de Assinatura**.

Seu runbook deve ser semelhante ao seguinte:  <br>![Configuração da autenticação do runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Etapa 7: adicionar a atividade para iniciar uma máquina virtual
Aqui, adicionaremos uma atividade **Start-AzureRmVM** para iniciar uma máquina virtual.  Você pode escolher qualquer máquina virtual na sua assinatura do Azure e, por enquanto, deve embutir esse nome no cmdlet.

1. No controle Biblioteca, digite **Start-AzureRm** na caixa de texto de pesquisa.
2. Adicione **Start-AzureRmVM** à tela, clique e arraste-a para baixo de **Especificar Id de Assinatura**.
3. Passe o mouse sobre **Especificar Id de Assinatura** até que um círculo apareça na parte inferior da forma.  Clique no círculo e arraste a seta para **Start-AzureRmVM**.
4. Selecione **Start-AzureRmVM**.  Clique em **Parâmetros** e **Conjunto de Parâmetros** para exibir os conjuntos de **Start-AzureRmVM**.  Selecione o conjunto de parâmetros **ResourceGroupNameParameterSetName** . Observe que há pontos de exclamação ao lado de **ResourceGroupName** e **Name**.  Isso indica que são os parâmetros obrigatórios.  Observe também que ambos esperam valores da cadeia de caracteres.
5. Selecione **Name**.  Selecione **Expressão do PowerShell** para a **Fonte de dados** e digite o nome da máquina virtual entre aspas duplas com a qual iniciamos este runbook.  Clique em **OK**.<br>![Valor do Parâmetro Name do Start-AzureRmVM](media/automation-first-runbook-graphical/runbook-startvm-nameparameter.png)
6. Selecione **ResourceGroupName**. Use a a **Expressão do PowerShell** para o **Fonte de dados** e digite o nome do grupo de recursos entre aspas duplas.  Clique em **OK**.<br> ![Parâmetros do Start-AzureRmVM](media/automation-first-runbook-graphical/startazurermvm-params.png)
7. Clique em Painel de teste para que possamos testar o runbook.
8. Clique em **Iniciar** para iniciar o teste.  Quando for concluído, verifique se a máquina virtual foi iniciada.

Seu runbook deve ser semelhante ao seguinte:  <br>![Configuração da autenticação do runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters-to-the-runbook"></a>Etapa 8: adicionar parâmetros de entrada adicionais ao runbook
Nosso runbook atualmente inicia a máquina virtual no grupo de recursos especificado no cmdlet **Start-AzureRmVM**.  Nosso runbook seria mais útil se ambos pudessem ser especificados quando o runbook é iniciado.  Agora, adicionamos parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Abra o editor gráfico clicando em **Editar** no painel **MyFirstRunbook**.
2. Clique em **Entrada e saída** e em **Adicionar entrada** para abrir o painel Parâmetro de Entrada do Runbook.<br> ![Entrada e Saída de runbook](media/automation-first-runbook-graphical/runbook-toolbar-InputandOutput-revised20165.png)
3. Especifique *VMName* para o **Nome**.  Mantenha a *cadeia de caracteres* para o **Tipo**, mas altere **Obrigatório** para *Sim*.  Clique em **OK**.
4. Crie um segundo parâmetro de entrada obrigatório denominado *ResourceGroupName* e clique em **OK** para fechar o painel de **Entrada e Saída**.<br> ![Parâmetros de Entrada de Runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
5. Selecione a atividade **Start-AzureRmVM** e clique em **Parâmetros**.
6. Altere a **Fonte de dados** de **Name** para a **Entrada do runbook** e selecione **VMName**.<br>
7. Altere a **Fonte de dados** de **ResourceGroupName** para a **Entrada do runbook** e selecione **ResourceGroupName**.<br> ![Parâmetros de Start-AzureVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
8. Salve o runbook e abra o Painel de teste.  Observe que agora você pode fornecer valores para as duas variáveis de entrada usadas no teste.
9. Feche o Painel de teste.
10. Clique em **Publicar** para publicar a nova versão do runbook.
11. Pare a máquina virtual que você iniciou na etapa anterior.
12. Clique em **Iniciar** para iniciar o runbook.  Digite o **VMName** e o **ResourceGroupName** da máquina virtual que você iniciará.<br> ![Start Runbook](media/automation-first-runbook-graphical/runbook-start-inputparams.png)
13. Quando o runbook for concluído, verifique se a máquina virtual foi iniciada.

## <a name="step-9---create-a-conditional-link"></a>Etapa 9: criar um link condicional
Agora, modificamos o runbook para que ele tente iniciar a máquina virtual apenas se ela ainda não tiver iniciado.  Faça isso adicionando um cmdlet **Get-AzureRmVM** ao runbook que obtém o status do nível da instância da máquina virtual. Em seguida, adicione um módulo de código do Fluxo de Trabalho do PowerShell denominado **Obter Status** com um trecho de código do PowerShell para determinar se o estado da máquina virtual está em execução ou parado.  Um link condicional do módulo **Obter Status** apenas executa **Start-AzureRmVM** se o estado de execução atual é interrompido.  Por fim, enviamos uma mensagem para informar se a VM foi iniciada com êxito ou não usando o cmdlet Write-Output do PowerShell.

1. Abra o **MyFirstRunbook** no editor gráfico.
2. Remova o link entre **Especificar Id de Assinatura** e **Start-AzureRmVM** clicando nele e pressionando a tecla *Delete*.
3. No controle Biblioteca, digite **Get-AzureRm** na caixa de texto de pesquisa.
4. Adicione **Get-AzureRmVM** à tela.
5. Selecione **Get-AzureRmVM** e **Conjunto de Parâmetros** para exibir os conjuntos de **Get-AzureRmVM**.  Selecione o conjunto de parâmetros **GetVirtualMachineInResourceGroupNameParamSet** .  Observe que há pontos de exclamação ao lado de **ResourceGroupName** e **Name**.  Isso indica que são os parâmetros obrigatórios.  Observe também que ambos esperam valores da cadeia de caracteres.
6. Em **Fonte de dados** para **Name**, selecione **Entrada do runbook** e, em seguida, selecione **VMName**.  Clique em **OK**.
7. Em **Fonte de dados** para **ResourceGroupName**, selecione **Entrada do runbook** e, em seguida, selecione **ResourceGroupName**.  Clique em **OK**.
8. Em **Fonte de dados** para **Status**, selecione **Valor constante** e, em seguida, clique em **True**.  Clique em **OK**.  
9. Criar um link de **Especificar Id da Assinatura** para **Get-AzureRmVM**.
10. No controle da biblioteca, expanda **Controle do runbook** e adicione o **Código** à tela.  
11. Crie um link de **Get-AzureRmVM** para **Código**.  
12. Clique em **Código** e no painel de Configuração, altere o rótulo para **Obter Status**.
13. Selecione o parâmetro **Código** e a folha **Editor de Código** aparecerá.  
14. No editor de código, cole o seguinte trecho de código:
    
     ```
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```
15. Crie um link de **Obter Status** para **Start-AzureRmVM**.<br> ![Runbook com Módulo de Código](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
16. Selecione o link e no painel de Configuração, altere **Aplicar condição** para **Sim**.   Observe que o link muda para uma linha tracejada, indicando que a atividade de destino é executada apenas se a condição é resolvida como verdadeira.  
17. Para a **Expressão da condição**, digite *$ActivityOutput['Get Status'] -eq "Stopped"*.  **Start-AzureRmVM** é executada apenas se a máquina virtual está parada.
18. No controle de biblioteca, expanda **Cmdlets** e **Microsoft.PowerShell.Utility**.
19. Adicione **Write-Output** à tela duas vezes.<br> ![Runbook com Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
20. No primeiro controle **Write-Output**, clique em **Parâmetros** e altere o valor **Rótulo** para *Notificar VM Iniciada*.
21. Para **InputObject**, altere a **Fonte de dados** para **Expressão do PowerShell** e digite a expressão *"$VMName.Name iniciada com êxito."*.
22. No segundo controle **Write-Output**, clique em **Parâmetros** e altere o valor **Rótulo** para *Falha ao Notificar VM Iniciada*
23. Para **InputObject**, altere a **Fonte de dados** para a **Expressão do PowerShell** e digite a expressão *"$VMName não pôde iniciar."*.
24. Criar um link de **Start-AzureRmVM** para **Notificar VM Iniciada** e **Falha ao Notificar VM Iniciada**.
25. Selecione o link para **Notificar VM Iniciada** e altere **Aplicar condição** para **True**.
26. Para a **Expressão da condição**, digite *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*.  Agora, o controle Write-Output é executado apenas se a máquina virtual é iniciada com êxito.
27. Selecione o link para **Falha ao Notificar VM Iniciada** e altere **Aplicar condição** para **True**.
28. Para a **Expressão da condição**, digite *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*.  Agora, o controle Write-Output será executado apenas se a máquina virtual não for iniciada com êxito.
29. Salve o runbook e abra o Painel de teste.
30. Inicie o runbook com a máquina virtual parada e ela deve ser iniciada.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a Criação Gráfica, veja [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)
* Para começar a usar os runbooks do PowerShell, veja [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md)
* Para começar a usar os runbooks de fluxo de trabalho do PowerShell, confira [Meu primeiro runbook de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)

