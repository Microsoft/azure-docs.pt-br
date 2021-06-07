---
title: Ajustando o WAF (firewall do aplicativo Web) para a porta frontal do Azure
description: Neste artigo, você aprenderá a ajustar o WAF para a porta frontal.
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: b2f551257fb6869d5dec47014be3a8522b61b9fa
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506626"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Ajustando o WAF (firewall do aplicativo Web) para a porta frontal do Azure
 
O conjunto de regras padrão gerenciadas pelo Azure é baseado no [CRS (OWASP Core Rule Set)](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) e é projetado para ser estrito. Geralmente, é esperado que as regras de WAF precisem ser ajustadas para atender às necessidades específicas do aplicativo ou da organização usando o WAF. Isso é normalmente obtido pela definição de exclusões de regra, criação de regras personalizadas e até mesmo a desabilitação de regras que podem estar causando problemas ou falsos positivos. Há algumas coisas que você pode fazer se as solicitações que devem passar pelo firewall do aplicativo Web (WAF) estiverem bloqueadas.

Primeiro, verifique se você leu a [visão geral de WAF da porta frontal](afds-overview.md) e a [política WAF para documentos de porta frontal](waf-front-door-create-portal.md) . Além disso, verifique se você habilitou o [monitoramento e o registro em log do WAF](waf-front-door-monitor.md). Estes artigos explicam como o WAF funciona, como a regra WAF define o trabalho e como acessar logs do WAF.
 
## <a name="understanding-waf-logs"></a>Noções básicas sobre logs do WAF
 
A finalidade dos logs do WAF é mostrar cada solicitação que é correspondida ou bloqueada pelo WAF. É uma coleção de todas as solicitações avaliadas que são correspondidas ou bloqueadas. Se você observar que o WAF bloqueia uma solicitação que não deveria (um falso positivo), você pode fazer algumas coisas. Primeiro, restrinja e localize a solicitação específica. Se desejar, você pode [Configurar uma mensagem de resposta personalizada](./waf-front-door-configure-custom-response-code.md) para incluir o `trackingReference` campo para identificar facilmente o evento e executar uma consulta de log nesse valor específico. Examine os logs para localizar o URI específico, o carimbo de data/hora ou o IP do cliente da solicitação. Ao encontrar as entradas de log relacionadas, você pode começar a agir em falsos positivos. 
 
Por exemplo, digamos que você tenha um tráfego legítimo contendo a cadeia de caracteres `1=1` que você deseja passar pelo seu WAF. Esta é a aparência da solicitação:

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

Se você tentar a solicitação, o WAF bloqueará o tráfego que contém a cadeia de caracteres *1 = 1* em qualquer parâmetro ou campo. Essa é uma cadeia de caracteres com frequência associada a um ataque de injeção de SQL. Você pode examinar os logs e ver o carimbo de data/hora da solicitação e as regras que foram bloqueadas/correspondidas.
 
No exemplo a seguir, exploramos um `FrontdoorWebApplicationFirewallLog` log gerado devido a uma correspondência de regra. A consulta Log Analytics a seguir pode ser usada para localizar solicitações que foram bloqueadas nas últimas 24 horas:

```kusto
AzureDiagnostics
| where Category == 'FrontdoorWebApplicationFirewallLog'
| where TimeGenerated > ago(1d)
| where action_s == 'Block'

```
 
