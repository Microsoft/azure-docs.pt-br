---
title: incluir arquivo
description: incluir arquivo
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: ebc23ce4238c736442fbc4507e858876f9192fd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "76021102"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associar uma conta do Armazenamento do Azure ao Hub IoT

Como o aplicativo do dispositivo simulado carrega um arquivo em um blob, você deve ter uma conta de [Armazenamento do Azure](../articles/storage/common/storage-account-create.md) associada ao seu Hub IoT. Quando você associa uma conta de armazenamento do Azure a um hub IoT, o hub IoT gera um URI de SAS. Um dispositivo pode usar esse URI de SAS para carregar um arquivo com segurança em um contêiner de blob. O serviço de Hub IoT e os SDKs do dispositivo coordenam o processo que gera o URI de SAS e o torna disponível para um dispositivo a ser usado para carregar um arquivo.

Siga as instruções em [Configurar carregamentos de arquivo usando o portal do Azure](../articles/iot-hub/iot-hub-configure-file-upload.md). Certifique-se de que um contêiner de blob é associado ao hub IoT e que as notificações de arquivo estão habilitadas.

![Habilitar Notificações de Arquivo no portal](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
