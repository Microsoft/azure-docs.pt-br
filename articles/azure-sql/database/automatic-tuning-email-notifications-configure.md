---
title: Guia de instruções sobre o ajuste automático de notificações por email
description: Habilite notificações por email para o ajuste automático de consulta do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/03/2019
ms.openlocfilehash: a373a28a180b2a6c72f6a291b9d1437a2e88d9ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96500947"
---
# <a name="email-notifications-for-automatic-tuning"></a>Notificações por email para ajuste automático
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


As recomendações de ajuste do banco de dados SQL do Azure são geradas pelo [ajuste automático](automatic-tuning-overview.md)do banco de dados SQL. Essa solução monitora e analisa continuamente as cargas de trabalho de bancos de dados que fornecem recomendações de ajuste personalizadas para cada banco de dados individual relacionado à criação de índice, exclusão de índice e otimização de planos de execução de consulta.

As recomendações de ajuste automático do banco de dados SQL do Azure podem ser exibidas na [portal do Azure](database-advisor-find-recommendations-portal.md), recuperadas com chamadas à [API REST](/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) ou usando comandos [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) e [PowerShell](/powershell/module/az.sql/get-azsqldatabaserecommendedaction) . Este artigo se baseia no uso de um script de PowerShell para recuperar as recomendações de ajuste automático.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatizar notificações por email para recomendações de ajuste automático

