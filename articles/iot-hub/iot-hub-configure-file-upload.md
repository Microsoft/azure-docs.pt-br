---
title: Usar o portal do Azure para configurar o carregamento do arquivo | Microsoft Docs
description: Como usar o Portal do Azure para configurar o Hub IoT a fim de habilitar transferências de arquivos de dispositivos conectados. Inclui informações sobre como configurar a conta de armazenamento do Azure de destino.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: da28bfa31c74ff33a200967267500033dd6a9b1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92535868"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Configurar uploads de arquivo do Hub IoT usando o Portal do Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Upload de arquivos

Para usar a funcionalidade de upload de arquivos no [Hub IoT](iot-hub-devguide-file-upload.md), você deve primeiro associar uma conta do Armazenamento do Azure ao seu hub. Selecione **Upload de arquivo** para exibir uma lista de propriedades de upload de arquivo para o hub IoT que está sendo modificado.

![Exibir configurações de upload de arquivo do Hub IoT no portal](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Contêiner de armazenamento**: use o Portal do Azure para selecionar um contêiner de blobs em uma conta de Armazenamento do Azure na assinatura atual do Azure para associar ao Hub IoT. Se for necessário, você pode criar uma conta de Armazenamento do Azure na folha **Contas de armazenamento** e um contêiner de blob na folha **Contêineres**. O Hub IoT gera automaticamente os URIs de SAS com permissões de gravação para esse contêiner de blob para dispositivos a serem usados ao carregar arquivos.

   ![Exibir contêineres de armazenamento para upload de arquivos no portal](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Receber notificações para os arquivos carregados**: habilitar ou desabilitar notificações de upload de arquivo por meio de um botão de opção.

* **TTL de SAS**: essa configuração é o tempo de vida dos URIs de SAS retornados para o dispositivo pelo Hub IoT. Definido para uma hora por padrão, mas pode ser personalizado para outros valores usando o controle deslizante.

* **TTL de configurações de notificação de arquivo padrão**: o tempo de vida de uma notificação de upload de arquivo antes de sua expiração. Definido para um dia por padrão, mas pode ser personalizado para outros valores usando o controle deslizante.

* **Contagem de entrega máxima de notificação de arquivo**: o número de vezes que o Hub IoT tenta entregar uma notificação de carregamento de arquivo. Definido como 10 por padrão, mas pode ser personalizado para outros valores usando o controle deslizante.

   ![Configurar o upload de arquivos do Hub IoT no portal](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os recursos de upload de arquivos do Hub IoT, consulte [Upload de arquivos de um dispositivo](iot-hub-devguide-file-upload.md) no guia do desenvolvedor Hub IoT.

Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

* [Gerenciamento em massa de dispositivos IoT](iot-hub-bulk-identity-mgmt.md)
* [Monitorar o Hub IoT](monitor-iot-hub.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md)
* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/quickstart-linux.md)
* [Proteger sua solução de IoT desde o início](../iot-fundamentals/iot-security-ground-up.md)