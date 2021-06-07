---
title: Regras personalizadas do WAF (Firewall de Aplicativo Web) do Azure v2 no Gateway de Aplicativo
description: Este artigo fornece uma visão geral das regras personalizadas do WAF (Firewall de Aplicativo Web) v2 no Gateway de Aplicativo do Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 9a5f64687937479d65f94010bbe4f0a5f1cf5ca2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548216"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Regras personalizadas do Firewall de Aplicativo Web v2 no Gateway de Aplicativo do Azure

O WAF (Firewall de Aplicativo Web) v2 do Gateway de Aplicativo do Azure vem com um conjunto de regras predefinido e gerenciado por plataforma que oferece proteção contra vários tipos diferentes de ataques. Esses ataques incluem scripts entre sites, injeção de SQL e outros. Se você for um administrador do WAF, convém escrever regras próprias para ampliar as regras do CRS (conjunto de regras principais). Suas regras podem bloquear ou permitir o tráfego solicitado com base nos critérios de correspondência.

As regras personalizadas permitem que você crie regras próprias avaliadas para cada solicitação que passa pelo WAF. Essas regras têm uma prioridade mais alta do que o restante das regras nos conjuntos de regras gerenciadas. As regras personalizadas contêm um nome de regra, prioridade de regra e a matriz de condições de correspondência. Se essas condições forem atendidas, será executada uma ação (para permitir ou bloquear).

Por exemplo, você pode bloquear todas as solicitações provenientes de endereços IP pertencentes ao intervalo 192.168.5.4/24. Nessa regra, o operador é o *IPMatch*, os valores de correspondência são o intervalo de endereços IP (192.168.5.4/24) e a ação é bloquear o tráfego. Você também define o nome e a prioridade da regra.

As regras personalizadas dão suporte ao uso da lógica de composição para fazer regras mais avançadas que atendam às suas necessidades de segurança. Por exemplo, (Condição 1 **e** Condição 2 **ou** Condição 3). Isso significa que, se a Condição 1 **e** a Condição 2 forem atendidas **ou** se a Condição 3 for atendida, o WAF deverá executar a ação especificada na regra personalizada.

Diferentes condições de correspondência dentro da mesma regra sempre são compostas usando **e**. Por exemplo, bloqueie o tráfego de um endereço IP específico, somente se ele estiver usando um determinado navegador.

Se você quiser usar **ou** entre duas condições diferentes, ambas deverão estar em regras diferentes. Por exemplo, bloqueie o tráfego de um endereço IP específico ou bloqueie o tráfego se ele estiver usando um navegador específico.

