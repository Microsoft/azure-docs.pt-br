---
title: Limites de R Azure Stack Edge pro | Microsoft Docs
description: Descreve os limites do sistema e os tamanhos recomendados para o Azure Stack Edge pro R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dfff3bdd716c54a6c83dbc9fec63c794c1fba85b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96466024"
---
# <a name="azure-stack-edge-pro-r-limits"></a>Limites de R Azure Stack Edge pro

Considere esses limites ao implantar e operar sua solução Azure Stack Edge pro R.

## <a name="azure-stack-edge-pro-r-service-limits"></a>Limites do serviço R Azure Stack Edge pro

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-pro-r-device-limits"></a>Limites de dispositivo do Azure Stack Edge pro R

A tabela a seguir descreve os limites para o dispositivo Azure Stack R Edge pro.

| Descrição | Valor |
|---|---|
|Não. de arquivos por dispositivo |100 milhões |
|Não. de compartilhamentos por contêiner |1 |
|N º máximo de pontos de extremidade de compartilhamento e pontos de extremidade REST por dispositivo| 24 |
|N º máximo de contas de armazenamento em camadas por dispositivo| 24|
|Tamanho máximo do arquivo gravado em um compartilhamento| 5 TB |
|Número máximo de grupos de recursos por dispositivo| 800 |

## <a name="azure-storage-limits"></a>Limites de armazenamento do Azure

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Limitações de upload de dados

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limites de tamanho da conta de armazenamento e tamanho do objeto do Azure

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limites de tamanho do objeto do Azure

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar o Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-prep.md)
