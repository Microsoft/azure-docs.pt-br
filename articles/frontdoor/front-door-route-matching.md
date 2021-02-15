---
title: Regra de roteamento de porta frontal do Azure que corresponde ao monitoramento | Microsoft Docs
description: Este artigo ajuda você a entender como a porta frontal do Azure corresponde à regra de roteamento a ser usada para uma solicitação de entrada
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 67940db973f494cd4a12c2f16db528e0b113d656
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449214"
---
# <a name="how-requests-are-matched-to-a-routing-rule"></a>Como as solicitações são correspondidas a uma regra de roteamento

Depois de estabelecer uma conexão e concluir um handshake de TLS, quando uma solicitação chega em um ambiente de porta de front-end, uma das primeiras coisas que a porta da frente faz é determinar a qual regra de roteamento específica corresponder à solicitação e, em seguida, executar a ação definida na configuração. O documento a seguir explica como o Front Door determina qual configuração de Rota usar ao processar uma solicitação HTTP.

## <a name="structure-of-a-front-door-route-configuration"></a>Estrutura de uma configuração de rota do Front Door
Uma configuração de regra de roteamento do Front Door é composta por duas partes principais: um "lado esquerdo" e um "lado direito". Fazemos a correspondência da solicitação de entrada om o lado esquerdo da rota enquanto o lado direito define como podemos processar a solicitação.

### <a name="incoming-match-left-hand-side"></a>Correspondência de entrada (lado esquerdo)
As propriedades a seguir determinam se a solicitação de entrada corresponde à regra de roteamento (ou lado esquerdo):

* **Protocolos HTTP** (HTTP/HTTPS)
* **Hosts** (por exemplo, www \. foo.com, \* . bar.com)
* **Caminhos** (por exemplo, /\*, /users/\*, /file.gif)

Essas propriedades são expandidas internamente para que cada combinação de Protocolo/Host/Caminho seja um possível conjunto de correspondências.

### <a name="route-data-right-hand-side"></a>Rotear dados (lado direito)
A decisão de como processar a solicitação depende de se o cache está habilitado ou não para a rota específica. Dessa forma, se não tivermos uma resposta em cache para a solicitação, encaminharemos a solicitação para o back-end apropriado no pool de back-end configurado.

## <a name="route-matching"></a>Correspondência de rotas
Esta seção se concentrará na forma como fazemos a correspondência para uma dada regra de roteamento de Front Door. O conceito básico é que sempre fazemos a correspondência com a **correspondência mais específica primeiro** olhando apenas para o "lado esquerdo".  Primeiro fazemos a correspondência com base no protocolo HTTP, em seguida, o host de Front-end e então o caminho.

### <a name="frontend-host-matching"></a>Correspondência do host de front-end
Ao fazer correspondência de hosts de front-end, usamos a lógica definida abaixo:

1. Procure por qualquer roteamento com uma correspondência exata no host.
2. Se não houver correspondência exata de hosts de front-end, rejeite a solicitação e envie um erro 400 Solicitação inválida.

Para explicar esse processo ainda mais, vamos examinar um exemplo de configuração de rotas de Front Door (somente lateral esquerda):

| Regra de roteamento | Hosts de front-end | Caminho |
|-------|--------------------|-------|
| Um | foo.contoso.com | /\* |
| B | foo.contoso.com | /users/\* |
| C | www \. fabrikam.com, foo.Adventure-Works.com  | /\*, /images/\* |

Se as solicitações de entrada a seguir forem enviadas para a porta da frente, elas precisarão ser combinadas com as seguintes regras de roteamentos de acima:

| Host de front-end de entrada | Regras de roteamentos com correspondência |
|---------------------|---------------|
| foo.contoso.com | A, B |
| \.fabrikam.com www | C |
| images.fabrikam.com | Erro 400: solicitação inválida |
| foo.adventure-works.com | C |
| contoso.com | Erro 400: solicitação inválida |
| \.Adventure-Works.com www | Erro 400: solicitação inválida |
| \.northwindtraders.com www | Erro 400: solicitação inválida |

### <a name="path-matching"></a>Correspondência de caminho
Depois de determinar o host de front-end específico e filtrar possíveis regras de roteamentos para apenas as rotas com aquele host de front-end, o Front Door então filtrará as regras de roteamento com base no caminho solicitado. Usamos uma lógica semelhante que a de hosts com front-end:

1. Procure qualquer regra de roteamento com uma correspondência exata no Caminho
2. Se não houver nenhum Caminho com correspondência exata, procure regras de roteamento com um Caminho curinga que seja correspondente
3. Se nenhuma regra de roteamento for encontrada com um Caminho correspondente, rejeite a solicitação e retorne uma resposta HTTP 400: Erro de solicitação inválida.

>[!NOTE]
> Todos os Caminhos sem um caractere curinga são considerados Caminhos com correspondência exata. Mesmo que o caminho termine em uma barra, ele ainda será considerado uma correspondência exata.

Para explicar melhor, vejamos outro conjunto de exemplos:

| Regra de roteamento | Host de front-end    | Caminho     |
|-------|---------|----------|
| Um     | www\.contoso.com | /        |
| B     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /path/   |

Dada essa configuração, a tabela de correspondência de exemplo a seguir resultaria em:

| Solicitação de entrada    | Rota correspondente |
|---------------------|---------------|
| \.contoso.com/www            | Um             |
| \.contoso.com/a www           | B             |
| \.contoso.com/AB www          | C             |
| \.contoso.com/ABC www         | D             |
| \.contoso.com/abzzz www       | B             |
| \.contoso.com/ABC/www        | E             |
| \.contoso.com/ABC/d www       | F             |
| \.contoso.com/ABC/def www     | G             |
| \.contoso.com/ABC/defzzz www  | F             |
| \.contoso.com/ABC/def/GHI www | F             |
| \.contoso.com/Path www        | B             |
| \.contoso.com/Path/www       | H             |
| \.contoso.com/Path/zzz www    | B             |

>[!WARNING]
> </br> Se não houver regras de roteamento para um host de front-end de correspondência exata com um Caminho de rota catch-all (`/*`), não haverá uma correspondência com nenhuma regra de roteamento.
>
> Exemplo de configuração:
>
> | Rota | Host             | Caminho    |
> |-------|------------------|---------|
> | Um     | profile.contoso.com | /api/\* |
>
> Tabela de correspondência:
>
> | Solicitação de entrada       | Rota correspondente |
> |------------------------|---------------|
> | profile.domain.com/other | Nenhum. Erro 400: solicitação inválida |

### <a name="routing-decision"></a>Decisão de roteamento
Depois que você obteve correspondência para uma única regra de roteamento de Front Door, precisaremos escolher como processar a solicitação. Se, para a regra de roteamento correspondente, a porta da frente tiver uma resposta em cache disponível, ela será fornecida de volta ao cliente. Caso contrário, o próximo item que é avaliada é se você configurou [Reescrita de URL (caminho personalizado de encaminhamento)](front-door-url-rewrite.md) para o roteamento de regra correspondente ou não. Se não houver um caminho de encaminhamento personalizado definido, a solicitação será encaminhada para o back-end apropriado no pool de back-end configurado como está. Caso contrário, o caminho da solicitação será atualizado de acordo o [caminho de encaminhamento personalizado](front-door-url-rewrite.md) definido e, em seguida, encaminhado para o back-end.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
