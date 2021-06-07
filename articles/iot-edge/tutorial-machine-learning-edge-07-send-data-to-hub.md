---
title: 'Tutorial: Enviar dados do dispositivo por gateway transparente – Machine Learning no Azure IoT Edge'
description: Este tutorial mostra como você pode usar seu computador de desenvolvimento como um dispositivo IoT Edge simulado para enviar dados ao Hub IoT passando por um dispositivo configurado como um gateway transparente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: a830c2cbbc16d5d5e93e6f2283af012e4412c0be
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310641"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Tutorial: Enviar dados por meio do gateway transparente

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Neste artigo, usamos novamente a VM de desenvolvimento como um dispositivo simulado. Mas, em vez de enviar dados diretamente para o Hub IoT, o dispositivo envia os dados para o dispositivo do IoT Edge configurado como um gateway transparente.

Monitoramos a operação do dispositivo IoT Edge enquanto o dispositivo simulado está enviando dados. Depois que a execução do dispositivo for concluída, examinaremos os dados em nossa conta de armazenamento para validar se tudo funcionou conforme o esperado.

Esta etapa normalmente é executada por um desenvolvedor de nuvem ou de dispositivo.

Nesta seção do tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Criar e executar um dispositivo folha.
> * Confirmar se os dados gerados estão sendo armazenados no Armazenamento de Blobs do Azure.
> * Validar se o modelo de machine learning classificou os dados do dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo faz parte de uma série para um tutorial sobre como usar o Azure Machine Learning no IoT Edge. Cada artigo da série se baseia no trabalho do artigo anterior. Se você chegou a este artigo diretamente, acesse o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série.

## <a name="review-device-harness"></a>Examinar o agente do dispositivo

Reutilize o [projeto DeviceHarness](tutorial-machine-learning-edge-03-generate-data.md) para simular o dispositivo downstream (ou folha). Conectar-se ao gateway transparente requer duas coisas adicionais:

* Registrar o certificado para fazer o dispositivo IoT downstream confiar na autoridade de certificado que está sendo usada pelo runtime do IoT Edge. Em nosso caso, o dispositivo downstream é a VM de desenvolvimento.
* Adicione o FQDN (nome de domínio totalmente qualificado) do gateway de borda à cadeia de conexão do dispositivo.

Examine o código para ver como esses dois itens são implementados.

1. Em seu computador de desenvolvimento, abra o Visual Studio Code.

1. Use **Arquivo** > **Abrir Pasta...** para abrir C:\\source\\IoTEdgeAndMlSample\\DeviceHarness.

1. Examine o método InstallCertificate() em Program.cs.

1. Observe que, se o código encontrar o caminho do certificado, ele chamará o método CertificateManager.InstallCACert para instalar o certificado no computador.

1. Agora examine o método GetIotHubDevice na classe TurbofanDevice.

1. Quando o usuário especifica o FQDN do gateway usando a opção "-g", esse valor é passado para esse método como a variável `gatewayFqdn`, que é acrescentada à cadeia de conexão do dispositivo.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Criar e executar dispositivo folha

1. Compile o projeto DeviceHarness, com ele ainda aberto no Visual Studio Code. No menu **Terminal**, selecione **Executar Tarefa de Build** e selecione **Compilar**.

1. Localize o FQDN (nome de domínio totalmente qualificado) para seu gateway de borda navegando até o dispositivo do IoT Edge (VM do Linux) no portal do Azure e copiando o valor de **nome DNS** da página de visão geral.

1. Inicie o dispositivo IoT (VM do Linux) se ele ainda não estiver em execução.

1. Abra o terminal do Visual Studio Code. No menu **Terminal**, selecione **Novo Terminal** e execute o seguinte comando, substituindo `<edge_device_fqdn>` pelo nome DNS que você copiou do dispositivo do IoT Edge (VM do Linux):

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

1. O aplicativo tenta instalar o certificado em seu computador de desenvolvimento. Quando ele fizer isso, aceite o aviso de segurança.

1. Quando solicitado para a cadeia de conexão do Hub IoT, clique nas reticências ( **...** ) no painel de dispositivos do Hub IoT do Azure e selecione **Copiar Cadeia de Conexão do Hub IoT**. Cole o valor no terminal.

1. Você verá uma saída como:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Observe a adição de “GatewayHostName” à cadeia de conexão do dispositivo, que faz o dispositivo se comunicar por meio do Hub IoT pelo gateway transparente do IoT Edge.

## <a name="check-output"></a>Verificar saída

