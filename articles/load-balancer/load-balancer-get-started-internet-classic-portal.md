---
title: "Criar um balanceador de carga voltado para a Internet - Portal clássico do Azure | Microsoft Docs"
description: "Saiba como criar um balanceador de carga para a Internet no modelo de implantação clássico usando o portal clássico do Azure"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: fa3e93c0-968a-472d-a17c-65665c050db2
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.openlocfilehash: e07b6808f2401ac7b2b21e5f8816bac5a15b50b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>Introdução à criação de um balanceador de carga para a Internet (clássico) no portal clássico do Azure

> [!div class="op_single_selector"]
> * [Portal clássico do Azure](../load-balancer/load-balancer-get-started-internet-classic-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Serviços de Nuvem do Azure](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Antes de trabalhar com os recursos do Azure, é importante entender que, no momento, o Azure apresenta dois modelos de implantação: Azure Resource Manager e clássico. Verifique se você entendeu [os modelos e as ferramentas de implantação](../azure-classic-rm.md) antes de trabalhar com qualquer recurso do Azure. Você pode exibir a documentação para ferramentas diferentes clicando nas guias na parte superior deste artigo. Este artigo aborda o modelo de implantação clássico. Também é possível [Saber como criar um balanceador de carga para a Internet usando o Gerenciador de Recursos do Azure](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>Configurar um balanceador de carga da Internet para máquinas virtuais

Para balancear carga de tráfego de rede da Internet entre máquinas virtuais de um serviço de nuvem, você deve criar um conjunto de balanceamento de carga. Esse procedimento pressupõe que você já tenha criado as máquinas virtuais e que elas estejam todas no mesmo serviço de nuvem.

**Para configurar um conjunto com balanceamento de carga de máquinas virtuais**

1. No portal clássico do Azure, clique em **Máquinas Virtuais**e, em seguida, clique no nome de uma máquina virtual no conjunto com balanceamento de carga.
2. Clique em **Pontos de Extremidade** e depois em **Adicionar**.
3. Na página **Adicionar um ponto de extremidade a uma máquina virtual** , clique na seta para a direita.
4. Na página **Especificar os detalhes do ponto de extremidade** :

   * Em **Nome**, digite um nome para o ponto de extremidade ou selecione o nome da lista de pontos de extremidade predefinidos para protocolos comuns.
   * Em **Protocolo**, selecione o protocolo necessário para o tipo de ponto de extremidade, TCP ou UDP, conforme necessário.
   * Em **Porta pública e Porta privada**, digite os números de porta que você deseja que a máquina virtual use, conforme necessário. Você pode usar as regras de firewall e porta privada na máquina virtual para redirecionar o tráfego de uma maneira que seja apropriado ao seu aplicativo. A porta privada pode ser a mesma que a porta pública. Por exemplo, para um ponto de extremidade para o tráfego da web (HTTP), você pode atribuir a porta 80 à porta pública e privada.

5. Selecione **Criar um conjunto de balanceamento de carga**e depois clique na seta para a direita.
6. Na página **Configurar o conjunto de balanceamento de carga** , digite um nome para o conjunto e atribua os valores do comportamento de investigação do Balanceador de Carga do Azure. O Balanceador de Carga usa testes para determinar se as máquinas virtuais do conjunto de balanceamento de carga estão disponíveis para receber o tráfego de entrada.
7. Clique na marca de seleção para criar o ponto de extremidade de balanceamento de carga. Você verá **Sim** na coluna **Nome do conjunto de balanceamento de carga** da página **Pontos de extremidade** da máquina virtual.
8. No portal, clique em **Máquinas Virtuais**, clique no nome de uma máquina virtual adicional no conjunto de balanceamento de carga, clique em **Pontos de Extremidade** e em **Adicionar**.
9. Na página **Adicionar ponto de extremidade a uma máquina virtual**, clique em **Adicionar ponto de extremidade a um conjunto existente de balanceamento de carga**, selecione o nome do conjunto de balanceamento de carga e, em seguida, clique na seta para a direita.
10. Na página **Especificar os detalhes do ponto de extremidade** , digite um nome para o ponto de extremidade e, em seguida, clique na marca de seleção.

Para as outras máquinas virtuais no conjunto de balanceamento de carga, repita as etapas de 8 a 10.

## <a name="next-steps"></a>Próximas etapas

[Introdução à configuração de um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
