---
title: Acompanhar mensagens B2B com logs do Azure Monitor - aplicativos lógicos do Azure | Microsoft Docs
description: Rastrear comunicação B2B para sua conta de integração e os Aplicativos Lógicos do Azure com o Azure Log Analytics
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 8cf5d9f3ee1503769a2ec199847175899bcd86bf
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120119"
---
# <a name="track-b2b-messages-with-azure-monitor-logs"></a>Rastrear mensagens B2B com os logs do Azure Monitor

Depois de configurar a comunicação B2B entre parceiros comerciais em sua conta de integração, esses parceiros podem trocar mensagens com protocolos como AS2, X12 e EDIFACT. Para verificar se essas mensagens são processadas corretamente, você pode controlar essas mensagens com [registra em log do Azure Monitor](../log-analytics/log-analytics-overview.md). Por exemplo, você pode usar essas funcionalidades de acompanhamento baseado na Web para o acompanhamento de mensagens:

* Status e contagem de mensagens
* Status de confirmações
* Correlacionar mensagens com confirmações
* Descrição de erro detalhada para falhas
* Recursos de pesquisa

> [!NOTE]
> Anteriormente, esta página descrevia as etapas para executar essas tarefas com o OMS (Microsoft Operations Management Suite), que será [desativado em janeiro de 2019](../azure-monitor/platform/oms-portal-transition.md), substituindo essas etapas pelo Azure Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Um aplicativo lógico configurado com o log de diagnósticos. Saiba [como criar um aplicativo lógico](quickstart-create-first-logic-app-workflow.md) e [como configurar o log para esse aplicativo lógico](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Uma conta de integração configurada com o monitoramento e log. Saiba [como criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [como configurar o monitoramento e log para essa conta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Se você ainda não o fez [publicar dados de diagnóstico para os logs do Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

* Após atender aos requisitos anteriores, você também precisa de um espaço de trabalho do Log Analytics, que você usa para rastrear comunicação B2B por meio do Log Analytics. Se você não tiver um espaço de trabalho do Log Analytics, saiba[como criar um espaço de trabalho do Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

## <a name="install-logic-apps-b2b-solution"></a>Instalar a solução de B2B de aplicativos lógicos

Antes de você pode ter os logs do Azure Monitor rastrear mensagens B2B para seu aplicativo lógico, adicione a **B2B de aplicativos lógicos** solução aos logs do Azure Monitor. Saiba mais sobre [adicionando soluções para os logs do Azure Monitor](../azure-monitor/learn/quick-create-workspace.md).

1. No [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**. Na caixa de pesquisa, encontre "log analytics" e selecione **Log Analytics**.

   ![Selecione Log Analytics](media/logic-apps-track-b2b-messages-omsportal/find-log-analytics.png)

1. Em **Log Analytics**, localize e selecione o espaço de trabalho do Log Analytics. 

   ![Selecione o Espaço de Trabalho do Log Analytics](media/logic-apps-track-b2b-messages-omsportal/select-log-analytics-workspace.png)

1. Em **Introdução ao Log Analytics** > **Configurar soluções de monitoramento**, escolha **Exibir soluções**.

   ![Escolha "Exibir soluções"](media/logic-apps-track-b2b-messages-omsportal/log-analytics-workspace.png)

1. Na página de visão geral, escolha **Adicionar**, que abre a lista **Soluções de gerenciamento**. Na lista, selecione **Logic Apps B2B**. 

   ![Selecione a solução de B2B de aplicativos lógicos](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

   Se você não encontrar a solução, na parte inferior da lista, escolha **Carregar mais** até que a solução seja exibida.

1. Escolha **Criar**, confirme o espaço de trabalho do Log Analytics onde deseja instalar a solução e, em seguida, escolha **Criar** novamente.   

   ![Escolha "Criar" para B2B de aplicativos lógicos](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   Se você não quiser usar um espaço de trabalho existente, também poderá criar um novo espaço de trabalho neste momento.

1. Quando terminar, volte para a página **Visão geral** do seu espaço de trabalho. 

   A solução B2B do Logic Apps agora aparece na página Visão geral. 
   Quando mensagens B2B são processadas, a contagem de mensagens nesta página é atualizada.

<a name="message-status-details"></a>

## <a name="view-b2b-message-information"></a>Ver informações da mensagem B2B

Depois que as mensagens B2B são processadas, você pode visualizar o status e os detalhes dessas mensagens no bloco **Logic Apps B2B**.

1. Vá para seu espaço de trabalho de análise de lógica e abra a página de visão geral. Escolha **Logic Apps B2B**.

   ![Contagem de mensagens atualizada](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > Por padrão, o bloco **Logic Apps B2B** mostra dados com base em um único dia. Para alterar o escopo de dados para um intervalo diferente, escolha o controle de escopo na parte superior da página:
   > 
   > ![Alterar intervalo](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)

1. Após a exibição do painel de status de mensagens, você poderá exibir mais detalhes de um tipo de mensagem específico, que mostra dados com base em um único dia. Escolha o bloco **AS2**, **X12** ou **EDIFACT**.

   ![Exibir status da mensagem](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   Uma lista de mensagens é exibida para o bloco escolhido. 
   Para saber mais sobre as propriedades de cada tipo de mensagem, consulte estas descrições das propriedades de mensagens:

   * [Propriedades de mensagens AS2](#as2-message-properties)
   * [Propriedades de mensagens X12](#x12-message-properties)
   * [Propriedades de mensagens EDIFACT](#EDIFACT-message-properties)

   Por exemplo, veja abaixo a aparência de uma lista de mensagens AS2:

   ![Exibir mensagens AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Para exibir ou exportar as entradas e saídas de mensagens específicas, selecione as mensagens e escolha **Baixar**. Quando solicitado, salve o arquivo .zip no computador local e, em seguida, extraia o arquivo. 

   A pasta extraída inclui uma pasta para cada mensagem selecionada. 
   Se você configurar confirmações, a pasta de mensagens também incluirá arquivos com detalhes de confirmação. 
   Cada pasta de mensagens tem, pelo menos, estes arquivos: 
   
   * Arquivos legíveis por humanos com os detalhes de conteúdo de entrada e de saída
   * Arquivos codificados com as entradas e saídas

   Para cada tipo de mensagem, encontre formatos de nome de pasta e arquivo aqui:

   * [Formatos de nome de pasta e arquivo AS2](#as2-folder-file-names)
   * [Formatos de nome de pasta e arquivo X12](#x12-folder-file-names)
   * [Formatos de nome de pasta e arquivo EDIFACT](#edifact-folder-file-names)

   ![Baixar arquivos de mensagem](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Para exibir todas as ações que têm a mesma ID da execução, na página **Pesquisa de Logs**, escolha uma mensagem na lista de mensagens.

   Classifique essas ações por coluna ou pesquise resultados específicos.

   ![Ações com a mesma ID da execução](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Para pesquisar resultados com consultas pré-criadas, escolha **Favoritos**.

   * Saiba [como criar consultas adicionando filtros](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Ou Saiba mais sobre [como encontrar dados com pesquisas de log nos logs do Azure Monitor](../log-analytics/log-analytics-log-searches.md).

   * Para alterar a consulta na caixa de pesquisa, atualize a consulta com as colunas e os valores que você deseja usar como filtros.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Descrições de propriedade e formatos de nome de mensagens AS2, X12 e EDIFACT

Para cada tipo de mensagem, estas são as descrições de propriedade e os formatos de nome dos arquivos de mensagem baixados.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Descrições de propriedade da mensagem AS2

Estas são as descrições das propriedades de cada mensagem AS2.

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Remetente | O parceiro convidado especificado em **Configurações de Recebimento** ou o parceiro host especificado em **Configurações de Envio** de um contrato do AS2 |
| Receptor | O parceiro host especificado em **Configurações de Recebimento** ou o parceiro convidado em **Configurações de Envio** de um contrato do AS2 |
| Aplicativo Lógico | O aplicativo lógico no qual as ações do AS2 são configuradas |
| Status | O status da mensagem AS2 <br>Êxito = recebimento ou envio de uma mensagem AS2 válida. Nenhum MDN está configurado. <br>Êxito = recebimento ou envio de uma mensagem AS2 válida. O MDN está configurado e é recebido ou o MDN é enviado. <br>Com Falha = recebimento de uma mensagem AS2 inválida. Nenhum MDN está configurado. <br>Pendente = recebimento ou envio de uma mensagem AS2 válida. O MDN está configurado e o MDN é esperado. |
| Ack | O status da mensagem MDN <br>Aceito = recebimento ou envio de um MDN positivo. <br>Pendente = aguardando recebimento ou envio de um MDN. <br>Rejeitado = recebimento ou envio de um MDN negativo. <br>Não Obrigatório = o MDN não está configurado no contrato. |
| Direção | A direção da mensagem AS2 |
| ID de Correlação | A ID que correlaciona todos os gatilhos e todas as ações em um aplicativo lógico |
| ID da Mensagem | ID da mensagem AS2 dos cabeçalhos da mensagem AS2 |
| Timestamp | A hora em que a ação do AS2 processou a mensagem |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>Formatos de nome AS2 para arquivos de mensagem baixados

Estes são os formatos de nome de cada pasta de mensagens e arquivos AS2 baixados.

| Pasta ou arquivo | Formato de nome |
| :------------- | :---------- |
| Pasta de mensagens | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Entrada, saída e, se configurado, os arquivos de confirmação | **Conteúdo de entrada**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Conteúdo de saída**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Entradas**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Saídas**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Descrições das propriedades da mensagem X12

Estas são as descrições das propriedades de cada mensagem X12.

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Remetente | O parceiro convidado especificado em **Configurações de Recebimento** ou o parceiro host especificado em **Configurações de Envio** de um contrato do X12 |
| Receptor | O parceiro host especificado em **Configurações de Recebimento** ou o parceiro convidado especificado em **Configurações de Envio** de um contrato do X12 |
| Aplicativo Lógico | O aplicativo lógico no qual as ações do X12 são configuradas |
| Status | O status da mensagem X12 <br>Êxito = recebimento ou envio de uma mensagem X12 válida. Nenhuma confirmação funcional está configurada. <br>Êxito = recebimento ou envio de uma mensagem X12 válida. Uma confirmação funcional está configurada e é recebida ou uma confirmação funcional é enviada. <br>Com Falha = recebimento ou envio de uma mensagem X12 inválida. <br>Pendente = recebimento ou envio de uma mensagem X12 válida. Uma confirmação funcional está configurada e uma confirmação funcional é esperada. |
| Ack | Status da Confirmação Funcional (997) <br>Aceito = recebimento ou envio de uma confirmação funcional positiva. <br>Rejeitado = recebimento ou envio de uma confirmação funcional negativa. <br>Pendente = aguardando uma confirmação funcional, mas não recebida. <br>Pendente = geração de uma confirmação funcional, mas não foi possível enviá-la ao parceiro. <br>Não Obrigatório = uma confirmação funcional não está configurada. |
| Direção | A direção da mensagem X12 |
| ID de Correlação | A ID que correlaciona todos os gatilhos e todas as ações em um aplicativo lógico |
| Tipo de mensagem | O tipo de mensagem EDI X12 |
| ICN | O Número de Controle de Intercâmbio da mensagem X12 |
| TSCN | O Número de Controle do Conjunto de Transações da mensagem X12 |
| Timestamp | A hora em que a ação do X12 processou a mensagem |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>Formatos de nome X12 para arquivos de mensagem baixados

Estes são os formatos de nome de cada pasta de mensagens e arquivos X12 baixados.

| Pasta ou arquivo | Formato de nome |
| :------------- | :---------- |
| Pasta de mensagens | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Entrada, saída e, se configurado, os arquivos de confirmação | **Conteúdo de entrada**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Conteúdo de saída**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Entradas**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Saídas**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Descrições das propriedades da mensagem EDIFACT

Estas são as descrições das propriedades de cada mensagem EDIFACT.

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Remetente | O parceiro convidado especificado em **Configurações de Recebimento** ou o parceiro host especificado em **Configurações de Envio** de um contrato do EDIFACT |
| Receptor | O parceiro host especificado em **Configurações de Recebimento** ou o parceiro convidado especificado em **Configurações de Envio** de um contrato do EDIFACT |
| Aplicativo Lógico | O aplicativo lógico no qual as ações do EDIFACT são configuradas |
| Status | O status da mensagem EDIFACT <br>Êxito = recebimento ou envio de uma mensagem EDIFACT válida. Nenhuma confirmação funcional está configurada. <br>Êxito = recebimento ou envio de uma mensagem EDIFACT válida. Uma confirmação funcional está configurada e é recebida ou uma confirmação funcional é enviada. <br>Com Falha = recebimento ou envio de uma mensagem EDIFACT inválida <br>Pendente = recebimento ou envio de uma mensagem EDIFACT válida. Uma confirmação funcional está configurada e uma confirmação funcional é esperada. |
| Ack | Status da Confirmação Funcional (997) <br>Aceito = recebimento ou envio de uma confirmação funcional positiva. <br>Rejeitado = recebimento ou envio de uma confirmação funcional negativa. <br>Pendente = aguardando uma confirmação funcional, mas não recebida. <br>Pendente = geração de uma confirmação funcional, mas não foi possível enviá-la ao parceiro. <br>Não Obrigatório = uma confirmação funcional não está configurada. |
| Direção | A direção da mensagem EDIFACT |
| ID de Correlação | A ID que correlaciona todos os gatilhos e todas as ações em um aplicativo lógico |
| Tipo de mensagem | O tipo da mensagem EDIFACT |
| ICN | O Número de Controle de Intercâmbio da mensagem EDIFACT |
| TSCN | O Número de Controle do Conjunto de Transações da mensagem EDIFACT |
| Timestamp | A hora em que a ação do EDIFACT processou a mensagem |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>Formatos de nome EDIFACT para arquivos de mensagem baixados

Estes são os formatos de nome de cada pasta de mensagens e arquivos EDIFACT baixados.

| Pasta ou arquivo | Formato de nome |
| :------------- | :---------- |
| Pasta de mensagens | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Entrada, saída e, se configurado, os arquivos de confirmação | **Conteúdo de entrada**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Conteúdo de saída**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Entradas**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Saídas**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>Próximas etapas

* [Consulta de mensagens de B2B nos logs do Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [Esquemas de acompanhamento de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de acompanhamento de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de acompanhamento personalizado](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
