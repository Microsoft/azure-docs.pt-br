---
title: Porta frontal do Azure-regravação de URL | Microsoft Docs
description: Este artigo ajuda você a entender como a porta frontal do Azure faz a regravação da URL para suas rotas, se configurada.
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
ms.openlocfilehash: eb5b4ab8a23a374aec54d65dd5390ab3fec3e905
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91445479"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Regeneração de URL (caminho personalizado de encaminhamento)
A Azure front door dá suporte à regravação de URL Configurando um **caminho de encaminhamento personalizado** opcional para usar ao construir a solicitação para encaminhar para o back-end. Por padrão, se um caminho de encaminhamento personalizado não for fornecido, a porta frontal copiará o caminho da URL de entrada para a URL usada na solicitação encaminhada. O cabeçalho do Host usado na solicitação encaminhada ficará conforme configurado no back-end selecionado. Leia [Cabeçalho de host de back-end](front-door-backend-pool.md#hostheader) para saber o que ele faz e como configurá-lo.

A parte avançada da reescrita de URL é que o caminho de encaminhamento personalizado copiará qualquer parte do caminho de entrada que corresponde a um caminho curinga para o caminho encaminhado (esses segmentos de caminho são os segmentos **verdes** no exemplo abaixo):
</br>

:::image type="content" source="./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg" alt-text="Regeneração de URL do Azure Front Door":::

## <a name="url-rewrite-example"></a>Exemplo de regeneração de URL
Considere uma regra de roteamento com a seguinte combinação de hosts front-end e caminhos configurados:

| Hosts      | Caminhos       |
|------------|-------------|
| www\.contoso.com | /\*   |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

A primeira coluna da tabela abaixo mostra exemplos de solicitações de entrada, e a segunda coluna mostra qual seria a rota "mais específica" correspondente com 'Path'.  As colunas terceira e incorreta da tabela são exemplos de caminhos de **encaminhamento personalizados**configurados.

Por exemplo, se lermos na segunda linha, veremos que para a solicitação de entrada `www.contoso.com/sub`, se o caminho de encaminhamento personalizado fosse `/`, o caminho encaminhado seria `/sub`. Se o caminho de encaminhamento personalizado fosse `/fwd/`, o caminho encaminhado seria `/fwd/sub`. E assim por diante nas colunas restantes. As partes **enfatizadas** dos caminhos abaixo representam as que fazem parte da correspondência curinga.

| Solicitação de entrada       | Caminho de correspondência mais específica | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| \.contoso.com/www            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| \.**sub** contoso.com/da Web     | /\*                      | /**projeto**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www \. contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| \.contoso.com/foo www         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| \.contoso.com/foo/www        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| \.**barra** de contoso.com/foo/da Web | /foo/\*                  | /**ênfase**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |

## <a name="optional-settings"></a>Configurações opcionais
Há outras configurações opcionais, que você também pode especificar para qualquer configuração de regra de roteamento:

* **Configuração de cache** -se desabilitada ou não especificada, as solicitações que correspondem a essa regra de roteamento não tentarão usar o conteúdo em cache e, em vez disso, sempre serão buscadas do back-end. Leia mais sobre [Cache com o Front Door](front-door-caching.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
