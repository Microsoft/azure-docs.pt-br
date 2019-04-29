---
title: Configurações da oferta para um módulo do Azure IoT Edge | Microsoft Docs
description: Defina as configurações de oferta para um módulo Azure IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 2a2ec41d7e2f21dc1550cd3c56009204842e1641
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60912070"
---
# <a name="iot-edge-module-offer-settings-tab"></a>Guia de Configurações da Oferta do módulo Azure IoT Edge

A página **Módulos do Azure IoT Edge > Nova oferta** é aberta com o foco na guia **Configurações de Oferta**. 

![Nova página de oferta para módulos do Azure IoT Edge](./media/iot-edge-module-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Configurações de Identidade de Oferta

Em **Oferecer Identidade**, forneça as informações para os campos descritos na tabela a seguir. Um asterisco (*) anexado ao nome do campo indica que é obrigatório. 

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **ID da oferta**       | Um identificador exclusivo (dentro de um perfil de editor) para a oferta. Este identificador estará visível em URLs de produto e relatórios de insight. Tem um comprimento máximo de 50 caracteres e pode usar caracteres alfanuméricos minúsculos e traços (-). (o identificador não pode terminar com um traço). **Observação:** Este campo não pode ser alterado depois que uma oferta entra no ar. <br> Por exemplo, se a Contoso publica uma oferta com a ID de oferta **sample-iot-edge-module**, ela é atribuída à URL do Azure Marketplace`https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`. |
| **Publicador**     | Identificador exclusivo da sua organização no Azure Marketplace. Todas as suas ofertas devem ser associadas à sua ID do publicador. Esse valor não pode ser alterado depois que a oferta é salva. |
| **Nome**          | O nome de exibição da oferta. Esse nome é exibido no Azure Marketplace e no Portal do Cloud Partner. Ele pode ter um máximo de 50 caracteres. É recomendável usar um nome de marca reconhecível para o seu produto. Não inclua o nome da sua organização, a menos que seja como o seu produto é comercializado. Se você estiver promovendo essa oferta em outros sites e publicações, verifique se o nome é exatamente o mesmo em todas as publicações. |
|  |  |

Selecione **Salvar** para salvar Configurações de Oferta.

## <a name="next-steps"></a>Próximas etapas

Use a guia [SKUs](./cpp-skus-tab.md) para configurar as SKUs para sua oferta.
