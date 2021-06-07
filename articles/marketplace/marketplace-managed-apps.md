---
title: Guia de publicação da oferta de aplicativo gerenciado por aplicativos do Azure-Azure Marketplace
description: Este artigo descreve os requisitos para publicar um aplicativo gerenciado no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 09/04/2020
ms.openlocfilehash: 09badebab86da8e4fd8d35307aa6648a26e91821
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232249"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Guia de publicação para aplicativos gerenciados do Azure

Uma oferta de *aplicativo gerenciado* do Azure é uma maneira de publicar um aplicativo do Azure no Azure Marketplace. Os aplicativos gerenciados são ofertas do Transact que são implantadas e cobradas por meio do Azure Marketplace. A opção de listagem que um usuário vê é *obtê-la agora*.

Este artigo explica os requisitos para o tipo de oferta de aplicativo gerenciado.

Use o tipo de oferta de aplicativo gerenciado sob as seguintes condições:

- Você está implantando uma solução baseada em assinatura para seu cliente usando uma VM (máquina virtual) ou uma solução baseada em IaaS (infraestrutura como serviço) inteira.
- Você ou seu cliente exige que a solução seja gerenciada por um parceiro.

>[!NOTE]
>Por exemplo, um parceiro pode ser um integrador de sistemas ou um provedor de serviços gerenciado (MSP).  

## <a name="managed-application-offer-requirements"></a>Requisitos da oferta de aplicativo gerenciado

|Requisitos |Detalhes  |
|---------|---------|
|Uma assinatura do Azure | Os aplicativos gerenciados devem ser implantados na assinatura de um cliente, mas podem ser gerenciados por terceiros. |
|Cobrança e medição    |  Os recursos são fornecidos na assinatura do Azure de um cliente. Os recursos do Azure que usam o modelo de pagamento pago conforme o uso são transacionados com o cliente pela Microsoft e cobrados por meio da assinatura do Azure do cliente. <br><br> Para recursos do Azure traga sua própria licença, a Microsoft cobra todos os custos de infraestrutura incorridos na assinatura do cliente, mas você tem taxas de licenciamento de software com o cliente diretamente.        |
|Um pacote de aplicativos gerenciados do Azure    |   O modelo de Azure Resource Manager configurado e criar a definição de interface do usuário que será usado para implantar seu aplicativo na assinatura do cliente.<br><br>Para obter mais informações sobre como criar um aplicativo gerenciado, consulte [visão geral do aplicativo gerenciado](../azure-resource-manager/managed-applications/publish-service-catalog-app.md).|

---

> [!NOTE]
> Os aplicativos gerenciados devem ser implantados por meio do Azure Marketplace. Se a comunicação do cliente for uma preocupação, entre em contato com os clientes interessados depois de habilitar o compartilhamento de leads.  

> [!Note]
> Uma aceitação de canal de parceiro do CSP (provedor de soluções na nuvem) já está disponível. Para obter mais informações sobre como comercializar sua oferta por meio dos canais de parceiros do Microsoft CSP, consulte [provedores de soluções de nuvem](./cloud-solution-providers.md).

## <a name="next-steps"></a>Próximas etapas

Se você ainda não fez isso, saiba como [Aumentar seus negócios na nuvem com o Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Para se registrar e começar a trabalhar no Partner Center:

- [Entre no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou concluir sua oferta.
- Consulte [criar uma oferta de aplicativo do Azure](./create-new-azure-apps-offer.md) para obter mais informações.
