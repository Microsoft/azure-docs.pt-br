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
ms.openlocfilehash: b59b45de04ebb717dfe55eb17c9dbd92f7523976
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997980"
---
[!INCLUDE [resource group intro text](resource-group.md)]

No Cloud Shell, crie um grupo de recursos com o comando [`az group create`](/cli/azure/group?view=azure-cli-latest). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *Europa Ocidental*. Para ver todos os locais com suporte para o Serviço de Aplicativo no nível **Gratuito**, execute o comando [`az appservice list-locations --sku FREE`](/cli/azure/appservice?view=azure-cli-latest).

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Em geral, você cria seu grupo de recursos e os recursos em uma região próxima a você. 

Quando o comando for concluído, uma saída JSON mostra as propriedades do grupo de recursos.