---
title: "Introdução à verificação de fluxo de IP no Observador de Rede do Azure | Microsoft Docs"
description: "Esta página fornece uma visão geral da capacidade de verificação de fluxo de IP do Observador de Rede"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b2ad45e76320c59d18dce7b39166679801b4170a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Introdução à verificação de fluxo de IP no Observador de Rede do Azure

A verificação de fluxo de IP confere se um pacote é permitido ou negado para ou a partir de uma máquina virtual com base nas informações de cinco tuplas. Essas informações consistem em direção, protocolo, IP local, IP remoto, porta local e porta remota. Se o pacote for negado por um grupo de segurança, o nome da regra que negou o pacote será retornado. Embora qualquer IP de origem ou destino possa ser escolhido, esse recurso ajuda os administradores a diagnosticarem rapidamente os problemas de conectividade a partir de ou com a Internet, e a partir de ou com o ambiente local.

A verificação de fluxo de IP tem como alvo uma interface de rede de uma máquina virtual. O fluxo do tráfego é verificado com base nas configurações feitas para ou a partir dessa interface de rede. Esse recurso é útil ao confirmar se uma regra em um Grupo de Segurança da Rede está bloqueando o tráfego de entrada ou saída para ou a partir de uma máquina virtual.

Uma instância do Observador de Rede precisa ser criada em todas as regiões nas quais você pretende executar a verificação de fluxo de IP. O Observador de Rede é um serviço regional e só pode ser executado em recursos na mesma região. Isso não afeta os resultados da verificação de fluxo de IP, pois a rota associada à NIC ainda será retornada.

![1][1]

## <a name="next-steps"></a>Próximas etapas

Visite o seguinte artigo para saber se um pacote é permitido ou negado para uma máquina virtual por meio do portal. [Verifique se o tráfego é permitido em uma VM com uma Verificação de Fluxo de IP usando o portal](network-watcher-check-ip-flow-verify-portal.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png












