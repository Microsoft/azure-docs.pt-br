---
title: Regra personalizada da firewall de aplicativo da Web para frente do Azure
description: Saiba como usar o WAF (firewall) personalizado regras de aplicativo web protegendo seus aplicativos web contra ataques mal-intencionados.
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud;tyao
ms.openlocfilehash: 744c6fb9235c9daa2d5239ef9fd13679db943650
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459701"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Regras personalizadas para o firewall do aplicativo web da frente com o Azure
Firewall do aplicativo web do Azure (WAF) com o serviço de porta da frente permite que você controle o acesso aos seus aplicativos web com base nas condições que você definir. Uma regra personalizada do WAF consiste em um número de prioridade, um tipo de regra, condições de correspondência e uma ação. Há dois tipos de regras personalizadas: coincide com regras e regras de limite de taxa. Uma regra de correspondência controla o acesso com base em condições de correspondência, enquanto uma regra de limite de taxa controla o acesso com base na correspondência de condições e as taxas de solicitações de entrada. Você pode desabilitar uma regra personalizada para impedir que ele está sendo avaliada, mas ainda manter a configuração. Este artigo discute as regras de correspondência com base em parâmetros de http.

## <a name="priority-match-conditions-and-action-types"></a>Prioridade, condições de correspondência e tipos de ação
Você pode controlar o acesso com uma regra WAf personalizada que define um número de prioridade, um tipo de regra, condições de correspondência e uma ação. 

- **Prioridade:** é um inteiro exclusivo que descreve a ordem de avaliação das regras de WAF. As regras com valores mais baixos são avaliadas antes das regras com valores mais altos

- **Ação:** define como uma solicitação de rota se corresponder a uma regra WAF. Você pode escolher uma do abaixo de ações a serem aplicadas quando uma solicitação corresponde a uma regra personalizada.

    - *Permitir* -WAF encaminha a busca para o back-end, registra uma entrada nos logs de WAF e sai.
    - *Bloco* -solicitação é bloqueada, o WAF envia a resposta ao cliente sem encaminhar a solicitação para o back-end. WAF registra uma entrada em logs de WAF.
    - *Log* -logs de WAF uma entrada no WAF registra em log e continua avaliar a próxima regra.
    - *Redirecionar* -WAF redireciona a solicitação para um URI especificado, registra uma entrada nos logs de WAF e será encerrado.

- **Condição de correspondência:** define uma variável de correspondência, um operador e corresponde ao valor. Cada regra pode conter várias condições de correspondência. Uma condição de correspondência pode se basear na abaixo *corresponder variáveis*:
    - RemoteAddr (IP do cliente)
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody

- **Operador:** lista inclui o seguinte:
    - Qualquer: geralmente é usado para definir a ação padrão se nenhuma regra forem atendida. Qualquer é uma correspondência operador all.
    - IPMatch: definir a restrição de IP para RemoteAddr variável
    - GeoMatch: definir geográfica filtragem RemoteAddr variável
    - Igual a
    - Contém:
    - LessThan: restrição de tamanho
    - GreaterThan: restrição de tamanho
    - LessThanOrEqual: size constraint
    - GreaterThanOrEqual: size constraint
    - BeginsWith
     - EndsWith

Você pode definir *negate* condição seja verdadeira se o resultado de uma condição deve ser negado.

*Corresponde ao valor* define a lista de valores de correspondência possível.
Suporte para o método de solicitação HTTP, os valores incluem:
- GET
- POST
- PUT
- HEAD
- EXCLUIR
- BLOQUEIO
- DESBLOQUEAR
- PERFIL
- OPÇÕES
- PROPFIND
- PROPPATCH
- MKCOL
- CÓPIA
- MOVER

## <a name="examples"></a>Exemplos

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Exemplo de regras personalizadas de WAF com base nos parâmetros de http

Aqui está um exemplo que mostra a configuração de uma regra personalizada com duas condições de correspondência. As solicitações são de um site especificado, conforme definido por referenciador e cadeia de caracteres de consulta não contém "senha".

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
Um exemplo de configuração para bloqueio de método "PUT" é mostrado abaixo:

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>Restrição de tamanho

Você pode criar uma regra personalizada que especifica a restrição de tamanho de parte de uma solicitação de entrada. Por exemplo, abaixo da regra bloqueia uma Url que é maior que 100 caracteres.

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [firewall do aplicativo web](waf-overview.md)
- Saiba como [criar um Front Door](quickstart-create-front-door.md).

