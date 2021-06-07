---
title: Latência de conexão de usuário da área de trabalho virtual do Windows – Azure
description: Latência de conexão para usuários da área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: bb0ee52d37fe901a610723d5864240b8778d8b17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574587"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Determinar a latência de conexão de usuário na área de trabalho virtual do Windows

A área de trabalho virtual do Windows está disponível globalmente. Os administradores podem criar máquinas virtuais (VMs) em qualquer região do Azure que desejarem. A latência de conexão irá variar dependendo do local dos usuários e das máquinas virtuais. Os serviços de área de trabalho virtual do Windows serão continuamente distribuídos para novas regiões para melhorar a latência.

A [ferramenta estimador de experiência de área de trabalho virtual do Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/) pode ajudá-lo a determinar o melhor local para otimizar a latência de suas VMs. Recomendamos que você use a ferramenta a cada dois ou três meses para garantir que o local ideal não seja alterado à medida que a área de trabalho virtual do Windows se acumula em novas áreas.

## <a name="interpreting-results-from-the-windows-virtual-desktop-experience-estimator-tool"></a>Interpretação de resultados da ferramenta estimador de experiência de área de trabalho virtual do Windows

Na área de trabalho virtual do Windows, a latência de até 150 ms não deve afetar a experiência do usuário que não envolve renderização ou vídeo. As latências entre 150 ms e 200 MS devem ser bem para o processamento de texto. A latência acima de 200 MS pode afetar a experiência do usuário. 

Além disso, a conexão de área de trabalho virtual do Windows depende da conexão com a Internet do computador do qual o usuário está usando o serviço. Os usuários podem perder a conexão ou a experiência de atraso de entrada em uma das seguintes situações:

 - O usuário não tem uma conexão de Internet local estável e a latência é de mais de 200 ms.
 - A rede está saturada ou com taxa limitada.

Recomendamos que você escolha locais de VMs que estejam o mais próximo possível dos seus usuários. Por exemplo, se o usuário estiver localizado na Índia, mas a VM estiver na Estados Unidos, haverá uma latência que afetará a experiência geral do usuário. 

## <a name="azure-front-door"></a>Porta da frente do Azure

A área de trabalho virtual do Windows usa o [Azure front door](https://azure.microsoft.com/services/frontdoor/) para redirecionar a conexão do usuário para o gateway de área de trabalho virtual do Windows mais próximo com base no endereço IP de origem. A área de trabalho virtual do Windows sempre usará o gateway de área de trabalho virtual do Windows que o cliente escolher.

## <a name="next-steps"></a>Próximas etapas

- Para verificar o melhor local para obter a latência ideal, consulte a [ferramenta estimador de experiência de área de trabalho virtual do Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Para os planos de preços, consulte [preços da área de trabalho virtual do Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Para começar a usar sua implantação de área de trabalho virtual do Windows, confira [nosso tutorial](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
