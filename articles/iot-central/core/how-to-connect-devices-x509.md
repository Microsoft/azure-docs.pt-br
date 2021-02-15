---
title: Conectar dispositivos com certificados X. 509 em um aplicativo de IoT Central do Azure
description: Como conectar dispositivos com certificados X. 509 usando Node.js SDK do dispositivo para IoT Central aplicativo
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: cf0db71600c9350b4d70e6375f509a6e88709f70
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378325"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>Como conectar dispositivos com certificados X. 509 usando Node.js SDK do dispositivo para IoT Central aplicativo

O IoT Central dá suporte a SAS (assinaturas de acesso compartilhado) e a certificados X. 509 para proteger a comunicação entre um dispositivo e seu aplicativo. O tutorial [criar e conectar um aplicativo cliente ao seu aplicativo IOT central do Azure](./tutorial-connect-device.md) usa SAS. Neste artigo, você aprenderá a modificar o exemplo de código para usar X. 509.  Os certificados X.509 são recomendados para ambientes de produção. Para obter mais informações, consulte [conectar-se ao Azure IOT central](./concepts-get-connected.md).

Este artigo mostra duas maneiras de usar os registros X. 509- [Group](how-to-connect-devices-x509.md#use-a-group-enrollment) normalmente usados em um ambiente de produção e [registros individuais](how-to-connect-devices-x509.md#use-an-individual-enrollment) úteis para teste.

Os trechos de código neste artigo usam JavaScript. Para obter exemplos de código em outros idiomas, consulte:

- [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_ll_client_x509_sample)
- [C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/X509DeviceCertWithChainSample)
- [Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
- [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)

## <a name="prerequisites"></a>Pré-requisitos

- Conclusão de [criar e conectar um aplicativo cliente ao tutorial do aplicativo de IOT central do Azure (JavaScript)](./tutorial-connect-device.md) .
- [Git](https://git-scm.com/download/).
- Baixe e instale o [OpenSSL](https://www.openssl.org/). Se você estiver usando o Windows, poderá usar os binários da [página OpenSSL em sourceforge](https://sourceforge.net/projects/openssl/).

## <a name="use-a-group-enrollment"></a>Usar um registro de grupo

Use certificados X. 509 com um registro de grupo em um ambiente de produção. Em um registro de grupo, você adiciona um certificado X. 509 intermediário ou raiz ao seu aplicativo IoT Central. Dispositivos com certificados folha derivados do certificado raiz ou intermediário podem se conectar ao seu aplicativo.

## <a name="generate-root-and-device-cert"></a>Gerar certificado de dispositivo e raiz

Nesta seção, você usa um certificado X. 509 para conectar um dispositivo com um certificado derivado do certificado do grupo de registro, que pode se conectar ao seu aplicativo IoT Central.

> [!WARNING]
> Essa forma de gerar certificados X. 509 é apenas para teste. Para um ambiente de produção, você deve usar seu mecanismo oficial e seguro para a geração de certificado.

1. Abra um prompt de comando. Clone o repositório GitHub para os scripts de geração de certificado:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

1. Navegue até o script gerador de certificado e instale os pacotes necessários:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. Crie um certificado raiz e, em seguida, derive um certificado de dispositivo executando o script:

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device sample-device-01 mytestrootcert
    ```

    > [!TIP]
    > Uma identificação do dispositivo pode conter letras, números e o caractere `-`.

Esses comandos produzem três arquivos cada para a raiz e o certificado do dispositivo

filename | conteúdos
-------- | --------
\<name\>_cert. pem | A parte pública do certificado X509
\<name\>_key. pem | A chave privada para o certificado X509
\<name\>_fullchain. pem | O conjunto de chaves inteiro para o certificado X509.

## <a name="create-a-group-enrollment"></a>Criar um registro de grupo

1. Abra o aplicativo IoT Central e navegue até **Administração**  no painel esquerdo e selecione **conexão do dispositivo**.

1. Selecione **+ Criar grupo de registro** e crie um novo grupo de registro chamado _MyX509Group_ com um tipo de atestado de **certificados (X. 509)**.

1. Abra o grupo de registro que você criou e selecione **gerenciar primário**.

1. Selecione a opção arquivo e carregue o arquivo de certificado raiz chamado _mytestrootcert_cert. pem_ que você gerou anteriormente:

    ![Upload de certificado](./media/how-to-connect-devices-x509/certificate-upload.png)

1. Para concluir a verificação, gere o código de verificação, copie-o e, em seguida, use-o para criar um certificado de verificação X. 509 no prompt de comando:

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. Carregue o certificado de verificação assinado _verification_cert. pem_ para concluir a verificação:

    ![Certificado verificado](./media/how-to-connect-devices-x509/verified.png)

Agora você pode conectar dispositivos que têm um certificado X. 509 derivado deste certificado raiz primário.

Depois de salvar o grupo de registro, anote o escopo da ID.

## <a name="run-sample-device-code"></a>Executar código de dispositivo de exemplo

1. Copie os arquivos **sampleDevice01_key. pem** e **sampleDevice01_cert. pem** para a pasta _Azure-IOT-SDK-node/Device/Samples/pnp_ que contém o aplicativo **simple_thermostat.js** . Você usou esse aplicativo quando concluiu o [tutorial conectar um dispositivo (JavaScript)](./tutorial-connect-device.md).

1. Navegue até a pasta _Azure-IOT-SDK-node/Device/Samples/PNP_ que contém o aplicativo **simple_thermostat.js** e execute o seguinte comando para instalar o pacote X. 509:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. Abra o arquivo **simple_thermostat.js** em um editor de texto.

1. Edite as `require` instruções para incluir o seguinte:

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. Adicione as quatro linhas a seguir à seção "informações de conexão do DPS" para inicializar a `deviceCert` variável:

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. Edite a `provisionDevice` função que cria o cliente substituindo a primeira linha pelo seguinte:

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. Na mesma função, modifique a linha que define a `deviceConnectionString` variável da seguinte maneira:

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. Na `main` função, adicione a seguinte linha após a linha que chama `Client.fromConnectionString` :

    ```javascript
    client.setOptions(deviceCert);
    ```

1. No ambiente do Shell, defina as duas variáveis de ambiente a seguir:

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=sampleDevice01_key.pem
    ```

    > [!TIP]
    > Você define as outras variáveis de ambiente necessárias quando concluiu o tutorial [criar e conectar um aplicativo cliente ao seu aplicativo do Azure IOT central](./tutorial-connect-device.md) .

1. Execute o script e verifique se o dispositivo foi provisionado com êxito:

    ```cmd/sh
    node simple_thermostat.js
    ```

    Você também pode verificar se a telemetria aparece no painel.

    ![Verificar telemetria do dispositivo](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>Usar um registro individual

Use certificados X. 509 com um registro individual para testar seu dispositivo e sua solução. Em um registro individual, não há nenhum certificado X. 509 raiz ou intermediário no aplicativo IoT Central. Os dispositivos usam um certificado X. 509 autoassinado para se conectar ao seu aplicativo.

## <a name="generate-self-signed-device-cert"></a>Gerar certificado de dispositivo autoassinado

Nesta seção, você usa um certificado X. 509 autoassinado para conectar dispositivos para registro individual, que são usados para registrar um único dispositivo. Os certificados autoassinados são apenas para teste.

Crie um certificado de dispositivo X. 509 autoassinado executando o script. Certifique-se de usar apenas caracteres alfanuméricos minúsculos e hifens para o nome do certificado:

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>Criar registro individual

1. No aplicativo IoT Central do Azure, selecione **dispositivos** e crie um novo dispositivo com a **ID do dispositivo** como _mytestselfcertprimary_ do modelo de dispositivo termostato. Anote o **escopo da ID**, você o usará mais tarde.

1. Abra o dispositivo que você criou e selecione **conectar**.

1. Selecione **registros individuais** como o **método Connect** e **certificados (X. 509)** como mecanismo:

    ![Registro individual](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. Selecione a opção arquivo em primário e carregue o arquivo de certificado chamado _mytestselfcertprimary_cert. pem_ que você gerou anteriormente.

1. Selecione a opção arquivo para o certificado secundário e carregue o arquivo de certificado chamado _mytestselfcertsecondary_cert. PEM._ Em seguida, selecione **salvar**:

    ![Carregamento de certificado de registro individual](./media/how-to-connect-devices-x509/individual-enrollment.png)

O dispositivo agora é provisionado com o certificado X. 509.

## <a name="run-a-sample-individual-enrollment-device"></a>Executar um dispositivo de registro individual de exemplo

1. Copie os arquivos _mytestselfcertprimary_key. pem_ e _mytestselfcertprimary_cert. pem_ para a pasta _Azure-IOT-SDK-node/Device/Samples/pnp_ que contém o aplicativo **simple_thermostat.js** . Você usou esse aplicativo quando concluiu o [tutorial conectar um dispositivo (JavaScript)](./tutorial-connect-device.md).

1. Modifique as variáveis de ambiente usadas no exemplo acima da seguinte maneira:

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. Execute o script e verifique se o dispositivo foi provisionado com êxito:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Você também pode verificar se a telemetria aparece no painel.

    ![Registro individual de telemetria](./media/how-to-connect-devices-x509/telemetry-primary.png)

Você também pode repetir as etapas acima para o certificado _mytestselfcertsecondary_ .

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como conectar dispositivos usando certificados X. 509, a próxima etapa sugerida é aprender a [monitorar a conectividade do dispositivo usando o CLI do Azure](howto-monitor-devices-azure-cli.md)
