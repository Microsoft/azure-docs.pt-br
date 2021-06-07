---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88602351"
---
<!-- Create a resource group -->

Use o comando a seguir para criar um grupo de recursos. Selecione a região geográfica que será usada para armazenar os registros de mídia e metadados da sua conta dos Serviços de Mídia. Essa região será usada para processar e transmitir a mídia.

```azurecli
az group create --name amsResourceGroup --location westus2
```
