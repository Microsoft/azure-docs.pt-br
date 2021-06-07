---
title: Redirecionamento de URL e regravação de URL com a porta frontal do Azure Standard/Premium (visualização)
description: Este artigo ajuda você a entender como a Azure front door dá suporte ao redirecionamento de URL e à reescrita de URL usando o conjunto de regras de porta frontal do Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 382a4c040c7a519462ee3e35119b9471031e0724
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101098426"
---
# <a name="url-redirect-and-url-rewrite-with-azure-front-door-standardpremium-preview"></a>Redirecionamento de URL e regravação de URL com a porta frontal do Azure Standard/Premium (visualização)

> [!Note]
> Esta documentação é específica para o Azure Front Door Standard/Premium (Versão Prévia). Está procurando informações sobre o Azure Front Door? Veja [aqui](../front-door-overview.md).

Este artigo ajuda você a entender como o Azure front door Standard/Premium dá suporte ao redirecionamento de URL e à reescrita de URL usada em um conjunto de regras.

> [!IMPORTANT]
> O Azure Front Door Standard/Premium (versão prévia) está na fase de versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="url-redirect"></a>Redirecionamento de URL

A porta frontal do Azure pode redirecionar o tráfego em cada um dos seguintes níveis: protocolo, nome do host, caminho, Cadeia de caracteres de consulta e fragmento. Essas funcionalidades podem ser configuradas para o micro-Service individual, uma vez que o redirecionamento é baseado em caminho. Com o redirecionamento de URL, você pode simplificar a configuração do aplicativo otimizando o uso de recursos e dá suporte a novos cenários de redirecionamento, incluindo o redirecionamento global e baseado em caminho.

Você pode configurar o redirecionamento de URL por meio do conjunto de regras.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-redirect.png" alt-text="Captura de tela da criação de redirecionamento de URL com conjunto de regras." lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-redirect-expanded.png":::

### <a name="redirection-types"></a>Tipos de redirecionamento
Um tipo de redirecionamento define o código de status de resposta para que os clientes compreendam a finalidade do redirecionamento. Há suporte para os seguintes tipos de redirecionamento:

* **301 (movido permanentemente)**: indica que um novo URI permanente foi atribuído ao recurso de destino. Todas as referências futuras a esse recurso usarão um dos URIs incluídos. Use o código de status 301 para o redirecionamento de HTTP para HTTPS.
* **302 (encontrado)**: indica que o recurso de destino está temporariamente sob um URI diferente. Como o redirecionamento pode mudar ocasionalmente, o cliente deve continuar a usar o URI de solicitação efetiva para solicitações futuras.
* **307 (redirecionamento temporário)**: indica que o recurso de destino está temporariamente sob um URI diferente. O agente do usuário não deve alterar o método de solicitação se ele faz um redirecionamento automático para esse URI. Como o redirecionamento pode mudar com o passar do tempo, o cliente deve continuar usando o URI de solicitação efetivo original para solicitações futuras.
* **308 (redirecionamento permanente)**: indica que um novo URI permanente foi atribuído ao recurso de destino. Todas as referências futuras a esse recurso devem usar um dos URIs incluídos.

### <a name="redirection-protocol"></a>Protocolo de redirecionamento
Você pode definir o protocolo que será usado para redirecionamento. Os casos de uso mais comuns do recurso de redirecionamento é definir HTTP para redirecionamento de HTTPS.

* **Somente HTTPS**: defina o protocolo como HTTPS somente se você pretende redirecionar o tráfego de http para https. A porta frontal do Azure recomenda que você sempre defina o redirecionamento para HTTPS apenas.
* **Somente http**: redireciona a solicitação de entrada para http. Use esse valor somente se você quiser manter o tráfego HTTP que está, não criptografado.
* **Solicitação de correspondência**: essa opção mantém o protocolo usado pela solicitação de entrada. Portanto, uma solicitação HTTP permanece HTTP e uma solicitação HTTPS permanece o redirecionamento de HTTPS post.

