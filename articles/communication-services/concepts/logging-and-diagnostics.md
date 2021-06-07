---
title: Logs dos Serviços de Comunicação
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre o log dos Serviços de Comunicação do Azure
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0bf7213e5cfdc57194482c6914d4e25b9336832e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935867"
---
# <a name="communication-services-logs"></a>Logs dos Serviços de Comunicação

Os Serviços de Comunicação do Azure oferecem funcionalidades de log que você pode usar para monitorar e depurar sua solução dos Serviços de Comunicação. Essas funcionalidades podem ser configuradas por meio do portal do Azure.

## <a name="enable-diagnostic-logs-in-your-resource"></a>Habilitar os logs de diagnóstico no seu recurso

O log é desativado por padrão quando um recurso é criado. Para habilitar o log, acesse a folha **Configurações de diagnóstico** no menu de recursos, na seção **Monitoramento**. Depois, clique em **Adicionar configuração de diagnóstico**.

Em seguida, selecione o destino de arquivo desejado. Atualmente, damos suporte a contas de armazenamento e ao Log Analytics como destinos de arquivos. Depois de selecionar os tipos de logs que deseja capturar, salve as configurações de diagnóstico.
 
As novas configurações terão efeito em aproximadamente dez minutos. Os logs começarão a aparecer no destino de arquivamento configurado no painel Logs do recurso dos Serviços de Comunicação.

:::image type="content" source="./media/diagnostic-settings.png" alt-text="Opções de configurações de diagnóstico do ACS.":::

