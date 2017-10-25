---
title: "Verificar o tráfego com a verificação de fluxo de IP do Observador de Rede do Azure - Portal do Azure | Microsoft Docs"
description: "Esse artigo descreve como verificar se o tráfego de ou para uma máquina virtual é permitido ou negado"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: e0e3e9a8-70eb-409a-a744-0ce9deb27148
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 8333ccfd1e4cc917c8af4b3006292e43b77ecc7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Verifique se o tráfego é permitido ou negado para ou de uma VM com IP fluxo verificar um componente do Observador de Rede do Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [API REST do Azure](network-watcher-check-ip-flow-verify-rest.md)


Fluxo de IP Verifique se é um recurso do Observador de Rede que permite verificar se o tráfego é permitido para ou de uma máquina virtual. A validação pode ser executada para o tráfego de entrada ou saída. Esse cenário é útil para obter o estado atual de condição de uma máquina virtual poder se comunicar com um recurso externo ou outro recurso. Fluxo IP, verifique se pode ser usado para verificar se as regras de grupo de segurança de rede (NSG) estão configuradas corretamente e solucionar problemas de fluxos que estão sendo bloqueados por regras NSG. Outro motivo para usar IP fluxo Verifique se é para garantir que deseja bloquear o tráfego está sendo bloqueado corretamente por NSG.

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que você já tenha seguido as etapas em [criar um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede ou ter uma instância existente do Gerenciador da rede. O cenário também pressupõe que exista um grupo de recursos com uma máquina virtual válida a ser usada.

## <a name="scenario"></a>Cenário

Esse cenário usa a Verificação de Fluxo de IP para verificar se uma máquina virtual pode se comunicar com outra máquina pela porta 443. Se o tráfego é negado, ele retorna a regra de segurança que está negando esse tráfego. Para saber mais sobre IP fluxo verificar, visite [fluxo verificar visão geral de IP](network-watcher-ip-flow-verify-overview.md)

### <a name="run-ip-flow-verify"></a>Executar a verificação de fluxo de IP

Navegue até o Observador de Rede e clique em **Verificação de fluxo de IP**. Selecione a máquina virtual e a interface de rede a partir da qual você deseja verificar. Insira as informações adicionais de filtragem e clique em **Verificar**.

Após clicar em **Verificar**, o fluxo com base nos critérios que você forneceu será verificado. O resultado é **Acesso permitido** ou **Acesso negado**. Se o acesso for negado, o NSG (Grupo de segurança de rede) e a regra de segurança que bloqueiam o tráfego serão fornecidos. Se a negação do tráfego for o comportamento esperado, a regra terá sido bem-sucedida.

> [!NOTE]
> A verificação de fluxo de IP exige que o recurso de máquina virtual seja alocado.

Como você pode ver na imagem a seguir, o tráfego HTTPS de saída foi permitido.

![visão geral da verificação de fluxo de IP][1]

Conforme a imagem a seguir mostra, o tráfego é alterado para entrada e a porta de entrada para 123. O tráfego agora é negado, a mensagem "Acesso negado" é fornecida junto com o grupo de segurança de rede e a regra de segurança que negam o tráfego.

![resultados do fluxo de IP][2]

## <a name="next-steps"></a>Próximas etapas

Se o tráfego está sendo bloqueado e não deve ser, consulte [gerenciar grupos de segurança de rede](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para rastrear as rede segurança e grupo de regras de segurança que são definidas.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png













