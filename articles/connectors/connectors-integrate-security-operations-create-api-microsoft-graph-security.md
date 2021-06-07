---
title: Integrar e gerenciar as operações de segurança & segurança de Microsoft Graph
description: Melhorar a proteção contra ameaças do seu aplicativo, a detecção e a resposta com o Microsoft Graph Security & aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: ecfan
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: a83cd68df2f1d722517d6239bf6959075860d0b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94888531"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Melhore a proteção contra ameaças integrando as operações de segurança com a Segurança do Microsoft Graph e os Aplicativos Lógicos do Azure

Com os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e o conector da [Segurança do Microsoft Graph](/graph/security-concept-overview), você pode melhorar o modo como seu aplicativo detecta, protege e responde às ameaças criando fluxos de trabalho automatizados para a integração de parceiros, serviços e produtos de segurança Microsoft. Por exemplo, você pode criar [Guias estratégicos de Central de Segurança do Azure](../security-center/workflow-automation.md) que monitoram e gerenciam entidades de segurança do Microsoft Graph, tais como alertas. Aqui estão alguns cenários com suporte pelo conector de segurança Microsoft Graph:

* Obtenha alertas com base em consultas ou pela ID do alerta. Por exemplo, você pode obter uma lista que inclui alertas de severidade alta.

* Atualize os alertas. Por exemplo, você pode atualizar atribuições de alertas, adicionar comentários a alertas ou marcar alertas.

* Monitore quando os alertas são criados ou alterados, criando [Assinaturas de alertas (webhooks)](/graph/api/resources/webhooks).

* Gerencie suas assinaturas de alertas. Por exemplo, você pode obter assinaturas ativas, estender o tempo de expiração para uma assinatura ou excluir assinaturas.

O fluxo de trabalho do aplicativo lógico pode usar ações que obtêm as respostas do conector da Segurança do Microsoft Graph e disponibilizam essa saída para outras ações no fluxo de trabalho. Você também pode fazer com que outras ações no fluxo de trabalho usem a saída das ações de conector da Segurança do Microsoft Graph. Por exemplo, se receber alertas de severidade alta por meio do conector da Segurança do Microsoft Graph, você poderá enviar os alertas em uma mensagem de email usando o conector do Outlook. 

