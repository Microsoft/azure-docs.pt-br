---
title: Limites do Azure Data Box Gateway | Microsoft Docs
description: Descreve os limites do sistema e tamanhos recomendados para o Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e80b03f696a78887676e9f16750055a4dcfac230
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60755164"
---
# <a name="azure-data-box-gateway-limits"></a>Limites de Gateway da caixa de dados do Azure

Considere esses limites ao implantar e operar sua solução do Microsoft Azure Data Box Gateway. 


## <a name="data-box-gateway-service-limits"></a>Limites do serviço do Data Box Gateway

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Limites de dispositivo do Data Box Gateway

A tabela a seguir descreve esses limites para o dispositivo do Data Box Gateway.

| DESCRIÇÃO | Valor |
|---|---|
|Não. de arquivos por dispositivo |100 milhões <br> O limite é aproximadamente 25 milhões de arquivos para cada 2 TB de espaço em disco com o limite máximo em 100 milhões |
|Não. de compartilhamentos por dispositivo |24 |
|Não. de compartilhamentos por contêiner de armazenamento do Azure |1 |
|Tamanho máximo do arquivo gravado em um compartilhamento|Para um dispositivo virtual de 2 TB, o tamanho máximo do arquivo é de 500 GB. <br> O tamanho máximo do arquivo aumenta com o tamanho do disco de dados na proporção anterior até atingir um máximo de 5 TB. |

## <a name="azure-storage-limits"></a>Limites de armazenamento do Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Limitações de upload de dados

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limites de tamanho da conta de armazenamento e tamanho do objeto do Azure

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limites de tamanho do objeto do Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
