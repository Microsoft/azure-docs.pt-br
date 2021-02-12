---
title: Monitoramento de métricas e logs na porta frontal do Azure | Microsoft Docs
description: Este artigo descreve as diferentes métricas e logs de acesso aos quais a porta frontal do Azure dá suporte
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: cd99be40700ab1c34176f2bf7497e4debf5cd424
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483790"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Monitoramento de métricas e logs na porta frontal do Azure

Usando a porta frontal do Azure, você pode monitorar os recursos das seguintes maneiras:

- **Métricas**. Atualmente, a porta frontal do Azure tem oito métricas para exibir os contadores de desempenho.
- **Logs**. Os logs de atividade e diagnóstico permitem que o desempenho, o acesso e outros dados sejam salvos ou consumidos de um recurso para fins de monitoramento.

### <a name="metrics"></a>Métricas

As métricas são um recurso para determinados recursos do Azure que permitem Exibir contadores de desempenho no Portal. A seguir estão as métricas de porta frontal disponíveis:

| Métrica | Nome de exibição da métrica | Unidade | Dimensões | Descrição |
| --- | --- | --- | --- | --- |
| RequestCount | Contagem de solicitações | Contagem | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de solicitações de cliente atendidas pelo Front Door.  |
| RequestSize | Tamanho da solicitação | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de bytes enviados como solicitações de clientes para o Front Door. |
| ResponseSize | Tamanho da resposta | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de bytes enviados como respostas do Front Door para clientes. |
| TotalLatency | Latência total | Milissegundos | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O tempo total da solicitação do cliente recebida pela porta frontal até o último byte de resposta enviado de AFD ao cliente. |
| BackendRequestCount | Contagem de solicitações de back-end | Contagem | HttpStatus</br>HttpStatusGroup</br>Back-end | O número de solicitações enviadas do Front Door aos back-ends. |
| BackendRequestLatency | Latência de solicitação de back-end | Milissegundos | Back-end | O tempo calculado de quando a solicitação foi enviada pelo Front Door ao back-end até o Front Door receber o último byte de resposta do back-end. |
| BackendHealthPercentage | Percentual de integridade do back-end | Porcentagem | Back-end</br>BackendPool | O percentual de investigações de integridade bem-sucedidas do Front Door aos back-ends. |
| WebApplicationFirewallRequestCount | Contagem de solicitações do Firewall de Aplicativo Web | Contagem | PolicyName</br>RuleName</br>Ação | O número de solicitações de cliente processadas pela segurança da camada de aplicativo do Front Door. |

## <a name="activity-logs"></a><a name="activity-log"></a>Logs de atividade

Os logs de atividade fornecem informações sobre as operações realizadas na porta da frente. Eles também determinam o que, quem e quando para qualquer operação de gravação (put, post ou Delete) realizada na porta frontal.

>[!NOTE]
>Os logs de atividade não incluem operações de leitura (Get). Eles também não incluem operações que você executa usando o portal do Azure ou a API de gerenciamento original.

Acesse os logs de atividade em sua porta de front-end ou todos os logs dos recursos do Azure no Azure Monitor. Para exibir logs de atividade:

1. Selecione sua instância de porta frontal.
2. Selecione **Log de atividades**.

    :::image type="content" source="./media/front-door-diagnostics/activity-log.png" alt-text="Log de atividades":::

3. Escolha um escopo de filtragem e, em seguida, selecione **aplicar**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Logs de diagnóstico
Os logs de diagnóstico fornecem informações avançadas sobre operações e erros que são importantes para auditoria e solução de problemas. Os logs de diagnóstico são diferentes dos logs de atividades.

Os logs de atividades fornecem informações sobre as operações realizadas nos recursos do Azure. Os logs de diagnóstico fornecem informações sobre as operações que o recurso fez. Para obter mais informações, consulte [Azure monitor logs de diagnóstico](../azure-monitor/platform/platform-logs-overview.md).

:::image type="content" source="./media/front-door-diagnostics/diagnostic-log.png" alt-text="Logs de diagnóstico":::

Para configurar os logs de diagnóstico para sua porta frontal:

1. Selecione sua porta frontal do Azure.

2. Escolha **configurações de diagnóstico**.

3. Selecione **Ativar diagnóstico**. Arquive logs de diagnóstico junto com as métricas para uma conta de armazenamento, transmita-os para um hub de eventos ou envie-os para Azure Monitor logs.

Atualmente, a porta frontal fornece logs de diagnóstico (em lote). Os logs de diagnóstico fornecem solicitações de API individuais com cada entrada com o seguinte esquema:

