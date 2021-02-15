---
title: Solução de problemas-Firewall do aplicativo Web do Azure
description: Este artigo fornece informações de solução de problemas para o WAF (firewall do aplicativo Web) para Aplicativo Azure gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 483d261a8cc107d01cfb7a405eac43667d7efcc6
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131829"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Solucionar problemas do WAF (firewall do aplicativo Web) para Aplicativo Azure gateway

Há algumas coisas que você pode fazer se as solicitações que devem passar pelo firewall do aplicativo Web (WAF) estiverem bloqueadas.

Primeiro, verifique se você leu a [visão geral do WAF](ag-overview.md) e os documentos de [configuração do WAF](application-gateway-waf-configuration.md) . Além disso, verifique se você habilitou o [monitoramento de WAF](../../application-gateway/application-gateway-diagnostics.md) esses artigos explicam como o WAF funciona, como a regra WAF define o trabalho e como acessar os logs do WAF.

Os conjuntos de regras OWASP são projetados para ser muito estritos e devem ser ajustados para atender às necessidades específicas do aplicativo ou da organização usando o WAF. Ele é totalmente normal e, na verdade, esperado em muitos casos, para criar exclusões, regras personalizadas e até mesmo desabilitar regras que podem estar causando problemas ou falsos positivos. Políticas por site e por URI permitem que essas alterações afetem apenas sites/URIs específicos, portanto, as alterações não devem afetar outros sites que podem não estar em execução nos mesmos problemas. 

## <a name="understanding-waf-logs"></a>Noções básicas sobre logs do WAF

A finalidade dos logs do WAF é mostrar cada solicitação que é correspondida ou bloqueada pelo WAF. É um razão de todas as solicitações avaliadas que são correspondidas ou bloqueadas. Se você observar que o WAF bloqueia uma solicitação que não deveria (um falso positivo), você pode fazer algumas coisas. Primeiro, restrinja e localize a solicitação específica. Examine os logs para localizar o URI específico, o carimbo de data/hora ou a ID da transação da solicitação. Ao encontrar as entradas de log associadas, você pode começar a agir sobre os falsos positivos.

Por exemplo, digamos que você tenha um tráfego legítimo contendo a cadeia de caracteres *1 = 1* que você deseja passar pelo seu WAF. Se você tentar a solicitação, o WAF bloqueará o tráfego que contém a cadeia de caracteres *1 = 1* em qualquer parâmetro ou campo. Essa é uma cadeia de caracteres com frequência associada a um ataque de injeção de SQL. Você pode examinar os logs e ver o carimbo de data/hora da solicitação e as regras que foram bloqueadas/correspondidas.

