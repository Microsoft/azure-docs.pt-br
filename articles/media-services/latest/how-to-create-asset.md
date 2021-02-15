---
title: Carregar conteúdo para uma CLI de ativo
description: O script de CLI do Azure neste tópico mostra como criar um ativo dos Serviços de Mídia para carregar o conteúdo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6f3c5fa41150f5df2b0e89c4253cb0bb55b1d625
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955810"
---
# <a name="create-an-asset"></a>Criar um ativo

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra como criar um ativo dos Serviços de Mídia.  Você usará um ativo para armazenar o conteúdo de mídia para codificação e streaming.  Para saber mais sobre os ativos dos Serviços de Mídia, leia [Ativos dos Serviços de Mídia do Azure v3](assets-concept.md)

## <a name="prerequisites"></a>Pré-requisitos

Siga as etapas descritas em [Criar uma conta dos Serviços de Mídia](./create-account-howto.md) para criar a conta dos Serviços de Mídia e o grupo de recursos necessários para criar um ativo.

## <a name="methods"></a>Métodos

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST](#tab/rest/)

### <a name="using-rest"></a>Usar o REST

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>Usando cURL

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="using-postman"></a>Uso do Postman

[!INCLUDE[Create an asset with Postman](./includes/task-create-asset-postman.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>Próximas etapas

[Visão geral dos serviços de mídia](media-services-overview.md)
