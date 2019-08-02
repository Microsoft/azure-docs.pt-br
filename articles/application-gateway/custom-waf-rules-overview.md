---
title: Azure regras personalizadas v2 de Firewall de aplicativo da Web (WAF)
description: Este artigo fornece uma visão geral de regras personalizadas de v2 de Firewall de aplicativo da Web (WAF) no Gateway de aplicativo do Azure.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: f6ea831771a8ffecfdd4c7c0d6374c16894e25ed
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164656"
---
# <a name="custom-rules-for-web-application-firewall-v2"></a>Regras personalizadas para o Firewall do aplicativo Web v2

O Azure Application Gateway Web aplicativo WAF (Firewall) v2 vem com um conjunto de regras pré-configuradas, plataforma gerenciada que oferece proteção contra muitos tipos diferentes de ataques. Esses ataques incluem entre scripts sites, injeção de SQL e outros. Se você for um administrador de WAF, você talvez queira escrever a suas próprias regras para aumentar a regra principal definir regras (CRS). As regras podem bloquear ou permitir tráfego solicitado com base em critérios de correspondência.

Regras personalizadas permitem que você crie suas próprias regras que estão sendo avaliadas para cada solicitação que passa pelo WAF. Essas regras mantenha uma prioridade maior que o restante das regras em conjuntos de regras gerenciado. As regras personalizadas contêm um nome de regra, a prioridade da regra e uma matriz de critérios de correspondência. Se essas condições forem atendidas, uma ação é executada (para permitir ou bloquear).

Por exemplo, você pode bloquear todas as solicitações de um endereço IP no 192.168.5.4/24 intervalo. Nessa regra, é o operador *IPMatch*, o matchValues é o intervalo de endereços IP (192.168.5.4/24) e a ação é bloquear o tráfego. Você também definir o nome e a prioridade da regra.

Dar suporte a regras personalizadas, usando a lógica de composição para tornar mais avançado de regras que abordam que suas necessidades de segurança. Por exemplo, (condição 1 **e** condição 2) **ou** condição 3).  Este exemplo significa que, se condição 1 **e** condição 2 forem atendidos, **ou** se 3 da condição for atendida, o WAF deve levar a ação especificada na regra personalizada.

Condições de correspondência diferentes dentro da mesma regra sempre são compostas usando **e**. Por exemplo, bloquear o tráfego de um endereço IP específico, e somente se eles estão usando um determinado navegador.

Se você quiser **ou** diferentes condições, as duas condições devem ser nas regras diferentes. Por exemplo, bloquear o tráfego de um tráfego específico de endereço ou bloco IP se eles estão usando um navegador específico.

> [!NOTE]
> O número máximo de regras personalizadas do WAF é 100. Para obter mais informações sobre os limites de Gateway de aplicativo, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md#application-gateway-limits).

Expressões regulares também têm suporte em regras personalizadas, assim como em conjuntos de regras de CRS. Para obter exemplos deles, consulte exemplos 3 e 5 [criar e usar regras de firewall de aplicativo da web personalizado](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Permitindo versus bloqueio

Permitir e bloquear o tráfego são simple com regras personalizadas. Por exemplo, você pode bloquear todo o tráfego proveniente de um intervalo de endereços IP. Você pode fazer outra regra para permitir o tráfego se a solicitação vier de um navegador específico.

Para permitir que algo, certifique-se de que o `-Action` parâmetro é definido como **permitir**. Para bloquear algo, certifique-se de que o `-Action` parâmetro é definido como **bloco**.

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Anterior `$BlockRule` é mapeado para a seguinte regra personalizada no Azure Resource Manager:

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationConditon": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

Essa regra personalizada contém um nome, prioridade, uma ação e a matriz de correspondência de condições que devem ser atendidas para a ação entrar em vigor. Para obter mais explicações desses campos, consulte as seguintes descrições do campo. Por exemplo regras personalizadas, consulte [criar e usar regras de firewall de aplicativo da web personalizado](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Campos para regras personalizadas

### <a name="name-optional"></a>Nome [opcional]

Esse é o nome da regra. Esse nome aparece nos logs.

### <a name="priority-required"></a>Prioridade [obrigatório]

- Determina a ordem em que as regras são avaliadas no. Quanto menor o valor, mais antiga a avaliação da regra.
-Deve ser exclusivo entre todas as regras personalizadas. Uma regra com prioridade 100 será avaliada antes de uma regra com prioridade 200.

### <a name="rule-type-required"></a>Tipo de regra [obrigatório]

Atualmente, deve ser **MatchRule**.

### <a name="match-variable-required"></a>Variável de correspondência [obrigatório]

Deve ser uma das variáveis:

- RemoteAddr – IP endereço/nome do host da conexão do computador remoto
- RequestMethod – método de solicitação HTTP (GET, POST, PUT, DELETE e assim por diante.)
- QueryString – variável no URI
- PostArgs – os argumentos enviados no corpo da POSTAGEM
- RequestUri – o URI da solicitação
- RequestHeaders – cabeçalhos da solicitação
- RequestBody – o corpo da solicitação
- RequestCookies – Cookies da solicitação

### <a name="selector-optional"></a>Seletor de [opcional]

Descreve o campo da coleção matchVariable. Por exemplo, se o matchVariable for RequestHeaders, o seletor pode ser na *User-Agent* cabeçalho.

### <a name="operator-required"></a>Operador [obrigatório]

Deve ser um dos seguintes operadores:

- IPMatch - usado somente quando a variável de correspondência é *RemoteAddr*
- É igual a – de entrada é o mesmo que o MatchValue
- Contém:
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex

### <a name="negate-condition-optional"></a>Negar a condição [opcional]

Nega a condição atual.

### <a name="transform-optional"></a>Transformação [opcional]

Tentativa de obter uma lista de cadeias de caracteres com nomes de transformações para fazer antes da correspondência. Eles podem ser as seguintes transformações:

- Letras minúsculas
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Corresponder aos valores [obrigatório]

Lista de valores para correspondência com, que pode ser considerada como sendo *ou*' ed. Por exemplo, é possível endereços IP ou outras cadeias de caracteres. O formato do valor depende do operador anterior.

### <a name="action-required"></a>Ação [obrigatório]

- Permitir – autoriza a transação, ignorando todas as demais regras. Isso significa que a solicitação especificada é adicionada à lista de permissões e depois correspondentes, a solicitação para avaliação adicional e é enviada para o pool de back-end. Regras que estão na lista de permissões não são avaliadas para qualquer ainda mais as regras personalizadas ou regras gerenciadas.
- Bloquear – bloqueia a transação com base em *SecDefaultAction* (modo de detecção/prevenção). Assim como a ação de permissão, depois que a solicitação é avaliada e adicionada à lista de bloqueios, avaliação for interrompida e a solicitação é bloqueada. Qualquer solicitação depois que satisfazem as mesmas condições não será avaliada e apenas será bloqueada. 
- Log – permite que a regra de gravar no log, mas permite que o restante das regras de execução para avaliação. Regras personalizadas subsequentes são avaliadas na ordem de prioridade, seguida pelas regras gerenciadas.

## <a name="next-steps"></a>Próximas etapas

Depois que você saiba mais sobre regras personalizadas, [criar suas próprias regras personalizadas](create-custom-waf-rules.md).
