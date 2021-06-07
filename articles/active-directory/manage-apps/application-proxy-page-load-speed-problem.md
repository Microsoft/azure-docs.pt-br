---
title: Um aplicativo de Proxy de Aplicativo demora muito para carregar | Microsoft Docs
description: Solucionar problemas de desempenho de carregamento de página com o Proxy de Aplicativo do Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17cb11cc82ede3b51b5502730079618e136d3ca5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254467"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Um aplicativo de Proxy de Aplicativo demora muito para carregar

Este artigo ajuda-o a entender porque um aplicativo de Proxy de Aplicativo do Azure AD pode demorar muito tempo para carregar. Ele também explica o que você pode fazer para resolver esse problema.

## <a name="overview"></a>Visão geral
Embora os aplicativos estejam funcionando, eles podem experimentar uma longa latência. Pode haver alterações de topologia de rede que você pode fazer para melhorar a velocidade. Para uma avaliação de diferentes topologias, consulte o [documento de considerações de rede](application-proxy-network-topology.md).

Além da topologia de rede, não há atualmente nenhuma recomendação adicional para ajuste de desempenho. Conforme o serviço Proxy de Aplicativo é expandido, ela pode ir para um data center que esteja fisicamente mais próximo. A proximidade maior pode ajudar na latência. Para uma lista de data centers do Azure, consulte a [página de teste de latência](http://www.azurespeed.com/Azure/Latency). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Comentários sobre locais de data centers do Proxy de Aplicativo 
Podem existir data centers do Azure que ainda não incluem Proxy de Aplicativo, mas poderiam conduzi-lo a uma grande melhoria de latência. Envie o local do data center para aadapfeedback@microsoft.com. A Microsoft usa seus comentários para planos de expansão.

A Microsoft está trabalhando em recursos adicionais para melhorar a latência. Assim que essas melhorias estiverem disponíveis, a documentação será atualizada.

## <a name="next-steps"></a>Próximas etapas
[Trabalhar com servidores proxy locais existentes](application-proxy-configure-connectors-with-proxy-servers.md)