| Propriedade  | Descrição |
| ------------- | ------------- |
| BackendHostname | Se a solicitação estava sendo encaminhada para um back-end, esse campo representa o nome do host do back-end. Esse campo ficará em branco se a solicitação for redirecionada ou encaminhada para um cache regional (quando o Caching for habilitado para a regra de roteamento). |
| CacheStatus | Para cenários de cache, esse campo define o impacto/perda do cache no POP |
| ClientIp | Endereço IP do cliente que fez a solicitação. Se houver um cabeçalho X-Forwardd-for na solicitação, o IP do cliente será escolhido do mesmo. |
| ClientPort | A porta IP do cliente que fez a solicitação. |
| HttpMethod | Método HTTP usado pela solicitação. |
| HttpStatusCode | O código de status HTTP retornado do proxy. |
| HttpStatusDetails | Status resultante na solicitação. O significado desse valor de cadeia de caracteres pode ser encontrado em uma tabela de referência de status. |
| HttpVersion | O tipo da conexão ou solicitação. |
| POP | Nome curto da borda em que a solicitação descarregou. |
| RequestBytes | O tamanho da mensagem de solicitação HTTP em bytes, incluindo os cabeçalhos de solicitação e o corpo da solicitação. |
| RequestUri | URI da solicitação recebida. |
| ResponseBytes | Bytes enviados pelo servidor de back-end como a resposta.  |
| RoutingRuleName | O nome da regra de roteamento com a qual a solicitação foi correspondida. |
| RulesEngineMatchNames | Os nomes das regras que a solicitação correspondeu. |
| SecurityProtocol | A versão do protocolo TLS/SSL usada pela solicitação ou NULL se não houver criptografia. |
| SentToOriginShield </br> (preterido) * **Veja observações sobre a reprovação na seção a seguir.**| Se for verdadeiro, significa que a solicitação foi respondida do cache da blindagem de origem em vez do pop de borda. A blindagem de origem é um cache pai usado para melhorar a taxa de acertos do cache. |
| isReceivedFromClient | Se for true, significa que a solicitação veio do cliente. Se for false, a solicitação será um erro na borda (POP filho) e será respondida do escudo de origem (POP pai). |
| TimeTaken | O período de tempo do primeiro byte de solicitação na porta frontal até o último byte de resposta, em segundos. |
| TrackingReference | A cadeia de caracteres de referência exclusiva que identifica uma solicitação atendida pela Front Door, também enviada como o cabeçalho X-Azure-Ref para o cliente. Necessário para pesquisar detalhes nos logs de acesso para uma solicitação específica. |
| UserAgent | O tipo de navegador que o cliente usou. |
| ErrorInfo | Este campo contém o tipo específico de erro para a solução de problemas. </br> Os valores possíveis incluem: </br> **NOERROR**: indica que nenhum erro foi encontrado. </br> **CertificateError**: erro de certificado SSL genérico.</br> **CertificateNameCheckFailed**: o nome do host no certificado SSL é inválido ou não corresponde. </br> **ClientDisconnected**: falha de solicitação devido à conexão de rede do cliente. </br> **UnspecifiedClientError**: erro de cliente genérico. </br> **InvalidRequest**: solicitação inválida. Pode ocorrer devido a cabeçalho, corpo e URL malformados. </br> **DNSFailure**: falha de DNS. </br> **DNSNameNotResolved**: não foi possível resolver o nome ou o endereço do servidor. </br> **OriginConnectionAborted**: a conexão com a origem foi interrompida abruptamente. </br> **OriginConnectionError**: erro de conexão de origem genérica. </br> **OriginConnectionRefused**: a conexão com a origem não foi capaz de estabelecer. </br> **OriginError**: erro de origem genérica. </br> **OriginInvalidResponse**: Origin retornou uma resposta inválida ou não reconhecida. </br> **OriginTimeout**: o período de tempo limite para a solicitação de origem expirou. </br> **ResponseHeaderTooBig**: a origem retornou muito grande de um cabeçalho de resposta. </br> **RestrictedIP**: a solicitação foi bloqueada devido ao IP restrito. </br> **SSLHandshakeError**: não é possível estabelecer a conexão com a origem devido a uma falha de mistura de SSL. </br> **UnspecifiedError**: ocorreu um erro que não se ajustou a nenhum dos erros na tabela. |

### <a name="sent-to-origin-shield-deprecation"></a>Enviado à substituição da blindagem de origem
A propriedade de log bruto **isSentToOriginShield** foi preterida e substituída por um novo campo **isReceivedFromClient**. Use o novo campo se você já estiver usando o campo preterido. 

Os logs brutos incluem logs gerados a partir da borda da CDN (POP filho) e da blindagem de origem. A blindagem de origem se refere a nós pai que estão estrategicamente localizados em todo o mundo. Esses nós se comunicam com servidores de origem e reduzem a carga de tráfego na origem. 

Para cada solicitação que vai para a blindagem de origem, há duas entradas de log:

* Um para nós de borda
* Uma para a blindagem de origem. 

