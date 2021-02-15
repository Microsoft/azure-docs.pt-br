---
title: Criar uma entidade de serviço do Azure
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: dc5297a3abb913c7bef7c1669fa319d01a0c0203
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947100"
---
## <a name="create-an-azure-service-principal"></a>Criar uma entidade de serviço do Azure

Para que o aplicativo interaja com sua conta do Azure, você precisa de uma entidade de serviço do Azure para gerenciar permissões. Siga as instruções em [Criar uma entidade de serviço do Azure](/powershell/azure/create-azure-service-principal-azureps?viewFallbackFrom=azps-3.3.0).

Ao criar uma entidade de serviço, você verá que ela tem um valor secreto, uma ID e uma ID de aplicativo. Salve a ID do aplicativo e o segredo em um local temporário para etapas posteriores.
