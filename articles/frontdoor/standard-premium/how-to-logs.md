---
title: Registro em log do Standard/Premium da porta do Azure (versão prévia)
description: Este artigo explica como o log funciona no Azure front door Standard/Premium.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 03/15/2021
ms.author: duau
ms.openlocfilehash: 531f4a9c9f535779e451ca316a8a5867f6cdaba5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573890"
---
# <a name="azure-front-door-standardpremium-preview-logging"></a>Registro em log do Standard/Premium da porta do Azure (versão prévia)

> [!Note]
> Esta documentação é específica para o Azure Front Door Standard/Premium (Versão Prévia). Está procurando informações sobre o Azure Front Door? Veja [aqui](../front-door-overview.md).

A porta frontal do Azure fornece registro em log diferente para ajudá-lo a acompanhar, monitorar e depurar sua porta frontal. 

* Os logs de acesso têm informações detalhadas sobre cada solicitação que o AFD recebe e ajuda você a analisar e monitorar padrões de acesso e depurar problemas. 
* Os logs de atividade fornecem visibilidade das operações realizadas nos recursos do Azure.  
* Os logs de investigação de integridade fornecem os logs para cada investigação com falha em sua origem. 
* Os logs do WAF (firewall do aplicativo Web) fornecem informações detalhadas de solicitações que são registradas por meio do modo de detecção ou prevenção de um ponto de extremidade de porta de recepção do Azure. Um domínio personalizado que é configurado com WAF também pode ser exibido por meio desses logs.

> [!IMPORTANT]
> O Azure Front Door Standard/Premium (versão prévia) está na fase de versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Logs de acesso, logs de investigação de integridade e logs de WAF não são habilitados por padrão. Use as etapas abaixo para habilitar o registro em log. As entradas do log de atividades são coletadas por padrão e podem ser exibidas no portal do Azure. Os logs podem ter atrasos de até alguns minutos. 

Você tem três opções para armazenar os logs: 

* **Conta de armazenamento:** As contas de armazenamento são mais bem usadas para cenários quando os logs são armazenados por uma duração maior e revisados quando necessário. 
* **Hubs de eventos:** Os hubs de eventos são uma ótima opção para a integração com outras ferramentas de SIEM (gerenciamento de eventos e informações de segurança) ou armazenamentos de dados externos. Por exemplo: Splunk/DataDog/o resumo. 
* **Log Analytics do Azure:** O Azure Log Analytics no Azure Monitor é mais bem usado para monitoramento geral em tempo real e análise do desempenho da porta frontal do Azure.

## <a name="configure-logs"></a>Configurar logs

