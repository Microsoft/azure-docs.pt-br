---
title: Autenticar dispositivos downstream - Azure IoT Edge | Microsoft Docs
description: Como autenticar dispositivos downstream ou dispositivos de folha no Hub IoT e rotear sua conexão por meio de dispositivos de gateway do Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c799e38092c5983b4ad0e3daea6aae99934c7302
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200894"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autenticar um dispositivo downstream no Hub IoT do Azure

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Em um cenário de gateway transparente, dispositivos downstream (às vezes chamados de dispositivos folha ou dispositivos filho) precisam de identidades no Hub IoT como qualquer outro dispositivo. Este artigo percorre as opções de autenticação de um dispositivo downstream no Hub IoT e demonstra como declarar a conexão de gateway.

Há três etapas gerais para configurar uma conexão de gateway transparente bem-sucedida. Este artigo aborda a segunda etapa:

1. Configure o dispositivo de gateway como um servidor para que os dispositivos downstream possam se conectar com segurança. Configure o gateway para receber mensagens de dispositivos downstream e roteá-los para o destino apropriado. Para essas etapas, consulte [configurar um dispositivo de IOT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md).
2. **Crie uma identidade de dispositivo para o dispositivo downstream para que ele possa autenticar com o Hub IoT. Configure o dispositivo downstream para enviar mensagens por meio do dispositivo de gateway.**
3. Conecte o dispositivo downstream ao dispositivo de gateway e comece a enviar mensagens. Para essas etapas, consulte [conectar um dispositivo downstream a um gateway de Azure IOT Edge](how-to-connect-downstream-device.md).

Os dispositivos downstream podem se autenticar no Hub IoT usando um dos três métodos: chaves simétricas (às vezes chamadas de chaves de acesso compartilhado), certificados autoassinados X.509 ou certificados assinados por AC (autoridade de certificação) X.509. As etapas de autenticação são semelhantes às etapas usadas para configurar qualquer dispositivo não IoT Edge com o Hub IoT, com pequenas diferenças para declarar a relação de gateway.

Não há suporte para o provisionamento automático de dispositivos downstream com o serviço de provisionamento de dispositivos do Hub IoT do Azure (DPS).

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Configurar um dispositivo de IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md).

Se você estiver usando a autenticação X. 509, gerará certificados para o dispositivo downstream. Tenha o mesmo certificado de autoridade de certificação raiz e o script que gera o certificado que você usou para o artigo de gateway transparente disponível para uso novamente.

Este artigo refere-se ao *nome do host do gateway* em vários pontos. O nome do host do gateway é declarado no parâmetro **hostname** do arquivo de configuração no dispositivo IOT Edge gateway. Ele é mencionado na cadeia de conexão do dispositivo downstream. O nome do host do gateway precisa ser resolvido para um endereço IP, usando DNS ou uma entrada de arquivo de host no dispositivo downstream.

## <a name="register-device-with-iot-hub"></a>Registrar dispositivo com o Hub IoT

Escolha como você deseja que seu dispositivo downstream autentique com o Hub IoT:

