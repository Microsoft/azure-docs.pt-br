---
title: Redirecionamento de URL da porta frontal do Azure | Microsoft Docs
description: Este artigo ajuda você a entender como a Azure front door dá suporte ao redirecionamento de URL para suas regras de roteamento.
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
ms.openlocfilehash: 61077c7900530fd4c5be64054bedd9c5d087fe77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91442046"
---
# <a name="url-redirect"></a>Redirecionamento de URL
A porta frontal do Azure pode redirecionar o tráfego em cada um dos seguintes níveis: protocolo, nome do host, caminho, Cadeia de caracteres de consulta. Essas funcionalidades podem ser configuradas para microserviços individuais, uma vez que o redirecionamento é baseado em caminho. Isso pode simplificar a configuração do aplicativo otimizando o uso de recursos e dá suporte a novos cenários de redirecionamento, incluindo o redirecionamento global e baseado em caminho.
</br>

:::image type="content" source="./media/front-door-url-redirect/front-door-url-redirect.png" alt-text="Redirecionamento de URL de porta frontal do Azure":::

## <a name="redirection-types"></a>Tipos de redirecionamento
Um tipo de redirecionamento define o código de status de resposta para que os clientes compreendam a finalidade do redirecionamento. Há suporte para os seguintes tipos de redirecionamento:

- **301 (movido permanentemente)**: indica que um novo URI permanente foi atribuído ao recurso de destino. Todas as referências futuras a esse recurso usarão um dos URIs incluídos. Use o código de status 301 para o redirecionamento de HTTP para HTTPS. 
- **302 (encontrado)**: indica que o recurso de destino está temporariamente sob um URI diferente. Como o redirecionamento pode mudar ocasionalmente, o cliente deve continuar a usar o URI de solicitação efetiva para solicitações futuras.
- **307 (redirecionamento temporário)**: indica que o recurso de destino está temporariamente sob um URI diferente. O agente do usuário não deve alterar o método de solicitação se ele faz um redirecionamento automático para esse URI. Como o redirecionamento pode mudar com o passar do tempo, o cliente deve continuar usando o URI de solicitação efetivo original para solicitações futuras.
- **308 (redirecionamento permanente)**: indica que um novo URI permanente foi atribuído ao recurso de destino. Todas as referências futuras a esse recurso devem usar um dos URIs incluídos.

## <a name="redirection-protocol"></a>Protocolo de redirecionamento
Você pode definir o protocolo que será usado para redirecionamento. Os casos de uso mais comuns do recurso de redirecionamento é definir HTTP para redirecionamento de HTTPS.

- **Somente HTTPS**: defina o protocolo como HTTPS somente se você pretende redirecionar o tráfego de http para https. A porta frontal do Azure recomenda que você sempre defina o redirecionamento para HTTPS apenas.
- **Somente http**: redireciona a solicitação de entrada para http. Use esse valor somente se você quiser manter o tráfego HTTP que está, não criptografado.
- **Solicitação de correspondência**: essa opção mantém o protocolo usado pela solicitação de entrada. Portanto, uma solicitação HTTP permanece HTTP e uma solicitação HTTPS permanece o redirecionamento de HTTPS post.

## <a name="destination-host"></a>Host de destino
Como parte da configuração de um roteamento de redirecionamento, você também pode alterar o nome do host ou domínio para a solicitação de redirecionamento. Você pode definir esse campo para alterar o nome do host na URL para o redirecionamento ou preservar o nome do host da solicitação de entrada. Portanto, usando esse campo, você pode redirecionar todas as solicitações enviadas `https://www.contoso.com/*` para `https://www.fabrikam.com/*` .

## <a name="destination-path"></a>Caminho de destino
Para casos em que você deseja substituir o segmento de caminho de uma URL como parte do redirecionamento, você pode definir esse campo com o novo valor de caminho. Caso contrário, você pode optar por preservar o valor do caminho como parte do redirecionamento. Portanto, usando esse campo, você pode redirecionar todas as solicitações enviadas ao `https://www.contoso.com/\*` para  `https://www.contoso.com/redirected-site` .

## <a name="query-string-parameters"></a>Parâmetros de cadeia de caracteres de consulta
Você também pode substituir os parâmetros da cadeia de caracteres de consulta na URL redirecionada. Para substituir qualquer cadeia de caracteres de consulta existente da URL de solicitação de entrada, defina esse campo como ' replace ' e defina o valor apropriado. Caso contrário, você pode manter o conjunto original de cadeias de consulta definindo o campo como ' preserve '. Como exemplo, usando esse campo, você pode redirecionar todo o tráfego enviado para o `https://www.contoso.com/foo/bar` `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

## <a name="destination-fragment"></a>Fragmento de destino
O fragmento de destino é a parte da URL após ' # ', que é usada pelo navegador para se colocar em uma seção específica de uma página da Web. Você pode definir esse campo para adicionar um fragmento à URL de redirecionamento.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
