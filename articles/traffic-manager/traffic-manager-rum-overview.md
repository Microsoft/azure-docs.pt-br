---
title: Medidas de Usuário Reais do Gerenciador de Tráfego do Azure
description: Nesta introdução, saiba como o Gerenciador de tráfego do Azure Medidas de Usuário Reais funcionar.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 618f8fff532da0f6ae315ad9e4cda35a289949d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98183703"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Visão geral das Medidas Reais de Usuário do Gerenciador de Tráfego

Quando você configurar um perfil do Gerenciador de Tráfego para usar o método de roteamento de desempenho, o serviço analisará de onde as solicitações de consulta DNS estão chegando e tomará decisões de roteamento para direcionar esses solicitantes para a região do Azure que oferece a latência mais baixa. Isso é feito utilizando a inteligência de latência de rede que o Gerenciador de Tráfego mantém para diferentes redes de usuário final.

O recurso de Medidas Reais de Usuário permite obter as medidas de latência de rede das regiões do Azure, dos aplicativos cliente que seus usuários finais usam e fazer com que o Gerenciador de Tráfego também considere essas informações ao tomar decisões de roteamento. Ao escolher usar as Medidas Reais de Usuário, você pode aumentar a precisão do roteamento para solicitações provenientes dessas redes em que residem os usuários finais. 

## <a name="how-real-user-measurements-work"></a>Como funcionam as Medidas Reais de Usuário

As Medidas de Usuário Reais funcionam fazendo com que os aplicativos clientes meçam a latência das regiões do Azure como ela é percebida nas redes de usuário final em que esses aplicativos são usados. Por exemplo, se houver uma página da Web que seja acessada por usuários em diferentes locais (por exemplo, nas regiões na América do Norte), você poderá usar as Medidas de Usuário Reais com o método de roteamento de desempenho para chegar à melhor região do Azure em que o aplicativo para servidores esteja hospedado.

Ele começa pela inserção de um JavaScript fornecido pelo Azure (contendo uma chave exclusiva) nas páginas da Web. Depois disso, sempre que um usuário visitar a página da Web, o JavaScript consultará o Gerenciador de Tráfego para obter informações sobre as regiões do Azure que ele deve medir. O serviço retorna um conjunto de pontos de extremidade para o script que, em seguida, mede essas regiões consecutivamente ao baixar uma imagem de pixel único hospedada nessas regiões do Azure e observar a latência entre o momento em que a solicitação foi enviada e o momento em que o primeiro byte foi recebido. Em seguida, essas medidas são retornadas ao serviço do Gerenciador de Tráfego.

Ao longo do tempo, isso ocorre muitas vezes e em várias redes levando o Gerenciador de Tráfego a obter informações mais precisas sobre as características de latência das redes em que os usuários finais residem. Essas informações começam a ser incluídas nas decisões de roteamento feitas pelo Gerenciador de Tráfego. Como resultado, isso leva a um aumento de precisão nas decisões baseadas nas Medidas Reais de Usuário enviadas.

Ao usar as Medidas Reais de Usuário, você é cobrado com base no número de medidas enviadas para o Gerenciador de Tráfego. Para obter mais detalhes sobre os preços, visite o [página de preços do Gerenciador de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Perguntas frequentes

* [Quais são os benefícios do uso de Medidas Reais de Usuário?](./traffic-manager-faqs.md#what-are-the-benefits-of-using-real-user-measurements)

* [É possível usar as Medidas Reais de Usuário com regiões que não são do Azure?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-non-azure-regions)

* [Qual método de roteamento se beneficia das Medidas Reais de Usuário?](./traffic-manager-faqs.md#which-routing-method-benefits-from-real-user-measurements)

* [É necessário habilitar as Medidas Reais de Usuário para cada perfil separadamente?](./traffic-manager-faqs.md#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [Como desligar as Medidas Reais de Usuário da minha assinatura?](./traffic-manager-faqs.md#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [Posso usar as Medidas Reais de Usuário com aplicativos clientes que não sejam páginas da Web?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Quantas medidas são feitas quando uma página da Web habilitada para Medidas Reais de Usuário é renderizada?](./traffic-manager-faqs.md#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [Existe algum atraso antes que o script das Medidas Reais de Usuário seja executado na minha página da Web?](./traffic-manager-faqs.md#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [Posso usar as Medidas de Usuário Reais apenas com as regiões do Azure que desejo medir?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [Posso limitar o número de medidas feitas para um número específico?](./traffic-manager-faqs.md#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [Posso ver as medidas obtidas pelo meu aplicativo cliente como parte das Medidas Reais de Usuário?](./traffic-manager-faqs.md#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Posso modificar o script de medida fornecido pelo Gerenciador de Tráfego?](./traffic-manager-faqs.md#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Será possível que outras pessoas vejam a chave que eu uso com as Medidas Reais de Usuário?](./traffic-manager-faqs.md#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [Outras pessoas podem usar minha chave de RUM?](./traffic-manager-faqs.md#can-others-abuse-my-rum-key)

* [É necessário colocar o JavaScript de medida em todas as páginas da Web?](./traffic-manager-faqs.md#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [As informações sobre meus usuários finais poderão ser identificadas pelo Gerenciador de Tráfego se eu usar as Medidas Reais de Usuário?](./traffic-manager-faqs.md#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [A página da Web que está medindo as Medidas Reais de Usuário precisa estar usando o Gerenciador de Tráfego para o roteamento?](./traffic-manager-faqs.md#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [É necessário hospedar algum serviço nas regiões do Azure a serem usadas com as Medidas Reais de Usuário?](./traffic-manager-faqs.md#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [Meu uso de largura de banda do Azure aumentará com o uso das Medidas Reais de Usuário?](./traffic-manager-faqs.md#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>Próximas etapas
- Saiba como usar as [Medidas Reais de Usuário com páginas da Web](traffic-manager-create-rum-web-pages.md)
- Saiba [como funciona o Gerenciador de tráfego](traffic-manager-overview.md)
- Saiba mais sobre o [Mobile Center](/mobile-center/)
- Saiba mais sobre os [métodos de roteamento do tráfego](traffic-manager-routing-methods.md) com suporte pelo Gerenciador de Tráfego
- Saiba como [criar um perfil do Gerenciador de tráfego](./quickstart-create-traffic-manager-profile.md)