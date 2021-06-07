---
title: Guia de publicação para ofertas de modelo de solução de aplicativos do Azure – Azure Marketplace
description: Este artigo descreve os requisitos para publicar modelos de solução no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 04/22/2020
ms.openlocfilehash: c7074981c8491460d6f2a8e7d40d086f261dfeb3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879336"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Guia de publicação para aplicativos do Azure ofertas de modelo de solução

Este artigo explica os requisitos para a publicação de ofertas de modelo de solução, que é uma maneira de publicar ofertas de aplicativos do Azure no Azure Marketplace. O tipo de oferta de modelo de solução requer um [modelo de Azure Resource Manager (modelo ARM)](../azure-resource-manager/templates/overview.md) para implantar automaticamente sua infraestrutura de solução.

Use o tipo de oferta de *modelo de solução* de aplicativo do Azure sob as seguintes condições:

- Sua solução requer implantação adicional e automação de configuração além de uma única VM (máquina virtual), como uma combinação de VMs, rede e recursos de armazenamento.
- Seus clientes vão gerenciar a solução em si.

A opção de listagem que um cliente vê para esse tipo de oferta é *obtê-la agora*.

## <a name="requirements-for-solution-template-offers"></a>Requisitos para ofertas de modelo de solução

| **Requisitos** | **Detalhes**  |
| ---------------  | -----------  |
|Cobrança e medição    |  As ofertas de modelo de solução não são ofertas de transações, mas podem ser usadas para implantar ofertas de VM pagas que são cobradas pelo Microsoft Commercial Marketplace. Os recursos que o modelo ARM da solução implanta são configurados na assinatura do Azure do cliente. As máquinas virtuais pagas conforme o uso são transacionadas com o cliente pela Microsoft e cobradas por meio da assinatura do Azure do cliente.<br/> Para cobrança de BYOL (traga sua própria licença), embora a Microsoft Bille os custos de infraestrutura incorridos na assinatura do cliente, você leva suas tarifas de licenciamento de software com o cliente diretamente.   |
|VHD (disco rígido virtual) compatível com Azure  |   As VMs devem ser criadas em Windows ou Linux. Para obter mais informações, consulte: <ul> <li>[Criar uma oferta de aplicativo do Azure](./create-new-azure-apps-offer.md) (para VHDs do Windows).</li><li>[Distribuições do Linux endossadas no Azure](../virtual-machines/linux/endorsed-distros.md) (para VHDs do Linux).</li></ul> |
| Atribuição de uso do cliente | É necessário habilitar a atribuição de uso do cliente em todos os modelos de solução publicados no Azure Marketplace. Para obter mais informações sobre a atribuição de uso do cliente e como habilitá-la, consulte [atribuição de uso do cliente do parceiro do Azure](./azure-partner-customer-usage-attribution.md).  |
| Usar discos gerenciados | O [Managed disks](../virtual-machines/managed-disks-overview.md) é a opção padrão para discos persistentes de VMs de IaaS (infraestrutura como serviço) no Azure. Você deve usar discos gerenciados em modelos de solução. <ul><li>Para atualizar seus modelos de solução, siga as orientações em [usar discos gerenciados em Azure Resource Manager modelos](../virtual-machines/using-managed-disks-template-deployments.md)e use os [exemplos](https://github.com/Azure/azure-quickstart-templates)fornecidos.<br><br> </li><li>Para publicar o VHD como uma imagem no Azure Marketplace, importe o VHD subjacente dos discos gerenciados para uma conta de armazenamento usando qualquer um dos seguintes métodos:<ul><li>[PowerShell do Azure](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) </li> <li> [A CLI do Azure](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd) </li> </ul></ul> |

## <a name="next-steps"></a>Próximas etapas

Se você ainda não fez isso, saiba como [Aumentar seus negócios na nuvem com o Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Para se registrar e começar a trabalhar no Partner Center:

- [Entre no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou concluir sua oferta.
- Consulte [criar uma oferta de aplicativo do Azure](./create-new-azure-apps-offer.md) para obter mais informações.