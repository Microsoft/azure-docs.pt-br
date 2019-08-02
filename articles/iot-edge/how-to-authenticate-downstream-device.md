---
title: Autenticar dispositivos downstream-Azure IoT Edge | Microsoft Docs
description: Como autenticar dispositivos downstream ou dispositivos de folha no Hub IoT e rotear sua conexão por meio de dispositivos Azure IoT Edge gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4d6c7665d281ff7c27fd8b61537804b6803b3b43
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360157"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autenticar um dispositivo downstream no Hub IoT do Azure

Em um cenário de gateway transparente, dispositivos downstream (às vezes chamados de dispositivos folha ou dispositivos filho) precisam de identidades no Hub IoT como qualquer outro dispositivo. Este artigo percorre as opções de autenticação de um dispositivo downstream no Hub IoT e demonstra como declarar a conexão de gateway.

Há três etapas gerais para configurar uma conexão de gateway transparente bem-sucedida. Este artigo aborda a segunda etapa:

1. O dispositivo de gateway precisa ser capaz de se conectar com segurança a dispositivos downstream, receber comunicações de dispositivos downstream e rotear mensagens para o destino adequado. Para obter mais informações, consulte [configurar um dispositivo de IOT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md).
2. **O dispositivo downstream precisa ter uma identidade de dispositivo para ser capaz de autenticar com o Hub IoT e saber se comunicar por meio de seu dispositivo de gateway.**
3. O dispositivo downstream precisa ser capaz de se conectar com segurança ao seu dispositivo de gateway. Para obter mais informações, consulte [Cnectar um dispositivo downstream a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md).

Os dispositivos downstream podem se autenticar no Hub IoT usando um dos três métodos: chaves simétricas (às vezes chamadas de chaves de acesso compartilhado), X. 509 certificados autoassinados ou certificados assinados por AC (autoridade de certificação) X. 509. As etapas de autenticação são semelhantes às etapas usadas para configurar qualquer dispositivo não IoT Edge com o Hub IoT, com pequenas diferenças para declarar a relação de gateway.

As etapas neste artigo mostram o provisionamento manual de dispositivos, não o provisionamento automático com o serviço de provisionamento de dispositivos no Hub IoT do Azure. 

## <a name="symmetric-key-authentication"></a>Autenticação de chave simétrica

A autenticação de chave simétrica ou a autenticação de chave de acesso compartilhado é a maneira mais simples de autenticar com o Hub IoT. Com a autenticação de chave simétrica, uma chave Base64 é associada à ID do dispositivo IoT no Hub IoT. Você inclui essa chave em seus aplicativos de IoT para que seu dispositivo possa apresentá-lo quando se conectar ao Hub IoT. 

### <a name="create-the-device-identity"></a>Criar a identidade do dispositivo 

Adicione um novo dispositivo IoT em seu hub IoT, usando o portal do Azure, CLI do Azure ou a extensão de IoT para Visual Studio Code. Lembre-se de que os dispositivos downstream precisam ser identificados no Hub IoT como dispositivo IoT regular, não IoT Edge dispositivos. 

Ao criar a nova identidade do dispositivo, forneça as seguintes informações: 

* Crie uma ID para seu dispositivo.

* Selecione **chave simétrica** como o tipo de autenticação. 