Para obter mais informações sobre como configurar um diagnóstico, confira a visão geral dos [logs de recursos do Azure](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="resource-log-categories"></a>Categorias de log de recursos

Os Serviços de Comunicação oferecem três tipos de logs que você pode habilitar:

* **Logs de uso**: fornecem dados de uso associados a cada oferta de serviço cobrada
* **Logs operacionais de chat**: fornecem informações básicas relacionadas ao serviço de chat
* **Logs operacionais de SMS**: fornecem informações básicas relacionadas ao serviço de SMS
* **Logs operacionais de autenticação** – fornece informações básicas relacionadas ao serviço de Autenticação

### <a name="usage-logs-schema"></a>Esquema de logs de uso

| Propriedade | Descrição |
| -------- | ---------------|
| Timestamp | O carimbo de data/hora (UTC) de quando o log foi gerado. |
| Nome de operação | A operação associada ao registro de log. |
| Versão da operação | A `api-version` associada à operação, caso a operationName tenha sido executada com uma API. Se não houver nenhuma API que corresponde a essa operação, a versão representará a versão dessa operação, caso as propriedades associadas à operação sejam alteradas no futuro. |
| Categoria | A categoria de log do evento. Categoria é a granularidade na qual você pode habilitar ou desabilitar os logs em determinado recurso. As propriedades exibidas no blob de propriedades de um evento são as mesmas em uma categoria de log e um tipo de recurso específicos. |
| ID de Correlação | A ID de eventos correlacionados. Pode ser usada para identificar os eventos correlacionados entre várias tabelas. |
| Propriedades | Outros dados aplicáveis a vários modos dos Serviços de Comunicação. |
| ID do Registro | A ID exclusiva de um registro de uso especificado. |
| Tipo de uso | O modo de uso. (por exemplo, chat, PSTN, NAT etc.) |
| Tipo unit | O tipo de unidade em que o uso se baseia para um modo de uso especificado. (por exemplo, minutos, megabytes, mensagens etc.). |
| Quantidade | O número de unidades usadas ou consumidas para este registro. |

### <a name="chat-operational-logs"></a>Logs operacionais de chat

| Propriedade | Descrição |
| -------- | ---------------|
| TimeGenerated | O carimbo de data/hora (UTC) de quando o log foi gerado. |
| OperationName | A operação associada ao registro de log. |
| CorrelationID | A ID de eventos correlacionados. Pode ser usada para identificar os eventos correlacionados entre várias tabelas. |
| OperationVersion | A api-version associada à operação, caso a operationName tenha sido executada com uma API. Se não houver nenhuma API que corresponde a essa operação, a versão representará a versão dessa operação, caso as propriedades associadas à operação sejam alteradas no futuro. |
| Categoria | A categoria de log do evento. Categoria é a granularidade na qual você pode habilitar ou desabilitar os logs em determinado recurso. As propriedades exibidas no blob de propriedades de um evento são as mesmas em uma categoria de log e um tipo de recurso específicos. |
| ResultType | O status da operação. |
| ResultSignature | O substatus da operação. Se essa operação corresponder a uma chamada à API REST, esse campo será o código de status HTTP da chamada REST correspondente. |
| ResultDescription | A descrição de texto estático desta operação. |
| DurationMs | A duração da operação em milissegundos. |
| CallerIpAddress | O endereço IP do chamador, caso a operação corresponda a uma chamada à API proveniente de uma entidade com um endereço IP disponível publicamente. |
| Nível | O nível de severidade do evento. |
| URI | O URI da solicitação. |
| UserId | A ID de usuário do remetente da solicitação. |
| ChatThreadId | A ID do thread de chat associado à solicitação. |
| ChatMessageId | A ID da mensagem de chat associada à solicitação. |
| SdkType | O tipo de SDK usado na solicitação. |
| PlatformType | O tipo de plataforma usado na solicitação. |

### <a name="sms-operational-logs"></a>Logs operacionais de SMS

| Propriedade | Descrição |
| -------- | ---------------|
| TimeGenerated | O carimbo de data/hora (UTC) de quando o log foi gerado. |
| OperationName | A operação associada ao registro de log. |
| CorrelationID | A ID de eventos correlacionados. Pode ser usada para identificar os eventos correlacionados entre várias tabelas. |
| OperationVersion | A api-version associada à operação, caso a operationName tenha sido executada com uma API. Se não houver nenhuma API que corresponde a essa operação, a versão representará a versão dessa operação, caso as propriedades associadas à operação sejam alteradas no futuro. |
| Categoria | A categoria de log do evento. Categoria é a granularidade na qual você pode habilitar ou desabilitar os logs em determinado recurso. As propriedades exibidas no blob de propriedades de um evento são as mesmas em uma categoria de log e um tipo de recurso específicos. |
| ResultType | O status da operação. |
| ResultSignature | O substatus da operação. Se essa operação corresponder a uma chamada à API REST, esse campo será o código de status HTTP da chamada REST correspondente. |
| ResultDescription | A descrição de texto estático desta operação. |
| DurationMs | A duração da operação em milissegundos. |
| CallerIpAddress | O endereço IP do chamador, caso a operação corresponda a uma chamada à API proveniente de uma entidade com um endereço IP disponível publicamente. |
| Nível | O nível de severidade do evento. |
| URI | O URI da solicitação. |
| OutgoingMessageLength | O número de caracteres na mensagem de saída. |
| IncomingMessageLength | O número de caracteres na mensagem de entrada. |
| DeliveryAttempts | O número de tentativas feitas para entregar esta mensagem. |
| PhoneNumber | O número de telefone do qual a mensagem SMS está sendo enviada. |
| SdkType | O tipo de SDK usado na solicitação. |
| PlatformType | O tipo de plataforma usado na solicitação. |
| Método | O método usado na solicitação. |

### <a name="authentication-operational-logs"></a>Logs operacionais de autenticação

| Propriedade | Descrição |
| -------- | ---------------|
| TimeGenerated | O carimbo de data/hora (UTC) de quando o log foi gerado. |
| OperationName | A operação associada ao registro de log. |
| CorrelationID | A ID de eventos correlacionados. Pode ser usada para identificar os eventos correlacionados entre várias tabelas. |
| OperationVersion | A `api-version` associada à operação, caso o `operationName` tenha sido executado com uma API. Se não houver nenhuma API que corresponde a essa operação, a versão representará a versão dessa operação, caso as propriedades associadas à operação sejam alteradas no futuro. |
| Categoria | A categoria de log do evento. Categoria é a granularidade na qual você pode habilitar ou desabilitar os logs em determinado recurso. As propriedades exibidas no blob de propriedades de um evento são as mesmas em uma categoria de log e um tipo de recurso específicos. |
| ResultType | O status da operação. |
| ResultSignature | O substatus da operação. Se essa operação corresponder a uma chamada à API REST, esse campo será o código de status HTTP da chamada REST correspondente. |
| DurationMs | A duração da operação em milissegundos. |
| CallerIpAddress | O endereço IP do chamador, caso a operação corresponda a uma chamada à API proveniente de uma entidade com um endereço IP disponível publicamente. |
| Nível | O nível de severidade do evento. |
| URI | O URI da solicitação. |
| SdkType | O tipo de SDK usado na solicitação. |
| PlatformType | O tipo de plataforma usado na solicitação. |
| Identidade | A identidade dos Serviços de Comunicação relacionados à operação. |
| Escopos | Os escopos dos Serviços de Comunicação presentes no token de acesso. |
