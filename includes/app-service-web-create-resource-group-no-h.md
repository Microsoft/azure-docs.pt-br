---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 520a32ed44951a711dcb1d0975fb452829530c4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244978"
---
[!INCLUDE [resource group intro text](resource-group.md)]

No Cloud Shell, crie um grupo de recursos com o comando [`az group create`](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *Europa Ocidental*. Para ver todos os locais com suporte para o Serviço de Aplicativo no nível **Gratuito**, execute o comando [`az appservice list-locations --sku FREE`](/cli/azure/appservice).

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Em geral, você cria seu grupo de recursos e os recursos em uma região próxima a você. 

Quando o comando for concluído, uma saída JSON mostra as propriedades do grupo de recursos.