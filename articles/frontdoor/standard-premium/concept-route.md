---
title: O que é a rota Premium/Standard da porta do Azure?
description: Este artigo ajuda você a entender como o Azure front door Standard/Premium corresponde a regra de roteamento a ser usada para uma solicitação de entrada.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: db026c4903aa30a0a4c8154af8ad6eeb4b72b706
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098511"
---
# <a name="what-is-azure-front-door-standardpremium-preview-route"></a>O que é a rota do Azure front door Standard/Premium (versão prévia)?

> [!Note]
> Esta documentação é para o Azure front door Standard/Premium (versão prévia). Procurando informações sobre a porta frontal do Azure? Veja [aqui](../front-door-overview.md).

A rota Premium/Standard da porta do Azure define como o tráfego é tratado quando a solicitação de entrada chega ao ambiente de porta frontal do Azure. Por meio das configurações de rota, uma associação é definida entre um domínio e um grupo de origem de back-end. Ao ativar os recursos avançados, como padrão para Mach, conjunto de regras, um controle mais granular sobre o tráfego é atingível.

Uma configuração de roteamento Standard/Premium da porta é composta de duas partes principais: "lado esquerdo" e "lado direito". Correspondemos à solicitação de entrada no lado esquerdo da rota e o lado direito define como processamos a solicitação.

> [!IMPORTANT]
> O Azure front door Standard/Premium (visualização) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="incoming-match-left-hand-side"></a>Correspondência de entrada (lado esquerdo)

As propriedades a seguir determinam se a solicitação de entrada corresponde à regra de roteamento (ou lado esquerdo):

* **Protocolos HTTP** (HTTP/HTTPS)
* **Hosts** (por exemplo, www \. foo.com, \* . bar.com)
* **Caminhos** (por exemplo, /\*, /users/\*, /file.gif)

Essas propriedades são expandidas internamente para que cada combinação de Protocolo/Host/Caminho seja um possível conjunto de correspondências.

### <a name="route-data-right-hand-side"></a>Rotear dados (lado direito)

A decisão de como processar a solicitação depende se o Caching está habilitado ou não para a rota. Se uma resposta armazenada em cache não estiver disponível, a solicitação será encaminhada para o back-end apropriado.

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

Depois que o Standard/Premium da porta do Azure determina o host de front-end específico e filtra possíveis regras de roteamento para apenas as rotas com esse host de front-end. A porta frontal filtra as regras de roteamento com base no caminho da solicitação. Usamos uma lógica semelhante que a de hosts com front-end:

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

Depois que o Standard/Premium da porta do Azure corresponder a uma única regra de roteamento, ele precisará escolher como processar a solicitação. Se o Standard/Premium da porta do Azure tiver uma resposta armazenada em cache disponível para a regra de roteamento correspondente, a solicitação será servida de volta para o cliente. A próxima etapa da porta do Azure Standard/Premium é avaliada se você tem ou não uma regra definida para a regra de roteamento correspondente. Se não houver um conjunto de regras definido, a solicitação será encaminhada para o pool de back-end como está. Caso contrário, o conjunto de regras é executado na ordem conforme eles são configurados.

## <a name="next-steps"></a>Próximas etapas

Saiba como [criar uma porta de front-Standard/Premium](create-front-door-portal.md).
