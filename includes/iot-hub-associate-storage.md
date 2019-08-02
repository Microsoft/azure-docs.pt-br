---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 8693c48905155ed757bb727e42f4180f36c015f1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172025"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associar uma conta do Armazenamento do Azure ao Hub IoT

Como o aplicativo do dispositivo simulado carrega um arquivo em um blob, você deve ter uma conta de [Armazenamento do Azure](../articles/storage/common/storage-quickstart-create-account.md) associada ao seu Hub IoT. Quando você associa uma conta de armazenamento do Azure a um hub IoT, o hub IoT gera um URI de SAS. Um dispositivo pode usar esse URI de SAS para carregar um arquivo com segurança em um contêiner de blob. O serviço de Hub IoT e os SDKs do dispositivo coordenam o processo que gera o URI de SAS e o torna disponível para um dispositivo a ser usado para carregar um arquivo.

Siga as instruções em [Configurar carregamentos de arquivo usando o portal do Azure](../articles/iot-hub/iot-hub-configure-file-upload.md). Certifique-se de que um contêiner de blob é associado ao hub IoT e que as notificações de arquivo estão habilitadas.

![Habilitar Notificações de Arquivo no portal](./media/iot-hub-associate-storage/enable-file-notifications.png)