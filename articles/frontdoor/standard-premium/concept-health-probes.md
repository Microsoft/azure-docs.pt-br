---
title: Monitoramento de investigação de integridade Premium (visualização) do Azure front door padrão
description: Este artigo ajuda você a entender como a porta frontal do Azure monitora a integridade do seu back-end.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 30a8208babab2991c9d9e86cc419ac50e1530d7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101098518"
---
# <a name="azure-front-door-standardpremium-preview-health-probe-monitoring"></a>Monitoramento de investigação de integridade Premium (visualização) do Azure front door padrão

> [!Note]
> Esta documentação é específica para o Azure Front Door Standard/Premium (Versão Prévia). Está procurando informações sobre o Azure Front Door? Veja [aqui](../front-door-overview.md).

A porta frontal do Azure envia periodicamente uma solicitação HTTP ou HTTPS para cada um dos back-end. Essas solicitações permitem que a porta frontal do Azure determine a integridade de cada ponto de extremidade no pool de back-end. A porta frontal usa essas respostas da investigação para determinar os "melhores" recursos de back-end para rotear suas solicitações de cliente. 

> [!WARNING]
> Como a porta frontal tem muitos ambientes de borda globalmente, o volume de investigação de integridade para seus back-ends pode ser muito alto, variando de 25 solicitações a cada minuto até 1200 solicitações por minuto, dependendo da frequência de investigação de integridade configurada. Com a frequência de investigação padrão de 30 segundos, o volume de investigação em seu back-end deve ser de cerca de 200 solicitações por minuto.

## <a name="supported-protocols"></a>Protocolos com suporte

O Front Door dá suporte para envio de investigações via protocolos HTTP ou HTTPS. Essas investigações são enviadas pelas mesmas portas TCP configuradas para rotear solicitações de clientes e não podem ser substituídas.

## <a name="supported-http-methods-for-health-probes"></a>Métodos HTTP com suporte para investigações de integridade

A porta frontal dá suporte aos seguintes métodos HTTP para enviar as investigações de integridade:

* **Obter:** O método GET significa recuperar qualquer informação (na forma de uma entidade) é identificada pelo URI da solicitação.
* **Cabeçalho:** O método HEAD é idêntico a GET, exceto que o servidor não deve retornar um corpo de mensagem na resposta. Para novos perfis de porta frontal, por padrão, o método de investigação é definido como HEAD.

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

1. Exclua back-ends com erros de investigações de integridade:

    * Essa seleção é feita observando as últimas _n_ respostas de investigação de integridade. Se pelo menos _x_ estiver íntegro, o back-end será considerado íntegro.

    * _n_ é configurado alterando a propriedade Samples em configurações de balanceamento de carga.

    * _x_ é configurado alterando a propriedade SuccessfulSamplesRequired nas configurações de balanceamento de carga.

1. Para os conjuntos de back-ends íntegros no pool de back-end, a porta da frente também mede e mantém a latência (tempo de ida e volta) para cada back-end.


## <a name="complete-health-probe-failure"></a>Falha completa na investigação de integridade

Se as investigações de integridade falharem em todos os back-end de um pool de back-end, o Front Door considerará todos os back-ends íntegros e roteará o tráfego em uma distribuição round robin em todos eles.

Depois que qualquer back-end retornar a um estado íntegro, a porta da frente retomará o algoritmo normal de balanceamento de carga.

## <a name="disabling-health-probes"></a>Desabilitando investigações de integridade

Se você tiver um único back-end em seu pool de back-end ou apenas um back-end estiver ativo em um pool de back-end, você poderá optar por desabilitar as investigações de integridade. Ao fazer isso, você reduzirá a carga no back-end do aplicativo.

## <a name="next-steps"></a>Próximas etapas

Saiba como [criar uma porta de front-Standard/Premium](create-front-door-portal.md).
