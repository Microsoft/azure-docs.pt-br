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
ms.openlocfilehash: cc44780bd9b42e00ecfb3d140486fec87c767a76
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767215"
---
[!INCLUDE [resource group intro text](resource-group.md)]

No Cloud Shell, crie um grupo de recursos com o comando [`az group create`](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *Europa Ocidental*. Para ver todos os locais com suporte para o Serviço de Aplicativo no Linux no nível **Básico**, execute o comando [`az appservice list-locations --sku B1 --linux-workers-enabled`](/cli/azure/appservice#az_appservice_list_locations).

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Em geral, você cria seu grupo de recursos e os recursos em uma região próxima a você. 

Quando o comando for concluído, uma saída JSON mostra as propriedades do grupo de recursos.