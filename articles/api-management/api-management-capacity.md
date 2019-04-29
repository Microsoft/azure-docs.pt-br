---
title: Capacidade de uma instância de gerenciamento de API do Azure | Microsoft Docs
description: Este artigo explica o que é a métrica de capacidade e como tomar decisões informadas sobre o dimensionamento de uma instância do Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.openlocfilehash: fe77361c4c9bed9310f8443ed4ff37faf7ea53a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60658270"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Capacidade de uma instância de gerenciamento de API do Azure

**Capacidade** é a única, mais importante [ métrica do Monitor do Azure ](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) para tomar decisões fundamentadas sobre dimensionar uma instância de Gerenciamento de API para acomodar mais carga. Sua construção é complexa e impõe certo comportamento.

Este artigo explica o que é **a capacidade** e como ela se comporta. Ele mostra como acessar métricas de **capacidade** no portal do Azure e sugere quando considerar a possibilidade de dimensionar ou atualizar sua instância de gerenciamento de API.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas deste artigo, você deve ter:

+ Uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância do APIM. Para obter mais informações, consulte [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Qual é a capacidade

![Métrica de capacidade](./media/api-management-capacity/capacity-ingredients.png)

**Capacidade** é um indicador de carga em uma instância APIM. Ele reflete o uso de recursos (CPU, memória) e comprimentos de fila de rede. O uso de CPU e memória revela o consumo de recursos por:

+ Serviços APIM, como ações de gerenciamento ou processamento de solicitações, que podem incluir solicitações de encaminhamento ou a execução de uma política
+ processos selecionados do sistema operacional, incluindo processos que envolvem custo de handshakes SSL em novas conexões.

A **capacidade** total de é uma média de seus próprios valores de cada unidade de uma instância de gerenciamento de API.

## <a name="capacity-metric-behavior"></a>Comportamento da métrica de capacidade

Por causa de sua construção, a capacidade **real** pode ser afetada por muitas variáveis, por exemplo:

+ padrões de conexão (nova conexão em uma solicitação versus reutilização da conexão existente)
+ tamanho de uma solicitação e resposta
+ políticas configuradas em cada API ou número de pedidos de envio de clientes.

Quanto mais complexas forem as operações nas solicitações, maior será o consumo de **capacidade**. Por exemplo, políticas de transformação complexas consomem muito mais CPU do que um simples encaminhamento de solicitação. Respostas de serviço de back-end lentas aumentarão também.

> [!IMPORTANT]
> **Capacidade** não é uma medida direta do número de solicitações processadas.

![Picos de métrica de capacidade](./media/api-management-capacity/capacity-spikes.png)

**Capacidade** também pode ser intermitente ou ser maior que zero, mesmo que não haja solicitações sendo processadas. Isso acontece por causa de ações específicas do sistema ou da plataforma e não deve ser levado em consideração ao decidir dimensionar uma instância.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Use o Portal do Azure para examinar a capacidade
  
![Métrica de capacidade](./media/api-management-capacity/capacity-metric.png)  

1. Navegue para sua instância do APIM no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Métricas (pré-visualização)**.
3. Na seção roxa, selecione a métrica **Capacidade** das métricas disponíveis e deixe a agregação padrão **Média**.

    > [!TIP]
    > Você deve sempre analisar uma divisão de **capacidade** por local para evitar interpretações erradas.

4. Na seção verde, selecione **Local** para dividir a métrica por dimensão.
5. Escolha um período de tempo desejado na barra superior da seção.

    Você pode definir um alerta de métrica para avisá-lo quando algo inesperado estiver acontecendo. Por exemplo, obter notificações quando sua instância do APIM tem sido que excede a capacidade máxima esperada por mais de 20 minutos.

    >[!TIP]
    > Você pode configurar alertas para informar quando o serviço está com pouca capacidade ou usar a funcionalidade de escalonamento automático do Monitor do Azure para adicionar automaticamente uma unidade de Gerenciamento de API do Azure. A operação de dimensionamento pode levar cerca de 30 minutos, portanto, você deve planejar suas regras adequadamente.  
    > Apenas o escalonamento da localização principal é permitido.

## <a name="use-capacity-for-scaling-decisions"></a>Use capacidade para escalar decisões

**Capacidade** é a métrica para tomar decisões sobre dimensionar uma instância de gerenciamento de API para acomodar mais carga. Considerar:

+ Olhando para uma tendência de longo prazo e média.
+ Ignorar picos repentinos que provavelmente não estão relacionados a nenhum aumento de carga (consulte a seção "Comportamento de métrica de capacidade" para obter explicações).
+ Atualizando ou dimensionando sua instância, quando o valor de **capacidade** exceder 60% ou 70% por um período de tempo maior (por exemplo, 30 minutos). Valores diferentes podem funcionar melhor para seu serviço ou cenário.

>[!TIP]  
> Se você puder estimar seu tráfego antecipadamente, teste sua instância APIM nas cargas de trabalho esperadas. Você pode aumentar a carga de solicitações em seu inquilino gradualmente e monitorar qual valor da métrica de capacidade corresponde ao seu pico de carga. Siga as etapas da seção anterior para usar o portal do Azure para entender quanta capacidade é usada a qualquer momento.

## <a name="next-steps"></a>Próximas etapas

[Como dimensionar ou atualizar uma instância do serviço de gerenciamento de API do Azure](upgrade-and-scale.md)