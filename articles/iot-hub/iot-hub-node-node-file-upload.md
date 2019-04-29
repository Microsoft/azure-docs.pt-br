---
title: Carregar arquivos dos dispositivos no Hub IoT do Azure com o Nó | Microsoft Docs
description: Como carregar arquivos de um dispositivo para a nuvem usando o SDK do dispositivo IoT do Azure para o Node.js. Os arquivos carregados são armazenados em um contêiner de Azure Storage Blob.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 7ad2c9dd89843a36a786eeefee8403d32027e11c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61443833"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Carregar arquivos do seu dispositivo para a nuvem com o Hub IoT

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial baseia-se no código do tutorial [Enviar mensagens da nuvem para o dispositivo com o Hub IoT](iot-hub-node-node-c2d.md) para mostrar como usar os [recursos de upload de arquivo do Hub IoT](iot-hub-devguide-file-upload.md) para carregar um arquivo para o [armazenamento de blobs do Azure](../storage/index.yml). Este tutorial mostra como:

- Fornecer com segurança um URI de blob do Azure a um dispositivo para carregamento de um arquivo.
- 
- Usar as notificações de carregamento de arquivo do Hub IoT para disparar o processamento do arquivo no back-end do aplicativo.

O tutorial [Introdução ao Hub IoT](quickstart-send-telemetry-node.md) demonstra a funcionalidade básica de mensagens de dispositivo para nuvem do Hub IoT. No entanto, em alguns cenários você não pode mapear facilmente os dados que seus dispositivos enviam em mensagens relativamente menores do dispositivo para a nuvem que o Hub IoT aceita. Por exemplo: 

*  Arquivos grandes que contêm imagens
*  vídeos
*  Dados de vibração amostrados a alta frequência
*  Alguma forma de dados pré-processados.

Esses arquivos normalmente são processados em lote na nuvem usando ferramentas como o [Azure Data Factory](../data-factory/introduction.md) ou a pilha do [Hadoop](../hdinsight/index.yml). Quando você precisar carregar arquivos de um dispositivo, ainda poderá usar a segurança e a confiabilidade do Hub IoT.

No final deste tutorial, você executará dois aplicativos de console do Node.js:

* **SimulatedDevice.js**, que carrega um arquivo no armazenamento usando um URI da SAS fornecido pelo seu Hub IoT.

* **ReadFileUploadNotification.js**, que recebe notificações de upload de arquivo de seu Hub IoT.

> [!NOTE]
> O Hub IoT é compatível com muitas plataformas de dispositivo e linguagens (incluindo C, .NET, Javascript, Python e Java) por meio dos SDKs do dispositivo IoT do Azure. Para obter instruções passo a passo sobre como conectar seu dispositivo ao IoT Hub do Azure, consulte o [Azure IoT Central de desenvolvedores].

Para concluir este tutorial, você precisará do seguinte:

* Node.js versão 4.0.x ou posterior.

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Carregar um arquivo de um aplicativo de dispositivo

Nesta seção, você criará o aplicativo de dispositivo para carregar um arquivo no hub IoT.

1. Crie uma pasta vazia chamada ```simulateddevice```.  Na pasta ```simulateddevice```, crie um arquivo package.json usando o comando a seguir no seu prompt de comando.  Aceite todos os padrões:

    ```cmd/sh
    npm init
    ```

2. No prompt de comando, na pasta ```simulateddevice```, execute o seguinte comando para instalar o pacote **azure-iot-device** do SDK do Dispositivo e o pacote **azure-iot-device-mqtt**:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Usando um editor de texto, crie um arquivo **SimulatedDevice.js** na pasta ```simulateddevice```.

4. Adicione as seguintes instruções ```require``` no início do arquivo **SimulatedDevice.js** :

    ```javascript
    'use strict';

    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

5. Adicione uma variável `deviceconnectionstring` e use-a para criar uma instância de **cliente**.  Substitua `{deviceconnectionstring}` pelo nome do dispositivo criado na seção *Criar um Hub IoT*:

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Por uma questão de simplicidade, a cadeia de conexão está incluída no código: esta não é uma prática recomendada e, dependendo do seu caso de uso e da arquitetura, talvez você queira considerar maneiras mais seguras de armazenar este segredo.

6. Adicione o código a seguir para se conectar ao cliente:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

7. Crie um retorno de chamada e use a função **uploadToBlob** para carregar o arquivo.

    ```javascript
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);

        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

8. Salve e feche o arquivo **SimulatedDevice.js** .

9. Copie um arquivo de imagem para a pasta `simulateddevice` e renomeie-o como `myimage.png`.

## <a name="receive-a-file-upload-notification"></a>Receber uma notificação de upload de arquivo

Nesta seção, você criará um aplicativo de console do Node.js que receba mensagens de notificação de upload de arquivo do Hub IoT.

Você pode usar a cadeia de conexão **iothubowner** do seu Hub IoT para concluir esta seção. Você encontrará a cadeia de conexão no [portal do Azure](https://portal.azure.com/), na folha **Política de acesso compartilhado**.

1. Crie uma pasta vazia chamada ```fileuploadnotification```.  Na pasta ```fileuploadnotification```, crie um arquivo package.json usando o comando a seguir no seu prompt de comando.  Aceite todos os padrões:

    ```cmd/sh
    npm init
    ```

2. No prompt de comando na pasta ```fileuploadnotification```, execute o seguinte comando para instalar o pacote do SDK **azure-iothub**:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Usando um editor de texto, crie um arquivo **FileUploadNotification.js** na pasta `fileuploadnotification`.

4. Adicione as seguintes instruções `require` no início do arquivo **FileUploadNotification.js**:

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Adicione uma variável `iothubconnectionstring` e use-a para criar uma instância de **cliente**.  Substitua `{iothubconnectionstring}` pela cadeia de conexão do Hub IoT criado na seção _Criar um Hub IoT_:

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Por uma questão de simplicidade, a cadeia de conexão está incluída no código: esta não é uma prática recomendada e, dependendo do seu caso de uso e da arquitetura, talvez você queira considerar maneiras mais seguras de armazenar este segredo.

6. Adicione o código a seguir para se conectar ao cliente:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

7. Abra o cliente e use a função **getFileNotificationReceiver** para receber atualizações de status.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

8. Salve e feche o arquivo **FileUploadNotification.js**.

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

Em um prompt de comando na pasta `fileuploadnotification`, execute o seguinte comando:

```cmd/sh
node FileUploadNotification.js
```

Em um prompt de comando na pasta `simulateddevice`, execute o seguinte comando:

```cmd/sh
node SimulatedDevice.js
```

A captura de tela a seguir mostra a saída do aplicativo **SimulatedDevice**:

![Saída do aplicativo simulated-device](./media/iot-hub-node-node-file-upload/simulated-device.png)

A captura de tela a seguir mostra a saída do aplicativo **FileUploadNotification**:

![Saída do aplicativo read-file-upload-notification](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Você pode usar o portal para exibir o arquivo carregado no contêiner de armazenamento configurado:

![Arquivo carregado](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar os recursos de carregamento de arquivo do Hub IoT para simplificar os carregamentos de arquivos de dispositivos. Você pode continuar explorando os recursos e cenários do Hub IoT com os seguintes artigos:

*  [Criar um Hub IoT de modo programático](iot-hub-rm-template-powershell.md)
*  [Introdução ao SDK C](iot-hub-device-sdk-c-intro.md)
*  [SDKs do Azure IoT](iot-hub-devguide-sdks.md)