---
title: Introdução à exibição do grupo de segurança no Observador de Rede do Azure | Microsoft Docs
description: Esta página fornece uma visão geral da capacidade de exibição da segurança do Observador de Rede
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: ad27ab85-9d84-4759-b2b9-e861ef8ea8d8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: kumud
ms.openlocfilehash: aae57603dea9b7142956065dd65727e59014dcb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684313"
---
# <a name="introduction-to-network-security-group-view-in-azure-network-watcher"></a>Introdução à exibição do grupo de segurança da rede no Observador de Rede do Azure

Os grupos Segurança da Rede podem ser associados no nível da sub-rede ou no nível da NIC. Quando associado no um nível da sub-rede, ele se aplica a todas as instâncias de VM na sub-rede. A exibição Grupo de Segurança da Rede retorna todo NSG configurado e regras associadas no nível da NIC e da sub-rede para uma máquina virtual que fornece informações sobre a configuração. Além disso, as regras de segurança efetivas são retornadas para cada NIC em uma VM. Usando a exibição Grupo de Segurança da Rede, você pode avaliar uma VM quanto às vulnerabilidades da rede, como as portas abertas. Também é possível validar se o Grupo de Segurança de Rede está funcionando como o esperado com base em uma [comparação entre as regras de segurança configuradas e as aprovadas](network-watcher-nsg-auditing-powershell.md).

Um caso de uso mais estendido está em conformidade com a segurança e auditoria. Você pode definir um conjunto prescritivo de regras de segurança como um modelo de controle da segurança em sua organização. Uma auditoria de conformidade periódica pode ser implementada de forma programática comparando as regras prescritivas com as regras efetivas para cada uma das VMs em sua rede.

No portal, as regras são divididas em Efetiva, Sub-rede, Interface de Rede e Padrão. Isso fornece uma exibição simples das regras aplicadas em uma máquina virtual. Um botão de download é fornecido para baixar com facilidade todas as regras de segurança, independentemente da guia em um arquivo CSV.

![exibição do grupo de segurança][1]

As regras podem ser selecionadas e uma nova folha é aberta para mostrar o Grupo de Segurança da Rede e os prefixos de origem e destino. Nessa folha, você pode navegar diretamente para o recurso Grupo de Segurança da Rede.

![busca detalhada][2]

### <a name="next-steps"></a>Próximos passos

Saiba como fazer a auditoria das configurações do Grupo de Segurança da Rede visitando [Auditar as configurações do Grupo de Segurança da Rede com o PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









