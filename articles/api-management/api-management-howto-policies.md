---
title: Políticas no Gerenciamento de API do Azure | Microsoft Docs
description: Aprenda a criar, editar e configurar políticas de Gerenciamento de API. Confira exemplos de código e exiba recursos adicionais disponíveis.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 37ac6369790ed526fd923819558863ae84432aed
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358109"
---
# <a name="policies-in-azure-api-management"></a>Políticas do Gerenciamento de API do Azure

No Gerenciamento de API do Azure (APIM), as políticas são uma poderosa funcionalidade do sistema que permite ao editor alterar o comportamento da API por meio da configuração. As políticas são um conjunto de instruções executadas em sequência, no momento da solicitação ou da resposta de uma API. Instruções populares incluem a conversão do formato de XML para JSON e limite de taxa de chamada para restringir a quantidade de chamadas recebidas de um desenvolvedor. Muitas políticas estão disponíveis pré-configuradas.

As políticas são aplicadas dentro do gateway que fica entre o consumidor da API e a API gerenciada. O gateway recebe todas as solicitações e normalmente as encaminha inalteradas à API subjacente. No entanto, uma política também pode aplicar mudanças à solicitação de entrada e à resposta de saída.

Expressões de política podem ser usadas como valores de atributo ou texto em qualquer uma das políticas de Gerenciamento de API, a menos que a política especifique o contrário. Algumas políticas, como [Controlar fluxo][Control flow] e [Definir variável][Set variable] se baseiam em expressões de políticas. Para obter mais informações, consulte [políticas avançadas][Advanced policies] e [expressões de política][Policy expressions].

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>Compreendendo configuração de políticas

A definição da política é um documento XML simples que descreve uma sequência de instruções de entrada e de saída. O XML pode ser editado diretamente na janela de definição. Uma lista de instruções é fornecida à direita e as declarações aplicáveis ao escopo atual ficam habilitadas e destacadas.

Clicar em uma instrução habilitada adicionará o XML adequado ao local onde estiver o cursor na exibição de definição. 

> [!NOTE]
> Se a política que deseja adicionar não estiver habilitada, verifique se você está no escopo correto para essa política. Cada declaração de política é projetada para uso em determinados escopos e seções de política. Para examinar as seções da política e os escopos de uma política, verifique a seção **Uso** dessa política na [Referência à política][Policy Reference].
> 
> 

A configuração é dividida em `inbound`, `backend`, `outbound` e `on-error`. A série de instruções de política especificadas é executada para uma solicitação e uma resposta.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Se houver um erro durante o processamento de uma solicitação, quaisquer etapas restantes nas seções `inbound`, `backend` ou `outbound` serão ignoradas e a execução saltará para as instruções na seção `on-error`. Ao colocar instruções de políticas na seção `on-error`, você pode revisar o erro usando a propriedade `context.LastError`, inspecionar e personalizar a resposta de erro usando a política `set-body` e configurar o que acontece se ocorrer um erro. Há códigos de erro para obter as etapas internas e erros que podem ocorrer durante o processamento de instruções de política. Para obter mais informações, consulte [Tratamento de erros em políticas de gerenciamento de API](./api-management-error-handling-policies.md).

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Como configurar políticas

Para obter informações sobre como configurar as políticas, confira [Definir ou editar políticas](set-edit-policies.md).

## <a name="policy-reference"></a>Referência de política

Consulte a [referência de política](./api-management-policies.md) para obter uma lista completa de instruções de política e suas configurações.

## <a name="policy-samples"></a>Exemplos de política

Consulte em [Exemplos de política](./policy-reference.md) mais exemplos de código.

## <a name="examples"></a>Exemplos

### <a name="apply-policies-specified-at-different-scopes"></a>Aplicar políticas especificadas a escopos diferentes

Se você tiver uma política a nível global e uma política configurada para uma API, sempre que essa API em particular for usada ambas as políticas serão aplicadas. O Gerenciamento de API permite uma ordenação determinista de instruções de política combinadas por meio do elemento base. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

No exemplo de definição de política acima, a instrução `cross-domain` seria executada antes de quaisquer políticas maiores que, por sua vez, seriam seguidas da política `find-and-replace`. 

### <a name="restrict-incoming-requests"></a>Restringir as solicitações de entrada

Para adicionar uma nova instrução para restringir as solicitações de entrada a endereços IP especificados, posicione o cursor dentro do conteúdo do elemento XML `inbound` e clique na instrução **Restringir IPs de chamada**.

![Políticas de restrição][policies-restrict]

Isto adicionará um snippet XML ao elemento `inbound` que fornecerá diretrizes de como configurar a instrução.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Para limitar as solicitações de entrada e aceitar somente as provenientes de um endereço IP 1.2.3.4, modifique o XML da seguinte forma:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [Transformar APIs](transform-api.md)
+ [Referência de Política](./api-management-policies.md) para uma lista completa das instruções de política e suas configurações
+ [Exemplos de política](./policy-reference.md)   

[Policy Reference]: ./api-management-policies.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: ./mock-api-responses.md

[Advanced policies]: ./api-management-advanced-policies.md
[Control flow]: ./api-management-advanced-policies.md#choose
[Set variable]: ./api-management-advanced-policies.md#set-variable
[Policy expressions]: ./api-management-policy-expressions.md

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
