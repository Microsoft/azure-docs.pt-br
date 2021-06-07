---
title: Serviço de emparelhamento-perguntas frequentes
titleSuffix: Azure
description: Serviço de emparelhamento-perguntas frequentes
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "75775466"
---
# <a name="peering-service---faqs"></a>Serviço de emparelhamento-perguntas frequentes

Você pode examinar as informações abaixo para perguntas gerais.

**Uma operadora pode usar o emparelhamento direto existente com a Microsoft para dar suporte ao serviço de emparelhamento?**

Sim, uma operadora pode aproveitar seu PNI existente para dar suporte ao serviço de emparelhamento. Um serviço de emparelhamento PNI requer diversidade para dar suporte à HA. Se PNI existente já tiver diversidade, nenhuma nova infraestrutura será necessária. Se o PNI existente precisar de diversidade, ele poderá ser aumentado.

**Uma operadora pode usar o novo emparelhamento direto com a Microsoft para dar suporte ao serviço de emparelhamento?**

Sim, isso também é possível. A Microsoft trabalhará com a operadora para criar um novo emparelhamento direto para dar suporte ao serviço de emparelhamento.  

**Por que o emparelhamento direto é um requisito para dar suporte ao serviço de emparelhamento?**

Um dos principais drivers por trás do serviço de emparelhamento é fornecer conectividade ao Microsoft serviços online por meio de um SP bem conectado. PNI estão sempre no intervalo de Gbps e, portanto, um bloco de construção fundamental para conectividade de alta taxa de transferência entre a transportadora e a Microsoft.

**Quais são os requisitos de diversidade em um emparelhamento direto para dar suporte ao serviço de emparelhamento?**

Um PNI deve dar suporte à redundância local e à redundância geográfica. A redundância local é definida como dois conjuntos diversificados de caminhos em um site de emparelhamento específico. A redundância geográfica exige que a portadora tenha conectividade adicional em um site diferente do Microsoft Edge, caso o site primário falhe. Para que a portadora de duração de falha curta possa rotear o tráfego pelo site de backup.

**A operadora já oferece SLA e Internet de nível empresarial, como essa oferta é diferente?**

Algumas operadoras oferecem SLA e Internet de nível empresarial em sua parte da rede. No serviço de emparelhamento, a Microsoft oferecerá o tráfego de oferta de SLA na Microsoft Part da rede. Ao selecionar o serviço de emparelhamento, o cliente obterá o SLA de ponta a ponta. O SLA de seu site para o Microsoft Edge na rede do ISP pode ser coberto pelo ISP. O SLA no aplicativo Microsoft Global Network do Microsoft Edge para usuários finais agora é coberto pela Microsoft.

**Se um provedor de serviços já estiver com a Microsoft usando PNI, que tipo de alterações será necessária para dar suporte ao serviço de emparelhamento?**

* Alterações de software para identificar um usuário de serviço de emparelhamento e seu tráfego. Pode exigir alterações de política de roteamento para trocar o tráfego de um usuário no Microsoft Edge mais próximo por meio de conexão de serviço de emparelhamento.
* Certifique-se de que a conectividade tenha redundância local e redundância geográfica.
