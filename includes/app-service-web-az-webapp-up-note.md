---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 02/27/2019
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: d285b48606485fbd7f53511a15be1e2a31791829
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244549"
---
> [!NOTE]
> O comando `az webapp up` realiza as seguintes ações:
>
>- Criar um grupo de [recursos padrão](/cli/azure/group#az-group-create).
>
>- Criar um [plano de serviço de aplicativo padrão](/cli/azure/appservice/plan#az-appservice-plan-create).
>
>- [Criar um aplicativo](/cli/azure/webapp#az-webapp-create) com o nome especificado.
>
>- Faz a [implantação de Zip](../articles/app-service/deploy-zip.md) dos arquivos do diretório de trabalho atual para o aplicativo.
>