Para diferenciar a saída ou as respostas dos nós de borda versus a blindagem de origem, você pode usar o campo **isReceivedFromClient** para obter os dados corretos. 

Se o valor for false, isso significa que a solicitação é respondida da blindagem de origem para nós de borda. Essa abordagem é eficaz para comparar logs brutos com dados de cobrança. Os encargos não são incorridos para a saída da blindagem de origem para os nós de borda. Os encargos são incorridos para a saída dos nós de borda para os clientes. 

**Exemplo de consulta Kusto para excluir logs gerados no escudo de origem no Log Analytics.**

`AzureDiagnostics 
| where Category == "FrontdoorAccessLog" and isReceivedFromClient_b == true`

> [!NOTE]
> Para várias configurações de roteamento e comportamentos de tráfego, alguns dos campos como backendHostname, cacheStatus, isReceivedFromClient e POP Field podem responder com valores diferentes. A tabela abaixo explica os diferentes valores que esses campos terão para vários cenários:

| Cenários | Contagem de entradas de log | POP | BackendHostname | isReceivedFromClient | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Regra de roteamento sem Caching habilitado | 1 | Código POP do Edge | Back-end em que a solicitação foi encaminhada | Verdadeiro | CONFIG_NOCACHE |
| Regra de roteamento com Caching habilitado. Cache atingido no POP de borda | 1 | Código POP do Edge | Vazio | Verdadeiro | CONTADOR |
| Regra de roteamento com Caching habilitado. Erros de cache no POP de borda, mas acesso ao cache no POP do cache pai | 2 | 1. código POP de borda</br>2. código POP do cache pai | 1. nome do host POP do cache pai</br>2. vazio | 1. verdadeiro</br>2. false | 1. PERDA</br>2. ATINGIR |
| Regra de roteamento com Caching habilitado. Perda de caches no POP de borda, mas acesso parcial ao cache no POP do cache pai | 2 | 1. código POP de borda</br>2. código POP do cache pai | 1. nome do host POP do cache pai</br>2. back-end que ajuda a preencher o cache | 1. verdadeiro</br>2. false | 1. PERDA</br>2. PARTIAL_HIT |
| Regra de roteamento com Caching habilitado. Cache PARTIAL_HIT no POP de borda, mas acesso ao cache no POP do cache pai | 2 | 1. código POP de borda</br>2. código POP do cache pai | 1. código POP de borda</br>2. código POP do cache pai | 1. verdadeiro</br>2. false | 1. PARTIAL_HIT</br>2. ATINGIR |
| Regra de roteamento com Caching habilitado. Erros de cache na borda e no pop-up de cache pai | 2 | 1. código POP de borda</br>2. código POP do cache pai | 1. código POP de borda</br>2. código POP do cache pai | 1. verdadeiro</br>2. false | 1. PERDA</br>2. PERDA |

> [!NOTE]
> Para cenários de cache, o valor do status do cache será partial_hit quando alguns dos bytes de uma solicitação forem atendidos do cache da borda da porta frontal ou da blindagem de origem, enquanto alguns dos bytes são atendidos da origem para objetos grandes.

O Front Door usa uma técnica chamada agrupamento de objeto. Quando um arquivo grande é solicitado, a porta frontal recupera partes menores do arquivo da origem. Depois que o servidor POP de porta frontal recebe um intervalo de bytes completo ou de byte do arquivo solicitado, o servidor de borda da porta frontal solicita o arquivo da origem em partes de 8 MB.

Depois que a parte chega à borda da porta frontal, ela é armazenada em cache e imediatamente fornecida ao usuário. Em seguida, a porta frontal antecipa a próxima parte em paralelo. Essa pré-busca garante que o conteúdo permaneça uma parte à frente do usuário, o que reduz a latência. Esse processo continua até que todo o arquivo seja baixado (se solicitado), todos os intervalos de bytes estão disponíveis (se solicitado) ou o cliente fecha a conexão. Para obter mais informações sobre a solicitação de intervalo de bytes, consulte RFC 7233. A porta frontal armazena em cache todas as partes conforme elas são recebidas. O arquivo inteiro não precisa ser armazenado em cache no cache da porta frontal. As solicitações fornecidas para os intervalos de arquivo ou de bytes são servidas do cache de porta frontal. Se nem todas as partes forem armazenadas em cache na porta da frente, a pré-busca será usada para solicitar partes da origem. Essa otimização se baseia na capacidade do servidor de origem de dar suporte a solicitações de intervalo de bytes. Se o servidor de origem não dá suporte a solicitações de intervalo de bytes, essa otimização não é eficaz.

## <a name="next-steps"></a>Próximas etapas

- [Criar um perfil de Front Door](quickstart-create-front-door.md)
- [Como funciona a porta frontal](front-door-routing-architecture.md)
