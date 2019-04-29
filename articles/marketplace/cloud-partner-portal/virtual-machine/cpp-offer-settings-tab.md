---
title: Guia Configurações de oferta de máquina virtual no Cloud Partner Portal para o Azure Marketplace | Microsoft Docs
description: Descreve a guia Configurações da oferta usada na criação de uma oferta de VM do Marketplace do Azure.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
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
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 43c830bea57a4c6f3b6c56552dbcacaccc75d54e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844299"
---
# <a name="virtual-machine-offer-settings-tab"></a>Guia de configuração da oferta de máquina virtual

A página **Nova oferta** para máquinas virtuais é aberta na primeira guia chamada **Offer Settings**.  Um asterisco anexado (*) no nome do campo indica que é necessário. 

![Nova página de oferta para máquinas virtuais](./media/publishvm_004.png)

Na guia **Configurações da oferta**, você deve fornecer os seguintes campos obrigatórios.

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **ID da oferta**       | Um identificador exclusivo (dentro de um perfil de editor) para a oferta. Esse identificador ficará visível em URLs de produtos, modelos do Azure Resource Manager e relatórios de faturamento. Ele tem um comprimento máximo de 50 caracteres, só pode ser composto de caracteres alfanuméricos minúsculos e traços (-), mas não pode terminar em um traço. Este campo não pode ser alterado depois que uma oferta entra no ar. <br> Por exemplo, se a Contoso publica uma oferta com o ID de oferta **sample-vm**, ela é atribuída ao URL do Azure Marketplace`https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview`. |
| **Publicador**     | Identificador exclusivo da sua organização no Azure Marketplace. Todas as suas ofertas devem ser associadas a sua ID do publicador. Este valor não pode ser modificado quando a oferta é salva. |
| **Nome**          | Nome de exibição da sua oferta. Este nome será exibido no Azure Marketplace e no Portal do Cloud Partner. Ele pode ter um máximo de 50 caracteres. A orientação aqui é incluir um nome de marca reconhecível para o seu produto. Não inclua o nome da sua organização aqui, a menos que seja assim que é comercializado. Se você estiver promovendo essa oferta em outros sites e publicações, verifique se o nome é exatamente o mesmo em todas as publicações. |
|  |  |
 
Clique em **Salvar** para salvar o progresso. A próxima guia, você adicionará [SKUs](./cpp-skus-tab.md) para sua oferta.
