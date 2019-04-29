---
title: Guia de publicação da oferta de aplicativo gerenciado de Aplicativos do Azure
description: Este artigo descreve os requisitos para publicar um aplicativo gerenciado no Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/19/2018
ms.author: ellacroi
ms.openlocfilehash: 15c559bb8b357b7776c101f88db8316b05edb677
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764230"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Aplicativos Azure: Guia de publicação da oferta de aplicativo gerenciado

Um aplicativo gerenciado é uma das principais formas de publicar uma solução no Marketplace. Use este guia para compreender os requisitos dessa oferta. 

Essas são ofertas de transação que são implantadas e cobradas pelo Marketplace. A chamada à ação que um usuário vê é "Obtenha agora."

Use o aplicativo do Azure: tipo de oferta de aplicativo gerenciado quando as seguintes condições forem necessárias:
- Você pode implantar uma solução baseada em assinatura para o cliente usando uma VM ou uma solução inteira com base em IaaS.
- Você ou seu cliente exigem que a solução seja gerenciada por um parceiro.

>[!NOTE]
>Por exemplo, um parceiro pode ser um SI ou MSP (provedor de serviços gerenciados).  

## <a name="managed-application-offer"></a>Oferta de aplicativo gerenciado

|Requisitos |Detalhes  |
|---------|---------|
|Implantado na assinatura do Azure de um cliente | Os aplicativos gerenciados devem ser implantados na assinatura do cliente e podem ser gerenciados por terceiros | 
|Cobrança e medição    |  Os recursos serão provisionados na assinatura do Azure do cliente. Máquinas virtuais pagas conforme o uso (PAYGO) serão negociadas com o cliente via Microsoft e cobradas por meio da assinatura do Azure do cliente (PAYGO) 
No caso do modelo traga sua própria licença, enquanto a Microsoft cobrará os custos de infraestrutura incorridos na assinatura do cliente, você negociará os valores referentes ao licenciamento de software diretamente com o cliente        |
|VHD (disco rígido virtual) compatível com Azure    |   As VMs devem ser criadas em Windows ou Linux.<ul> <ul> <li>Para obter mais informações sobre como criar um VHD Linux, consulte [Distribuições do Linux endossadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Para obter mais informações sobre como criar um VHD Windows, consulte [Criar um VHD compatível com o Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Aplicativos gerenciados devem poder ser implantados por meio do Marketplace. Se a comunicação do cliente é relevante, você deve falar com clientes interessados depois de habilitar o compartilhamento de leads.  

>[!Note]
>Cloud Solution Providers (CSP) parceiro canal participar agora está disponível.  Consulte [Cloud Solution Providers](./cloud-solution-providers.md) para obter mais informações sobre sua oferta por meio do Microsoft CSP de marketing de parceiros canais.

## <a name="next-steps"></a>Próximas etapas
Caso ainda não tenha feito isso, 

- [Registre-se](https://azuremarketplace.microsoft.com/sell) no Marketplace.

Se você estiver registrado e estiver criando uma nova oferta ou trabalhando em uma existente,

- [Entre no Portal do Cloud Partner](https://cloudpartner.azure.com) para criar ou concluir a oferta.
