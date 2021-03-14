---
title: Exemplo de script CLI do Azure – criar uma transformação
description: Transformações descreve um fluxo de trabalho simples de tarefas para processar seus arquivos de áudio ou vídeos (também conhecidos como “receita”). O script da CLI do Azure neste artigo mostra como criar uma transformação.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4623610960d8f21a2dab3293c7499a2112416254
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101718906"
---
# <a name="create-a-transform"></a>Criar uma transformação

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O script da CLI do Azure neste artigo mostra como criar uma transformação. Transformações descreve um fluxo de trabalho simples de tarefas para processar seus arquivos de áudio ou vídeos (também conhecidos como “receita”). Você sempre deve verificar se uma Transformação com o nome desejado e "receita" já existe. Se existir, você deve reutilizá-la.

## <a name="prerequisites"></a>Pré-requisitos

[Crie uma conta de Serviços de Mídia](./create-account-howto.md).

## <a name="cli"></a>[CLI](#tab/cli/)

> [!NOTE]
> Você só pode especificar um caminho para um arquivo JSON de predefinição de Codificador Standard personalizado para [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset); confira o exemplo [Codificação com uma transformação personalizada](custom-preset-cli-howto.md).
>
> Não é possível passar um nome de arquivo ao usar [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="rest"></a>[REST](#tab/rest/)

[!INCLUDE [task general transform creation](./includes/task-create-basic-audio-rest.md)]

---

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