> [!NOTE]
> O número máximo de regras personalizadas do WAF é 100. Para obter mais informações sobre os limites do Gateway de Aplicativo, confira [Assinatura do Azure e limites de serviços, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

Também há suporte para expressões regulares nas regras personalizadas, assim como nos conjuntos de regras do CRS. Para ver mais exemplos, confira os exemplos 3 e 5 em [Criar e usar regras personalizadas de Firewall de Aplicativo Web](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Permitindo vs. bloqueando

Permitir e bloquear o tráfego é simples com regras personalizadas. Por exemplo, é possível bloquear todo o tráfego proveniente de um intervalo de endereços IP. Você pode fazer outra regra para permitir o tráfego se a solicitação vier de um navegador específico.

Para permitir algo, verifique se o parâmetro `-Action` está definido como **Permitir**. Para bloquear algo, verifique se o parâmetro `-Action` está definido como **Bloquear**.

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

O `$BlockRule` anterior é mapeado para a seguinte regra personalizada no Azure Resource Manager:

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
            "negationCondition": false,
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

Essa regra personalizada contém um nome, uma prioridade, uma ação e a matriz de condições de correspondência que devem ser atendidas para que a ação ocorra. Para obter mais explicações sobre esses campos, confira as descrições de campo a seguir. Para ver exemplos de regras personalizadas, confira [Criar e usar regras personalizadas de Firewall de Aplicativo Web](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Campos para regras personalizadas

### <a name="name-optional"></a>Nome [opcional]

O nome da regra.  Aparece nos logs.

### <a name="priority-required"></a>Prioridade [obrigatório]

- Determina a ordem de avaliação da regra. Quanto menor o valor, mais cedo será a avaliação da regra. O intervalo permitido é de 1-100. 
- Deve ser exclusivo para cada regra personalizada. A regra com prioridade 40 será avaliada antes da regra com prioridade 80.

### <a name="rule-type-required"></a>Tipo de regra [obrigatório]

No momento, deve ser **MatchRule**.

### <a name="match-variable-required"></a>Variável de correspondência [obrigatório]

Deve ser uma das variáveis:

- RemoteAddr – Endereço IP/nome do host da conexão com o computador remoto
- RequestMethod – Método de solicitação HTTP (GET, POST, PUT, DELETE e assim por diante).
- QueryString – Variável no URI
- PostArgs – Argumentos enviados no corpo do POST. As Regras Personalizadas que usam essa variável de correspondência serão aplicadas somente se o cabeçalho "Content-Type" estiver definido como "application/x-www-form-urlencoded" e "multipart/form-data".
- RequestUri – URI da solicitação
- RequestHeaders – Cabeçalhos da solicitação
- RequestBody – Contém o corpo da solicitação como um todo. As Regras Personalizadas que usam essa variável de correspondência serão aplicadas somente se o cabeçalho "Content-Type" estiver definido como "application/x-www-form-urlencoded". 
- RequestCookies – Cookies da solicitação

### <a name="selector-optional"></a>Seletor [opcional]

Descreve o campo da coleção matchVariable. Por exemplo, se matchVariable for RequestHeaders, o seletor poderá estar no cabeçalho *User-Agent*.

### <a name="operator-required"></a>Operador [obrigatório]

Deve ser um dos seguintes operadores:

- IPMatch – Usado somente quando a variável de correspondência é *RemoteAddr*
- Igual – A entrada é a mesma que o MatchValue
- Contém
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex
- Geomatch (versão prévia)

### <a name="negate-condition-optional"></a>Negar condição [opcional]

Nega a condição atual.

### <a name="transform-optional"></a>Transformação [opcional]

Uma lista de cadeias de caracteres com nomes de transformações a serem realizadas antes da tentativa de correspondência. Elas podem ser as seguintes transformações:

- Minúsculas
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Valores de correspondência [obrigatório]

Lista de valores com os quais fazer correspondência, que pode aplicados com o operador *OR*. Por exemplo, podem ser endereços IP ou outras cadeias de caracteres. O formato do valor depende do operador anterior.

### <a name="action-required"></a>Ação [obrigatório]

- Permitir – Autoriza a transação, ignorando todas as outras regras. A solicitação especificada é adicionada à lista de permitidos e, uma vez encontrada a correspondência, a solicitação interrompe as avaliações adicionais e é enviada ao pool de back-end. As regras que estão na lista de permitidos não são avaliadas quanto a outras regras personalizadas ou gerenciadas.
- Bloquear – Bloqueia a transação com base em *SecDefaultAction* (modo de detecção/prevenção). Assim como a ação Permitir, depois que a solicitação é avaliada e adicionada à lista de bloqueios, a avaliação é interrompida e a solicitação é bloqueada. Qualquer solicitação posterior que atenda às mesmas condições não será avaliada e será bloqueada. 
- Log – Permite que a regra grave no log, mas permite que o restante das regras seja executado para avaliação. As outras regras personalizadas são avaliadas em ordem de prioridade, seguidas pelas regras gerenciadas.

## <a name="geomatch-custom-rules-preview"></a>Regras personalizadas do Geomatch (versão prévia)

As regras personalizadas permitem que você crie regras adaptadas para atender às necessidades exatas dos seus aplicativos e das suas políticas de segurança. Você pode restringir o acesso aos seus aplicativos Web por país/região. Para obter mais informações, confira [Regras personalizadas do Geomatch (versão prévia)](geomatch-custom-rules.md).

## <a name="next-steps"></a>Próximas etapas

Depois de aprender sobre regras personalizadas, [crie suas próprias regras personalizadas](create-custom-waf-rules.md).