1. Entre no [portal do Azure](https://portal.azure.com).

1. Pesquise por Standard/Premium da porta do Azure e selecione o perfil de porta frontal do Azure.

1. No perfil, vá para **monitoramento**, selecione **configuração de diagnóstico**. Selecione **Adicionar configuração de diagnóstico**.

   :::image type="content" source="../media/how-to-logging/front-door-logging-1.png" alt-text="Captura de tela da página inicial de configurações de diagnóstico.":::

1. Em **configurações de diagnóstico**, insira um nome para o  **nome das configurações de diagnóstico**.

1. Selecione o  **log** de **FrontDoorAccessLog**, **FrontDoorHealthProbeLog** e **FrontDoorWebApplicationFirewallLog**.

1. Selecione os **detalhes de destino**. As opções de destino são: 

    * **Enviar para o Log Analytics**
        * Selecione a *Assinatura* e o *Workspace do Log Analytics*.
    * **Arquivar em uma conta de armazenamento**
        * Selecione a *Assinatura* e a *Conta de Armazenamento*. e definir a **retenção (dias)**.
    * **Transmitir por streaming para um hub de eventos**
        * Selecione a *assinatura, o namespace do hub de eventos, o nome do hub de eventos (opcional)* e o *nome da política do hub de eventos*. 

     :::image type="content" source="../media/how-to-logging/front-door-logging-2.png" alt-text="Captura de tela da página de configurações de diagnóstico.":::

1. Clique em **Save**.

## <a name="access-log"></a>Log de acesso

Atualmente, a porta frontal do Azure fornece solicitações de API individuais com cada entrada com o esquema a seguir e conectado no formato JSON, conforme mostrado abaixo. 

| Propriedade | Descrição |
|----------|-------------| 
| TrackingReference | A cadeia de caracteres de referência exclusiva que identifica uma solicitação servida por AFD, também é enviada como um cabeçalho X-Azure-ref para o cliente. Necessário para pesquisar detalhes nos logs de acesso para uma solicitação específica. |
| Hora | A data e a hora em que a borda de AFD forneceu o conteúdo solicitado ao cliente (em UTC). |
| HttpMethod | Método HTTP usado pela solicitação: excluir, obter, cabeçalho, opções, PATCH, POST ou PUT. |
| HttpVersion | A versão HTTP que o visualizador especificou na solicitação. |
| RequestUri | URI da solicitação recebida. Este campo é um esquema completo, porta, domínio, caminho e cadeia de caracteres de consulta |
| HostName | O nome do host na solicitação do cliente. Se você habilitar domínios personalizados e tiver um domínio curinga (*. contoso.com), hostname será a.contoso.com. Se você usar o domínio de porta frontal do Azure (contoso.azurefd.net), hostname será contoso.azurefd.net. |
| RequestBytes | O tamanho da mensagem de solicitação HTTP em bytes, incluindo os cabeçalhos de solicitação e o corpo da solicitação. O número de bytes de dados que o visualizador incluiu na solicitação, incluindo cabeçalhos. |
| ResponseBytes | Bytes enviados pelo servidor de back-end como a resposta. |
| UserAgent | O tipo de navegador que o cliente usou. |
| ClientIp | O endereço IP do cliente que fez a solicitação original. Se houver um cabeçalho X-Forwardd-for na solicitação, o IP do cliente será escolhido do mesmo. |
| SocketIp | O endereço IP da conexão direta com a borda de AFD. Se o cliente usou um proxy HTTP ou um balanceador de carga para enviar a solicitação, o valor de SocketIp será o endereço IP do balanceador de carga ou proxy. |
| Latency | O período de tempo desde o momento em que o servidor de borda de AFD recebe a solicitação de um cliente até o momento em que o AFD envia o último byte de resposta para o cliente, em milissegundos. Esse campo não leva em conta a latência de rede e o buffer de TCP. |
| RequestProtocol | O protocolo que o cliente especificou na solicitação: HTTP, HTTPS. |
| SecurityProtocol | A versão do protocolo TLS/SSL usada pela solicitação ou NULL se não houver criptografia. Os valores possíveis incluem: SSLv3, TLSv1, TLSv 1.1, TLSv 1.2 |
| SecurityCipher | Quando o valor do protocolo de solicitação é HTTPS, esse campo indica a codificação TLS/SSL negociada pelo cliente e o AFD para criptografia. |
| Ponto de extremidade | O nome de domínio do ponto de extremidade de AFD, por exemplo, contoso.z01.azurefd.net |
| HttpStatusCode | O código de status HTTP retornado de AFD. |
| Pop | O pop de borda, que respondeu à solicitação do usuário.  |
| Status do cache | Fornece o código de status de como a solicitação é manipulada pelo serviço da CDN quando se trata do armazenamento em cache. Os valores possíveis são ATINGIDOs: a solicitação HTTP foi servida do cache POP de borda de AFD. <br> **Erro**: a solicitação HTTP foi servida da origem. <br/> **PARTIAL_HIT**: alguns dos bytes de uma solicitação foram atendidos do cache pop de borda de AFD, enquanto alguns dos bytes foram atendidos da origem para cenários de agrupamento de objetos. <br> **CACHE_NOCONFIG**: Encaminhando solicitações sem configurações de cache, incluindo o cenário de bypass. <br/> **PRIVATE_NOSTORE**: nenhum cache configurado nas configurações de cache por clientes. <br> **REMOTE_HIT**: a solicitação foi servida pelo cache de nó pai. <br/> **N/A**:* * solicitação negada por URL assinada e conjunto de regras. |
| MatchedRulesSetName | Os nomes das regras que foram processadas. |
| RouteName | O nome da rota com a qual a solicitação foi correspondida. |
| ClientPort | A porta IP do cliente que fez a solicitação. |
| Referenciador | A URL do site que originou a solicitação. |
| Primeiro byte | O período de tempo em milissegundos de AFD recebe a solicitação até o momento em que o primeiro byte é enviado para o cliente, conforme medido na porta frontal do Azure. Essa propriedade não mede os dados do cliente. |
| ErrorInfo | Este campo fornece informações detalhadas do token de erro para cada resposta. <br> **NOERROR**: indica que nenhum erro foi encontrado. <br> **CertificateError**: erro de certificado SSL genérico. <br> **CertificateNameCheckFailed**: o nome do host no certificado SSL é inválido ou não corresponde. <br> **ClientDisconnected**: falha de solicitação devido à conexão de rede do cliente. <br> **ClientGeoBlocked**: o cliente foi bloqueado devido à localização geográfica do IP. <br> **UnspecifiedClientError**: erro de cliente genérico. <br> **InvalidRequest**: solicitação inválida. Pode ocorrer devido a cabeçalho, corpo e URL malformados. <br> **DNSFailure**: falha de DNS. <br> **DNSTimeout**: a consulta DNS para resolver o back-end atingiu o tempo limite. <br> **DNSNameNotResolved**: não foi possível resolver o nome ou o endereço do servidor. <br> **OriginConnectionAborted**: a conexão com a origem foi desconectada de forma anormal. <br> **OriginConnectionError**: erro de conexão de origem genérica. <br> **OriginConnectionRefused**: a conexão com a origem não foi estabelecida. <br> **OriginError**: erro de origem genérica. <br> **OriginInvalidRequest**: uma solicitação inválida foi enviada para a origem. <br> **ResponseHeaderTooBig**: a origem retornou um cabeçalho de resposta muito grande. <br> **OriginInvalidResponse**:* * origem retornou uma resposta inválida ou não reconhecida. <br> **OriginTimeout**: o período de tempo limite para a solicitação de origem expirou. <br> **ResponseHeaderTooBig**: a origem retornou um cabeçalho de resposta muito grande. <br> **RestrictedIP**: a solicitação foi bloqueada devido ao IP restrito. <br> **SSLHandshakeError**: não é possível estabelecer a conexão com a origem devido a uma falha de mistura de SSL. <br> **SSLInvalidRootCA**: o RootCA era inválido. <br> **SSLInvalidCipher**: a codificação era inválida para a qual a conexão HTTPS foi estabelecida. <br> **OriginConnectionAborted**: a conexão com a origem foi desconectada de forma anormal. <br> **OriginConnectionRefused**: a conexão com a origem não foi estabelecida. <br> **UnspecifiedError**: ocorreu um erro que não se ajustou a nenhum dos erros na tabela. |
| OriginURL | A URL completa da origem em que as solicitações estão sendo enviadas. Composto pelo esquema, cabeçalho de host, porta, caminho e cadeia de caracteres de consulta. <br> **Regravação de URL**: se houver uma regra de reescrita de URL no conjunto de regras, o caminho se reescreverá no caminho reescrito. <br> **Pop-up do cache no Edge** Se for um impacto de cache no POP de borda, a origem será N/A. <br> **Solicitação grande** Se o conteúdo solicitado for grande com várias solicitações em partes retornando para a origem, esse campo corresponderá à primeira solicitação para a origem. Para obter mais informações, consulte Agrupamento de objetos para obter mais detalhes. |
| OriginIP | O IP de origem que serviu a solicitação. <br> **Cache atingido no pop de borda** Se for um impacto de cache no POP de borda, a origem será N/A. <br> **Solicitação grande** Se o conteúdo solicitado for grande com várias solicitações em partes retornando para a origem, esse campo corresponderá à primeira solicitação para a origem. Para obter mais informações, consulte Agrupamento de objetos para obter mais detalhes. |
| Origem do| O nome DNS completo (hostname na URL de origem) para a origem. <br> **Cache atingido no pop de borda** Se for um impacto de cache no POP de borda, a origem será N/A. <br> **Solicitação grande** Se o conteúdo solicitado for grande com várias solicitações em partes retornando para a origem, esse campo corresponderá à primeira solicitação para a origem. Para obter mais informações, consulte Agrupamento de objetos para obter mais detalhes. |

## <a name="health-probe-log"></a>Log de investigação de integridade

Os logs de investigação de integridade fornecem registro em log para cada investigação com falha para ajudá-lo a diagnosticar sua origem.Os logs fornecerão informações que você pode usar para retornar a origem ao serviço. Alguns cenários para os quais esse log pode ser útil são:

* Você observou que o tráfego de porta frontal do Azure foi enviado para algumas das origens. Por exemplo, apenas três de quatro origens recebem tráfego. Você deseja saber se as origens estão recebendo investigações e, se não o motivo da falha.  

* Você observou que a% de integridade da origem é menor do que o esperado e deseja saber qual origem falhou e o motivo da falha.

### <a name="health-probe-log-properties"></a>Propriedades do log de investigação de integridade

Cada log de investigação de integridade tem o esquema a seguir.

| Propriedade | Descrição |
| --- | --- |
| HealthProbeId  | Uma ID exclusiva para identificar a solicitação. |
| Hora | Tempo de conclusão da investigação |
| HttpMethod | Método HTTP usado pela solicitação de investigação de integridade. Os valores incluem GET e HEAD, com base nas configurações de investigação de integridade. |
| Resultado | Status da investigação de integridade para origem, valor inclui êxito e outro texto de erro. |
| HttpStatusCode  | O código de status HTTP retornado da origem. |
| ProbeURL (destino) | A URL completa da origem em que as solicitações estão sendo enviadas. Composto pelo esquema, cabeçalho de host, caminho e cadeia de caracteres de consulta. |
| Origem do  | A origem em que as solicitações estão sendo enviadas. Esse campo ajuda a localizar origens de interesse se a origem estiver configurada para FDQN. |
| POP | O pop de borda, que enviou a solicitação de investigação. |
| IP de Origem | IP de origem de destino. Esse campo é útil para localizar origens de interesse se você configurar a origem usando FDQN. |
| TotolaLatency | A hora da borda AFDX envia a solicitação de origem para a origem de tempo que envia a última resposta para o AFDX Edge. |
| ConnectionLatency| Tempo de duração gasto na configuração da conexão TCP para enviar a solicitação de investigação de HTTP para a origem. | 
| Latência de DNSResolution | Tempo de duração gasto na resolução DNS se a origem estiver configurada para ser um FDQN em vez de IP. N/A se a origem estiver configurada para IP. |

### <a name="health-probe-log-sample-in-json"></a>Exemplo de log de investigação de integridade em JSON

`{ "records": [ { "time": "2021-02-02T07:15:37.3640748Z",
      "resourceId": "/SUBSCRIPTIONS/27CAFCA8-B9A4-4264-B399-45D0C9CCA1AB/RESOURCEGROUPS/AFDXPRIVATEPREVIEW/PROVIDERS/MICROSOFT.CDN/PROFILES/AFDXPRIVATEPREVIEW-JESSIE",
      "category": "FrontDoorHealthProbeLog",
      "operationName": "Microsoft.Cdn/Profiles/FrontDoorHealthProbeLog/Write",
      "properties": { "healthProbeId": "9642AEA07BA64675A0A7AD214ACF746E",
        "POP": "MAA",
        "httpVerb": "HEAD",
        "result": "OriginError",
        "httpStatusCode": "400",
        "probeURL": "http://afdxprivatepreview.blob.core.windows.net:80/",
        "originName": "afdxprivatepreview.blob.core.windows.net",
        "originIP": "52.239.224.228:80",
        "totalLatencyMilliseconds": "141",
        "connectionLatencyMilliseconds": "68",
        "DNSLatencyMicroseconds": "1814" } } ]
} `

## <a name="activity-logs"></a>Logs de atividade

Os logs de atividade fornecem informações sobre as operações realizadas no Azure front door Standard/Premium. Os logs incluem detalhes sobre o que, quem e quando uma operação de gravação foi feita na porta de recepção do Azure. 

> [!NOTE]
> Os logs de atividades não incluem operações GET. Eles também não incluem operações que você executa usando o portal do Azure ou a API de gerenciamento original. 

Acesse os logs de atividade em sua porta de front-end ou todos os logs dos recursos do Azure no Azure Monitor.

Para exibir logs de atividade:

1. Selecione o perfil de porta frontal.

1. Selecione **log de atividades.**

1. Escolha um escopo de filtragem e, em seguida, selecione **aplicar**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [relatórios Standard/Premium (visualização) do Azure front door](how-to-reports.md).
- Saiba mais sobre as [métricas de monitoramento em tempo real do Azure front door Standard/Premium (visualização)](how-to-monitor-metrics.md).
