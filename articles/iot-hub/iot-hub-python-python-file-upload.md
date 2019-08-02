---
title: Carregar arquivos dos dispositivos no Hub IoT do Azure com Python| Microsoft Docs
description: Como carregar arquivos de um dispositivo para a nuvem usando o SDK do dispositivo IoT do Azure para Python. Os arquivos carregados são armazenados em um contêiner de Azure Storage Blob.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: kgremban
ms.openlocfilehash: 23b0a2ac8e0264ddc1592479759cc8398d9ef5f8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621274"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Carregar arquivos do seu dispositivo para a nuvem com o Hub IoT

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este artigo mostra como usar os [recursos de carregamento de arquivos do Hub IoT](iot-hub-devguide-file-upload.md) para carregar um arquivo para [armazenamento de blobs do Azure](../storage/index.yml). Este tutorial mostra como:

* Fornece um contêiner de armazenamento com segurança para carregar um arquivo.

* Use o cliente do Python para carregar um arquivo por meio de seu Hub IoT.

O [enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md) início rápido demonstra a funcionalidade básica de mensagens do dispositivo para a nuvem do IoT Hub. No entanto, em alguns cenários você não pode mapear facilmente os dados que seus dispositivos enviam em mensagens relativamente menores do dispositivo para a nuvem que o Hub IoT aceita. Quando você precisar carregar arquivos de um dispositivo, ainda poderá usar a segurança e a confiabilidade do Hub IoT.

> [!NOTE]
> SDK de Python de Hub IoT atualmente suporta apenas carregar arquivos baseados em caracteres, como arquivos **.txt**.

Ao final deste tutorial, você executará o aplicativo de console do Python:

* **FileUpload.py**, que carrega um arquivo para o armazenamento usando o SDK do dispositivo Python.

> [!NOTE]
> O Hub IoT é compatível com muitas plataformas de dispositivo e linguagens (incluindo C, .NET, Javascript, Python e Java) por meio dos SDKs do dispositivo IoT do Azure. Confira a [Central de Desenvolvedores do IoT do Azure](https://azure.microsoft.com/develop/iot) para obter instruções passo a passo sobre como conectar seu dispositivo ao Hub IoT do Azure.

Para concluir este tutorial, você precisará do seguinte:

* [Python 2.x ou 3.x](https://www.python.org/downloads/). Certifique-se de usar a instalação de 32 bits ou 64 bits conforme exigido pelo seu programa de instalação. Quando solicitado durante a instalação, certifique-se de adicionar Python à variável de ambiente específica da plataforma. Se você estiver usando o Python 2.x, talvez seja necessário [instalar ou atualizar o *pip*, o sistema de gerenciamento de pacotes do Python](https://pip.pypa.io/en/stable/installing/).

* Se você está usando o sistema operacional Windows, o [Pacote redistribuível do Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145) permite o uso de DLLs nativas do Python.

* Uma conta ativa do Azure. Se você não tem uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* Um Hub IoT em sua conta do Azure, com uma identidade do dispositivo para testar o recurso de upload de arquivo. 

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Carregar um arquivo de um aplicativo de dispositivo

Nesta seção, você criará o aplicativo de dispositivo para carregar um arquivo no hub IoT.

1. No prompt de comando, execute o seguinte comando para instalar o pacote **azure-iothub-device-client**:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Usando um editor de texto, crie um arquivo de teste que você carregará no armazenamento de blobs.

    > [!NOTE]
    > SDK de Python de Hub IoT atualmente suporta apenas carregar arquivos baseados em caracteres, como arquivos **.txt**.

3. Usando um editor de texto, crie um arquivo **FileUpload.py** em sua pasta de trabalho.

4. Adicione as instruções `import` e variáveis a seguir ao início do arquivo **FileUpload.py**. 

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name for storage]"
    ```

5. Substitua `[Device Connection String]` pela cadeia de conexão do dispositivo do Hub IoT no seu arquivo. Substitua `[Full path to file]` pelo caminho para o arquivo de teste que você criou, ou qualquer arquivo em seu dispositivo que você deseja carregar. Substitua `[File name for storage]` pelo nome que você deseja dar ao seu arquivo depois que ele for carregado no armazenamento de blobs. 

6. Criar um retorno de chamada para a função **upload_blob**:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Adicione o código a seguir para se conectar ao cliente e carregar o arquivo. Também inclua a rotina `main`:

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

8. Salve e feche o arquivo **UploadFile.py**.

## <a name="run-the-application"></a>Executar o aplicativo

Agora você está pronto para executar o aplicativo.

1. Em um prompt de comando na pasta de trabalho, execute o seguinte comando:

    ```cmd/sh
    python FileUpload.py
    ```

2. A captura de tela a seguir mostra a saída do aplicativo **FileUpload**:

    ![Saída do aplicativo simulated-device](./media/iot-hub-python-python-file-upload/1.png)

3. Você pode usar o portal para exibir o arquivo carregado no contêiner de armazenamento configurado:

    ![Arquivo carregado](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar os recursos de carregamento de arquivo do Hub IoT para simplificar os carregamentos de arquivos de dispositivos. Você pode continuar explorando os recursos e cenários do Hub IoT com os seguintes artigos:

* [Criar um Hub IoT de modo programático](iot-hub-rm-template-powershell.md)

* [Introdução ao SDK C](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)
