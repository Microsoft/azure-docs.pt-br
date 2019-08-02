---
title: Azure Front redirecionamento de URL de serviço - porta | Microsoft Docs
description: Este artigo ajuda você a entender como o serviço de porta da frente do Azure dá suporte a redirecionamento de URL para suas rotas, se configurado.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 3d77a16d24a1a843b39d97904a675518c43a525a
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332799"
---
# <a name="url-redirect"></a>Redirecionamento de URL
Você pode usar o serviço de porta da frente do Azure para redirecionar o tráfego. Você pode redirecionar o tráfego em vários níveis (protocolo, nome de host, caminho, cadeia de caracteres de consulta) e todas as funcionalidades podem ser configuradas para microsserviços individuais, como o redirecionamento é baseado no caminho. Isso simplifica a configuração do aplicativo, otimiza o uso de recursos e dá suporte a novos cenários de redirecionamento, incluindo redirecionamento global e baseado no caminho.
</br>

![Redirecionamento de URL da frente do Azure][1]

## <a name="redirection-types"></a>Tipos de redirecionamento
Um tipo de redirecionamento define o código de status de resposta para os clientes a entender a finalidade do redirecionamento. Há suporte para os seguintes tipos de redirecionamento:

- **301 (movido permanentemente)** : Indica que o recurso de destino foi atribuído a um novo URI permanente e qualquer futura referência a esse recurso deve usar um dos URIs incluído. Use o código de status 301 para HTTP para redirecionamento a HTTPS. 
- **302 (Found)** : Indica que o recurso de destino reside temporariamente em um URI diferente. Uma vez que o redirecionamento pode ser alterado na ocasião, o cliente deve continuar a usar o URI da solicitação em vigor para solicitações futuras.
- **307 (redirecionamento temporário)** : Indica que o recurso de destino reside temporariamente em um URI diferente e o agente do usuário não deve alterar o método de solicitação se ele executa um redirecionamento automático para esse URI. Como o redirecionamento pode ser alterado ao longo do tempo, o cliente deve continuar usando o URI da solicitação em vigor original para solicitações futuras.
- **308 (redirecionamento permanente)** : Indica que o recurso de destino foi atribuído a um novo URI permanente e qualquer futura referência a esse recurso deve usar um dos URIs incluído. Os clientes com link de edição de recursos, deveriam automaticamente as referências de vincular novamente para o efetivo URI de solicitação a um ou mais das novas referências enviadas pelo servidor, sempre que possível.

## <a name="redirection-protocol"></a>Protocolo de redirecionamento
Você pode definir o protocolo que será usado para o redirecionamento. Isso permite que um dos casos mais comuns de uso do recurso de redirecionamento, que é definir o HTTP para redirecionamento a HTTPS.

- **Somente HTTPS**: Defina o protocolo para HTTPS apenas, se você estiver procurando para redirecionar o tráfego de HTTP para HTTPS. Serviço de porta da frente do Azure recomenda que você sempre deve definir o redirecionamento para HTTPS apenas.
- **Somente HTTP**: Isso redireciona a solicitação de entrada para HTTP. Use esse valor somente se você deseja manter o tráfego HTTP que é, não criptografadas.
- **Corresponder solicitação**: Essa opção retém o protocolo usado pela solicitação de entrada. Portanto, uma solicitação HTTP permanece HTTP e uma solicitação HTTPS permanece redirecionamento a HTTPS post.

## <a name="destination-host"></a>Host de destino
Como parte da configuração de um redirecionamento de roteamento, você também pode alterar o nome do host ou o domínio para a solicitação de redirecionamento. Você pode definir esse campo para alterar o nome do host na URL para o redirecionamento ou preservar caso contrário, o nome do host da solicitação de entrada. Então, usar esse campo você pode redirecionar todas as solicitações enviadas https://www.contoso.com/ * para https://www.fabrikam.com/ *.

## <a name="destination-path"></a>Caminho de destino
Para casos em que você deseja substituir o segmento de caminho de URL como parte de redirecionamento, você pode definir esse campo com o novo valor de caminho. Caso contrário, você pode optar por preservar o valor do caminho como parte do redirecionamento. Então, usar esse campo, você pode redirecionar todas as solicitações enviadas para https://www.contoso.com/ * para https://www.contoso.com/redirected-site.

## <a name="query-string-parameters"></a>Parâmetros de cadeia de caracteres de consulta
Você também pode substituir os parâmetros de cadeia de caracteres de consulta na URL redirecionada. Para substituir qualquer cadeia de caracteres de consulta existente da URL de solicitação de entrada, defina este campo como 'Replace' e, em seguida, defina o valor apropriado. Caso contrário, você pode manter o conjunto original de cadeias de caracteres de consulta, definindo o campo para 'Preserve'. Por exemplo, ao usar esse campo, você pode redirecionar todo o tráfego enviado para https://www.contoso.com/foo/bar para https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F. 

## <a name="destination-fragment"></a>Fragmento de destino
O fragmento de destino é a parte da URL após '#', normalmente usado por navegadores cheguem em uma seção específica em uma página. Você pode definir esse campo para adicionar um fragmento para a URL de redirecionamento.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png