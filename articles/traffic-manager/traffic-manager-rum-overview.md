---
title: "Medidas Reais de Usuário no Gerenciador de Tráfego do Azure | Microsoft Docs"
description: "Introdução às Medidas Reais de Usuário do Gerenciador de Tráfego"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: a7e8ae605b6234341d9ab8b790f4c54d8627f29f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Visão geral das Medidas Reais de Usuário do Gerenciador de Tráfego

>[!NOTE]
>O recurso de Medidas Reais de Usuário no Gerenciador de Tráfego está na visualização pública e pode não ter o mesmo nível de disponibilidade e confiabilidade que os recursos que já estão disponíveis. Não há suporte para o recurso, o recurso pode ter funcionalidades restritas e ele pode não estar disponível em todas as localizações do Azure. Para receber as notificações mais recentes sobre a disponibilidade e o status desse recurso, confira a página [Atualizações do Gerenciador de Tráfego do Azure](https://azure.microsoft.com/updates/?product=traffic-manager).

Quando você configurar um perfil do Gerenciador de Tráfego para usar o método de roteamento de desempenho, o serviço analisará de onde as solicitações de consulta DNS estão chegando e tomará decisões de roteamento para direcionar esses solicitantes para a região do Azure que oferece a latência mais baixa. Isso é feito utilizando a inteligência de latência de rede que o Gerenciador de Tráfego mantém para diferentes redes de usuário final.

O recurso de Medidas Reais de Usuário permite obter as medidas de latência de rede das regiões do Azure, dos aplicativos cliente que seus usuários finais usam e fazer com que o Gerenciador de Tráfego também considere essas informações ao tomar decisões de roteamento. Ao escolher usar as Medidas Reais de Usuário, você pode aumentar a precisão do roteamento para solicitações provenientes dessas redes em que residem os usuários finais. 

## <a name="how-real-user-measurements-work"></a>Como funcionam as Medidas Reais de Usuário

As Medidas Reais de Usuário funcionam fazendo com que os aplicativos clientes meçam a latência das regiões do Azure como ela é percebida nas redes de usuário final em que esses aplicativos são usados. Por exemplo, se houver uma página da Web que seja acessada por usuários em diferentes locais (por exemplo, nas regiões na América do Norte), você poderá aproveitar a capacidade das Medidas Reais de Usuário ao usar o método de roteamento de desempenho para chegar à melhor região do Azure em que o aplicativo para servidores esteja hospedado.

Ele começa pela inserção de um JavaScript fornecido pelo Azure (contendo uma chave exclusiva) nas páginas da Web. Depois disso, sempre que um usuário visitar a página da Web, o JavaScript consultará o Gerenciador de Tráfego para obter informações sobre as regiões do Azure que ele deve medir. O serviço retorna um conjunto de pontos de extremidade para o script que, em seguida, mede essas regiões consecutivamente ao baixar uma imagem de pixel único hospedada nessas regiões do Azure e observar a latência entre o momento em que a solicitação foi enviada e o momento em que o primeiro byte foi recebido. Em seguida, essas medidas são retornadas ao serviço do Gerenciador de Tráfego.

Ao longo do tempo, isso ocorre muitas vezes e em várias redes levando o Gerenciador de Tráfego a obter informações mais precisas sobre as características de latência das redes em que os usuários finais residem. Essas informações começam a ser incluídas nas decisões de roteamento feitas pelo Gerenciador de Tráfego. Como resultado, isso leva a um aumento de precisão nas decisões baseadas nas Medidas Reais de Usuário enviadas.

Ao usar as Medidas Reais de Usuário, você é cobrado com base no número de medidas enviadas para o Gerenciador de Tráfego. Para obter mais detalhes sobre os preços, visite o [página de preços do Gerenciador de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="next-steps"></a>Próximas etapas
- Saiba como usar as [Medidas Reais de Usuário com páginas da Web](traffic-manager-create-rum-web-pages.md)
- Saiba [como funciona o Gerenciador de Tráfego](traffic-manager-overview.md)
- Saiba mais sobre o [Mobile Center](https://docs.microsoft.com/mobile-center/)
- Saiba mais sobre os [métodos de roteamento do tráfego](traffic-manager-routing-methods.md) com suporte pelo Gerenciador de Tráfego
- Aprenda a [criar um perfil do Gerenciador de Tráfego](traffic-manager-create-profile.md)

