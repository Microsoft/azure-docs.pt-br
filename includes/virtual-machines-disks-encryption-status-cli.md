---
title: arquivo de inclusão
description: arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 21295089b7ed59189aeb95ba112fb2553c454f11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85609361"
---
```azurecli
az disk show -g yourResourceGroupName -n yourDiskName --query [encryption.type] -o tsv
```