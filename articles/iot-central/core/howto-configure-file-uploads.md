---
title: Carregar arquivos de dispositivos para o armazenamento do Azure | Microsoft Docs
description: Como configurar uploads de arquivos de seus dispositivos para a nuvem. Depois de configurar os carregamentos de arquivo, implemente os carregamentos de arquivo em seus dispositivos.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: ed2eff4d6ccbb9f85dfaf7049fa3cc18711bae0f
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796897"
---
# <a name="upload-files-from-your-devices-to-the-cloud"></a>Carregar arquivos de seus dispositivos para a nuvem

*Este tópico se aplica a administradores e desenvolvedores de dispositivos.*

IoT Central permite carregar mídia e outros arquivos de dispositivos conectados para o armazenamento em nuvem. Você configura o recurso de upload de arquivo em seu aplicativo IoT Central e, em seguida, implementa os carregamentos de arquivo no código do dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ser um administrador em seu aplicativo IoT Central para configurar carregamentos de arquivos.

Você precisa de uma conta de armazenamento do Azure e contêiner para armazenar os arquivos carregados. Se você não tiver uma conta de armazenamento e um contêiner existentes para usar, crie uma [nova conta de armazenamento no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="configure-device-file-uploads"></a>Configurar carregamentos de arquivos do dispositivo

Para configurar carregamentos de arquivos do dispositivo:

1. Navegue até a seção **Administração** em seu aplicativo.

1. Selecione o **upload do arquivo do dispositivo**.

1. Selecione a conta de armazenamento e o contêiner a serem usados. Se a conta de armazenamento estiver em uma assinatura do Azure diferente do seu aplicativo, insira uma cadeia de conexão da conta de armazenamento.

1. Se necessário, ajuste o tempo limite de carregamento que define por quanto tempo uma solicitação de upload permanece válida para. Os valores válidos são de 1 a 24 horas.

1. Clique em **Salvar**. Quando o status mostrar **configurado**, você estará pronto para carregar arquivos de dispositivos.

:::image type="content" source="media/howto-configure-file-uploads/file-upload-configuration.png" alt-text="Configurar upload de arquivo no aplicativo":::

## <a name="disable-device-file-uploads"></a>Desabilitar carregamentos de arquivos do dispositivo

Se você quiser desabilitar os carregamentos de arquivos do dispositivo para seu aplicativo IoT Central:

1. Navegue até a seção **Administração** em seu aplicativo.

1. Selecione o **upload do arquivo do dispositivo**.

1. Selecione **Excluir**.

## <a name="upload-a-file-from-a-device"></a>Carregar um arquivo de um dispositivo

IoT Central usa o recurso de carregamento de arquivo do Hub IoT para permitir que dispositivos carreguem arquivos. Para obter um exemplo de código que mostra como carregar arquivos de um dispositivo, consulte a [amostra de dispositivo IOT central upload de arquivo](/samples/iot-for-all/iotc-file-upload-device/iotc-file-upload-device/).

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como configurar e implementar uploads de arquivo do dispositivo no IoT Central, uma próxima etapa sugerida é saber mais carregamentos de arquivos do dispositivo:

- [Fazer upload de arquivos do seu dispositivo na nuvem com o Hub IoT (.NET)](../../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [Fazer upload de arquivos do seu dispositivo para a nuvem com o Hub IoT (Java)](../../iot-hub/iot-hub-java-java-file-upload.md)
- [Fazer upload de arquivos do seu dispositivo na nuvem com o Hub IoT (Node.js)](../../iot-hub/iot-hub-node-node-file-upload.md)
- [Carregar arquivos do seu dispositivo para a nuvem com o Hub IoT (Python)](../../iot-hub/iot-hub-python-python-file-upload.md)