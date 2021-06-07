---
title: Limites do Azure Stack Edge pro | Microsoft Docs
description: Saiba mais sobre os limites e os tamanhos recomendados ao implantar e operar Azure Stack o Edge pro, incluindo limites de serviço, limites de dispositivo e limites de armazenamento.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: f1bb5662b48765c08ec58d1f7fa9d341de4937bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91992764"
---
# <a name="azure-stack-edge-pro-limits"></a>Limites do Azure Stack Edge Pro

Considere esses limites ao implantar e operar sua solução Microsoft Azure Stack Edge pro. 

## <a name="azure-stack-edge-service-limits"></a>Limites de serviço do Azure Stack Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Limites de dispositivo do Azure Stack Edge

A tabela a seguir descreve os limites para o dispositivo Azure Stack Edge pro. 

A tabela a seguir descreve os limites para o dispositivo Azure Stack Edge.

| Descrição | Valor |
|---|---|
|Não. de arquivos por dispositivo |100 milhões |
|Não. de compartilhamentos por contêiner |1 |
|N º máximo de pontos de extremidade de compartilhamento e pontos de extremidade REST por dispositivo| 24 |
|N º máximo de contas de armazenamento em camadas por dispositivo| 24|
|Tamanho máximo do arquivo gravado em um compartilhamento| 5 TB |
|Número máximo de grupos de recursos por dispositivo| 800 |

## <a name="azure-storage-limits"></a>Limites de armazenamento do Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Limitações de upload de dados

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limites de tamanho da conta de armazenamento e tamanho do objeto do Azure

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limites de tamanho do objeto do Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar o Azure Stack Edge pro](azure-stack-edge-deploy-prep.md)
