---
title: Configurações de oferta de um Aplicativo do Power BI - Azure Marketplace | Microsoft Docs
description: Defina configurações de oferta para uma oferta de aplicativo do Power BI para o marketplace do Microsoft AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: 49bbe5dcf17a9aa20cb47f477c59e7115d29dacc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60727924"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Guia Configurações de Oferta de Aplicativos do Power BI

Quando você abre o **nova oferta** página para aplicativos de serviço, você primeiro consulte o **oferecer configurações** guia. Forneça os identificadores de primários e o nome de sua oferta nesta guia. Um asterisco (*) indica um campo obrigatório.

![Guia Configurações da Oferta](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Campos de Configurações da Oferta 

Sobre o **oferecer configurações** guia, você precisa inserir informações nos seguintes campos obrigatórios:

|  Campo        |  DESCRIÇÃO                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID da oferta**  | Um identificador exclusivo (dentro de um perfil de editor) para a oferta. Esse identificador ficará visível em URLs de produtos, modelos do Azure Resource Manager e relatórios de faturamento. O tamanho máximo é de 50 caracteres. Ele pode conter somente caracteres alfanuméricos minúsculos e traços (-). Ele não pode terminar com um traço. Esse identificador não pode ser alterado depois que uma oferta entra no ar. Se a Contoso publica uma oferta com o ID da oferta `sample-SvcApp`, a oferta é atribuída a URL do AppSource `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Publicador** | O identificador exclusivo de sua organização no [AppSource](https://appsource.microsoft.com). Todas as suas ofertas devem ser associadas à sua ID do publicador. Esse valor não pode ser alterado depois que a oferta é salva.                         |
| **Nome**      | Um nome de exibição para a sua oferta. Esse nome será exibido no AppSource e no Portal do Cloud Partner. O tamanho máximo é de 50 caracteres. Use um nome de marca reconhecível para o seu produto. Não inclua o nome da sua organização aqui, a menos que o aplicativo é comercializado com esse nome. Se você estiver fornecendo essa oferta em outros sites e publicações, use o mesmo nome em todas as publicações.    <br/>Se você liberar uma oferta durante o período de visualização para aplicativos do Power BI, adicione a cadeia de caracteres `(Preview)` no final do nome do seu aplicativo, como este: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Próximas etapas

Na próxima guia, você especificará [informações técnicas](./cpp-technical-info-tab.md) para sua oferta.