* [Autenticação de chave simétrica](#symmetric-key-authentication): o Hub IOT cria uma chave que você coloca no dispositivo downstream. Quando o dispositivo é autenticado, o Hub IoT verifica se as duas chaves correspondem. Você não precisa criar certificados adicionais para usar a autenticação de chave simétrica.

  Esse método é mais rápido para começar se você estiver testando gateways em um cenário de desenvolvimento ou teste.

* [Autenticação autoassinada X. 509](#x509-self-signed-authentication): às vezes chamada de autenticação de impressão digital, porque você compartilha a impressão digital do certificado X. 509 do dispositivo com o Hub IOT.

  A autenticação de certificado é recomendada para dispositivos em cenários de produção.

* [Autenticação assinada por AC X. 509](#x509-ca-signed-authentication): carregue o certificado de autoridade de certificação raiz no Hub IOT. Quando os dispositivos apresentam seu certificado X. 509 para autenticação, o Hub IoT verifica se ele pertence a uma cadeia de confiança assinada pelo mesmo certificado de autoridade de certificação raiz.

  A autenticação de certificado é recomendada para dispositivos em cenários de produção.

### <a name="symmetric-key-authentication"></a>Autenticação da chave simétrica

A autenticação de chave simétrica ou a autenticação de chave de acesso compartilhada é a maneira mais simples de autenticar com o Hub IoT. Com a autenticação de chave simétrica, uma chave Base64 é associada à ID do dispositivo IoT no Hub IoT. Você inclui essa chave em seus aplicativos de IoT para que seu dispositivo possa apresentá-lo quando se conectar ao Hub IoT.

Adicione um novo dispositivo IoT em seu hub IoT, usando o portal do Azure, a CLI do Azure ou a extensão de IoT para Visual Studio Code. Lembre-se de que os dispositivos downstream precisam ser identificados no Hub IoT como dispositivos IoT regulares, e não dispositivos IoT Edge.

Ao criar a nova identidade do dispositivo, forneça as seguintes informações:

* Crie uma ID para seu dispositivo.

* Selecione **Chave simétrica** como o tipo de autenticação.

* Selecione **definir um dispositivo pai** e selecione o IOT Edge dispositivo de gateway ao qual esse dispositivo downstream se conectará. Você sempre pode alterar o pai mais tarde.

   ![Criar ID do dispositivo com autenticação de chave simétrica no portal](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

   >[!NOTE]
   >Definir o dispositivo pai usado como uma etapa opcional para dispositivos downstream que usam a autenticação de chave simétrica. No entanto, a partir do IoT Edge versão 1.1.0, cada dispositivo downstream deve ser atribuído a um dispositivo pai.
   >
   >Você pode configurar o Hub de IoT Edge para voltar ao comportamento anterior, definindo a variável de ambiente **AuthenticationMode** como o valor **CloudAndScope**.

Você também pode usar a [extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) para concluir a mesma operação. O exemplo a seguir usa o comando [AZ IOT Hub Device-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) para criar um novo dispositivo IOT com autenticação de chave simétrica e atribuir um dispositivo pai:

```azurecli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Em seguida, [Recuperar e modificar a cadeia de conexão](#retrieve-and-modify-connection-string) para que o dispositivo saiba se conectar por meio de seu gateway.

### <a name="x509-self-signed-authentication"></a>Autenticação autoassinada X. 509

Para a autenticação autoassinada X. 509, às vezes conhecida como autenticação de impressão digital, você precisa criar certificados para serem colocados em seu dispositivo downstream. Esses certificados têm uma impressão digital neles que você compartilha com o Hub IoT para autenticação.

1. Usando seu certificado AC, crie dois certificados de dispositivo (primário e secundário) para o dispositivo downstream.

   Se você não tiver uma autoridade de certificação para criar certificados X. 509, poderá usar os scripts de certificado de demonstração IoT Edge para [criar certificados de dispositivo downstream](how-to-create-test-certificates.md#create-downstream-device-certificates). Siga as etapas para criar certificados autoassinados. Use o mesmo certificado de autoridade de certificação raiz que gerou os certificados para o dispositivo de gateway.

   Se você criar seus próprios certificados, verifique se o nome da entidade do certificado do dispositivo está definido como a ID do dispositivo que você usa ao registrar o dispositivo IoT no Hub IoT do Azure. Essa configuração é necessária para autenticação.

2. Recupere a impressão digital SHA1 (chamada de impressão digital na interface do Hub IoT) de cada certificado, que é uma cadeia hexadecimal de 40 caracteres. Use o comando openssl a seguir para exibir o certificado e encontrar a impressão digital:

   * Windows:

     ```PowerShell
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint
     ```

   * Linux:

     ```Bash
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
     ```

   Execute este comando duas vezes, uma vez para o certificado primário e uma vez para o certificado secundário. Você fornece impressões digitais para ambos os certificados ao registrar um novo dispositivo IoT usando certificados X.509 autoassinados.

3. Navegue até o Hub IoT no portal do Azure e crie uma nova identidade de dispositivo IoT com os seguintes valores:

   * Forneça a **ID do dispositivo** que corresponda ao nome da entidade dos certificados do dispositivo.
   * Selecione **X.509 autoassinado** como o tipo de autenticação.
   * Cole as cadeias de caracteres hexadecimais que você copiou dos certificados primários e secundários de seu dispositivo.
   * Selecione **Definir um dispositivo pai** e escolha o dispositivo de gateway de IoT Edge ao qual esse dispositivo downstream se conectará. Você sempre pode alterar o pai mais tarde.

   ![Criar ID do dispositivo com a autenticação autoassinada X.509 no portal](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Copie os certificados de dispositivo primário e secundário e suas chaves para qualquer local no dispositivo downstream. Também mova uma cópia do certificado AC raiz compartilhado que gerou o certificado do dispositivo de gateway e os certificados de dispositivo downstream.

   Você fará referência a esses arquivos de certificado em todos os aplicativos no dispositivo downstream que se conectam ao Hub IoT. Você pode usar um serviço como [Azure Key Vault](../key-vault/index.yml) ou uma função como [Protocolo de cópia segura](https://www.ssh.com/ssh/scp/) para mover os arquivos de certificado.

5. Dependendo da linguagem preferida, examine exemplos de como os certificados X.509 podem ser referenciados em aplicativos de IoT:

   * C#: [Configurar a segurança de X.509 em seu Hub IoT do Azure](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Você também pode usar a [extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) para concluir a mesma operação de criação de dispositivo. O exemplo a seguir usa o comando [AZ IOT Hub Device-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) para criar um novo dispositivo IOT com a autenticação autoassinada X. 509 e atribui um dispositivo pai:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Em seguida, [Recuperar e modificar a cadeia de conexão](#retrieve-and-modify-connection-string) para que o dispositivo saiba se conectar por meio de seu gateway.

### <a name="x509-ca-signed-authentication"></a>Autenticação assinada por AC X. 509

Para autenticação assinada da AC (autoridade de certificação) X. 509, você precisa de um certificado de autoridade de certificação raiz registrado no Hub IoT que você usa para assinar certificados para seu dispositivo downstream. Qualquer dispositivo que use um certificado que foi emitido pelo certificado AC raiz ou qualquer um de seus certificados intermediários terá permissão para autenticar.

Esta seção se baseia nas instruções detalhadas no artigo do Hub IoT [Configurar a segurança X.509 no seu hub IoT do Azure](../iot-hub/iot-hub-security-x509-get-started.md).

1. Usando seu certificado AC, crie dois certificados de dispositivo (primário e secundário) para o dispositivo downstream.

   Se você não tiver uma autoridade de certificação para criar certificados X. 509, poderá usar os scripts de certificado de demonstração IoT Edge para [criar certificados de dispositivo downstream](how-to-create-test-certificates.md#create-downstream-device-certificates). Siga as etapas para criar certificados assinados por AC. Use o mesmo certificado de autoridade de certificação raiz que gerou os certificados para o dispositivo de gateway.

2. Siga as instruções na seção [Registrar certificados AC X.509 em seu hub IoT](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) na seção *Configurar a segurança X.509 no do Hub IoT do Azure*. Nessa seção, você executa as seguintes etapas:

   1. Carregue um certificado AC raiz. Se você estiver usando os certificados de demonstração, a autoridade de certificação raiz será **\<path> /certs/Azure-IOT-Test-only.root.ca.cert.pem**.

   2. Verifique se você possui esse certificado de autoridade de certificação raiz.

3. Siga as instruções na seção [Criar um dispositivo X.509 para o hub IoT](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) na seção *Configurar a segurança X.509 no seu hub IoT do Azure*. Nessa seção, você executa as seguintes etapas:

   1. Adicione um novo dispositivo. Forneça um nome em minúsculas para **ID do dispositivo** e escolha o tipo de autenticação **CA X.509 assinada**.

   2. Defina um dispositivo pai. Selecione **definir um dispositivo pai** e escolha o IOT Edge dispositivo de gateway que fornecerá a conexão ao Hub IOT.

4. Crie uma cadeia de certificados para seu dispositivo downstream. Use o mesmo certificado AC raiz que você carregou no Hub IoT para fazer essa cadeia. Use a mesma ID de dispositivo em minúsculas que você atribuiu à identidade do dispositivo no portal.

5. Copie o certificado e as chaves do dispositivo para qualquer local no dispositivo downstream. Também mova uma cópia do certificado AC raiz compartilhado que gerou o certificado do dispositivo de gateway e os certificados de dispositivo downstream.

   Você fará referência a esses arquivos em todos os aplicativos no dispositivo downstream que se conectam ao Hub IoT. Você pode usar um serviço como [Azure Key Vault](../key-vault/index.yml) ou uma função como [Protocolo de cópia segura](https://www.ssh.com/ssh/scp/) para mover os arquivos de certificado.

6. Dependendo da linguagem preferida, examine exemplos de como os certificados X.509 podem ser referenciados em aplicativos de IoT:

   * C#: [Configurar a segurança de X.509 em seu Hub IoT do Azure](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Você também pode usar a [extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) para concluir a mesma operação de criação de dispositivo. O exemplo a seguir usa o comando [AZ IOT Hub Device-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) para criar um novo dispositivo IOT com autenticação assinada da AC X. 509 e atribui um dispositivo pai:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Em seguida, [Recuperar e modificar a cadeia de conexão](#retrieve-and-modify-connection-string) para que o dispositivo saiba se conectar por meio de seu gateway.

## <a name="retrieve-and-modify-connection-string"></a>Recuperar e modificar a cadeia de conexão

Depois de criar uma identidade de dispositivo IoT no portal, você pode recuperar suas chaves primárias ou secundárias. Uma dessas chaves precisa ser incluída na cadeia de conexão que os aplicativos usam para se comunicar com o Hub IoT. Para a autenticação de chave simétrica, o Hub IoT fornece a cadeia de conexão totalmente formada nos detalhes do dispositivo para sua conveniência. Você precisa adicionar informações sobre o dispositivo de gateway à cadeia de conexão.

As cadeias de conexão para dispositivos downstream precisam dos seguintes componentes:

* O hub IoT ao qual o dispositivo se conecta: `Hostname={iothub name}.azure-devices.net`
* A ID do dispositivo registrada com o hub: `DeviceID={device ID}`
* O método de autenticação, se os certificados de chave simétrica ou X. 509
  * Se estiver usando a autenticação de chave simétrica, forneça a chave primária ou secundária: `SharedAccessKey={key}`
  * Se estiver usando a autenticação de certificado X. 509, forneça um sinalizador: `x509=true`
* O dispositivo de gateway ao qual o dispositivo se conecta. Forneça o valor do **nome do host** do arquivo de configuração do dispositivo IOT Edge gateway: `GatewayHostName={gateway hostname}`

Uma cadeia de conexão completa é semelhante a:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Ou:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;x509=true;GatewayHostName=myGatewayDevice
```

Graças à relação pai/filho, você pode simplificar a cadeia de conexão chamando o gateway diretamente como o host de conexão. Por exemplo:

```console
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Você usará essa cadeia de conexão modificada no próximo artigo da série de gateway transparente.

## <a name="next-steps"></a>Próximas etapas

Neste ponto, você tem um dispositivo IoT Edge registrado com o Hub IoT e configurado como um gateway transparente. Você também tem um dispositivo downstream registrado com o Hub IoT e apontando para seu dispositivo de gateway.

Em seguida, você precisa configurar seu dispositivo downstream para confiar no dispositivo de gateway e conectar-se a ele com segurança. Continue no próximo artigo da série de gateway transparente, [Conecte um dispositivo downstream a um gateway de Azure IOT Edge](how-to-connect-downstream-device.md).