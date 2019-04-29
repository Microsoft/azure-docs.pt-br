---
title: Azure Front Door Service – monitoramento de correspondência de regra de roteamento | Microsoft Docs
description: Este artigo o ajudará a entender como o Azure Front Door Service faz a correspondência de qual regra de roteamento a ser usada para uma solicitação de entrada
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: eec99bde0ea73a99a9dc1345f938b821a95a7c05
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736269"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Como o Front Door faz a correspondência com uma regra de roteamento

Depois de estabelecer uma conexão e fazer um handshake SSL, quando uma solicitação chega em um ambiente de Front Door, uma das primeiras ações que o Front Door realiza é determinar, entre todas as configurações, qual regra de roteamento específica corresponde à solicitação para, em seguida, executar a ação definida. O documento a seguir explica como o Front Door determina qual configuração de Rota usar ao processar uma solicitação HTTP.

## <a name="structure-of-a-front-door-route-configuration"></a>Estrutura de uma configuração de rota do Front Door
Uma configuração de regra de roteamento do Front Door é composta por duas partes principais: um "lado esquerdo" e um "lado direito". Fazemos a correspondência da solicitação de entrada om o lado esquerdo da rota enquanto o lado direito define como podemos processar a solicitação.

### <a name="incoming-match-left-hand-side"></a>Correspondência de entrada (lado esquerdo)
As propriedades a seguir determinam se a solicitação de entrada corresponde à regra de roteamento (ou lado esquerdo):

* **Protocolos HTTP** (HTTP/HTTPS)
* **Hosts** (por exemplo, www\.foo.com, \*. bar.com)
* **Caminhos** (por exemplo, /\*, /users/\*, /file.gif)

Essas propriedades são expandidas internamente para que cada combinação de Protocolo/Host/Caminho seja um possível conjunto de correspondências.

### <a name="route-data-right-hand-side"></a>Rotear dados (lado direito)
A decisão de como processar a solicitação depende de se o cache está habilitado ou não para a rota específica. Dessa forma, se não tivermos uma resposta em cache para a solicitação, encaminharemos a solicitação para o back-end apropriado no pool de back-end configurado.

## <a name="route-matching"></a>Correspondência de rotas
Esta seção se concentrará na forma como fazemos a correspondência para uma dada regra de roteamento de Front Door. O conceito básico é que sempre fazemos a correspondência com a **correspondência mais específica primeiro** olhando apenas para o "lado esquerdo".  Primeiro fazemos a correspondência com base no protocolo HTTP, em seguida, o host de Front-end e então o caminho.

### <a name="frontend-host-matching"></a>Correspondência do host de front-end
Ao fazer a correspondência de hosts de front-end, podemos usar a lógica conforme mostrado a seguir:

1. Procure por qualquer roteamento com uma correspondência exata no host.
2. Se não houver correspondência exata de hosts de front-end, rejeite a solicitação e envie um erro 400 Solicitação inválida.

Para explicar esse processo ainda mais, vamos examinar um exemplo de configuração de rotas de Front Door (somente lateral esquerda):

| Regra de roteamento | Hosts de front-end | Caminho |
|-------|--------------------|-------|
| O  | foo.contoso.com | /\* |
| b | foo.contoso.com | /users/\* |
| C | www\.fabrikam.com, foo.adventure-works.com  | /\*, /images/\* |

Se as solicitações de entrada a seguir forem enviadas para a porta da frente, elas precisarão ser combinadas com as seguintes regras de roteamentos de acima:

| Host de front-end de entrada | Regras de roteamentos com correspondência |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www\.fabrikam.com | C |
| images.fabrikam.com | Erro 400: Solicitação incorreta |
| foo.adventure-works.com | C |
| contoso.com | Erro 400: Solicitação incorreta |
| www\.adventure-works.com | Erro 400: Solicitação incorreta |
| www\.northwindtraders.com | Erro 400: Solicitação incorreta |

### <a name="path-matching"></a>Correspondência de caminho
Depois de determinar o host de front-end específico e filtrar possíveis regras de roteamentos para apenas as rotas com aquele host de front-end, o Front Door então filtrará as regras de roteamento com base no caminho solicitado. Usamos uma lógica semelhante que a de hosts com front-end:

1. Procure qualquer regra de roteamento com uma correspondência exata no Caminho
2. Se não houver nenhum Caminho com correspondência exata, procure regras de roteamento com um Caminho curinga que seja correspondente
3. Se nenhuma regra de roteamento forem encontrada com um caminho correspondente, em seguida, rejeite a solicitação e retorne um 400: Erro de solicitação inválido resposta HTTP.

>[!NOTE]
> Todos os Caminhos sem um caractere curinga são considerados Caminhos com correspondência exata. Mesmo que o caminho termine em uma barra, ele ainda será considerado uma correspondência exata.

Para explicar melhor, vejamos outro conjunto de exemplos:

| Regra de roteamento | Host de front-end    | Caminho     |
|-------|---------|----------|
| O      | www\.contoso.com | /        |
| b     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /path/   |

Dada essa configuração, a tabela de correspondência de exemplo a seguir resultaria em:

| Solicitação de entrada    | Rota correspondente |
|---------------------|---------------|
| www\.contoso.com/            | O              |
| www\.contoso.com/a           | b             |
| www\.contoso.com/ab          | C             |
| www\.contoso.com/abc         | D             |
| www\.contoso.com/abzzz       | b             |
| www\.contoso.com/abc/        | E             |
| www\.contoso.com/abc/d       | F             |
| www\.contoso.com/abc/def     | G             |
| www\.contoso.com/abc/defzzz  | F             |
| www\.contoso.com/abc/def/ghi | F             |
| www\.contoso.com/path        | b             |
| www\.contoso.com/path/       | H             |
| www\.contoso.com/path/zzz    | b             |

>[!WARNING]
> </br> Se não houver regras de roteamento para um host de front-end de correspondência exata com um Caminho de rota catch-all (`/*`), não haverá uma correspondência com nenhuma regra de roteamento.
>
> Exemplo de configuração:
>
> | Rota | Host             | Caminho    |
> |-------|------------------|---------|
> | O      | profile.contoso.com | /api/\* |
>
> Tabela de correspondência:
>
> | Solicitação de entrada       | Rota correspondente |
> |------------------------|---------------|
> | profile.domain.com/other | Nenhuma. Erro 400: Solicitação incorreta |

### <a name="routing-decision"></a>Decisão de roteamento
Depois que você obteve correspondência para uma única regra de roteamento de Front Door, precisaremos escolher como processar a solicitação. Se, para a regra de roteamento correspondente, a porta da frente tiver uma resposta em cache disponível, ela será fornecida de volta ao cliente. Caso contrário, o próximo item que é avaliada é se você configurou [Reescrita de URL (caminho personalizado de encaminhamento)](front-door-url-rewrite.md) para o roteamento de regra correspondente ou não. Se não houver um caminho de encaminhamento personalizado definido, a solicitação será encaminhada para o back-end apropriado no pool de back-end configurado como está. Caso contrário, o caminho da solicitação será atualizado de acordo o [caminho de encaminhamento personalizado](front-door-url-rewrite.md) definido e, em seguida, encaminhado para o back-end.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
