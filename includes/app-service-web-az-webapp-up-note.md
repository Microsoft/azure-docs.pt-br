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
ms.openlocfilehash: 08458bd170707b28c69fdad1d8aa115a7ad245a5
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559610"
---
> [!NOTE]
> O comando `az webapp up` realiza as seguintes ações:
>
>- Criar um grupo de [recursos padrão](/cli/azure/group?view=azure-cli-latest#az-group-create).
>
>- Criar um [plano de serviço de aplicativo padrão](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).
>
>- [Criar um aplicativo](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) com o nome especificado.
>
>- Faz a [implantação de Zip](../articles/app-service/deploy-zip.md) dos arquivos do diretório de trabalho atual para o aplicativo.
>