---
title: Azure Front Door Service - monitoramento de integridade de back-end | Microsoft Docs
description: Este artigo ajuda você a entender como o Azure Front Door Service monitora a integridade dos back-ends
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 59a3bac39437b91eeee3b005bd23476a34a308b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736574"
---
# <a name="health-probes"></a>Investigações de integridade

Para determinar a integridade de cada back-end, cada ambiente Front Door envia periodicamente uma solicitação HTTP/HTTPS sintética para cada um dos back-ends configurados. O Front Door, em seguida, usa as respostas desses testes para determinar os "melhores" back-ends aos quais ele deve rotear as solicitações reais do cliente.


## <a name="supported-protocols"></a>Protocolos com suporte

O Front Door dá suporte para envio de investigações via protocolos HTTP ou HTTPS. Essas investigações são enviadas pelas mesmas portas TCP configuradas para rotear solicitações de clientes e não podem ser substituídas.

## <a name="health-probe-responses"></a>Respostas de investigação de integridade

| Respostas  | DESCRIÇÃO | 
| ------------- | ------------- |
| Determinar integridade  |  Um código de status 200 OK indica que o back-end está íntegro. Quaisquer outros resultados são considerados uma falha. Se por algum motivo (incluindo falha de rede) uma resposta HTTP válida não for recebida para uma investigação, a investigação será contada como uma falha.|
| Medir latência  | A latência é o tempo total medido a partir do momento imediatamente antes de enviarmos a solicitação de investigação até o momento em que recebemos o último byte da resposta. Usamos uma nova conexão TCP para cada solicitação, portanto, essa medida não é tendenciosa em relação aos back-ends com conexões passivas existentes.  |

## <a name="how-front-door-determines-backend-health"></a>Como o Front Doo l determina a integridade do back-end

O Azure Front Door Service usa o mesmo processo de três etapas abaixo em todos os algoritmos para determinar a integridade.

1. Excluir back-ends desabilitados.

2. Exclua back-ends com erros de investigações de integridade:
    * Essa seleção é feita observando as últimas _n_ respostas de investigação de integridade. Se pelo menos _x_ estiver íntegro, o back-end será considerado íntegro.

    * _n_ é configurado, alterando a propriedade SampleSize nas configurações de balanceamento de carga.

    * _x_ é configurado, alterando a propriedade SuccessfulSamplesRequired nas configurações de balanceamento de carga.

3. Fora do conjunto de back-ends íntegros no pool de back-end, o Front Door também mede e mantém a latência (tempo de resposta) para cada back-end.


## <a name="complete-health-probe-failure"></a>Falha completa na investigação de integridade

Se as investigações de integridade falharem em todos os back-end de um pool de back-end, o Front Door considerará todos os back-ends íntegros e roteará o tráfego em uma distribuição round robin em todos eles.

Quando qualquer back-end retornar a um estado íntegro, o Front Door retomará o algoritmo normal de balanceamento de carga.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
