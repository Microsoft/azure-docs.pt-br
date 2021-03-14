---
title: A API REST de conversão de ativos
description: Descreve como converter um ativo por meio da API REST
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: f33e5717cd5556e72d996e7e943867c16805e71b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705170"
---
# <a name="use-the-model-conversion-rest-api"></a>Usar a API REST de conversão de modelo

O serviço de [conversão de modelo](model-conversion.md) é controlado por meio de uma [API REST](https://en.wikipedia.org/wiki/Representational_state_transfer). Essa API pode ser usada para criar conversões, obter propriedades de conversão e listar conversões existentes.

## <a name="rest-api-reference"></a>Referência da API REST

A documentação de referência da API REST de renderização remota pode ser encontrada [aqui](/rest/api/mixedreality/2021-01-01preview/remoterendering)e as definições do Swagger [aqui](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality).

Fornecemos um script do PowerShell no [repositório de exemplos do arr](https://github.com/Azure/azure-remote-rendering) na pasta *Scripts* , chamada *Conversion.ps1*, que demonstra o uso de nosso serviço. O script e sua configuração são descritos aqui: [exemplo de scripts do PowerShell](../../samples/powershell-example-scripts.md). Também fornecemos SDKs para [.net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering), Java e Python.

## <a name="next-steps"></a>Próximas etapas

- [Usar o armazenamento de Blobs do Azure para conversão de modelo](blob-storage.md)
- [Conversão de modelo](model-conversion.md)