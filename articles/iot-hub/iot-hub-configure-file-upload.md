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
ms.openlocfilehash: bd7cc37b8fc81fc9d4109826743f2243913d0604
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734996"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Configurar uploads de arquivo do Hub IoT usando o Portal do Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Upload de arquivos

Para usar a funcionalidade de upload de arquivos no [Hub IoT](iot-hub-devguide-file-upload.md), você deve primeiro associar uma conta do Armazenamento do Azure ao seu hub. Selecione **Upload de arquivo** para exibir uma lista de propriedades de upload de arquivo para o hub IoT que está sendo modificado.

![Exibir configurações de upload de arquivo do Hub IoT no portal](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Contêiner de armazenamento**: Use o portal do Azure para selecionar um contêiner de blob em uma conta de armazenamento do Azure na sua assinatura do Azure para associar ao IoT Hub. Se for necessário, você pode criar uma conta de Armazenamento do Azure na folha **Contas de armazenamento** e um contêiner de blob na folha **Contêineres**. O Hub IoT gera automaticamente os URIs de SAS com permissões de gravação para esse contêiner de blob para dispositivos a serem usados ao carregar arquivos.

   ![Exibir contêineres de armazenamento para upload de arquivos no portal](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Receber notificações para arquivos carregados**: Habilitar ou desabilitar notificações de upload de arquivo por meio de alternância.

* **TTL de SAS**: Essa configuração é a vida útil dos URIs de SAS retornados para o dispositivo pelo Hub IoT. Definido para uma hora por padrão, mas pode ser personalizado para outros valores usando o controle deslizante.

* **TTL padrão das configurações de notificação de arquivo**: A vida útil de uma notificação de upload de arquivo antes de sua expiração. Definido para um dia por padrão, mas pode ser personalizado para outros valores usando o controle deslizante.

* **Contagem de entrega máxima de notificação de arquivo**: O número de vezes que o Hub IoT tenta entregar uma notificação de upload de arquivo. Definido como 10 por padrão, mas pode ser personalizado para outros valores usando o controle deslizante.

   ![Configurar o upload de arquivos do Hub IoT no portal](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os recursos de upload de arquivos do Hub IoT, consulte [Upload de arquivos de um dispositivo](iot-hub-devguide-file-upload.md) no guia do desenvolvedor Hub IoT.

Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

* [Gerenciamento em massa de dispositivos IoT](iot-hub-bulk-identity-mgmt.md)
* [Métricas do IoT Hub](iot-hub-metrics.md)
* [Monitoramento de operações](iot-hub-operations-monitoring.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md)
* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Proteger sua solução de IoT desde o início](../iot-fundamentals/iot-security-ground-up.md)