* Opcionalmente, escolha **definir um dispositivo pai** e selecione o IOT Edge dispositivo de gateway ao qual esse dispositivo downstream se conectará. Essa etapa é opcional para a autenticação de chave simétrica, mas é recomendável porque a configuração de um dispositivo pai habilita [recursos offline](offline-capabilities.md) para seu dispositivo downstream. Você sempre pode atualizar os detalhes do dispositivo para adicionar ou alterar o pai mais tarde. 

   ![Criar ID do dispositivo com autenticação de chave simétrica no portal](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Você pode usar a [extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) para concluir a mesma operação. O exemplo a seguir cria um novo dispositivo IoT com autenticação de chave simétrica e atribui um dispositivo pai: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

Para obter mais informações sobre os comandos de CLI do Azure para a criação de dispositivos e o gerenciamento de pai/filho, consulte o conteúdo de referência para os comandos de [identidade de dispositivo do Hub IOT para AZ](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) .

### <a name="connect-to-iot-hub-through-a-gateway"></a>Conectar-se ao Hub IoT por meio de um gateway

O mesmo processo é usado para autenticar dispositivos IoT regulares no Hub IoT com chaves simétricas também se aplica a dispositivos downstream. A única diferença é que você precisa adicionar um ponteiro ao dispositivo de gateway para rotear a conexão ou, em cenários offline, para lidar com a autenticação em nome do Hub IoT. 

Para a autenticação de chave simétrica, não há etapas adicionais que você precise executar em seu dispositivo para que ele se autentique no Hub IoT. Você ainda precisa dos certificados em vigor para que seu dispositivo downstream possa se conectar ao seu dispositivo de gateway, conforme descrito em [conectar um dispositivo downstream a um gateway de Azure IOT Edge](how-to-connect-downstream-device.md).

Depois de criar uma identidade de dispositivo IoT no portal, você pode recuperar suas chaves primárias ou secundárias. Uma dessas chaves precisa ser incluída na cadeia de conexão que você inclui em qualquer aplicativo que se comunica com o Hub IoT. Para a autenticação de chave simétrica, o Hub IoT fornece a cadeia de conexão totalmente formada nos detalhes do dispositivo para sua conveniência. Você precisa adicionar informações extras sobre o dispositivo de gateway à cadeia de conexão. 

As cadeias de conexão de chave simétrica para dispositivos downstream precisam dos seguintes componentes: 

* O Hub IoT ao qual o dispositivo se conecta:`Hostname={iothub name}.azure-devices.net`
* A ID do dispositivo registrada com o Hub:`DeviceID={device ID}`
* A chave primária ou secundária:`SharedAccessKey={key}`
* O dispositivo de gateway ao qual o dispositivo se conecta. Forneça o valor do **nome do host** do arquivo config. YAML do dispositivo de gateway de IOT Edge:`GatewayHostName={gateway hostname}`

Juntos, uma cadeia de conexão completa é semelhante a:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Se você estabeleceu uma relação pai/filho para esse dispositivo downstream, você pode simplificar a cadeia de conexão chamando o gateway diretamente como o host de conexão. Por exemplo: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>Autenticação X. 509 

Há duas maneiras de autenticar um dispositivo IoT usando certificados X. 509. Independentemente da maneira que você escolher autenticar, as etapas para conectar seu dispositivo ao Hub IoT são as mesmas. Escolha certificados autoassinados ou assinados por AC para autenticação e continue a aprender a se conectar ao Hub IoT. 

Para obter mais informações sobre como o Hub IoT usa a autenticação X. 509, consulte os seguintes artigos: 
* [Autenticação de dispositivo usando certificados de autoridade de certificação X. 509](../iot-hub/iot-hub-x509ca-overview.md)
* [Compreensão conceitual dos certificados de autoridade de certificação X. 509 no setor IoT](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>Criar a identidade do dispositivo com os certificados autoassinados X. 509

Para a autenticação autoassinada X. 509, às vezes conhecida como autenticação de impressão digital, você precisa criar novos certificados para serem colocados em seu dispositivo IoT. Esses certificados têm uma impressão digital neles que você compartilha com o Hub IoT para autenticação. 

A maneira mais fácil de testar esse cenário é usar o mesmo computador usado para criar certificados em [configurar um dispositivo de IOT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md). Esse computador já deve estar configurado com a ferramenta correta, o certificado de autoridade de certificação raiz e o certificado de autoridade de certificação intermediário para criar os certificados de dispositivo IoT. Você pode copiar os certificados finais e suas chaves privadas para o dispositivo downstream posteriormente. Seguindo as etapas no artigo do gateway, você configura o OpenSSL em seu computador e, em seguida, clonou o repositório de IoT Edge para acessar os scripts de criação de certificado. Em seguida, você criou um diretório de trabalho que chamamos  **\<de WRKDIR >** para manter os certificados. Os certificados padrão destinam-se ao desenvolvimento e ao teste, portanto, somente os últimos 30 dias. Você deve ter criado um certificado de autoridade de certificação raiz e um certificado intermediário. 

1. Navegue até seu diretório de trabalho em uma janela do bash ou do PowerShell. 

2. Crie dois certificados (primário e secundário) para o dispositivo downstream. Forneça o nome do dispositivo e o rótulo primário ou secundário. Essas informações são usadas para nomear os arquivos para que você possa manter o controle de certificados para vários dispositivos. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. Recupere a impressão digital SHA1 (chamada de impressão digital na interface do Hub IoT) de cada certificado, que é uma cadeia de caracteres hexadecimal 40. Use o comando openssl a seguir para exibir o certificado e encontrar a impressão digital:

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Navegue até o Hub IoT no portal do Azure e crie uma nova identidade de dispositivo IoT com os seguintes valores: 

   * Selecione **X. 509 autoassinado** como o tipo de autenticação.
   * Cole as cadeias de caracteres hexadecimais que você copiou dos certificados primários e secundários de seu dispositivo.
   * Selecione **definir um dispositivo pai** e escolha o IOT Edge dispositivo de gateway ao qual este dispositivo downstream se conectará. Um dispositivo pai é necessário para a autenticação X. 509 de um dispositivo downstream. 

   ![Criar ID do dispositivo com a autenticação autoassinada X. 509 no portal](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. Copie os seguintes arquivos para qualquer diretório em seu dispositivo downstream:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   Você fará referência a esses arquivos nos aplicativos de dispositivo folha que se conectam ao Hub IoT. Você pode usar um serviço como [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como [Protocol de cópia segura](https://www.ssh.com/ssh/scp/) para mover os arquivos de certificado.

Você pode usar a [extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) para concluir a mesma operação de criação de dispositivo. O exemplo a seguir cria um novo dispositivo IoT com a autenticação autoassinada X. 509 e atribui um dispositivo pai: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Para obter mais informações sobre os comandos de CLI do Azure para a criação de dispositivos, a geração de certificados e o gerenciamento pai e filho, consulte o conteúdo de referência para comandos de [identidade de dispositivo do Hub IOT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) .

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>Criar a identidade do dispositivo com certificados assinados por AC X. 509

Para autenticação assinada da AC (autoridade de certificação) X. 509, você precisa de um certificado de autoridade de certificação raiz registrado no Hub IoT que você usa para assinar certificados para seu dispositivo IoT. Qualquer dispositivo que use um certificado que foi emitido pelo certificado de autoridade de certificação raiz ou qualquer um de seus certificados intermediários terá permissão para autenticar. 

Esta seção se baseia nas instruções detalhadas no artigo do Hub IoT [Configurar a segurança X. 509 no Hub IOT do Azure](../iot-hub/iot-hub-security-x509-get-started.md). Siga as etapas nesta seção para saber quais valores usar para configurar um dispositivo downstream que se conecta por meio de um gateway. 

A maneira mais fácil de testar esse cenário é usar o mesmo computador usado para criar certificados em [configurar um dispositivo de IOT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md). Esse computador já deve estar configurado com a ferramenta correta, o certificado de autoridade de certificação raiz e o certificado de autoridade de certificação intermediário para criar os certificados de dispositivo IoT. Você pode copiar os certificados finais e suas chaves privadas para o dispositivo downstream posteriormente. Seguindo as etapas no artigo do gateway, você configura o OpenSSL em seu computador e, em seguida, clonou o repositório de IoT Edge para acessar os scripts de criação de certificado. Em seguida, você criou um diretório de trabalho que chamamos  **\<de WRKDIR >** para manter os certificados. Os certificados padrão destinam-se ao desenvolvimento e ao teste, portanto, somente os últimos 30 dias. Você deve ter criado um certificado de autoridade de certificação raiz e um certificado intermediário. 

1. Siga as instruções na seção [registrar certificados de autoridade de certificação x. 509 em seu hub IOT](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) de *Configurar a segurança x. 509 em seu hub IOT do Azure*. Nessa seção, você executa as seguintes etapas: 

   1. Carregue um certificado de autoridade de certificação raiz. Se você estiver usando os certificados que criou no artigo gateway Transparent, carregue  **\<WRKDIR >/certs/Azure-IOT-Test-only.root.ca.cert.pem** como o arquivo de certificado raiz. 
   2. Verifique se você possui esse certificado de autoridade de certificação raiz. Você pode verificar a posse com as ferramentas de \<certificado no WRKDIR >. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. Siga as instruções na seção [criar um dispositivo X. 509 para o Hub IOT](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) de *Configurar a segurança x. 509 em seu hub IOT do Azure*. Nessa seção, você executa as seguintes etapas: 

   1. Adicionar um novo dispositivo. Forneça um nome em minúsculas para a **ID do dispositivo**e escolha o tipo de autenticação **X. 509 AC assinado**. 
   2. Definir um dispositivo pai. Para dispositivos downstream, selecione **definir um dispositivo pai** e escolha o IOT Edge dispositivo de gateway que fornecerá a conexão ao Hub IOT. 

3. Crie uma cadeia de certificados para seu dispositivo downstream. Use o mesmo certificado de autoridade de certificação raiz que você carregou no Hub IoT para fazer essa cadeia. Use a mesma ID de dispositivo em minúsculas que você atribuiu à identidade do dispositivo no Portal.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. Copie os seguintes arquivos para qualquer diretório em seu dispositivo downstream: 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   Você fará referência a esses arquivos nos aplicativos de dispositivo folha que se conectam ao Hub IoT. Você pode usar um serviço como [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como [Protocol de cópia segura](https://www.ssh.com/ssh/scp/) para mover os arquivos de certificado.

Você pode usar a [extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) para concluir a mesma operação de criação de dispositivo. O exemplo a seguir cria um novo dispositivo IoT com autenticação assinada da AC X. 509 e atribui um dispositivo pai: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Para obter mais informações sobre os comandos de CLI do Azure para a criação de dispositivos e o gerenciamento de pai/filho, consulte o conteúdo de referência para os comandos de [identidade de dispositivo do Hub IOT para AZ](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) .


### <a name="connect-to-iot-hub-through-a-gateway"></a>Conectar-se ao Hub IoT por meio de um gateway

Cada SDK do Azure IoT manipula a autenticação X. 509 um pouco diferente. No entanto, o mesmo processo é usado para autenticar dispositivos IoT regulares no Hub IoT com certificados X. 509 também se aplicam a dispositivos downstream. A única diferença é que você precisa adicionar um ponteiro ao dispositivo de gateway para rotear a conexão ou, em cenários offline, para lidar com a autenticação em nome do Hub IoT. Em geral, você pode seguir as mesmas etapas de autenticação X. 509 para todos os dispositivos do Hub IoT e, em seguida, simplesmente substituir o valor do **nome do host** na cadeia de conexão para ser o nome do host do seu dispositivo de gateway. 

As seções a seguir mostram alguns exemplos de diferentes idiomas do SDK. 

>[!IMPORTANT]
>Os exemplos a seguir demonstram como os SDKs do Hub IoT usam certificados para autenticar dispositivos. Em uma implantação de produção, você deve armazenar todos os segredos, como chaves privadas ou SAS, em um módulo de hardware seguro (HSM). 

#### <a name="net"></a>.NET

Para obter um exemplo de C# um programa que se autentica no Hub IOT com certificados X. 509, consulte [Configurar a segurança x. 509 no Hub IOT do Azure](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates). Algumas das principais linhas desse exemplo estão incluídas aqui para demonstrar o processo de autenticação.

Ao declarar o nome do host para sua instância do DeviceClient, use o nome do host do dispositivo IoT Edge gateway. O nome do host pode ser encontrado no arquivo config. YAML do dispositivo de gateway. 

Se você estiver usando os certificados de teste fornecidos pelo repositório Git do IoT Edge, a chave para os certificados será **1234**.

```csharp
try
{
    var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
    var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
    var deviceClient = DeviceClient.Create("<gateway hostname>", auth, TransportType.Amqp_Tcp_Only);

    if (deviceClient == null)
    {
        Console.WriteLine("Failed to create DeviceClient!");
    }
    else
    {
        Console.WriteLine("Successfully created DeviceClient!");
        SendEvent(deviceClient).Wait();
    }

    Console.WriteLine("Exiting...\n");
}
catch (Exception ex)
{
    Console.WriteLine("Error in sample: {0}", ex.Message);
}
```

#### <a name="c"></a>C

Para obter um exemplo de um programa do C Autenticando no Hub IoT com certificados X. 509, consulte a amostra [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) do SDK do IOT do c. Algumas das principais linhas desse exemplo estão incluídas aqui para demonstrar o processo de autenticação.

Ao definir a cadeia de conexão para o dispositivo downstream, use o nome do host do dispositivo IoT Edge gateway para o parâmetro **hostname** . O nome do host pode ser encontrado no arquivo config. YAML do dispositivo de gateway. 

```C
// If your downstream device uses X.509 authentication (self signed or X.509 CA) then
// resulting connection string should look like the following:
// "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
static const char* connectionString = "[Downstream device IoT Edge connection string]";

// Path to the Edge "owner" root CA certificate
static const char* edge_ca_cert_path = "[Path to root CA certificate]";

// When the downstream device uses X.509 authentication, a certificate and key 
// in PRM format must be provided.
static const char * x509_device_cert_path = "[Path to primary or secondary device cert]";
static const char * x509_device_key_path = "[Path to primary or secondary device key]";

int main(void)
{
    // Create the iothub handle here
    device_handle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, protocol);

    // Provide the Azure IoT device client with the same root
    // X509 CA certificate that was used to set up the IoT Edge gateway runtime
    if (edge_ca_cert_path != NULL)
    {
        cert_string = obtain_edge_ca_certificate();
        (void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
    }

    if ((x509_device_cert_path != NULL) && (x509_device_key_path != NULL))
    {
        const char *x509certificate = obtain_file_contents(x509_device_cert_path);
        const char *x509privatekey = obtain_file_contents(x509_device_key_path);
        if ((IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_CERT, x509certificate) != IOTHUB_CLIENT_OK) ||
            (IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_PRIVATE_KEY, x509privatekey) != IOTHUB_CLIENT_OK)
            )
        {
            printf("failure to set options for x509, aborting\r\n");
            exit(1);
        }
    }
}
```

#### <a name="nodejs"></a>Node.js

Para obter um exemplo de um programa node. js Autenticando no Hub IoT com certificados X. 509, consulte o exemplo de [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) do SDK do IOT do node. js. Algumas das principais linhas desse exemplo estão incluídas aqui para demonstrar o processo de autenticação.

Ao definir a cadeia de conexão para o dispositivo downstream, use o nome do host do dispositivo IoT Edge gateway para o parâmetro **hostname** . O nome do host pode ser encontrado no arquivo config. YAML do dispositivo de gateway. 

Se você estiver usando os certificados de teste fornecidos pelo repositório Git do IoT Edge, a chave para os certificados será **1234**.

```node
// String containing Hostname and Device Id in the following format:
//  "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
var connectionString = '<DEVICE CONNECTION STRING WITH x509=true>';
var certFile = '<PATH-TO-CERTIFICATE-FILE>';
var keyFile = '<PATH-TO-KEY-FILE>';
var passphrase = '<KEY PASSPHRASE IF ANY>';

// fromConnectionString must specify a transport constructor, coming from any transport package.
var client = Client.fromConnectionString(connectionString, Protocol);

var options = {
   cert : fs.readFileSync(certFile, 'utf-8').toString(),
   key : fs.readFileSync(keyFile, 'utf-8').toString(),
   passphrase: passphrase
 };

// Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client transport to use x509 when connecting to IoT Hub
client.setOptions(options);
```

#### <a name="python"></a>Python

Para obter um exemplo de um programa Python Autenticando no Hub IoT com certificados X. 509, consulte o exemplo [iothub_client_sample_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) do Java IOT SDK. Algumas das principais linhas desse exemplo estão incluídas aqui para demonstrar o processo de autenticação.

Ao definir a cadeia de conexão para o dispositivo downstream, use o nome do host do dispositivo IoT Edge gateway para o parâmetro **hostname** . O nome do host pode ser encontrado no arquivo config. YAML do dispositivo de gateway. 

```python
# String containing Hostname, Device Id in the format:
# "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
CONNECTION_STRING = "[Device Connection String]"

X509_CERTIFICATE = (
    "-----BEGIN CERTIFICATE-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXX""\n"
    "-----END CERTIFICATE-----"
)

X509_PRIVATEKEY = (
    "-----BEGIN RSA PRIVATE KEY-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    "-----END RSA PRIVATE KEY-----"
)


def iothub_client_init():
    # prepare iothub client
    client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    # this brings in x509 privateKey and certificate
    client.set_option("x509certificate", X509_CERTIFICATE)
    client.set_option("x509privatekey", X509_PRIVATEKEY)

    return client
```

#### <a name="java"></a>Java

Para obter um exemplo de uma autenticação de programa Java no Hub IoT com certificados X. 509, consulte o exemplo [SendEventX509. java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) do SDK do Java IOT. Algumas das principais linhas desse exemplo estão incluídas aqui para demonstrar o processo de autenticação.

Ao definir a cadeia de conexão para o dispositivo downstream, use o nome do host do dispositivo IoT Edge gateway para o parâmetro **hostname** . O nome do host pode ser encontrado no arquivo config. YAML do dispositivo de gateway. 

```java
//PEM encoded representation of the public key certificate
private static String publicKeyCertificateString =
    "-----BEGIN CERTIFICATE-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END CERTIFICATE-----\n";

//PEM encoded representation of the private key
private static String privateKeyString =
    "-----BEGIN EC PRIVATE KEY-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END EC PRIVATE KEY-----\n";

DeviceClient client = new DeviceClient(connectionString, protocol, publicKeyCertificateString, false, privateKeyString, false);
```

## <a name="next-steps"></a>Próximas etapas

Ao concluir este artigo, você deve ter um dispositivo IoT Edge funcionando como um gateway transparente e um dispositivo downstream registrado com um hub IoT. Em seguida, você precisa configurar seus dispositivos downstream para confiar no dispositivo de gateway e enviar mensagens para ele. Para obter mais informações, consulte [Cnectar um dispositivo downstream a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md).