### <a name="destination-host"></a>Host de destino
Como parte da configuração de um roteamento de redirecionamento, você também pode alterar o nome do host ou domínio para a solicitação de redirecionamento. Você pode definir esse campo para alterar o nome do host na URL para o redirecionamento ou preservar o nome do host da solicitação de entrada. Portanto, usando esse campo, você pode redirecionar todas as solicitações enviadas `https://www.contoso.com/*` para `https://www.fabrikam.com/*` .

### <a name="destination-path"></a>Caminho de destino
Para casos em que você deseja substituir o segmento de caminho de uma URL como parte do redirecionamento, você pode definir esse campo com o novo valor de caminho. Caso contrário, você pode optar por preservar o valor do caminho como parte do redirecionamento. Portanto, usando esse campo, você pode redirecionar todas as solicitações enviadas ao `https://www.contoso.com/\*` para  `https://www.contoso.com/redirected-site` .

### <a name="query-string-parameters"></a>Parâmetros de cadeia de caracteres de consulta
Você também pode substituir os parâmetros da cadeia de caracteres de consulta na URL redirecionada. Para substituir qualquer cadeia de caracteres de consulta existente da URL de solicitação de entrada, defina esse campo como ' replace ' e defina o valor apropriado. Caso contrário, você pode manter o conjunto original de cadeias de consulta definindo o campo como ' preserve '. Como exemplo, usando esse campo, você pode redirecionar todo o tráfego enviado para o `https://www.contoso.com/foo/bar` `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

### <a name="destination-fragment"></a>Fragmento de destino
O fragmento de destino é a parte da URL após ' # ', que é usada pelo navegador para se colocar em uma seção específica de uma página da Web. Você pode definir esse campo para adicionar um fragmento à URL de redirecionamento.

## <a name="url-rewrite"></a>Reconfiguração de URL

A porta frontal do Azure dá suporte à regravação de URL para regravar o caminho de uma solicitação que é roteada para sua origem. A reescrita de URL permite que você adicione condições para garantir que a URL ou os cabeçalhos especificados sejam regravados somente quando determinadas condições forem atendidas. Essas condições se baseiam nas informações de solicitação e resposta.

Com esse recurso, você pode redirecionar os usuários para origens diferentes com base no cenário, tipo de dispositivo e tipo de arquivo solicitado.

Você pode configurar o redirecionamento de URL por meio do conjunto de regras.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite.png" alt-text="Captura de tela da criação de regravação de URL com o conjunto de regras." lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite-expanded.png":::

### <a name="source-pattern"></a>Padrão de origem

O padrão de origem é o caminho da URL na solicitação de origem a ser substituída. Atualmente, o padrão de origem usa uma correspondência baseada em prefixo. Para corresponder a todos os caminhos de URL, use uma barra (/) como o valor de padrão de origem.

### <a name="destination"></a>Destino

Você pode definir o caminho de destino a ser usado na regravação. O caminho de destino substitui o padrão de origem.

### <a name="preserve-unmatched-path"></a>Preservar caminho sem correspondência

Preservar caminho sem correspondência permite acrescentar o caminho restante após o padrão de origem ao novo caminho.

Por exemplo, se eu definir **preservar caminho sem correspondência como Sim**.
* Se a solicitação de entrada for `www.contoso.com/sub/1.jpg` , o padrão de origem será definido como `/` , o destino será definido como `/foo/` e o conteúdo será servido do `/foo/sub/1` . jpg a partir da origem.

* Se a solicitação de entrada for `www.contoso.com/sub/image/1.jpg` , o padrão de origem será definido como `/sub/` , o destino definido como `/foo/` , o conteúdo será servido da `/foo/image/1.jpg` origem.

Por exemplo, se eu definir **preservar caminho sem correspondência como não**.
* Se a solicitação de entrada for `www.contoso.com/sub/image/1.jpg` , o padrão de origem é definido como `/sub/` , o destino é definido como `/foo/2.jpg` , o conteúdo sempre será servido a `/foo/2.jpg` partir da origem, independentemente de quais caminhos foram seguidos `wwww.contoso.com/sub/` .

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [conjunto de regras Standard/Premium da porta de front-end do Azure](concept-rule-set.md).
