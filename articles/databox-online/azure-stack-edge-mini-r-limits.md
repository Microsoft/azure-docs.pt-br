---
title: Limites de R Azure Stack de borda Microsoft Docs
description: Descreve os limites do sistema e os tamanhos recomendados para o Azure Stack borda mini R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 6f01d62d1d11f5ff90661482ffd2db112657eee5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466150"
---
# <a name="azure-stack-edge-mini-r-limits"></a>Limites de R de borda Azure Stack


Considere esses limites à medida que implantar e operar sua solução de Azure Stack Edge mini R.

## <a name="azure-stack-edge-service-limits"></a>Limites de serviço do Azure Stack Edge

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-mini-r-device-limits"></a>Limites de dispositivos do Azure Stack Edge de borda

A tabela a seguir descreve os limites para o dispositivo mini R do Azure Stack Edge.

| Descrição | Limite|
|---|---:|
|Não. de arquivos por dispositivo | 100 milhões <!--check with devs-->|
|Não. de compartilhamentos por contêiner | 1|
|N º máximo de pontos de extremidade de compartilhamento e pontos de extremidade REST por dispositivo| 24 |
|N º máximo de contas de armazenamento em camadas por dispositivo| 24|
|Tamanho máximo do arquivo gravado em um compartilhamento| 500 GB|
|Número máximo de grupos de recursos por dispositivo| 800|

## <a name="azure-storage-limits"></a>Limites de armazenamento do Azure

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Limitações de upload de dados

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limites de tamanho da conta de armazenamento e tamanho do objeto do Azure

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limites de tamanho do objeto do Azure

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar Azure Stack borda](azure-stack-edge-gpu-deploy-prep.md)