No `requestUri` campo, você pode ver que a solicitação foi feita `/api/Feedbacks/` especificamente. Indo além, encontramos a ID da regra `942110` no `ruleName` campo. Sabendo a ID da regra, você pode ir para o [OWASP ModSecurity Core Rule Set oficial Repository](https://github.com/coreruleset/coreruleset) e Pesquisar por essa [ID de regra](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) para examinar seu código e entender exatamente o que essa regra corresponde. 
 
Em seguida, ao verificar o `action` campo, vemos que essa regra está definida para bloquear solicitações na correspondência e confirmamos que a solicitação foi, de fato, bloqueada pelo WAF porque o `policyMode` está definido como `prevention` . 
 
Agora, vamos verificar as informações no `details` campo. É aqui que você pode ver as `matchVariableName` informações e `matchVariableValue` . Aprendemos que essa regra foi disparada porque alguém inseriu *1 = 1* no `comment` campo do aplicativo Web.
 
```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```
 
Também há um valor na verificação dos logs de acesso para expandir seu conhecimento sobre um determinado evento WAF. Abaixo, examinamos o `FrontdoorAccessLog` log que foi gerado como uma resposta ao evento acima.
 
Você pode ver que esses são logs relacionados com base no `trackingReference` valor sendo o mesmo. Entre vários campos que fornecem informações gerais, como `userAgent` e `clientIP` , chamamos a atenção para os `httpStatusCode` campos e `httpStatusDetails` . Aqui, podemos confirmar que o cliente recebeu uma resposta HTTP 403, que confirma absolutamente que essa solicitação foi negada e bloqueada. 
 
```json
{
    "time": "2020-09-24T16:43:04.5430764Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "httpMethod": "POST",
        "httpVersion": "1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "requestBytes": "2160",
        "responseBytes": "324",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36",
        "clientIp": "1.1.1.1",
        "socketIp": "1.1.1.1",
        "clientPort": "53566",
        "timeToFirstByte": "0.01",
        "timeTaken": "0.011",
        "securityProtocol": "",
        "routingRuleName": "DemoBERoutingRule",
        "rulesEngineMatchNames": [],
        "backendHostname": "13.88.65.130:3000",
        "isReceivedFromClient": true,
        "httpStatusCode": "403",
        "httpStatusDetails": "403",
        "pop": "WST",
        "cacheStatus": "CONFIG_NOCACHE"
    }
}
```

## <a name="resolving-false-positives"></a>Resolvendo falsos positivos
 
Para tomar uma decisão informada sobre a manipulação de um falso positivo, é importante se familiarizar com as tecnologias que seu aplicativo usa. Por exemplo, digamos que não haja um SQL Server em sua pilha de tecnologia e você esteja obtendo falsos positivos relacionados a essas regras. Desabilitar essas regras não reduz necessariamente a segurança. 

Com essas informações, e o conhecimento que a regra 942110 é aquela que correspondeu à `1=1` cadeia de caracteres em nosso exemplo, podemos fazer algumas coisas para impedir que essa solicitação legítima seja bloqueada:
 
* Usar listas de exclusão
  * Consulte [WAF (firewall do aplicativo Web) com listas de exclusão de serviço de porta frontal](waf-front-door-exclusion.md) para obter mais informações sobre listas de exclusão. 
* Alterar ações de WAF
  * Consulte [ações de WAF](afds-overview.md#waf-actions) para obter mais informações sobre quais ações podem ser executadas quando uma solicitação corresponde às condições de uma regra.
* Usar regras personalizadas
  * Consulte [regras personalizadas para o Firewall do aplicativo Web com a porta frontal do Azure](waf-front-door-custom-rules.md) para obter mais informações sobre regras personalizadas.
* Desabilitar regras 

> [!TIP]
> Ao selecionar uma abordagem para permitir solicitações legítimas por meio do WAF, tente torná-la tão estreita quanto possível. Por exemplo, é melhor usar uma lista de exclusão do que desabilitar totalmente uma regra.

### <a name="using-exclusion-lists"></a>Usando listas de exclusão

Um benefício de usar uma lista de exclusão é que apenas a variável de correspondência que você selecionar para excluir não será mais inspecionada para essa solicitação determinada. Ou seja, você pode escolher entre cabeçalhos de solicitação específicos, cookies de solicitação, argumentos de cadeia de caracteres de consulta ou argumentos de postagem de corpo de solicitação a serem excluídos se uma determinada condição for atendida, em vez de excluir a solicitação inteira de ser inspecionada. As outras variáveis não especificadas da solicitação ainda serão inspecionadas normalmente.
 
É importante considerar que as exclusões são uma configuração global. Isso significa que a exclusão configurada será aplicada a todo o tráfego que passa pelo WAF, não apenas a um aplicativo Web ou URI específico. Por exemplo, isso pode ser uma preocupação se *1 = 1* for uma solicitação válida no corpo de um determinado aplicativo Web, mas não para outros na mesma política de WAF. Se fizer sentido usar diferentes listas de exclusão para aplicativos diferentes, considere usar políticas WAF diferentes para cada aplicativo e aplicá-las ao front-end de cada aplicativo.
 
Ao configurar listas de exclusão para regras gerenciadas, você pode optar por excluir todas as regras dentro de um conjunto de regras, todas as regras dentro de um grupo de regras ou uma regra individual. Uma lista de exclusão pode ser configurada usando o [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject), [CLI do Azure](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion#ext_front_door_az_network_front_door_waf_policy_managed_rules_exclusion_add), [API REST](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)ou o portal do Azure.

* Exclusões em um nível de regra
  * A aplicação de exclusões em um nível de regra significa que as exclusões especificadas não serão analisadas somente em relação a essa regra individual, enquanto ainda serão analisadas por todas as outras regras no conjunto de regras. Esse é o nível mais granular para exclusões e pode ser usado para ajustar o conjunto de regras gerenciadas com base nas informações encontradas nos logs do WAF ao solucionar problemas de um evento.
* Exclusões no nível do grupo de regras
  * A aplicação de exclusões em um nível de grupo de regras significa que as exclusões especificadas não serão analisadas em relação a esse conjunto específico de tipos de regra. Por exemplo, selecionar *sqli* como um grupo de regras excluídas indica que as exclusões de solicitação definidas não seriam inspecionadas por nenhuma das regras específicas do sqli, mas ainda serão inspecionadas por regras em outros grupos, como *php*, *RFI* ou *XSS*. Esse tipo de exclusão pode ser útil quando temos certeza de que o aplicativo não é suscetível a tipos específicos de ataques. Por exemplo, um aplicativo que não tem nenhum banco de dados SQL poderia ter todas as regras *sqli* excluídas sem ser prejudicial para seu nível de segurança.
* Exclusões no nível de conjunto de regras 
  * A aplicação de exclusões em um nível de conjunto de regras significa que as exclusões especificadas não serão analisadas em relação a nenhuma das regras de segurança disponíveis nesse conjunto de regras. Essa é uma exclusão abrangente, portanto, deve ser usada com cuidado.

Neste exemplo, realizaremos uma exclusão no nível mais granular (aplicando a exclusão a uma única regra) e estamos procurando excluir o nome da solicitação de variável de correspondência de **argumentos post** que contém `comment` . Isso é aparente porque você pode ver os detalhes da variável de correspondência no log do firewall: `"matchVariableName": "PostParamValue:comment"` . O atributo é `comment` . Você também pode encontrar esse nome de atributo de outras maneiras, consulte [localizando nomes de atributo de solicitação](#finding-request-attribute-names).

![Regras de exclusão](../media/waf-front-door-tuning/exclusion-rules.png)

![Exclusão de regra para regra específica](../media/waf-front-door-tuning/exclusion-rule.png)

Ocasionalmente, há casos em que parâmetros específicos são passados para o WAF de uma maneira que pode não ser intuitiva. Por exemplo, há um token que é passado durante a autenticação usando Azure Active Directory. Esse token, `__RequestVerificationToken` normalmente é passado como um cookie de solicitação. No entanto, em alguns casos em que os cookies estão desabilitados, esse token também é passado como um argumento de postagem de solicitação. Por esse motivo, para resolver os falsos positivos do token do Azure AD, você precisa garantir que `__RequestVerificationToken` seja adicionado à lista de exclusão para o `RequestCookieNames` e o `RequestBodyPostArgsNames` .

Exclusões em um nome de campo (*seletor*) significa que o valor não será mais avaliado pelo WAF. No entanto, o próprio nome de campo continua sendo avaliado e, em casos raros, ele pode corresponder a uma regra de WAF e disparar uma ação.

![Exclusão de regra para conjunto de regras](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>Alterando ações de WAF

Outra maneira de lidar com o comportamento das regras WAF é escolhendo a ação que será tomada quando uma solicitação corresponder às condições de uma regra. As ações disponíveis são: [permitir, bloquear, registrar e redirecionar](afds-overview.md#waf-actions).

Neste exemplo, alteramos o *bloco* de ação padrão para a ação de *Log* na regra 942110. Isso fará com que o WAF Registre a solicitação e continue avaliando a mesma solicitação em relação às regras de prioridade mais baixa restantes.

![Ações de WAF](../media/waf-front-door-tuning/actions.png)

Depois de executar a mesma solicitação, podemos nos referir aos logs e veremos que essa solicitação foi uma correspondência na ID de regra 942110 e que o `action_s` campo agora indica *log* em vez de *bloco*. Em seguida, expandimos a consulta de log para incluir as `trackingReference_s` informações e ver o que mais aconteceu com essa solicitação.

![Log mostrando várias correspondências de regra](../media/waf-front-door-tuning/actions-log.png)

Curiosamente, vemos que uma correspondência de regra de SQLI diferente ocorre em milissegundos após a ID de regra 942110 ser processada. A mesma solicitação correspondeu à ID de regra 942310 e, desta vez, o *bloco* de ação padrão foi disparado.

Outra vantagem de usar a ação de *log* durante o ajuste WAF ou a solução de problemas é que você pode identificar se várias regras dentro de um grupo de regras específico correspondem e bloqueando uma determinada solicitação. Em seguida, você pode criar suas exclusões no nível apropriado, ou seja, no nível de regra ou grupo de regras. 

### <a name="using-custom-rules"></a>Usando regras personalizadas

Depois de identificar o que está causando uma correspondência de regra de WAF, você pode usar regras personalizadas para ajustar como o WAF responde ao evento. Regras personalizadas são processadas antes das regras gerenciadas, elas podem conter mais de uma condição, e suas ações podem ser [permitir, negar, registrar ou redirecionar](afds-overview.md#waf-actions). Quando há uma correspondência de regra, o mecanismo de WAF para de processar. Isso significa que outras regras personalizadas com menos prioridade e regras gerenciadas não são mais executadas.

No exemplo a seguir, criamos uma regra personalizada com duas condições. A primeira condição está procurando o `comment` valor no corpo da solicitação. A segunda condição é procurar o `/api/Feedbacks/` valor no URI de solicitação.

O uso de uma regra personalizada permite que você seja o mais granular ao ajustar suas regras WAF e para lidar com falsos positivos. Nesse caso, não estamos realizando uma ação apenas com base no `comment` valor do corpo da solicitação, que pode existir em vários sites ou aplicativos na mesma política de WAF. Ao incluir outra condição para corresponder também a um URI de solicitação específico `/api/Feedbacks/` , garantimos que essa regra personalizada realmente se aplique a esse caso de uso explícito que verificados. Isso garante que o mesmo ataque, se executado em condições diferentes, ainda seria inspecionado e impedido pelo mecanismo de WAF.

![Log](../media/waf-front-door-tuning/custom-rule.png)

Ao explorar o log, você pode ver que o `ruleName_s` campo contém o nome fornecido para a regra personalizada que criamos: `redirectcomment` . No `action_s` campo, você pode ver que a ação de *redirecionamento* foi executada para esse evento. No `details_matches_s` campo, podemos ver os detalhes de ambas as condições serem correspondidas.

### <a name="disabling-rules"></a>Desabilitando regras

Outra maneira de contornar um falso positivo é desabilitar a regra que correspondeu à entrada que o WAF pensou foi mal-intencionado. Como você analisou os logs do WAF e restringiu a regra para 942110, você pode desabilitá-la no portal do Azure. Consulte [Personalizar regras de firewall do aplicativo Web usando o portal do Azure](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules).
 
Desabilitar uma regra é um benefício quando você tem certeza de que todas as solicitações que atendem a essa condição específica são solicitações legítimas de fato ou quando você tem certeza de que a regra simplesmente não se aplica ao seu ambiente (como, por exemplo, desabilitar uma regra de injeção de SQL porque você tem back-ends não SQL). 
 
No entanto, desabilitar uma regra é uma configuração global que se aplica a todos os hosts de front-end associados à política WAF. Quando você opta por desabilitar uma regra, você pode estar deixando vulnerabilidades expostas sem proteção ou detecção para quaisquer outros hosts de front-end associados à política WAF.
 
Se você quiser usar Azure PowerShell para desabilitar uma regra gerenciada, consulte a [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject) documentação do objeto. Se você quiser usar CLI do Azure, consulte a [`az network front-door waf-policy managed-rules override`](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/override) documentação.

![Regras de WAF](../media/waf-front-door-tuning/waf-rules.png)

> [!TIP]
> É uma boa ideia documentar quaisquer alterações feitas na política do WAF. Inclua solicitações de exemplo para ilustrar a detecção de falsos positivos e explique claramente por que você adicionou uma regra personalizada, desabilitou uma regra ou conjunto de regras ou adicionou uma exceção. Esta documentação pode ser útil se você reprojetar seu aplicativo no futuro e precisar verificar se suas alterações ainda são válidas. Ele também pode ajudar se você já tiver auditado ou precisar justificar por que você reconfigurou a política de WAF de suas configurações padrão.

## <a name="finding-request-fields"></a>Localizando campos de solicitação

Usando um proxy de navegador como o [Fiddler](https://www.telerik.com/fiddler), você pode inspecionar solicitações individuais e determinar quais campos específicos de uma página da Web são chamados. Isso é útil quando precisamos excluir determinados campos da inspeção usando listas de exclusão no WAF.

### <a name="finding-request-attribute-names"></a>Localizando nomes de atributo de solicitação
 
Neste exemplo, você pode ver o campo onde a `1=1` cadeia de caracteres foi inserida é chamada `comment` . Esses dados foram passados no corpo de uma solicitação POST.

![Solicitação Fiddler mostrando corpo](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

Esse é um campo que você pode excluir. Para saber mais sobre listas de exclusão, consulte [listas de exclusão de firewall do aplicativo Web](./waf-front-door-exclusion.md). Você pode excluir a avaliação nesse caso Configurando a seguinte exclusão:

![Regra de exclusão](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

Você também pode examinar os logs de firewall para obter as informações para ver o que precisa adicionar à lista de exclusões. Para habilitar o registro em log, consulte [monitoramento de métricas e logs na porta frontal do Azure](./waf-front-door-monitor.md).

Examine o log do firewall no `PT1H.json` arquivo para a hora em que a solicitação que você deseja inspecionar ocorreu. `PT1H.json` os arquivos estão disponíveis nos contêineres da conta de armazenamento em que os `FrontDoorWebApplicationFirewallLog` `FrontDoorAccessLog` logs de diagnóstico e são armazenados.

Neste exemplo, você pode ver a regra que bloqueou a solicitação (com a mesma referência de transação) e ocorreu ao mesmo tempo:

```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```

Com o seu conhecimento de como os conjuntos de regras gerenciadas pelo Azure funcionam (consulte [Firewall do aplicativo Web na porta frontal do Azure](afds-overview.md)), você sabe que a regra com a propriedade *ação: bloquear* é o bloqueio com base nos dados correspondidos no corpo da solicitação. Você pode ver nos detalhes que ele correspondeu a um padrão ( `1=1` ) e o campo é nomeado `comment` . Siga as mesmas etapas anteriores para excluir o nome de argumentos de postagem do corpo da solicitação que contém `comment` .

### <a name="finding-request-header-names"></a>Localizando nomes de cabeçalho de solicitação

O Fiddler é uma ferramenta útil mais uma vez para localizar nomes de cabeçalho de solicitação. Na captura de tela a seguir, você pode ver os cabeçalhos dessa solicitação GET, que incluem Content-Type, User-Agent e assim por diante. Você também pode usar cabeçalhos de solicitação para criar exclusões e regras personalizadas no WAF.

![Solicitação Fiddler mostrando cabeçalho](../media/waf-front-door-tuning/fiddler-request-header-name.png)

Outra maneira de exibir cabeçalhos de solicitação e resposta é examinar as ferramentas de desenvolvedor do seu navegador, como o Edge ou o Chrome. Você pode pressionar F12 ou clicar com o botão direito do mouse > **inspecionar**  ->  **ferramentas para desenvolvedores** e selecionar a guia **rede** . Carregue uma página da Web e clique na solicitação que você deseja inspecionar.

![Solicitação de Inspetor de rede](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>Localizando nomes de cookie de solicitação

Se a solicitação contiver cookies, a guia cookies poderá ser selecionada para exibi-los no Fiddler. As informações de cookie também podem ser usadas para criar exclusões ou regras personalizadas no WAF.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Firewall do aplicativo Web do Azure](../overview.md).
- Saiba como [criar um Front Door](../../frontdoor/quickstart-create-front-door.md).
