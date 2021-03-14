---
title: Monitorar Azure AD B2C com Azure Monitor
titleSuffix: Azure AD B2C
description: Saiba como registrar Azure AD B2C eventos com Azure Monitor usando o gerenciamento de recursos delegado.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 01/29/2021
ms.openlocfilehash: 712a933276393890bf017a2517196031306233ad
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573006"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Monitorar Azure AD B2C com Azure Monitor

Use Azure Monitor para rotear logs de entrada e [auditoria](view-audit-logs.md) de Azure Active Directory B2C (Azure ad B2C) para diferentes soluções de monitoramento. Você pode reter os logs para uso de longo prazo ou integrá-lo com ferramentas de gerenciamento de eventos e informações de segurança (SIEM) de terceiros para obter informações sobre seu ambiente.

Você pode rotear eventos de log para:

* Uma [conta de armazenamento](../storage/blobs/storage-blobs-introduction.md)do Azure.
* Um [espaço de trabalho log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) (para analisar dados, criar painéis e alertar sobre eventos específicos).
* Um [Hub de eventos](../event-hubs/event-hubs-about.md) do Azure (e integre com suas instâncias de lógica Splunk e do resumo).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

Neste artigo, você aprende a transferir os logs para um espaço de trabalho do Azure Log Analytics. Em seguida, você pode criar um painel ou criar alertas baseados em atividades Azure AD B2C usuários.

> [!IMPORTANT]
> Quando você planeja transferir logs de Azure AD B2C para diferentes soluções de monitoramento ou repositório, considere o seguinte. Os logs de Azure AD B2C contêm dados pessoais. Esses dados devem ser processados de uma maneira que garanta a segurança apropriada dos dados pessoais, incluindo proteção contra processamento não autorizado ou ilegal, usando medidas técnicas ou organizacionais apropriadas.


## <a name="deployment-overview"></a>Visão geral da implantação

Azure AD B2C aproveita [Azure Active Directory monitoramento](../active-directory/reports-monitoring/overview-monitoring.md). Para habilitar *as configurações de diagnóstico* no Azure Active Directory em seu locatário do Azure ad B2C, use o [Lighthouse do Azure](../lighthouse/concepts/azure-delegated-resource-management.md) para [delegar um recurso](../lighthouse/concepts/azure-delegated-resource-management.md), que permite que seu Azure ad B2C (o **provedor de serviços**) gerencie um recurso do Azure AD (o **cliente**). Depois de concluir as etapas neste artigo, você terá acesso ao grupo de recursos *Azure-ad-B2C-monitor* que contém o espaço de [trabalho log Analytics](../azure-monitor/logs/quick-create-workspace.md) em seu portal de **Azure ad B2C** . Você também poderá transferir os logs de Azure AD B2C para seu espaço de trabalho do Log Analytics.

Durante essa implantação, você autorizará um usuário ou grupo em seu diretório de Azure AD B2C para configurar a instância do espaço de trabalho Log Analytics dentro do locatário que contém sua assinatura do Azure. Para criar a autorização, você implanta um modelo de [Azure Resource Manager](../azure-resource-manager/index.yml) no seu locatário do Azure AD que contém a assinatura.

O diagrama a seguir ilustra os componentes que você configurará em seu Azure AD e Azure AD B2C locatários.

![Projeção do grupo de recursos](./media/azure-monitor/resource-group-projection.png)

