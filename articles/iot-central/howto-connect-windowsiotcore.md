---
title: Conectar um dispositivo Windows IoT Core ao aplicativo Azure IoT Central | Microsoft Docs
description: Como um desenvolvedor de dispositivos, saiba como conectar um dispositivo MXChip IoT DevKit ao aplicativo Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: af6d66d2e3eae80477a151323578b930dcd7727a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886586"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Conectar um dispositivo Windows IoT Core ao aplicativo Azure IoT Central

Este artigo descreve como um desenvolvedor de dispositivos conecta um dispositivo Windows IoT Core ao aplicativo Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa do seguinte:

- Um aplicativo Azure IoT Central criado a partir do modelo de aplicativo de **Devkits de Exemplo**. Para obter mais informações, consulte o [Criar um início rápido de aplicativo](quick-deploy-iot-central.md).

- Um dispositivo executando o sistema operacional Windows 10 IoT Core. Para obter mais informações, consulte [configurando seu dispositivo Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- Um computador de desenvolvimento com [Node. js](https://nodejs.org/) versão 8.0.0 ou posterior instalado. É possível executar `node --version` na linha de comando para verificar a versão. O Node.js está disponível para uma ampla variedade de sistemas operacionais.

## <a name="the-sample-devkits-application"></a>O aplicativo de exemplo Devkits

Um aplicativo criado a partir do modelo de aplicativo de **Devkits de Exemplo** inclui um modelo de dispositivo **Windows IoT Core** com as características a seguir:

- Medidas de telemetria para o dispositivo: **Umidade**, **temperatura**, e **pressão**.
- Configuração para controlar **velocidade**.
- Uma propriedade de dispositivo **morrem número** e uma propriedade de nuvem **local**.

Para obter detalhes completos sobre a configuração do modelo de dispositivo, consulte [detalhes do modelo do dispositivo Windows IoT Core](#device-template-details).

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

Em seu aplicativo do Azure IoT Central, use o **Device Explorer** página para adicionar um dispositivo real da **Windows 10 IoT Core** modelo do dispositivo. Tome nota do dispositivo detalhes de conexão (**ID do escopo**, **ID do dispositivo**, e **chave primária**). Para obter mais informações, consulte [Obtenha informações de conexão](howto-generate-connection-string.md#get-connection-information).

## <a name="prepare-the-device"></a>Preparar o dispositivo

Para o dispositivo se conecte a IoT Central, ele precisa de uma cadeia de caracteres de conexão.

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

Para o código de dispositivo acessar a cadeia de caracteres de conexão, salve-o em um arquivo chamado **connection.string.iothub** na pasta `C:\Data\Users\DefaultAccount\Documents\` em seu dispositivo Windows 10 IoT Core.

Para copiar o **connection.string.iothub** arquivo do seu computador desktop para o `C:\Data\Users\DefaultAccount\Documents\` pasta em seu dispositivo, você pode usar os [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Use seu navegador da web para navegar até o do Windows Device Portal em seu dispositivo.
1. Para procurar os arquivos em seu dispositivo, escolha **aplicativos > Explorador de arquivos**.
1. Navegue até **Folders\Documents usuário**. Em seguida, carregue o **connection.string.iothub** arquivo:

    ![Carregar a cadeia de caracteres de conexão](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Implantar e executar

Para implantar e executar o aplicativo de exemplo em seu dispositivo, você pode usar o [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Use seu navegador da web para navegar até o do Windows Device Portal em seu dispositivo.
1. Para implantar e executar o **cliente do Azure IoT Hub** aplicativo, escolha **aplicativos > exemplos de execução rápida**. Em seguida, escolha **cliente do Azure IoT Hub**.
1. Em seguida, escolha **implantar e executar**.

    ![Implantar e executar](media/howto-connect-windowsiotcore/quick-run.png)

Depois de alguns minutos, você pode exibir a telemetria do seu dispositivo em seu aplicativo IoT Central.

O [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) inclui ferramentas que você pode usar para solucionar problemas do dispositivo:

- O **Gerenciador de aplicativos** página lhe permite controlar os aplicativos em execução no seu dispositivo.
- Se você não tiver um monitor conectado ao seu dispositivo, você pode usar o **configurações do dispositivo** página para capturar capturas de tela do dispositivo. Por exemplo: 

    ![Captura de tela do aplicativo](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Fazer o download do código-fonte

Se você quiser explorar e modificar o código-fonte para o aplicativo cliente, você pode baixá-lo partir o [repositório GitHub de exemplos do Windows-iotcore](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Detalhes do modelo de dispositivo

Um aplicativo criado a partir do modelo de aplicativo de **Devkits de Exemplo** inclui um modelo de dispositivo **Windows IoT Core** com as características a seguir:

### <a name="telemetry-measurements"></a>Medidas de telemetria

| Nome do campo     | Unidades  | Mínimo | Máximo | Casas decimais |
| -------------- | ------ | ------- | ------- | -------------- |
| umidade       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressão       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Configurações

Configurações numéricas

| Nome de exibição | Nome do campo | Unidades | Casas decimais | Mínimo | Máximo | Inicial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Velocidade da ventoinha    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>propriedades

| Type            | Nome de exibição | Nome do campo | Tipo de dados |
| --------------- | ------------ | ---------- | --------- |
| Propriedade de dispositivo | Número impresso   | dieNumber  | número    |
| Text            | Local padrão     | location   | N/D       |

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como conectar um Raspberry Pi ao seu aplicativo do Azure IoT Central, a próxima etapa sugerida é saber como [configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para seu próprio dispositivo de IoT.