---
title: Regra personalizada do firewall do aplicativo Web para a porta frontal do Azure
description: Saiba como usar as regras personalizadas do WAF (firewall do aplicativo Web), protegendo seus aplicativos Web contra ataques mal-intencionados.
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 02b335de7f105d768168d5f798ec9109136d7430
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846271"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Regras personalizadas para o Firewall do aplicativo Web com a porta frontal do Azure
O WAF (firewall do aplicativo Web) do Azure com o serviço de porta frontal permite que você controle o acesso aos seus aplicativos Web com base nas condições que você definir. Uma regra WAF personalizada consiste em um número de prioridade, um tipo de regra, condições de correspondência e uma ação. Há dois tipos de regras personalizadas: regras de correspondência e regras de limite de taxa. Uma regra de correspondência controla o acesso com base em condições de correspondência, enquanto uma regra de limite de taxa controla o acesso com base nas condições de correspondência e nas taxas de solicitações de entrada. Você pode desabilitar uma regra personalizada para impedir que ela seja avaliada, mas ainda manter a configuração. Este artigo discute as regras de correspondência baseadas em parâmetros http.

## <a name="priority-match-conditions-and-action-types"></a>Prioridade, condições de correspondência e tipos de ação
Você pode controlar o acesso com uma regra WAf personalizada que define um número de prioridade, um tipo de regra, condições de correspondência e uma ação. 

- **Priority:** é um inteiro exclusivo que descreve a ordem de avaliação das regras de WAF. As regras com valores inferiores são avaliadas antes das regras com valores mais altos

- **Ação:** define como rotear uma solicitação se uma regra de WAF for correspondida. Você pode escolher uma das ações abaixo para aplicar quando uma solicitação corresponde a uma regra personalizada.

    - *Allow* -WAF encaminha a Quest para o back-end, registra em log uma entrada em logs do WAF e sai.
    - A solicitação de *bloqueio* está bloqueada, o WAF envia resposta ao cliente sem encaminhar a solicitação para o back-end. WAF registra uma entrada nos logs do WAF.
    - *Log* -WAF registra uma entrada nos logs do WAF e continua avaliando a próxima regra.
    - *Redirect* -WAF redireciona a solicitação para um URI especificado, registra uma entrada em logs do WAF e sai.

- **Condição de correspondência:** define uma variável de correspondência, um operador e um valor de correspondência. Cada regra pode conter várias condições de correspondência. Uma condição de correspondência pode ser baseada nas *variáveis de correspondência*abaixo:
    - RemoteAddr (IP do cliente)
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody

- **Operador:** a lista inclui o seguinte:
    - Any: é geralmente usado para definir a ação padrão se nenhuma regra for correspondida. Any é um operador match ALL.
    - IPMatch: definir a restrição de IP para a variável RemoteAddr
    - Geocorrespondente: definir a filtragem geográfica para a variável RemoteAddr
    - Igual
    - Contém:
    - LessThan: restrição de tamanho
    - GreaterThan: restrição de tamanho
    - LessThanOrEqual: restrição de tamanho
    - GreaterThanOrEqual: restrição de tamanho
    - BeginsWith
     - EndsWith

Você pode definir  a condição de negação como true se o resultado de uma condição deve ser negado.

O *valor de correspondência* define a lista de possíveis valores de correspondência.
Os valores do método de solicitação HTTP com suporte incluem:
- OBTER
- POSTAR
- PUT
- HEAD
- DELETE
- PROPRIETÁRIO
- AUTOMÁTICO
- CRIAR
- OPÇÕES
- PROPFIND
- DEFINE
- MKCOL
- COPIAROBJETO
- PROSSEGUIR

## <a name="examples"></a>Exemplos

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Exemplo de regras personalizadas do WAF com base em parâmetros http

Aqui está um exemplo que mostra a configuração de uma regra personalizada com duas condições de correspondência. As solicitações são de um site especificado, conforme definido pelo referenciador, e a cadeia de caracteres de consulta não contém "senha".

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
Uma configuração de exemplo para bloquear o método "PUT" é mostrada abaixo:

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

Você pode criar uma regra personalizada que especifique a restrição de tamanho em parte de uma solicitação de entrada. Por exemplo, a regra abaixo bloqueia uma URL com mais de 100 caracteres.

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
- Saiba mais sobre o [Firewall do aplicativo Web](waf-overview.md)
- Saiba como [criar um Front Door](quickstart-create-front-door.md).