A solução a seguir automatiza o envio de notificações por email que contêm recomendações de ajuste automático. A solução descrita consiste em automatizar a execução de um script de PowerShell para recuperar as recomendações de ajuste usando a [Automação do Azure](../../automation/automation-intro.md) e automação de agendamento de trabalho de entrega de email usando o [Microsoft Flow](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Criar conta de Automação do Azure

Para usar a Automação do Azure, a primeira etapa é criar uma conta de automação e configurá-la com os recursos do Azure a serem usados para a execução do script de PowerShell. Para saber mais sobre a Automação do Azure e seus recursos, consulte [Introdução à Automação do Azure](../../automation/index.yml).

Siga estas etapas para criar uma conta de automação do Azure por meio do método de seleção e configuração de um aplicativo de automação do Azure Marketplace:

1. Faça logon no portal do Azure.
1. Clique em "**+ criar um recurso**" no canto superior esquerdo.
1. Pesquise "**automação**" (pressione Enter).
1. Clique no aplicativo de automação nos resultados da pesquisa.

    ![Adicionar Automação do Azure](./media/automatic-tuning-email-notifications-configure/howto-email-01.png)

1. Uma vez dentro do painel "criar uma conta de automação", clique em "**criar**".
1. Preencha as informações necessárias: Insira um nome para esta conta de automação, selecione sua ID de assinatura do Azure e os recursos do Azure a serem usados para a execução do script do PowerShell.
1. Para a opção "**criar conta Executar como do Azure**", selecione **Sim** para configurar o tipo de conta sob o qual o script do PowerShell é executado com a ajuda da automação do Azure. Para saber mais sobre os tipos de conta, consulte [Executar como conta](../../automation/manage-runas-account.md).
1. Conclua a criação da conta de automação clicando em **criar**.

> [!TIP]
> Registre o nome da conta de Automação do Azure, ID da assinatura e recursos (como copiar e colar em um bloco de notas) exatamente como digitado ao criar o aplicativo Automação. Você precisará dessas informações mais adiante.

Se tiver várias assinaturas do Azure para as quais você deseja criar a mesma automação, você precisa repetir este processo para as outras assinaturas.

## <a name="update-azure-automation-modules"></a>Atualizar Módulos de Automação do Azure

O script do PowerShell para recuperar a recomendação de ajuste automático usa os comandos [Get-AzResource](/powershell/module/az.Resources/Get-azResource) e [Get-AzSqlDatabaseRecommendedAction](/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) para os quais o módulo do Azure versão 4 e superior é necessário.

- Caso os módulos do Azure precisem de atualização, consulte [AZ module support in Azure Automation](../../automation/shared-resources/modules.md).

## <a name="create-azure-automation-runbook"></a>Criar runbook de automação do Azure

A próxima etapa é criar um Runbook na Automação do Azure no qual reside o script de PowerShell para recuperação de recomendações de ajuste.

Siga as etapas a seguir para criar um novo runbook da Automação do Azure:

1. Acesse a conta de automação do Azure criada na etapa anterior.
1. Uma vez no painel da conta de automação, clique no item de menu "**Runbooks**" no lado esquerdo para criar um novo runbook de automação do Azure com o script do PowerShell. Para saber mais sobre como criar runbooks de automação, confira [criar um novo runbook](../../automation/manage-runbooks.md#create-a-runbook).
1. Para adicionar um novo runbook, clique na opção de menu "**+ Adicionar um runbook**" e, em seguida, clique em "**criação rápida – criar um novo runbook**".
1. No painel runbook, digite o nome do seu runbook (para fins deste exemplo, "**AutomaticTuningEmailAutomation**" é usado), selecione o tipo de runbook como **PowerShell** e grave uma descrição desse runbook para descrever sua finalidade.
1. Clique no botão **criar** para concluir a criação de um novo runbook.

    ![Adicionar o Runbook de Automação do Azure](./media/automatic-tuning-email-notifications-configure/howto-email-03.png)

Siga estas etapas para carregar um script de PowerShell dentro do runbook criado:

1. Dentro do painel "**Editar runbook do PowerShell**", selecione "**RUNBOOKS**" na árvore de menu e expanda a exibição até ver o nome do runbook (neste exemplo, "**AutomaticTuningEmailAutomation**"). Selecione este runbook.
1. Na primeira linha do "Editar runbook do PowerShell" (começando com o número 1), copie-cole o seguinte código de script do PowerShell. Este script do PowerShell é fornecido no estado em que se encontra para você começar. Modifique o script para que se adeque às suas necessidades.

No cabeçalho do script do PowerShell fornecido, você precisa substituir `<SUBSCRIPTION_ID_WITH_DATABASES>` por sua ID de assinatura do Azure. Para saber como recuperar a ID de assinatura do Azure, consulte [Obter a GUID de assinatura do Azure](/archive/blogs/mschray/getting-your-azure-subscription-guid-new-portal).

No caso de várias assinaturas, você pode adicioná-las como delimitado por vírgula à propriedade "$subscriptions" no cabeçalho do script.

```powershell
# PowerShell script to retrieve Azure SQL Database automatic tuning recommendations.
#
# Provided "as-is" with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Clique no botão "**salvar**" no canto superior direito para salvar o script. Quando estiver satisfeito com o script, clique no botão "**publicar**" para publicar esse runbook.

No painel principal do runbook, você pode optar por clicar no botão "**Iniciar**" para **testar** o script. Clique na "**saída**" para exibir os resultados do script executado. Essa saída será o conteúdo do seu email. O exemplo de saída do script pode ser visto na captura de tela a seguir.

![Executar a exibição de recomendações de ajuste automático da Automação do Azure](./media/automatic-tuning-email-notifications-configure/howto-email-04.png)

Certifique-se de ajustar o conteúdo personalizando o script de PowerShell para suas necessidades.

Com as etapas acima, o script do PowerShell para recuperar as recomendações de ajuste automático é carregado na automação do Azure. A próxima etapa é automatizar e agendar o trabalho de entrega de email.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatizar os trabalhos de email com o Microsoft Flow

Para concluir a solução, como etapa final, crie um fluxo de automação no Microsoft Flow que consista em três ações (trabalhos):

- "**Automação do Azure-criar trabalho**" – usado para executar o script do PowerShell para recuperar as recomendações de ajuste automático dentro do runbook de automação do Azure.
- "**Automação do Azure-obter saída do trabalho**" – usada para recuperar a saída do script do PowerShell executado.
- "**Office 365 Outlook – enviar um email**" – usado para enviar emails. Os emails são enviados usando a conta corporativa ou de estudante do indivíduo que cria o fluxo.

Para saber mais sobre os recursos do Microsoft Flow, consulte [Introdução ao Microsoft Flow](/flow/getting-started).

O pré-requisito para esta etapa é inscrever-se em uma conta de [Microsoft Flow](https://flow.microsoft.com) e fazer logon. Uma vez dentro da solução, siga estas etapas para configurar um **novo fluxo**:

1. Acesse o item de menu "**meus fluxos**".
1. Dentro de meus fluxos, selecione o link "**+ criar de branco**" na parte superior da página.
1. Clique no link "**Pesquisar por centenas de conectores e gatilhos**" na parte inferior da página.
1. No campo de pesquisa, digite "**recorrência**" e selecione "**agenda-recorrência**" nos resultados da pesquisa para agendar a execução do trabalho de entrega de email.
1. No painel Recorrência no campo Frequência, selecione a frequência de agendamento para este fluxo de execução, como enviar email automatizado a cada Minuto, Hora, Dia, Semana, etc.

A próxima etapa é adicionar três trabalhos (criar, obter saída e enviar email) ao fluxo recorrente recém-criado. Para realizar a adição dos trabalhos necessários para o fluxo, siga estas etapas:

1. Crie uma ação para executar o script de PowerShell para recuperar as recomendações de ajuste

   - Selecione "**+ nova etapa**", seguida por "**Adicionar uma ação**" dentro do painel fluxo de recorrência.
   - No campo de pesquisa, digite "**automação**" e selecione "**automação do Azure – criar trabalho**" nos resultados da pesquisa.
   - No painel de trabalho Criar, configure as propriedades do trabalho. Para essa configuração, você precisará de detalhes da sua ID de assinatura do Azure, grupo de recursos e conta de automação **registrada anteriormente** no **painel da conta de automação**. Para saber mais sobre as opções disponíveis nesta seção, consulte [Automação do Azure – criar trabalho](/connectors/azureautomation/#create-job).
   - Conclua a criação dessa ação clicando em "**salvar fluxo**".

2. Criar uma ação para recuperar a saída do script do PowerShell executado

   - Selecione "**+ nova etapa**", seguida por "**Adicionar uma ação**" dentro do painel fluxo de recorrência
   - No campo de pesquisa, digite "**automação**" e selecione "**automação do Azure – obter saída do trabalho**" nos resultados da pesquisa. Para saber mais sobre as opções disponíveis nesta seção, consulte [Automação do Azure – obter saída do trabalho](/connectors/azureautomation/#get-job-output).
   - Preencher campos necessários (semelhante à criação do trabalho anterior) – preencha sua ID de assinatura do Azure, grupo de recursos e conta de automação (conforme inserido no painel da conta de automação).
   - Clique dentro do campo "**ID do trabalho**" para que o menu "**conteúdo dinâmico**" apareça. No menu, selecione a opção "ID do **trabalho**".
   - Conclua a criação dessa ação clicando em "**salvar fluxo**".

3. Criar uma ação para enviar email usando a integração do Office 365

   - Selecione "**+ nova etapa**", seguida por "**Adicionar uma ação**" dentro do painel fluxo de recorrência.
   - No campo de pesquisa, digite "**enviar um email**" e selecione "**Office 365 Outlook – enviar um email**" nos resultados da pesquisa.
   - No campo "**para**", digite o endereço de email para o qual você precisa enviar o email de notificação.
   - No campo "**assunto**", digite o assunto do seu email, por exemplo "notificação por email de recomendações de ajuste automático".
   - Clique dentro do campo "**corpo**" para que o menu "**conteúdo dinâmico**" apareça. A partir desse menu, em "**obter saída do trabalho**", selecione "**conteúdo**".
   - Conclua a criação dessa ação clicando em "**salvar fluxo**".

> [!TIP]
> Para enviar emails automatizados para diferentes destinatários, crie fluxos separados. Nesses fluxos adicionais, altere o endereço de email do destinatário no campo "para" e a linha de assunto do email no campo "assunto". A criação de novos runbooks na automação do Azure com scripts personalizados do PowerShell (como com a alteração da ID de assinatura do Azure) permite a personalização adicional de cenários automatizados, como por exemplo, enviando por email destinatários separados sobre recomendações de ajuste automatizado para assinaturas separadas.

As instruções acima concluem as etapas necessárias para configurar o fluxo de trabalho de entrega de email. O fluxo inteiro que consiste na criação de três ações é mostrado na imagem a seguir.

![Exibir o fluxo de notificações por email para ajuste automático](./media/automatic-tuning-email-notifications-configure/howto-email-05.png)

Para testar o fluxo, clique em "**executar agora**" no canto superior direito dentro do painel fluxo.

Estatísticas da execução das tarefas automatizadas, mostrando o êxito de notificações por email enviadas, podem ser vistas no painel de análise do Flow.

![Executar o fluxo de notificações por email para ajuste automático](./media/automatic-tuning-email-notifications-configure/howto-email-06.png)

O painel análise de fluxo é útil para monitorar o sucesso das execuções de trabalho e, se necessário, para solução de problemas.  No caso de solução de problemas, você também pode querer examinar o log de execução de script do PowerShell acessível por meio do aplicativo de automação do Azure.

A saída final do email automatizado é semelhante ao seguinte email recebido depois de criar e executar essa solução:

![Amostra de saída de email de notificações por email para ajuste automático](./media/automatic-tuning-email-notifications-configure/howto-email-07.png)

Ajustando o script do PowerShell, você pode ajustar a saída e a formatação do email automatizado às suas necessidades.

Você pode personalizar ainda mais a solução para criar notificações por email com base em um evento de ajuste específico e para vários destinatários, várias assinaturas ou bancos de dados, dependendo de seus cenários personalizados.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como o ajuste automático pode ajudar a melhorar o desempenho do banco de dados, consulte [Ajuste automático no Banco de Dados SQL do Azure](automatic-tuning-overview.md).
- Para habilitar o ajuste automático no Banco de Dados SQL do Azure para gerenciar a carga de trabalho, consulte [Habilitar ajuste automático](automatic-tuning-enable.md).
- Para examinar e aplicar manualmente as recomendações de ajuste automático, consulte [Localizar e aplicar recomendações de desempenho](database-advisor-find-recommendations-portal.md).