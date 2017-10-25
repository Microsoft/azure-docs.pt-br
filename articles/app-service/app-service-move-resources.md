---
title: Mover os recursos do aplicativo Web para outro grupo de recursos
description: "Descreve os cenários em que você pode mover aplicativos Web e Serviços de Aplicativos de um grupo de recursos para outro."
services: app-service
documentationcenter: 
author: ZainRizvi
manager: erikre
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: zarizvi
ms.openlocfilehash: 1b5059dc052005b6079f70ecf6771a3771df8d87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="supported-move-configurations"></a>Configurações de movimentação com suporte
Você pode mover recursos de Aplicativo Web do Azure usando a [API Mover Recursos do Resource Manager](../azure-resource-manager/resource-group-move-resources.md).

Atualmente, os aplicativos Web do Azure dão suporte para os seguintes cenários de movimentação:

* Mover todo o conteúdo de um grupo de recursos (aplicativos Web, planos do serviço de aplicativo e certificados) para outro grupo de recursos. 
   > [!Note]
   > O grupo de recursos de destino não pode conter nenhum recurso Microsoft.Web nesse cenário.

* Mover aplicativos Web individuais para um grupo de recursos diferente, enquanto ainda os hospeda em seu plano do serviço de aplicativo atual (o plano do serviço de aplicativo permanece no grupo de recursos antigo).