### <a name="iot-edge-device-output"></a>Saída do dispositivo IoT Edge

A saída do módulo avroFileWriter pode ser prontamente observada examinando o dispositivo IoT Edge.

1. SSH na máquina virtual do IoT Edge.

1. Procure arquivos gravados em disco.

   ```bash
   find /data/avrofiles -type f
   ```

1. A saída do comando terá a aparência do seguinte exemplo:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Talvez você tenha mais de um arquivo dependendo do tempo da execução.

1. Preste atenção nos carimbos de hora. O módulo avroFileWriter carrega os arquivos para a nuvem quando a hora da última modificação é maior do que 10 minutos no passado (confira MODIFIED\_FILE\_TIMEOUT em uploader.py no módulo avroFileWriter).

1. Quando os 10 minutos tiverem decorrido, o módulo deverá fazer upload dos arquivos. Se o upload for bem-sucedido, ele excluirá os arquivos do disco.

### <a name="azure-storage"></a>Armazenamento do Azure

Podemos observar os resultados dos dados de envio do dispositivo folha examinando as contas de armazenamento em que esperamos que os dados sejam roteados.

1. No computador de desenvolvimento, abra o Visual Studio Code.

1. No painel de “ARMAZENAMENTO DO AZURE” na janela explorar, navegue até a árvore para localizar sua conta de armazenamento.

1. Expanda o nó **Contêineres de Blob**.

1. Com base no trabalho que fizemos na parte anterior do tutorial, esperamos que o contêiner **ruldata** deva conter mensagens com RUL. Expanda o nó **ruldata**.

1. Você verá um ou mais arquivos de blob nomeados da seguinte maneira: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`.

1. Clique com o botão direito do mouse em um dos arquivos e escolha **Baixar Blob** para salvar o arquivo em seu computador de desenvolvimento.

1. Em seguida, expanda o nó **uploadturbofanfiles**. No artigo anterior, definimos esta localização como o destino para os arquivos carregados pelo módulo avroFileWriter.

1. Clique com o botão direito do mouse nos arquivos e escolha **Baixar Blob** para salvá-lo em seu computador de desenvolvimento.

### <a name="read-avro-file-contents"></a>Ler conteúdo do arquivo Avro

Incluímos um utilitário de linha de comando simples para ler um arquivo Avro e retornar uma cadeia de caracteres JSON das mensagens no arquivo. Nesta seção, nós o instalaremos e o executaremos.

1. Abra um terminal no Visual Studio Code (**Terminal** > **Novo terminal**).

1. Instale hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

1. Use hubavroreader para ler o arquivo Avro que você baixou de **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

1. Observe que o corpo da mensagem tem a aparência esperada com a identificação do dispositivo e a RUL prevista.

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

1. Execute o mesmo comando passando o arquivo Avro que você baixou de **uploadturbofanfiles**.

1. Conforme esperado, essas mensagens contêm todos os dados de sensor e configurações operacionais da mensagem original. Esses dados podem ser usados para melhorar o modelo de RUL em nosso dispositivo de borda.

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>Limpar os recursos

Se você planeja explorar os recursos usados por este tutorial de ponta a ponta, aguarde até concluir a limpeza dos recursos criados. Caso contrário, use as seguintes etapas para excluí-los:

1. Exclua os grupos de recursos criados para conter a VM do Desenvolvedor, a VM do IoT Edge, o Hub IoT, a conta de armazenamento, o serviço do Workspace do Machine Learning (e os recursos criados: registro de contêiner, Application Insights, cofre de chaves e conta de armazenamento).

1. Exclua o projeto de machine learning no [Azure Notebooks](https://notebooks.azure.com).

1. Se você clonou o repositório localmente, feche as janelas do PowerShell e do VS Code que se referem ao repositório local e, em seguida, exclua o diretório do repositório.

1. Se você criou os certificados localmente, exclua a pasta c:\\edgeCertificates.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, usamos nossa VM de desenvolvimento para simular um sensor de envio de dispositivo folha e os dados operacionais para nosso dispositivo do IoT Edge. Ao examinarmos a operação em tempo real do dispositivo de borda e os arquivos carregados para a conta de armazenamento, validamos que os módulos no dispositivo rotearam, classificaram, persistiram e carregaram os dados.

Para continuar aprendendo sobre as funcionalidades do IoT Edge, experimente este tutorial em seguida:

> [!div class="nextstepaction"]
> [Criar uma hierarquia de dispositivos IoT Edge](tutorial-nested-iot-edge.md?view=iotedge-2020-11&preserve-view=true)