Para saber mais sobre a Segurança do Microsoft Graph, confira a [Visão geral da API de Segurança do Microsoft Graph](/graph/security-concept-overview). Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md). Se você estiver procurando energia automatizada ou PowerApps, consulte [o que é Power Automate?](https://flow.microsoft.com/) ou [o que é o Power apps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* Para usar o conector da Segurança do Microsoft Graph, você precisa ter *dado explicitamente* seu consentimento de administrador do locatário do Azure AD (Active Directory), o que faz parte dos [requisitos de autenticação da Segurança do Microsoft Graph](/graph/security-authorization). Esse consentimento requer o nome e a ID do aplicativo do conector da Segurança do Microsoft Graph, que você também pode encontrar no [portal do Azure](https://portal.azure.com):

  | Propriedade | Valor |
  |----------|-------|
  | **Nome do Aplicativo** | `MicrosoftGraphSecurityConnector` |
  | **ID do Aplicativo** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Para conceder consentimento para o conector, o administrador de locatário do Azure AD pode seguir estas etapas:

  * [Conceder consentimento do administrador de locatário para aplicativos do Azure AD](../active-directory/develop/v2-permissions-and-consent.md).

  * Durante a primeira execução do aplicativo lógico, seu aplicativo pode solicitar consentimento do administrador do locatário do Azure AD por meio de [experiência de consentimento de aplicativo](../active-directory/develop/application-consent-experience.md).
   
* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar suas entidades de Segurança do Microsoft Graph, tais como alertas. Para usar um gatilho de segurança Microsoft Graph, você precisa de um aplicativo lógico em branco. Para usar uma ação de segurança Microsoft Graph, você precisa de um aplicativo lógico que comece com o gatilho apropriado para seu cenário.

## <a name="connect-to-microsoft-graph-security"></a>Conectar à Segurança do Microsoft Graph 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com/) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para aplicativos lógicos em branco, adicione o gatilho e todas as outras ações que desejar antes de adicionar uma ação de Microsoft Graph segurança.

   – ou –

   Para os aplicativos lógicos existentes, na última etapa em que você deseja adicionar uma Microsoft Graph ação de segurança, selecione **nova etapa**.

   – ou –

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o sinal de adição (+) que aparece e selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira "segurança do microsoft graph" como o filtro. Na lista de ações, selecione a ação desejada.

1. Entre com suas credenciais da Segurança do Microsoft Graph.

1. Forneça os detalhes necessários para a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="add-triggers"></a>Adicionar gatilhos

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de aplicativos lógicos cria uma instância de aplicativo lógico e começa a executar o fluxo de trabalho do aplicativo.

> [!NOTE] 
> Quando um gatilho é acionado, o gatilho processa todos os novos alertas. Se nenhum alerta for recebido, a execução do gatilho será ignorada. A próxima pesquisa de gatilhos acontecerá com base no intervalo de recorrência que você especificar nas propriedades do gatilho.

Este exemplo mostra como você pode iniciar um fluxo de trabalho de aplicativo lógico quando novos alertas são enviados para seu aplicativo.

1.  No portal do Azure ou no Visual Studio, crie um aplicativo lógico em branco, que abre o designer do aplicativo lógico. Este exemplo usa o portal do Azure.

1.  No designer, na caixa de pesquisa, digite "Microsoft Graph Security" como filtro. Na lista de gatilhos, selecione este gatilho: **em todos os novos alertas**

1.  No gatilho, forneça informações sobre os alertas que você deseja monitorar. Para obter mais propriedades, abra a lista **Adicionar novo parâmetro** e selecione um parâmetro para adicionar essa propriedade ao gatilho.

   | Propriedade | Propriedade (JSON) | Obrigatório | Type | Descrição |
   |----------|-----------------|----------|------|-------------|
   | **Intervalo** | `interval` | Sim | Integer | Um inteiro positivo que descreve a frequência na qual o fluxo de trabalho é executado com base na frequência. Aqui estão os intervalos mínimos e máximos: <p><p>– Mês: 1 a 16 meses <br>–Dia: 1 a 500 dias <br>– Hora: 1 a 12.000 horas <br>– Minuto: 1 a 72.000 minutos <br>– Segundo: 1 a 9.999.999 segundos <p>Por exemplo, se o intervalo for 6 e a frequência for "Mês", a recorrência será a cada 6 meses. |
   | **Frequência** | `frequency` | Sim | String | A unidade de tempo para a recorrência: **Segundo**, **Minuto**, **Hora**, **Dia**, **Semana** ou **Mês** |
   | **Fuso horário** | `timeZone` | Não | String | Aplica-se somente quando você especifica uma hora de início, porque o gatilho não aceita [diferença UTC](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que você deseja aplicar. |
   | **Hora de início** | `startTime` | Não | String | Forneça uma data e hora de início neste formato: <p><p>AAAA-MM-DDThh:mm:ss se você selecionar um fuso horário <p>– ou – <p>AAAA-MM-DDThh:mm:ssZ se você não selecionar um fuso horário <p>Por exemplo, se você quiser 18 de setembro de 2017 às 2:00 PM, especifique "2017-09-18T14:00:00" e selecione um fuso horário como hora padrão do Pacífico. Ou, especifique "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Observação:** Essa hora de início tem um máximo de 49 anos no futuro e deve seguir a [especificação de data e hora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data e hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem um [deslocamento UTC](https://en.wikipedia.org/wiki/UTC_offset). Se você não selecionar um fuso horário, será necessário adicionar a letra "Z" no final sem espaços. Essa letra "Z" refere-se ao equivalente em [hora náutica](https://en.wikipedia.org/wiki/Nautical_time). <p>Para agendamentos simples, a hora de início é a primeira ocorrência, enquanto que, para agendamentos complexos, o gatilho não é disparado antes da hora de início. [*Quais são as maneiras que posso usar a data e hora de início?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

1.  Agora, continue a adicionar uma ou mais ações ao aplicativo lógico para as tarefas que você deseja executar com os resultados do gatilho.

## <a name="add-actions"></a>Adicionar ações

Aqui estão detalhes mais específicos sobre como usar as diversas ações disponíveis com o conector da Segurança do Microsoft Graph.

### <a name="manage-alerts"></a>Gerenciar alertas

Para filtrar, classificar ou obter os resultados mais recentes, forneça *somente* os [parâmetros de consulta ODATA compatíveis com o Microsoft Graph](/graph/query-parameters). *Não especifique* a URL base completa ou a ação HTTP, por exemplo, `https://graph.microsoft.com/v1.0/security/alerts`, ou a operação `GET` ou `PATCH`. Aqui está um exemplo específico que mostra os parâmetros para uma ação **Obter alertas** quando você quer uma lista com os alertas de severidade alta:

`Filter alerts value as Severity eq 'high'`

Para obter mais informações sobre as consultas que você pode usar com esse conector, confira a [documentação de referência de alertas da Segurança do Microsoft Graph](/graph/api/alert-list). Para criar experiências aprimoradas com esse conector, saiba mais sobre os [alertas de propriedades de esquema](/graph/api/resources/alert) compatíveis com o conector.

| Ação | Descrição |
|--------|-------------|
| **Obter alertas** | Obtenha alertas filtrados com base em uma ou mais [Propriedades de alerta](/graph/api/resources/alert), por exemplo, `Provider eq 'Azure Security Center' or 'Palo Alto Networks'` . | 
| **Obter alerta por ID** | Obter um alerta específico com base na ID do alerta. | 
| **Atualizar alerta** | Atualizar um alerta específico com base na ID do alerta. Para assegurar que você passe as propriedades necessárias e editáveis na sua solicitação, confira as [propriedades editáveis para alertas](/graph/api/alert-update). Por exemplo, para atribuir um alerta para um analista de segurança para que ele possa investigar, você pode atualizar a propriedade **Atribuído a** do alerta. |
|||

### <a name="manage-alert-subscriptions"></a>Gerenciar assinaturas de alertas

O Microsoft Graph dá suporte a [*inscrições*](/graph/api/resources/subscription) ou [*webhooks*](/graph/api/resources/webhooks). Para obter, atualizar ou excluir assinaturas, forneça os [parâmetros de consulta ODATA compatíveis com o Microsoft Graph](/graph/query-parameters) para o constructo da entidade do Microsoft Graph e inclua `security/alerts`, seguido da consulta ODATA. *Não inclua* a URL base, por exemplo, `https://graph.microsoft.com/v1.0`. Em vez disso, use o formato neste exemplo:

`security/alerts?$filter=status eq 'NewAlert'`

| Ação | Descrição |
|--------|-------------|
| **Criar assinaturas** | [Crie uma assinatura](/graph/api/subscription-post-subscriptions) que notifique você sobre quaisquer alterações. Você pode filtrar essa assinatura para os tipos de alertas específicos que você deseja. Por exemplo, você pode criar uma assinatura que notifica você sobre alertas de severidade alta. |
| **Obter assinaturas ativas** | [Obtenha assinaturas não expiradas](/graph/api/subscription-list). | 
| **Atualizar assinatura** | [Atualize uma assinatura](/graph/api/subscription-update) fornecendo a respectiva ID. Por exemplo, para estender sua assinatura, você pode atualizar a respectiva propriedade `expirationDateTime`. | 
| **Excluir assinatura** | [Exclua uma assinatura](/graph/api/subscription-delete) fornecendo a ID da assinatura. | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>Gerenciar indicadores de inteligência contra ameaças

Para filtrar, classificar ou obter os resultados mais recentes, forneça *somente* os [parâmetros de consulta ODATA compatíveis com o Microsoft Graph](/graph/query-parameters). *Não especifique* a URL base completa ou a ação HTTP, por exemplo, `https://graph.microsoft.com/beta/security/tiIndicators`, ou a operação `GET` ou `PATCH`. Aqui está um exemplo específico que mostra os parâmetros para uma ação **Get tiIndicators** quando você deseja uma lista que tenha o `DDoS` tipo de ameaça:

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

Para obter mais informações sobre as consultas que você pode usar com esse conector, consulte ["parâmetros de consulta opcionais" na documentação de referência do indicador Microsoft Graph Security Threat Intelligence](/graph/api/tiindicators-list). Para criar experiências aprimoradas com esse conector, saiba mais sobre as [Propriedades do esquema indicador de inteligência contra ameaças](/graph/api/resources/tiindicator) ao qual o conector dá suporte.

| Ação | Descrição |
|--------|-------------|
| **Obter indicadores de inteligência contra ameaças** | Obtenha tiIndicators filtrado com base em uma ou mais [Propriedades tiIndicator](/graph/api/resources/tiindicator), por exemplo, `threatType eq 'MaliciousUrl' or 'DDoS'` |
| **Obter indicador de inteligência contra ameaças por ID** | Obtenha um tiIndicator específico com base na ID do tiIndicator. | 
| **Criar indicador de inteligência contra ameaças** | Crie um novo tiIndicator postando na coleção tiIndicators. Para garantir que você passe as propriedades necessárias em sua solicitação, consulte as [Propriedades necessárias para criar tiIndicator](/graph/api/tiindicators-post). |
| **Enviar vários indicadores de inteligência contra ameaças** | Crie vários novos tiIndicators postando uma coleção tiIndicators. Para garantir que você passe as propriedades necessárias em sua solicitação, consulte as [Propriedades necessárias para enviar vários tiIndicators](/graph/api/tiindicator-submittiindicators). |
| **Atualizar indicador de inteligência contra ameaças** | Atualize um tiIndicator específico com base na ID do tiIndicator. Para ter certeza de que você passa as propriedades requeridas e editáveis em sua solicitação, consulte as [Propriedades editáveis de tiIndicator](/graph/api/tiindicator-update). Por exemplo, para atualizar a ação a ser aplicada se o indicador for correspondido de dentro da ferramenta de segurança do targetProduct, você poderá atualizar a propriedade de **ação** do tiIndicator. |
| **Atualizar vários indicadores de inteligência contra ameaças** | Atualizar vários tiIndicators. Para garantir que você passe as propriedades necessárias em sua solicitação, consulte as [Propriedades necessárias para atualizar vários tiIndicators](/graph/api/tiindicator-updatetiindicators). |
| **Excluir indicador de inteligência contra ameaças por ID** | Exclua um tiIndicator específico com base na ID do tiIndicator. |
| **Excluir vários indicadores de inteligência contra ameaças por IDs** | Exclua várias tiIndicators por suas IDs. Para garantir que você passe as propriedades necessárias em sua solicitação, consulte as [Propriedades necessárias para excluir vários tiIndicators por IDs](/graph/api/tiindicator-deletetiindicators). |
| **Excluir vários indicadores de inteligência contra ameaças por IDs externas** | Exclua várias tiIndicators pelas IDs externas. Para garantir que você passe as propriedades necessárias em sua solicitação, consulte as [Propriedades necessárias para excluir vários tiIndicators por IDs externas](/graph/api/tiindicator-deletetiindicatorsbyexternalid). |
|||

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/microsoftgraphsecurity/) do conector.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