Durante essa implantação, você configurará seu locatário Azure AD B2C e o locatário do Azure AD onde o espaço de trabalho Log Analytics será hospedado. A conta usada para executar a implantação deve ser atribuída à função de [administrador global](../active-directory/roles/permissions-reference.md#limit-use-of-global-administrator) em ambos os locatários. Também é importante verificar se você está conectado ao diretório correto ao concluir cada etapa, conforme descrito.

## <a name="1-create-or-choose-resource-group"></a>1. criar ou escolher grupo de recursos

Primeiro, crie ou escolha um grupo de recursos que contenha o espaço de trabalho Log Analytics de destino que receberá dados do Azure AD B2C. Você especificará o nome do grupo de recursos ao implantar o modelo de Azure Resource Manager.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém seu **locatário do Azure ad**.
1. [Crie um grupo de recursos](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) ou escolha um existente. Este exemplo usa um grupo de recursos chamado *Azure-ad-B2C-monitor*.

## <a name="2-create-a-log-analytics-workspace"></a>2. criar um espaço de trabalho Log Analytics

Um **espaço de trabalho log Analytics** é um ambiente exclusivo para dados de Azure monitor log. Você usará esse espaço de trabalho Log Analytics para coletar dados de Azure AD B2C [logs de auditoria](view-audit-logs.md)e, em seguida, visualizá-los com consultas e pastas de trabalho ou criar alertas.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém seu **locatário do Azure ad**.
1. [Crie um espaço de trabalho log Analytics](../azure-monitor/logs/quick-create-workspace.md). Este exemplo usa um espaço de trabalho Log Analytics chamado *AzureAdB2C*, em um grupo de recursos chamado *Azure-ad-B2C-monitor*.

## <a name="3-delegate-resource-management"></a>3. delegar o gerenciamento de recursos

Nesta etapa, você escolhe seu locatário Azure AD B2C como um **provedor de serviços**. Você também define as autorizações necessárias para atribuir as funções internas do Azure apropriadas a grupos em seu locatário do Azure AD.

### <a name="31-get-your-azure-ad-b2c-tenant-id"></a>3,1 obter sua ID de locatário Azure AD B2C

Primeiro, obtenha a **ID do locatário** do seu diretório de Azure ad B2C (também conhecido como a ID do diretório).

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e selecione o diretório que contém seu locatário **Azure ad B2C** .
1. Selecione **Azure Active Directory**, selecione **visão geral**.
1. Registre a **ID do locatário**.

### <a name="32-select-a-security-group"></a>3,2 selecionar um grupo de segurança

Agora, selecione um grupo de Azure AD B2C ou usuário ao qual você deseja conceder permissão para o grupo de recursos que você criou anteriormente no diretório que contém sua assinatura.  

Para facilitar o gerenciamento, é recomendável usar *grupos* de usuários do Azure ad para cada função, permitindo que você adicione ou remova usuários individuais do grupo em vez de atribuir permissões diretamente a esse usuário. Neste tutorial, vamos adicionar um grupo de segurança.

> [!IMPORTANT]
> Para adicionar permissões para um grupo do Azure AD, o **tipo de grupo** deve ser definido como **segurança**. Essa opção é selecionada quando o grupo é criado. Para obter mais informações, consulte [Criar um grupo básico e adicionar membros usando o Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. Com **Azure Active Directory** ainda selecionado no diretório **Azure ad B2C** , selecione **grupos** e, em seguida, selecione um grupo. Se você não tiver um grupo existente, crie um grupo de **segurança** e, em seguida, adicione membros. Para obter mais informações, siga o procedimento [criar um grupo básico e adicionar membros usando Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 
1. Selecione **visão geral** e registre a ID de **objeto** do grupo.

### <a name="33-create-an-azure-resource-manager-template"></a>3,3 criar um modelo de Azure Resource Manager

Em seguida, você criará um modelo de Azure Resource Manager que concede Azure AD B2C acesso ao grupo de recursos do Azure AD que você criou anteriormente (por exemplo, *Azure-ad-B2C-monitor*). Implante o modelo do exemplo do GitHub usando o botão **implantar no Azure** , que abre a Portal do Azure e permite que você configure e implante o modelo diretamente no Portal. Para essas etapas, verifique se você está conectado ao seu locatário do Azure AD (não o locatário Azure AD B2C).

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém seu locatário **do Azure ad** .
3. Use o botão **implantar no Azure** para abrir o portal do Azure e implantar o modelo diretamente no Portal. Para obter mais informações, consulte [criar um modelo de Azure Resource Manager](../lighthouse/how-to/onboard-customer.md#create-an-azure-resource-manager-template).

   [![Implantar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Lighthouse-samples%2Fmaster%2Ftemplates%2Frg-delegated-resource-management%2FrgDelegatedResourceManagement.json)

5. Na página **implantação personalizada** , insira as seguintes informações:

   | Campo   | Definição |
   |---------|------------|
   | Subscription |  Selecione o diretório que contém a assinatura do Azure na qual o grupo de recursos *Azure-ad-B2C-monitor* foi criado. |
   | Região| Selecione a região em que o recurso será implantado.  | 
   | Nome da oferta MSP| Um nome que descreve essa definição. Por exemplo, *Azure ad B2C monitoramento*.  |
   | Descrição da oferta MSP| Uma breve descrição da sua oferta. Por exemplo, *habilita Azure monitor em Azure ad B2C*.|
   | Gerenciado por ID de locatário| A **ID de locatário** do seu locatário de Azure ad B2C (também conhecido como a ID de diretório). |
   |Autorizações|Especifique uma matriz JSON de objetos que incluam o Azure AD `principalId` , o `principalIdDisplayName` e o Azure `roleDefinitionId` . O `principalId` é a **ID de objeto** do grupo B2C ou usuário que terá acesso aos recursos nesta assinatura do Azure. Para este guia, especifique a ID de objeto do grupo que você registrou anteriormente. Para o `roleDefinitionId` , use o valor da [função interna](../role-based-access-control/built-in-roles.md) para a *função colaborador*, `b24988ac-6180-42a0-ab88-20f7382dd24c` .|
   | Nome do Rg | O nome do grupo de recursos que você cria anteriormente no seu locatário do Azure AD. Por exemplo, *Azure-ad-B2C-monitor*. |

   O exemplo a seguir demonstra uma matriz de autorizações com um grupo de segurança.

   ```json
   [
       {
           "principalId": "<Replace with group's OBJECT ID>",
           "principalIdDisplayName": "Azure AD B2C tenant administrators",
           "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
       }
   ]
   ```

Depois de implantar o modelo, pode levar alguns minutos (normalmente não mais do que cinco) para que a projeção de recursos seja concluída. Você pode verificar a implantação em seu locatário do Azure AD e obter os detalhes da projeção de recursos. Para obter mais informações, consulte [Exibir e gerenciar provedores de serviços](../lighthouse/how-to/view-manage-service-providers.md).

## <a name="4-select-your-subscription"></a>4. Selecione sua assinatura

Depois de implantar o modelo e aguardar alguns minutos para que a projeção de recursos seja concluída, siga estas etapas para associar sua assinatura ao seu diretório de Azure AD B2C.

1. Saia do portal do Azure se você estiver conectado no momento (isso permite que suas credenciais de sessão sejam atualizadas na próxima etapa).
2. Entre no [portal do Azure](https://portal.azure.com) com sua conta administrativa do **Azure ad B2C** . Essa conta deve ser um membro do grupo de segurança especificado na etapa de [Gerenciamento de recursos de representante](#3-delegate-resource-management) .
3. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal.
4. Selecione o diretório do Azure AD que contém a assinatura do Azure e o grupo de recursos *Azure-ad-B2C-monitor* que você criou.

    ![Mudar diretório](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)

1. Verifique se você selecionou o diretório e a assinatura corretos. Neste exemplo, todos os diretórios e todas as assinaturas são selecionados.

    ![Todos os diretórios selecionados no diretório & filtro de assinatura](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="5-configure-diagnostic-settings"></a>5. definir configurações de diagnóstico

As configurações de diagnóstico definem onde os logs e as métricas de um recurso devem ser enviados. Os possíveis destinos são:

- [Conta de Armazenamento do Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)
- Soluções de [hubs de eventos](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)
- [Espaço de Trabalho do Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)

Neste exemplo, usamos o espaço de trabalho Log Analytics para criar um painel.

### <a name="51-create-diagnostic-settings"></a>5,1 criar configurações de diagnóstico

Você está pronto para [criar configurações de diagnóstico](../active-directory/reports-monitoring/overview-monitoring.md) no portal do Azure.

Para definir configurações de monitoramento para Azure AD B2C logs de atividades:

1. Entre no [portal do Azure](https://portal.azure.com/) com sua conta administrativa do Azure ad B2C. Essa conta deve ser um membro do grupo de segurança especificado na etapa [selecionar um grupo de segurança](#32-select-a-security-group) .
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. Selecionar **Azure Active Directory**
1. Em **Monitoramento**, selecione **Configurações de diagnóstico**.
1. Se houver configurações existentes para o recurso, você verá uma lista de configurações já configuradas. Selecione **Adicionar configuração de diagnóstico** para adicionar uma nova configuração ou selecione **Editar** para editar uma configuração existente. Cada configuração não pode ter mais de um de cada um dos tipos de destino.

    ![Painel de configurações de diagnóstico no portal do Azure](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Dê um nome à sua configuração se ela ainda não tiver uma.
1. Marque a caixa para cada destino para enviar os logs. Selecione **Configurar** para especificar suas configurações **, conforme descrito na tabela a seguir**.
1. Selecione **Enviar para log Analytics** e, em seguida, selecione o **nome do espaço de trabalho** que você criou anteriormente ( `AzureAdB2C` ).
1. Selecione **AuditLogs** e **SignInLogs**.
1. Selecione **Salvar**.

> [!NOTE]
> Pode levar até 15 minutos depois que um evento é emitido para que ele [apareça em um espaço de trabalho log Analytics](../azure-monitor/logs/data-ingestion-time.md). Além disso, saiba mais sobre [Active Directory latências de relatório](../active-directory/reports-monitoring/reference-reports-latencies.md), que podem afetar a desatualização dos dados e desempenhar uma função importante no relatório.

Se você vir a mensagem de erro "para configurar as configurações de diagnóstico para usar Azure Monitor para seu diretório de Azure AD B2C, você precisará configurar o gerenciamento de recursos delegado", certifique-se de entrar com um usuário que seja membro do [grupo de segurança](#32-select-a-security-group) e [Selecione sua assinatura](#4-select-your-subscription).

## <a name="6-visualize-your-data"></a>6. Visualize seus dados

Agora você pode configurar seu espaço de trabalho Log Analytics para visualizar seus dados e configurar alertas. Essas configurações podem ser feitas tanto no locatário do Azure AD quanto no locatário do Azure AD B2C.

### <a name="61-create-a-query"></a>6,1 criar uma consulta

As consultas de log ajudam você a aproveitar tudo o que os dados coletados nos logs do Azure Monitor têm a oferecer. Uma linguagem de consulta eficiente permite unir dados de várias tabelas, agregar grandes conjuntos de dados e executar operações complexas com o mínimo de código. Praticamente qualquer pergunta pode ser respondida e a análise realizada desde que os dados de suporte tenham sido coletados e você entenda como construir a consulta correta. Para obter mais informações, consulte Introdução [às consultas de log em Azure monitor](../azure-monitor/logs/get-started-queries.md).

1. Em **log Analytics espaço de trabalho**, selecione **logs**
1. No editor de consultas, Cole a seguinte consulta de [linguagem de consulta Kusto](/azure/data-explorer/kusto/query/) . Essa consulta mostra o uso de políticas por operação nos últimos x dias. A duração padrão é definida como 90 dias (90d). Observe que a consulta é focada apenas na operação em que um token/código é emitido pela política.

    ```kusto
    AuditLogs
    | where TimeGenerated  > ago(90d)
    | where OperationName contains "issue"
    | extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
    | extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
    | summarize SignInCount = count() by Policy, OperationName
    | order by SignInCount desc  nulls last
    ```

1. Selecione **Executar**. Os resultados da consulta são exibidos na parte inferior da tela.
1. Para salvar sua consulta para uso posterior, selecione **salvar**.

   ![Log Analytics o editor de log](./media/azure-monitor/query-policy-usage.png)

1. Preencha os seguintes detalhes:

    - **Nome** – Insira o nome da sua consulta.
    - **Salvar como** -selecionar `query` .
    - **Categoria** -selecione `Log` .

1. Selecione **Salvar**.

Você também pode alterar sua consulta para visualizar os dados usando o operador [render](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) .

```kusto
AuditLogs
| where TimeGenerated  > ago(90d)
| where OperationName contains "issue"
| extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
| extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
| summarize SignInCount = count() by Policy
| order by SignInCount desc  nulls last
| render  piechart
```

![Pizza do Log Analytics editor de log](./media/azure-monitor/query-policy-usage-pie.png)

Para obter mais exemplos, consulte o [repositório GitHub do SIEM](https://aka.ms/b2csiem)Azure ad B2C.

### <a name="62-create-a-workbook"></a>6,2 criar uma pasta de trabalho

As pastas de trabalho fornecem uma tela flexível para análise de dados e a criação de relatórios visuais avançados no portal do Azure. Com elas, você pode explorar várias fontes de dados em todo o Azure e combiná-las em experiências interativas unificadas. Para obter mais informações, consulte [Azure monitor pastas de trabalho](../azure-monitor/visualize/workbooks-overview.md).

Siga as instruções abaixo para criar uma nova pasta de trabalho usando um modelo de galeria JSON. Esta pasta de trabalho fornece um painel de **informações do usuário** e de **autenticação** para Azure ad B2C locatário.

1. No **espaço de trabalho log Analytics**, selecione **pastas de trabalho**.
1. Na barra de ferramentas, selecione **+ nova** opção para criar uma nova pasta de trabalho.
1. Na página **nova pasta de trabalho** , selecione o **Editor avançado** usando a **</>** opção na barra de ferramentas.

     ![Modelo de galeria](./media/azure-monitor/wrkb-adv-editor.png)

1. Selecione **modelo da Galeria**.
1. Substitua o JSON no **modelo da Galeria**  pelo conteúdo da pasta de [trabalho do Azure ad B2C Basic](https://raw.githubusercontent.com/azure-ad-b2c/siem/master/workbooks/dashboard.json):
1. Aplique o modelo usando o botão **aplicar** .
1. Selecione o botão de **edição concluído** na barra de ferramentas para concluir a edição da pasta de trabalho.
1. Por fim, salve a pasta de trabalho usando o botão **salvar** da barra de ferramentas.
1. Forneça um **título**, como *Azure ad B2C Dashboard*.
1. Selecione **Salvar**.

    ![Salvar a pasta de trabalho](./media/azure-monitor/wrkb-title.png)

A pasta de trabalho exibirá relatórios na forma de um Dashboard.

![Primeiro painel da pasta de trabalho](./media/azure-monitor/wkrb-dashboard-1.png)

![Segundo painel da pasta de trabalho](./media/azure-monitor/wrkb-dashboard-2.png)

![Terceiro painel da pasta de trabalho](./media/azure-monitor/wrkb-dashboard-3.png)


## <a name="create-alerts"></a>Criar alertas

Alertas são criados por regras de alerta no Azure Monitor e podem executar automaticamente consultas salvas ou pesquisas de logs personalizadas em intervalos regulares. Crie alertas com base em métricas de desempenho específicas ou quando determinados eventos são criados, na ausência de um evento ou quando um número de eventos são criados em uma janela de tempo específica. Por exemplo, alertas podem ser usados para notificá-lo quando o número médio de entrada exceder um determinado limite. Para obter mais informações, consulte [Criar alertas](../azure-monitor/alerts/tutorial-response.md).


Use as instruções a seguir para criar um novo alerta do Azure, que enviará uma [notificação por email](../azure-monitor/alerts/action-groups.md#configure-notifications) sempre que houver um descarte de 25% no **total de solicitações** comparadas ao período anterior. O alerta será executado a cada 5 minutos e procurará o descarte nas últimas 24 horas do Windows. Os alertas são criados usando a linguagem de consulta Kusto.


1. Em **log Analytics espaço de trabalho**, selecione **logs**. 
1. Crie uma nova **consulta Kusto** usando a consulta abaixo.

    ```kusto
    let start = ago(24h);
    let end = now();
    let threshold = -25; //25% decrease in total requests.
    AuditLogs
    | serialize TimeGenerated, CorrelationId, Result
    | make-series TotalRequests=dcount(CorrelationId) on TimeGenerated in range(start, end, 1h)
    | mvexpand TimeGenerated, TotalRequests
    | where TotalRequests > 0
    | serialize TotalRequests, TimeGenerated, TimeGeneratedFormatted=format_datetime(todatetime(TimeGenerated), 'yyyy-M-dd [hh:mm:ss tt]')
    | project   TimeGeneratedFormatted, TotalRequests, PercentageChange= ((toreal(TotalRequests) - toreal(prev(TotalRequests,1)))/toreal(prev(TotalRequests,1)))*100
    | order by TimeGeneratedFormatted
    | where PercentageChange <= threshold   //Trigger's alert rule if matched.
    ```

1. Selecione **executar** para testar a consulta. Você deverá ver os resultados se houver uma queda de 25% ou mais no total de solicitações nas últimas 24 horas.
1. Para criar uma regra de alerta com base na consulta acima, use a opção **+ nova regra de alerta** disponível na barra de ferramentas.
1. Na página **criar uma regra de alerta** , selecione **nome da condição** 
1. Na página **Configurar lógica de sinal** , defina os valores a seguir e use o botão **concluído** para salvar as alterações.
    * Lógica de alerta: defina o **número de resultados** **maiores que** **0**.
    * Avaliação baseada em: selecione **1440** para o período (em minutos) e **5** para frequência (em minutos) 

    ![Criar uma condição de regra de alerta](./media/azure-monitor/alert-create-rule-condition.png)

Depois que o alerta for criado, vá para **log Analytics espaço de trabalho** e selecione **alertas**. Esta página exibe todos os alertas que foram disparados na opção duração definida por **intervalo de tempo** .  

### <a name="configure-action-groups"></a>Configurar grupos de ação

Alertas do Azure Monitor e da Integridade do Serviço usam grupos de ações para notificar usuários de que um alerta foi disparado. Você pode incluir o envio de uma chamada de voz, SMS, email; ou disparando vários tipos de ações automatizadas. Siga as orientações [criar e gerenciar grupos de ações no portal do Azure](../azure-monitor/alerts/action-groups.md)

Aqui está um exemplo de um email de notificação de alerta. 

   ![Notificação por email](./media/azure-monitor/alert-email-notification.png)

## <a name="multiple-tenants"></a>Vários locatários

Para integrar vários logs de locatário Azure AD B2C ao mesmo espaço de trabalho de Log Analytics (ou conta de armazenamento do Azure ou Hub de eventos), você precisará de implantações separadas com valores de **nome de oferta MSP** diferentes. Verifique se o espaço de trabalho do Log Analytics está no mesmo grupo de recursos que você configurou em [criar ou escolher grupo de recursos](#1-create-or-choose-resource-group).

Ao trabalhar com vários espaços de trabalho do Log Analytics, use a [consulta entre espaços de trabalho](../azure-monitor/logs/cross-workspace-query.md) para criar consultas que funcionam em vários espaços de trabalho. Por exemplo, a consulta a seguir executa uma junção de dois logs de auditoria de diferentes locatários com base na mesma categoria (por exemplo, autenticação):

```kusto
workspace("AD-B2C-TENANT1").AuditLogs
| join  workspace("AD-B2C-TENANT2").AuditLogs
  on $left.Category== $right.Category
```

## <a name="change-the-data-retention-period"></a>Alterar o período de retenção de dados

Os logs de Azure Monitor são projetados para dimensionar e dar suporte à coleta, indexação e armazenamento de grandes quantidades de dados por dia de qualquer fonte em sua empresa ou implantados no Azure. Por padrão, os logs são retidos por 30 dias, mas a duração da retenção pode ser aumentada para até dois anos. Saiba como [gerenciar o uso e os custos com logs de Azure monitor](../azure-monitor/logs/manage-cost-storage.md). Depois de selecionar o tipo de preço, você pode [alterar o período de retenção de dados](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period).

## <a name="next-steps"></a>Próximas etapas

* Encontre mais exemplos na galeria do [SIEM](https://aka.ms/b2csiem)Azure ad B2C. 

* Para obter mais informações sobre como adicionar e definir configurações de diagnóstico no Azure Monitor, consulte [tutorial: coletar e analisar logs de recursos de um recurso do Azure](../azure-monitor/essentials/monitor-azure-resource.md).

* Para obter informações sobre streaming de logs do Azure AD para um hub de eventos, consulte [tutorial: transmitir Azure Active Directory logs para um hub de eventos do Azure](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
