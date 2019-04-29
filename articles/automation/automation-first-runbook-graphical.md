---
title: Meu primeiro runbook gráfico na Automação do Azure
description: Tutorial que orienta você pela criação, teste e publicação de um runbook gráfico simples.
keywords: runbook, modelo de runbook, automação de runbook, runbook do azure
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/13/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: be811d0dc2ce2eca0b20ca12165eaf0799bd6b5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61077489"
---
# <a name="my-first-graphical-runbook"></a>O meu primeiro runbook gráfico

> [!div class="op_single_selector"]
> * [Gráfico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Este tutorial orienta você durante a criação de um [runbook gráfico](automation-runbook-types.md#graphical-runbooks) na Automação do Azure. Você começa com um runbook simples, que é testado e publicado enquanto aprende como acompanhar o status do trabalho do runbook. Em seguida, você modifica o runbook para gerenciar os recursos do Azure, neste caso, iniciando uma máquina virtual do Azure. Depois, você conclui o tutorial, tornando o runbook mais robusto adicionando parâmetros de runbook e links condicionais.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para manter o runbook e se autenticar nos recursos do Azure. Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Você para e inicia essa máquina, portanto, ele não deve ser uma VM de produção.

## <a name="create-runbook"></a>Criar runbook

Comece criando um runbook simples que exibe o texto *Olá, Mundo*.

1. No portal do Azure, abra sua conta da Automação.

   A página da conta de Automação fornece uma exibição rápida dos recursos nessa conta. Você já deve ter alguns ativos. A maioria desses ativos é de módulos incluídos automaticamente em uma nova conta de Automação. Você também deve ter o ativo de credencial que é mencionado nos [pré-requisitos](#prerequisites).

2. Selecione **Runbooks** em **GERENCIAMENTO DE PROCESSO** para abrir a lista de runbooks.
3. Crie um novo runbook selecionando **Adicionar um runbook** e, em seguida, clique em **Criar um novo runbook**.
4. Atribua o nome *MyFirstRunbook-Graphical*ao runbook.
5. Neste caso, você vai criar um [runbook gráfico](automation-graphical-authoring-intro.md), portanto, escolha **Gráfico** para **Tipo de runbook**.<br> ![Novo runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Clique em **criar** para criar o runbook e abrir o editor gráfico.

## <a name="add-activities"></a>Adicionar atividades

O controle Biblioteca no lado esquerdo do editor permite que você selecione as atividades para adicionar ao runbook. Você vai adicionar um cmdlet **Write-Output** para exibir nosso o texto a partir do runbook.

1. No controle Biblioteca, clique na caixa de texto de pesquisa e digite **Write-Output**. Os resultados de pesquisa são mostrados na imagem a seguir: <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Role até o final da lista. Você pode clicar com o botão direito do mouse em **Write-Output** e selecionar **Adicionar à tela** ou clicar nas elipses ao lado do cmdlet, em seguida, selecionar **Adicionar à tela**.
1. Clique na atividade **Write-Output** na tela. Esta ação abrirá a página Controle da configuração, que permite configurar a atividade.
1. O **Rótulo** usa por padrão o nome do cmdlet, mas você pode alterá-lo para algo mais amigável. Altere-o para *Write Hello World to output*.
1. Clique em **Parâmetros** para fornecer valores para os parâmetros do cmdlet.

   Alguns cmdlets têm vários conjuntos de parâmetros e você precisa selecionar o que será usado. Nesse caso, **Write-Output** tem apenas um conjunto de parâmetros, portanto, não é necessário selecionar um.

1. Selecione o parâmetro **InputObject** . Esse é o parâmetro em que você especifica o texto a ser enviado ao fluxo de saída.
1. No menu suspenso **Fonte de dados**, selecione **Expressão do PowerShell**. O menu suspenso **Fonte de dados** fornece diferentes fontes que podem ser usadas para popular um valor de parâmetro.

   Você pode usar a saída dessas fontes como outra atividade, um ativo de Automação ou uma expressão do PowerShell. Neste caso, a saída é apenas *Olá, Mundo*. Você pode usar uma expressão do PowerShell e especificar uma cadeia de caracteres.<br>

1. Na caixa **Expressão**, digite *"Olá mundo"* e, em seguida, clique em **OK** duas vezes para retornar à tela.
1. Salve o runbook clicando em **Salvar**.

## <a name="test-the-runbook"></a>Testar o runbook

Antes de publicar o runbook para disponibilizá-lo na produção, teste-o para verificar se ele funciona corretamente. Quando você testa um runbook, executa sua versão de **Rascunho** e vê sua saída interativamente.

1. Selecione **Painel de teste** para abrir a página de Teste.
1. Clique em **Iniciar** para iniciar o teste. Essa deve ser a única opção habilitada.
1. Um [trabalho de runbook](automation-runbook-execution.md) é criado e seu status é exibido no painel.

   O status do trabalho inicia como *Na fila* , indicando que ele está aguardando um trabalho de runbook na nuvem ficar disponível. Em seguida, ele muda para *Iniciando* quando um trabalhador reivindicar o trabalho e para *Executando* quando o runbook realmente começar a ser executado.

1. Quando o trabalho do runbook é concluído, sua saída é exibida. Neste caso, você vê *Olá, Mundo*.<br> ![Olá mundo](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Feche o página de Teste para retornar à tela.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar o runbook

O runbook criado ainda está em modo de Rascunho. Ele precisa ser publicado antes de poder ser executado na produção. Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Nesse caso, ainda não há uma versão publicada porque você acabou de criar o runbook.

1. Selecione **Publicar** para publicar o runbook e em **Sim** quando solicitado.
1. Se você rolar para a esquerda para exibir o runbook na página **Runbooks**, ele mostrará um **Status de Criação** **Publicado**.
1. Role de volta para a direita para visualizar a página para **MyFirstRunbook-Graphical**.

   As opções na parte superior nos permitem iniciar o runbook, agendá-lo para iniciar em algum momento no futuro ou criar um [webhook](automation-webhooks.md) para que ele possa ser iniciado por meio de uma chamada de HTTP.

1. Selecione **Iniciar** e em **Sim** quando solicitado para iniciar o runbook.
1. Uma página de trabalho será aberta para o trabalho de runbook criado. Verifique se o **Status de trabalho** mostra **Completo**.
1. Assim que o status do runbook mostrar *Concluído*, clique em **Saída**. A página **Saída** é aberta e é possível ver o *Olá, Mundo* no painel.
1. Feche a página de Saída.
1. Clique em **Todos os Logs** para abrir a página Fluxos do trabalho do runbook. Você só deve ver *Olá, Mundo* no fluxo de saída, mas isso pode mostrar outros fluxos de um trabalho do runbook como Detalhado e Erro se o runbook gravar neles.
1. Feche a página Todos os Logs e a página Trabalho para retornar para a página MyFirstRunbook-Graphical.
1. Para exibir todos os trabalhos do runbook, feche a página **Trabalho** e selecione **Trabalhos** em **RECURSOS**. Ele lista todos os trabalhos criados por esse runbook. Você deve ver apenas um trabalho listado, já que executou o trabalho apenas uma vez.
1. Você pode clicar nesse trabalho para abrir o mesmo painel do Trabalho exibido ao iniciar o runbook. Isso permite que você volte no tempo e veja os detalhes de qualquer trabalho que foi criado para um determinado runbook.

## <a name="create-variable-assets"></a>Criar ativos de variável

Você testou e publicou seu runbook, mas, até o momento, ele não faz nada útil. Você gostaria que ele gerencie recursos do Azure. Antes de configurar o runbook a ser autenticado, crie uma variável para conter a ID de assinatura e usá-la como referência depois de configurar a atividade a ser autenticada na etapa 6 abaixo. A inclusão de uma referência no contexto da assinatura facilita o seu trabalho entre as várias assinaturas. Antes de prosseguir, copie a ID de assinatura na opção Assinaturas do Painel de navegação.

1. Na página de Contas de Automação, selecione **Variáveis** em **RECURSOS COMPARTILHADOS**.
1. Selecione **Adicionar uma variável**.
1. Na página Nova variável, na caixa **Nome**, digite **AzureSubscriptionId** e na caixa **Valor**, insira sua ID de Assinatura. Mantenha a *cadeia de caracteres* para o **Tipo** e o valor padrão para a **Criptografia**.
1. Clique em **Criar** para criar a variável.

## <a name="add-authentication"></a>Adicionar autenticação

Agora que você tem uma variável para manter a ID de assinatura, você pode configurar nosso runbook para autenticar usando as credenciais Executar Como que são referenciadas nos [pré-requisitos](#prerequisites). Você faz isso adicionando o **Ativo** de conexão Executar Como do Azure e o cmdlet **Connect-AzureRmAccount** à tela.

1. Navegue de volta até seu runbook e selecione **Editar** na página MyFirstRunbook-Graphical.
1. Você não precisa mais do **Escrever Olá, Mundo para a saída**, portanto, clique nas elipses (...) e selecione **Excluir**.
1. No controle de Biblioteca, expanda **ATIVOS**, **Conexões** e adicione **AzureRunAsConnection** à tela selecionando **Adicionar à tela**.
1. No controle Biblioteca, digite **Connect-AzureRmAccount** na caixa de texto de pesquisa.

   > [!IMPORTANT]
   > **Connect-AzureRmAccount** agora é um alias para **Connect-AzureRMAccount**. Ao pesquisar sua biblioteca de itens, se você não vir **Connect-AzureRMAccount**, você pode usar **Connect-AzureRmAccount**, ou você pode atualizar seus módulos em sua Conta de Automação.

1. Adicione **Connect-AzureRmAccount** à tela.
1. Passe o mouse sobre **Obter Conexão Executar Como** até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta para **Connect-AzureRmAccount**. A seta que você criou é um *link*. O runbook inicia com **Obter Conexão Executar Como** e executa **Connect-AzureRmAccount**.<br> ![Criar link entre as atividades](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Na tela, selecione **Connect-AzureRmAccount** e no tipo de painel de controle de configuração **Fazer Logon no Azure** na caixa de texto de **Rótulo**.
1. Clique em **Parâmetros** e a página Configuração do Parâmetro da Atividade aparecerá.
1. **Connect-AzureRmAccount** tem vários conjuntos de parâmetros, portanto, você precisa selecionar um antes de poder fornecer os valores do parâmetro. Clique em **Conjunto de Parâmetros** e selecione o conjunto de parâmetros **ServicePrincipalCertificate**.
1. Depois de selecionar o conjunto de parâmetros, os parâmetros serão exibidos na página Configuração do Parâmetro da Atividade. Clique em **APPLICATIONID**.<br> ![Adicionar parâmetros da conta do Azure RM](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Na página Valor do Parâmetro, selecione **Saída da atividade** para a **Fonte de dados**, selecione **Obter Conexão Executar Como** da lista, na caixa de texto **Caminho do campo**, escreva **ApplicationId** e então clique em **OK**. Você está especificando o nome da propriedade para o caminho de Campo porque a atividade produziu um objeto com várias propriedades.
1. Clique em **CERTIFICATETHUMBPRINT** e na página de Valor do Parâmetro, selecione **Saída de atividade** para a **Fonte de dados**. Selecione **Obter Conexão Executar Como** da lista; na caixa de texto **Caminho de campo**, escreva **CertificateThumbprint** e clique em **OK**.
1. Clique em **SERVICEPRINCIPAL**, e na página de Valor do Parâmetro, selecione **ConstantValue** para a **Fonte de dados**, clique na opção **True**, e em seguida, clique em **OK**.
1. Clique em **TENANTID** e na página de Valor do Parâmetro, selecione **Saída de atividade** para a **Fonte de dados**. Selecione **Obter Conexão Executar Como** da lista; na caixa de texto **Caminho de campo**, escreva **TenantId** e, em seguida, clique em **OK** duas vezes.
1. No controle Biblioteca, digite **Set-AzureRmContext** na caixa de texto de pesquisa.
1. Adicionar **Set-AzureRmContext** à tela.
1. Na tela, selecione **Set-AzureRmContext** e no painel de controle da Configuração, digite **Especificar ID de Assinatura** na caixa de texto **Rótulo**.
1. Clique em **Parâmetros** e a página Configuração do Parâmetro da Atividade aparecerá.
1. **Set-AzureRmContext** tem vários conjuntos de parâmetros, portanto, você precisa selecionar um antes de poder fornecer os valores do parâmetro. Clique em **Conjunto de Parâmetros** e selecione o conjunto de parâmetros **SubscriptionId**.
1. Depois de selecionar o conjunto de parâmetros, os parâmetros serão exibidos na página Configuração do Parâmetro da Atividade. Clique em **SubscriptionID**
1. Na página de Valor do Parâmetro, selecione **Ativo de Variável** para a **Fonte de dados**, selecione **AzureSubscriptionId** na lista e clique em **OK** duas vezes.
1. Passe o mouse sobre **Login no Azure** até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta para **Especificar Id de Assinatura**.

Seu runbook deve ser semelhante ao seguinte:  <br>![Configuração da autenticação do runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="add-activity-to-start-a-vm"></a>Adicionar atividade para iniciar uma VM

Aqui, adicione uma atividade **Start-AzureRmVM** para iniciar uma máquina virtual. Você pode escolher qualquer máquina virtual na sua assinatura do Azure e, por enquanto, deve embutir esse nome no cmdlet.

1. No controle Biblioteca, digite **Start-AzureRm** na caixa de texto de pesquisa.
2. Adicione **Start-AzureRmVM** à tela, clique e arraste-a para baixo de **Especificar Id de Assinatura**.
1. Passe o mouse sobre **Especificar Id de Assinatura** até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta para **Start-AzureRmVM**.
1. Selecione **Start-AzureRmVM**. Clique em **Parâmetros** e **Conjunto de Parâmetros** para exibir os conjuntos de **Start-AzureRmVM**. Selecione o conjunto de parâmetros **ResourceGroupNameParameterSetName** . Há pontos de exclamação ao lado de **ResourceGroupName** e **Nome**. Isso indica que são os parâmetros obrigatórios. Observe também que ambos esperam valores da cadeia de caracteres.
1. Selecione **Name**. Selecione **Expressão do PowerShell** para a **Fonte de dados** e digite o nome da máquina virtual entre aspas duplas com a qual você inicia este runbook. Clique em **OK**.
1. Selecione **ResourceGroupName**. Use a a **Expressão do PowerShell** para o **Fonte de dados** e digite o nome do grupo de recursos entre aspas duplas. Clique em **OK**.
1. Clique em Painel de teste para que você possa testar o runbook.
1. Clique em **Iniciar** para iniciar o teste. Quando for concluído, verifique se a máquina virtual foi iniciada.

Seu runbook deve ser semelhante ao seguinte:  <br>![Configuração da autenticação do runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="add-additional-input-parameters"></a>Adicionar parâmetros de entrada adicionais

Nosso runbook atualmente inicia a máquina virtual no grupo de recursos especificado no cmdlet **Start-AzureRmVM**. O runbook seria mais útil se ambos pudessem ser especificados quando o runbook é iniciado. Agora, adicione parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Abra o editor gráfico clicando em **Editar** no painel **MyFirstRunbook-Graphical**.
1. Selecione **Entrada e saída** e em **Adicionar entrada** para abrir o painel Parâmetro de Entrada do Runbook.
1. Especifique *VMName* para o **Nome**. Mantenha a *cadeia de caracteres* para o **Tipo**, mas altere **Obrigatório** para *Sim*. Clique em **OK**.
1. Crie um segundo parâmetro de entrada obrigatório denominado *ResourceGroupName* e clique em **OK** para fechar o painel de **Entrada e Saída**.<br> ![Parâmetros de Entrada de Runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Selecione a atividade **Start-AzureRmVM** e clique em **Parâmetros**.
1. Altere a **Fonte de dados** de **Name** para a **Entrada do runbook** e selecione **VMName**.
1. Altere a **Fonte de dados** de **ResourceGroupName** para a **Entrada do runbook** e selecione **ResourceGroupName**.<br> ![Parâmetros de Start-AzureVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Salve o runbook e abra o Painel de teste. Agora você pode fornecer valores para as duas variáveis de entrada usadas no teste.
1. Feche o Painel de teste.
1. Clique em **Publicar** para publicar a nova versão do runbook.
1. Pare a máquina virtual que você iniciou na etapa anterior.
1. Clique em **Iniciar** para iniciar o runbook. Digite o **VMName** e o **ResourceGroupName** da máquina virtual que você iniciará.
1. Quando o runbook for concluído, verifique se a máquina virtual foi iniciada.

## <a name="create-a-conditional-link"></a>Criar um link condicional

Agora, modifique o runbook para que ele tente iniciar a máquina virtual apenas se ela ainda não tiver iniciado. Faça isso adicionando um cmdlet **Get-AzureRmVM** ao runbook que obtém o status do nível da instância da máquina virtual. Em seguida, adicione um módulo de código do Fluxo de Trabalho do PowerShell denominado **Obter Status** com um snippet de código do PowerShell para determinar se o estado da máquina virtual está em execução ou parado. Um link condicional do módulo **Obter Status** apenas executa **Start-AzureRmVM** se o estado de execução atual é interrompido. Por fim, envie uma mensagem para informar se a VM foi iniciada com êxito ou não usando o cmdlet Write-Output do PowerShell.

1. Abra o **MyFirstRunbook-Graphical** no editor gráfico.
1. Remova o link entre **Especificar Id de Assinatura** e **Start-AzureRmVM** clicando nele e pressionando a tecla *Delete*.
1. No controle Biblioteca, digite **Get-AzureRm** na caixa de texto de pesquisa.
1. Adicione **Get-AzureRmVM** à tela.
1. Selecione **Get-AzureRmVM** e **Conjunto de Parâmetros** para exibir os conjuntos de **Get-AzureRmVM**. Selecione o conjunto de parâmetros **GetVirtualMachineInResourceGroupNameParamSet** . Há pontos de exclamação ao lado de **ResourceGroupName** e **Nome**. Isso indica que são os parâmetros obrigatórios. Observe também que ambos esperam valores da cadeia de caracteres.
1. Em **Fonte de dados** para **Name**, selecione **Entrada do runbook** e, em seguida, selecione **VMName**. Clique em **OK**.
1. Em **Fonte de dados** para **ResourceGroupName**, selecione **Entrada do runbook** e, em seguida, selecione **ResourceGroupName**. Clique em **OK**.
1. Em **Fonte de dados** para **Status**, selecione **Valor constante** e, em seguida, clique em **True**. Clique em **OK**.
1. Criar um link de **Especificar Id da Assinatura** para **Get-AzureRmVM**.
1. No controle da biblioteca, expanda **Controle do runbook** e adicione o **Código** à tela.  
1. Crie um link de **Get-AzureRmVM** para **Código**.  
1. Clique em **Código** e no painel de Configuração, altere o rótulo para **Obter Status**.
1. Selecione o parâmetro **Código** e a página **Editor de Código** aparecerá.  
1. No editor de código, cole o seguinte snippet de código:

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Crie um link de **Obter Status** para **Start-AzureRmVM**.<br> ![Runbook com Módulo de Código](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Selecione o link e no painel de Configuração, altere **Aplicar condição** para **Sim**. Observe que o link muda para uma linha tracejada, indicando que a atividade de destino é executada apenas se a condição é resolvida como verdadeira.  
1. Para a **Expressão da condição**, digite *$ActivityOutput['Get Status'] -eq "Stopped"*. **Start-AzureRmVM** é executada apenas se a máquina virtual está parada.
1. No controle de biblioteca, expanda **Cmdlets** e **Microsoft.PowerShell.Utility**.
1. Adicione **Write-Output** à tela duas vezes.
1. No primeiro controle **Write-Output**, clique em **Parâmetros** e altere o valor **Rótulo** para *Notificar VM Iniciada*.
1. Para **InputObject**, altere a **Fonte de dados** para **Expressão do PowerShell** e digite a expressão *"$VMName.Name iniciada com êxito."*.
1. No segundo controle **Write-Output**, clique em **Parâmetros** e altere o valor **Rótulo** para *Falha ao Notificar VM Iniciada*
1. Para **InputObject**, altere a **Fonte de dados** para a **Expressão do PowerShell** e digite a expressão *"$VMName não pôde iniciar."*.
1. Criar um link de **Start-AzureRmVM** para **Notificar VM Iniciada** e **Falha ao Notificar VM Iniciada**.
1. Selecione o link para **Notificar VM Iniciada** e altere **Aplicar condição** para **True**.
1. Para a **Expressão da condição**, digite *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*. Agora, o controle Write-Output é executado apenas se a máquina virtual é iniciada com êxito.
1. Selecione o link para **Falha ao Notificar VM Iniciada** e altere **Aplicar condição** para **True**.
1. Para a **Expressão da condição**, digite *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*. Agora, o controle Write-Output será executado apenas se a máquina virtual não for iniciada com êxito. Seu runbook deve ser semelhante à seguinte imagem: <br> ![Runbook com Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Salve o runbook e abra o Painel de teste.
1. Inicie o runbook com a máquina virtual parada e ela deve ser iniciada.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a Criação Gráfica, veja [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)
* Para começar a usar os runbooks do PowerShell, veja [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md)
* Para começar a usar runbooks de fluxo de trabalho do PowerShell, veja [Meu primeiro runbook de Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)