No exemplo a seguir, você pode ver que quatro regras são disparadas durante a mesma solicitação (usando o campo TransactionId). O primeiro diz que ele correspondeu porque o usuário usou uma URL numérica/IP para a solicitação, o que aumenta a pontuação de anomalias em três, uma vez que é um aviso. A próxima regra correspondente é 942130, que é a que você está procurando. Você pode ver o *1 = 1* no `details.data` campo. Isso aumenta ainda mais a pontuação de anomalias em três, pois também é um aviso. Em geral, cada regra que tem a ação **correspondente** aumenta a pontuação de anomalias e, nesse ponto, a pontuação de anomalias seria seis. Para obter mais informações, consulte [modo de Pontuação de anomalias](ag-overview.md#anomaly-scoring-mode).

As duas entradas finais de log mostram que a solicitação foi bloqueada porque a pontuação de anomalia foi alta o suficiente. Essas entradas têm uma ação diferente das outras duas. Eles mostram que eles realmente *bloquearam* a solicitação. Essas regras são obrigatórias e não podem ser desabilitadas. Eles não devem ser considerados como regras, mas mais como a infra-estrutura principal dos internos do WAF.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>Corrigindo falsos positivos

Com essas informações, e o conhecimento de que a regra 942130 é aquela que correspondeu a *1 = 1* cadeia de caracteres, você pode fazer algumas coisas para impedir que isso bloqueie seu tráfego:

- Usar uma lista de exclusão

   Consulte [configuração do WAF](application-gateway-waf-configuration.md#waf-exclusion-lists) para obter mais informações sobre listas de exclusão.
- Desabilite a regra.

### <a name="using-an-exclusion-list"></a>Usando uma lista de exclusão

Para tomar uma decisão informada sobre a manipulação de um falso positivo, é importante se familiarizar com as tecnologias que seu aplicativo usa. Por exemplo, digamos que não haja um SQL Server em sua pilha de tecnologia e você esteja obtendo falsos positivos relacionados a essas regras. Desabilitar essas regras não reduz necessariamente a segurança.

Um benefício de usar uma lista de exclusão é que apenas uma parte específica de uma solicitação está sendo desabilitada. No entanto, isso significa que uma exclusão específica é aplicável a todo o tráfego que passa pelo seu WAF porque é uma configuração global. Por exemplo, isso pode levar a um problema se *1 = 1* for uma solicitação válida no corpo de um determinado aplicativo, mas não para outros. Outro benefício é que você pode escolher entre o corpo, os cabeçalhos e os cookies a serem excluídos se uma determinada condição for atendida, em vez de excluir a solicitação inteira.

Ocasionalmente, há casos em que parâmetros específicos são passados para o WAF de uma maneira que pode não ser intuitiva. Por exemplo, há um token que é passado durante a autenticação usando Azure Active Directory. Esse token, *__RequestVerificationToken*, geralmente é passado como um cookie de solicitação. No entanto, em alguns casos em que os cookies estão desabilitados, esse token também é passado como um atributo de solicitação ou "ARG". Se isso acontecer, você precisará garantir que *__RequestVerificationToken* também seja adicionada à lista de exclusões como um **nome de atributo de solicitação** .

![Exclusões](../media/web-application-firewall-troubleshoot/exclusion-list.png)

Neste exemplo, você deseja excluir o nome do **atributo de solicitação** que é igual a *text1*. Isso é aparente porque você pode ver o nome do atributo nos logs de firewall: **dados: dados correspondidos: 1 = 1 encontrado em args: text1:1 = 1**. O atributo é **text1**. Você também pode encontrar esse nome de atributo de outras maneiras, consulte [localizando nomes de atributo de solicitação](#finding-request-attribute-names).

![Listas de exclusão de WAF](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Desabilitando regras

Outra maneira de contornar um falso positivo é desabilitar a regra que correspondeu à entrada que o WAF pensou foi mal-intencionado. Como você analisou os logs do WAF e restringiu a regra para 942130, você pode desabilitá-la no portal do Azure. Consulte [Personalizar regras de firewall do aplicativo Web por meio do portal do Azure](application-gateway-customize-waf-rules-portal.md).

Um dos benefícios de desabilitar uma regra é que, se você souber que todo o tráfego que contém uma determinada condição que normalmente será bloqueada é um tráfego válido, poderá desabilitar essa regra para todo o WAF. No entanto, se ele for apenas um tráfego válido em um caso de uso específico, você abrirá uma vulnerabilidade desabilitando essa regra para o WAF inteiro, uma vez que ela é uma configuração global.

Se você quiser usar Azure PowerShell, consulte [Personalizar as regras de firewall do aplicativo Web por meio do PowerShell](application-gateway-customize-waf-rules-powershell.md). Se você quiser usar CLI do Azure, consulte [Personalizar regras de firewall do aplicativo Web por meio do CLI do Azure](application-gateway-customize-waf-rules-cli.md).

![Regras de WAF](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Localizando nomes de atributo de solicitação

Com a ajuda do [Fiddler](https://www.telerik.com/fiddler), você inspeciona solicitações individuais e determina quais campos específicos de uma página da Web são chamados. Isso pode ajudar a excluir determinados campos da inspeção usando listas de exclusão.

Neste exemplo, você pode ver que o campo onde a cadeia de caracteres *1 = 1* foi inserida é chamado **text1**.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-1.png" alt-text="Captura de tela do progresso do depurador Web Telerik Fiddler. Na guia RAW, 1 = 1 é visível após o nome text1." border="false":::

Esse é um campo que você pode excluir. Para saber mais sobre listas de exclusão, consulte [limites de tamanho de solicitação de firewall do aplicativo Web e listas de exclusão](application-gateway-waf-configuration.md#waf-exclusion-lists). Você pode excluir a avaliação nesse caso Configurando a seguinte exclusão:

![WAF exclusão](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

Você também pode examinar os logs de firewall para obter as informações para ver o que precisa adicionar à lista de exclusões. Para habilitar o registro em log, consulte [integridade de back-end, logs de recursos e métricas para o gateway de aplicativo](../../application-gateway/application-gateway-diagnostics.md).

Examine o log do firewall e exiba o PT1H.jsno arquivo para a hora em que a solicitação que você deseja inspecionar ocorreu.

Neste exemplo, você pode ver que tem quatro regras com o mesmo TransactionId e que todas elas ocorreram ao mesmo tempo:

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

Com seu conhecimento de como a regra do CRS define o funcionamento e que o conjunto de regras do CRS 3,0 funciona com um sistema de Pontuação de anomalias (consulte [Firewall do aplicativo Web para gateway de aplicativo Azure](ag-overview.md)), você sabe que as duas regras inferiores com a **ação: Propriedade bloqueada** são bloqueios com base na pontuação de anomalia total. As regras para se concentrar são as duas principais.

A primeira entrada é registrada porque o usuário usou um endereço IP numérico para navegar até o gateway de aplicativo, que pode ser ignorado nesse caso.

A segunda (regra 942130) é a interessante. Você pode ver nos detalhes que ele correspondeu a um padrão (1 = 1) e o campo é chamado de **text1**. Siga as mesmas etapas anteriores para excluir o **nome do atributo de solicitação** que **é igual** a **1 = 1**.

## <a name="finding-request-header-names"></a>Localizando nomes de cabeçalho de solicitação

O Fiddler é uma ferramenta útil mais uma vez para localizar nomes de cabeçalho de solicitação. Na captura de tela a seguir, você pode ver os cabeçalhos dessa solicitação GET, que incluem *Content-Type*, *User-Agent*e assim por diante.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-2.png" alt-text="Captura de tela do progresso do depurador Web Telerik Fiddler. A guia bruto lista detalhes do cabeçalho da solicitação, como a conexão, o tipo de conteúdo e o agente do usuário." border="false":::

Outra maneira de exibir cabeçalhos de solicitação e resposta é procurar nas ferramentas de desenvolvedor do Chrome. Você pode pressionar F12 ou clicar com o botão direito do mouse > **inspecionar**  ->  **ferramentas para desenvolvedores**e selecionar a guia **rede** . Carregue uma página da Web e clique na solicitação que você deseja inspecionar.

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Localizando nomes de cookie de solicitação

Se a solicitação contiver cookies, a guia **cookies** poderá ser selecionada para exibi-los no Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Restringir parâmetros globais para eliminar falsos positivos

- Desabilitar inspeção do corpo da solicitação

   Ao definir o **corpo da solicitação de inspeção** como desativado, os corpos de solicitação de todo o tráfego não serão avaliados pelo seu WAF. Isso pode ser útil se você souber que os corpos de solicitação não são mal-intencionados para seu aplicativo.

   Ao desabilitar essa opção, apenas o corpo da solicitação não é inspecionado. Os cabeçalhos e cookies permanecem inspecionados, a menos que sejam excluídos usando a funcionalidade da lista de exclusão.

- Limites de tamanho de arquivo

   Limitando o tamanho do arquivo para seu WAF, você está limitando a possibilidade de um ataque ocorrer em seus servidores Web. Ao permitir que arquivos grandes sejam carregados, o risco de seu back-end ser sobrecarregado aumenta. Limitar o tamanho do arquivo a um caso de uso normal para seu aplicativo é apenas outra maneira de evitar ataques.

   > [!NOTE]
   > Se você sabe que seu aplicativo nunca precisará de nenhum carregamento de arquivo acima de um determinado tamanho, você pode restringir isso ao definir um limite.

## <a name="firewall-metrics-waf_v1-only"></a>Métricas de firewall (somente WAF_v1)

Para firewalls de aplicativo Web v1, as seguintes métricas agora estão disponíveis no Portal: 

1. Contagem de solicitação bloqueada do firewall do aplicativo Web o número de solicitações que foram bloqueadas
2. A regra bloqueada de firewall do aplicativo Web conta todas as regras que foram correspondidas **e** a solicitação foi bloqueada
3. Distribuição de regra total do firewall do aplicativo Web todas as regras que foram correspondidas durante a avaliação
     
Para habilitar as métricas, selecione a guia **métricas** no portal e selecione uma das três métricas.

## <a name="next-steps"></a>Próximas etapas

Consulte [como configurar o Firewall do aplicativo Web no gateway de aplicativo](tutorial-restrict-web-traffic-powershell.md).
