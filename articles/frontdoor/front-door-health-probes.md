---
title: Porta frontal do Azure-monitoramento de integridade de back-end | Microsoft Docs
description: Este artigo ajuda você a entender como a porta frontal do Azure monitora a integridade dos back-ends
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4cbeea8ad20d41daff3d4ad086a36df5e988991f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449249"
---
# <a name="health-probes"></a>Investigações de integridade

Para determinar a integridade e a proximidade de cada back-end para um determinado ambiente de porta frontal, cada ambiente de porta frontal envia periodicamente uma solicitação HTTP/HTTPS sintética para cada um dos back-ends configurados. A porta frontal usa essas respostas da investigação para determinar os "melhores" recursos de back-end para rotear suas solicitações de cliente. 

> [!WARNING]
> Como a porta frontal tem muitos ambientes de borda globalmente, o volume de investigação de integridade para seus back-ends pode ser muito alto, variando de 25 solicitações a cada minuto até 1200 solicitações por minuto, dependendo da frequência de investigação de integridade configurada. Com a frequência de investigação padrão de 30 segundos, o volume de investigação em seu back-end deve ser de cerca de 200 solicitações por minuto.

## <a name="supported-protocols"></a>Protocolos com suporte

O Front Door dá suporte para envio de investigações via protocolos HTTP ou HTTPS. Essas investigações são enviadas pelas mesmas portas TCP configuradas para rotear solicitações de clientes e não podem ser substituídas.

## <a name="supported-http-methods-for-health-probes"></a>Métodos HTTP com suporte para investigações de integridade

A porta frontal dá suporte aos seguintes métodos HTTP para enviar as investigações de integridade:

1. **Obter:** O método GET significa recuperar qualquer informação (na forma de uma entidade) é identificada pelo URI da solicitação.
2. **Cabeçalho:** O método HEAD é idêntico a GET, exceto que o servidor não deve retornar um corpo de mensagem na resposta. Para novos perfis de porta frontal, por padrão, o método de investigação é definido como HEAD.

> [!NOTE]
> Para menor carga e custo em seus back-ends, a porta da frente recomenda o uso de solicitações HEAD para investigações de integridade.

## <a name="health-probe-responses"></a>Respostas de investigação de integridade

| Respostas  | Descrição | 
| ------------- | ------------- |
| Determinar integridade  |  Um código de status 200 OK indica que o back-end está íntegro. Quaisquer outros resultados são considerados uma falha. Se por qualquer motivo (incluindo falha de rede) uma resposta HTTP válida não for recebida para uma investigação, a investigação será contada como uma falha.|
| Medir latência  | A latência é o tempo total medido a partir do momento imediatamente antes de enviarmos a solicitação de investigação até o momento em que recebemos o último byte da resposta. Usamos uma nova conexão TCP para cada solicitação, portanto, essa medida não é tendenciosa em relação aos back-ends com conexões quentes existentes.  |

## <a name="how-front-door-determines-backend-health"></a>Como o Front Doo l determina a integridade do back-end

A porta frontal do Azure usa o mesmo processo de três etapas abaixo em todos os algoritmos para determinar a integridade.

1. Excluir back-ends desabilitados.

2. Exclua back-ends com erros de investigações de integridade:
    * Essa seleção é feita observando as últimas _n_ respostas de investigação de integridade. Se pelo menos _x_ estiver íntegro, o back-end será considerado íntegro.

    * _n_ é configurado alterando a propriedade Samples em configurações de balanceamento de carga.

    * _x_ é configurado alterando a propriedade SuccessfulSamplesRequired nas configurações de balanceamento de carga.

3. Para os conjuntos de back-ends íntegros no pool de back-end, a porta da frente também mede e mantém a latência (tempo de ida e volta) para cada back-end.


## <a name="complete-health-probe-failure"></a>Falha completa na investigação de integridade

Se as investigações de integridade falharem em todos os back-end de um pool de back-end, o Front Door considerará todos os back-ends íntegros e roteará o tráfego em uma distribuição round robin em todos eles.

Depois que qualquer back-end retornar a um estado íntegro, a porta da frente retomará o algoritmo normal de balanceamento de carga.

## <a name="disabling-health-probes"></a>Desabilitando investigações de integridade

Se você tiver um único back-end em seu pool de back-end, poderá optar por desabilitar as investigações de integridade reduzindo a carga no back-end do aplicativo. Mesmo se você tiver vários back-ends no pool de back-end, mas apenas um deles estiver no estado habilitado, você poderá desabilitar investigações de integridade.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
