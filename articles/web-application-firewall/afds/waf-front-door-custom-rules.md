---
title: Regra personalizada do firewall do aplicativo Web para a porta frontal do Azure
description: Saiba como usar as regras personalizadas do WAF (firewall do aplicativo Web), protegendo seus aplicativos Web contra ataques mal-intencionados.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "79475817"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Regras personalizadas para o Firewall do aplicativo Web com a porta frontal do Azure

O Firewall do aplicativo Web do Azure (WAF) com a porta frontal permite que você controle o acesso aos seus aplicativos Web com base nas condições que você definir. Uma regra WAF personalizada consiste em um número de prioridade, tipo de regra, condições de correspondência e uma ação. Há dois tipos de regras personalizadas: regras de correspondência e regras de limite de taxa. Uma regra de correspondência controla o acesso com base em um conjunto de condições de correspondência, enquanto uma regra de limite de taxa controla o acesso com base nas condições de correspondência e nas taxas de solicitações de entrada. Você pode desabilitar uma regra personalizada para impedir que ela seja avaliada, mas ainda manter a configuração. 

## <a name="priority-match-conditions-and-action-types"></a>Prioridade, condições de correspondência e tipos de ação

Você pode controlar o acesso com uma regra WAf personalizada que define um número de prioridade, um tipo de regra, uma matriz de condições de correspondência e uma ação. 

- **Priority:** é um inteiro exclusivo que descreve a ordem de avaliação das regras de WAF. As regras com valores de prioridade mais baixa são avaliadas antes das regras com valores mais altos. Os números de prioridade devem ser exclusivos entre todas as regras personalizadas.

- **Ação:** define como rotear uma solicitação se uma regra de WAF for correspondida. Você pode escolher uma das ações abaixo para aplicar quando uma solicitação corresponde a uma regra personalizada.

    - *Allow* -WAF encaminha a Quest para o back-end, registra em log uma entrada em logs do WAF e sai.
    - A solicitação de *bloqueio* está bloqueada, o WAF envia resposta ao cliente sem encaminhar a solicitação para o back-end. WAF registra uma entrada nos logs do WAF.
    - *Log* -WAF registra uma entrada nos logs do WAF e continua a avaliar a próxima regra.
    - *Redirect* -WAF redireciona a solicitação para um URI especificado, registra uma entrada em logs do WAF e sai.

- **Condição de correspondência:** define uma variável de correspondência, um operador e um valor de correspondência. Cada regra pode conter várias condições de correspondência. Uma condição de correspondência pode ser baseada em localização geográfica, endereços IP do cliente (CIDR), tamanho ou correspondência de cadeia de caracteres. A correspondência de cadeia de caracteres pode ser em uma lista de variáveis de correspondência.
  - **Variável de correspondência:**
    - RequestMethod
    - QueryString
    - Argumentos de subargs
    - RequestUri
    - RequestHeader
    - RequestBody
    - Cookies
  - **Operador**
    - Any: é geralmente usado para definir a ação padrão se nenhuma regra for correspondida. Any é um operador match ALL.
    - Igual a
    - Contém
    - LessThan: restrição de tamanho
    - GreaterThan: restrição de tamanho
    - LessThanOrEqual: restrição de tamanho
    - GreaterThanOrEqual: restrição de tamanho
    - BeginsWith
    - EndsWith
    - Regex
  
  - O **Regex** não oferece suporte às seguintes operações: 
    - Referências anteriores e captura de subexpressãos
    - Asserções de largura zero arbitrárias
    - Referências de sub-rotinas e padrões recursivos
    - Padrões condicionais
    - Verbos de controle de retrocesso
    - A diretiva \c Single-byte
    - A diretiva de correspondência de nova linha \r
    - A diretiva de início de redefinição de correspondência \K
    - Textos explicativos e código inserido
    - Quantificadores de Possessive e agrupamento atômico

  - **Negar [opcional]:** Você pode definir a condição de *negação* como true se o resultado de uma condição deve ser negado.
      
  - **Transform [opcional]:** Uma lista de cadeias de caracteres com nomes de transformações a serem realizadas antes da tentativa de correspondência. Elas podem ser as seguintes transformações:
     - Letras Maiúsculas 
     - Letras minúsculas
     - Trim
     - RemoveNulls
     - UrlDecode
     - UrlEncode
     
   - **Valor de correspondência:** Os valores do método de solicitação HTTP com suporte incluem:
     - GET
     - POST
     - PUT
     - HEAD
     - Delete (excluir)
     - LOCK
     - UNLOCK
     - PERFIL
     - OPÇÕES
     - PROPFIND
     - DEFINE
     - MKCOL
     - COPIAR
     - MOVE

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
- [Configurar uma política de firewall do aplicativo Web usando Azure PowerShell](waf-front-door-custom-rules-powershell.md) 
- Saiba mais sobre o [Firewall do aplicativo Web com a porta frontal](afds-overview.md)
- Saiba como [criar um Front Door](../../frontdoor/quickstart-create-front-door.md).

