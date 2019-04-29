---
title: Pré-requisitos de máquina virtual do Microsoft Azure | Microsoft Docs
description: Lista de pré-requisitos necessários para publicar uma oferta VM no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: 5c26fd3d6c2593217aef8588e2a58377a86412ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844033"
---
# <a name="virtual-machine-prerequisites"></a>Pré-requisitos de máquina virtual

Este artigo lista as duas técnicas e requisitos de negócios que você precisa cumprir antes de você podem publicar uma oferta VM para o [do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Se você ainda não fez isso, examine os [guia de publicação da oferta de máquina Virtual](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Requisitos técnicos

Pré-requisitos técnicos para a publicação de uma solução de máquina virtual (VM) são simples:

- Você deve ter uma conta ativa do Azure. Se você não tiver uma, inscreva-se no [site do Microsoft Azure](https://azure.microsoft.com).  
- Você deve ter um ambiente configurado para dar suporte ao desenvolvimento da Vm do Windows ou Linux.  Para obter mais informações, consulte o site de documentação de VM associado:
    - [Documentação de VMs do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Documentação de VMs do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Requisitos de negócios

Os requisitos de negócios incluem obrigações contratuais, legais e de procedimentos: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Você deve ser um Editor de Marketplace de Nuvem registrado.  Se você não estiver registrado ainda, siga as etapas no artigo [Tornar-se um Editor do Marketplace de Nuvem](../../become-publisher.md).

    > [!NOTE]
    > Você deve usar a mesma conta de registro do Microsoft Developer Center para entrar no [Portal do Cloud Partner](https://cloudpartner.azure.com).
    > Você deve ter apenas uma conta da Microsoft para suas ofertas do Azure Marketplace. Ela não deve ser específica a serviços ou ofertas.
    
- Sua empresa (ou sua subsidiária) deve estar localizada em um país de origem de venda ao qual o Azure Marketplace dá suporte.  Para obter uma lista atual desses países, consulte [Políticas de Participação do Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- O produto deve ser licenciado de forma que seja compatível com modelos de cobrança com suporte no Azure Marketplace.  Para obter mais informações, consulte [Opções de cobrança no Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Você é responsável por disponibilizar o suporte técnico a clientes de modo comercialmente razoável. Esse suporte pode ser gratuito, pago ou por meio de abordagens de comunidade.
- Você é responsável pelo licenciamento do software e quaisquer dependências de software de terceiros.
- Você deve fornecer conteúdo que atenda aos critérios da oferta a serem listados no Azure Marketplace e no portal do Azure. <!-- TD: Meaning/links? -->
- Você deve concordar com os termos do [Políticas de Participação do Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) e Contrato de Editor.
- Você deve estar em conformidade com o [termos de uso do Microsoft Azure site](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Microsoft Privacy Statement](https://privacy.microsoft.com/privacystatement), e [contrato de programa de certificação do Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Próximas etapas

Depois que esses pré-requisitos foram atendidos, você pode [criar sua oferta de VM](./cpp-create-offer.md).
