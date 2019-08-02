---
title: Conectar dispositivos downstream – Azure IoT Edge | Microsoft Docs
description: Como configurar dispositivos downstream ou de folha para se conectar a dispositivos Azure IoT Edge gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4e13075eabcdb482616f1e500fd739ca58a39003
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501219"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Conecte um dispositivo downstream a um gateway do Azure IoT Edge

Este artigo fornece instruções para estabelecer uma conexão confiável entre dispositivos downstream e IoT Edge gateways transparentes. Em um cenário de gateway transparente, um ou mais dispositivos podem passar suas mensagens por meio de um único dispositivo de gateway que mantém a conexão com o Hub IoT. Um dispositivo downstream pode ser qualquer aplicativo ou plataforma que tenha uma identidade criada com o serviço de nuvem do [Hub IoT do Azure](https://docs.microsoft.com/azure/iot-hub). Em muitos casos, esses aplicativos usam o [SDK do dispositivo IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md). Um dispositivo downstream poderia até ser um aplicativo em execução no próprio dispositivo de gateway de IoT Edge. 

Há três etapas gerais para configurar uma conexão de gateway transparente bem-sucedida. Este artigo aborda a terceira etapa:

1. O dispositivo de gateway precisa conectar-se com segurança a dispositivos downstream, receber comunicações de dispositivos downstream e rotear mensagens para o destino apropriado. Para obter mais informações, consulte [configurar um dispositivo de IOT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md).
2. O dispositivo downstream precisa de uma identidade de dispositivo para ser capaz de se autenticar no Hub IoT e conhecer a comunicação por meio de seu dispositivo de gateway. Para obter mais informações, consulte [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).
3. **O dispositivo downstream precisa ser capaz de se conectar com segurança ao seu dispositivo de gateway.**

Este artigo identifica problemas comuns com conexões de dispositivos downstream e orienta você na configuração de seus dispositivos de recebimento de dados por meio de: 

* Explicando a segurança do protocolo TLS e os fundamentos do certificado. 
* Explicando como as bibliotecas TLS funcionam em diferentes sistemas operacionais e como cada sistema operacional lida com certificados.
* Explicando passo a passo exemplos de IoT do Azure em vários idiomas para ajudar você a começar. 

Neste artigo, os termos *gateway* e *gateway IoT Edge* se referem a um dispositivo IoT Edge configurado como um gateway transparente. 

## <a name="prepare-a-downstream-device"></a>Preparar um dispositivo downstream

Um dispositivo downstream pode ser qualquer aplicativo ou plataforma que tenha uma identidade criada com o serviço de nuvem do [Hub IoT do Azure](https://docs.microsoft.com/azure/iot-hub). Em muitos casos, esses aplicativos usam o [SDK do dispositivo IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md). Um dispositivo downstream poderia até ser um aplicativo em execução no próprio dispositivo de gateway de IoT Edge. No entanto, outro dispositivo de IoT Edge não pode ser downstream de um gateway de IoT Edge. 

>[!NOTE]
>Os dispositivos IoT que têm identidades registradas no Hub IoT podem usar o [módulo gêmeos](/iot-hub/iot-hub-devguide-module-twins.md) para isolar diferentes processos, hardware ou funções em um único dispositivo. IoT Edge gateways dão suporte a conexões de módulo downstream usando a autenticação de chave simétrica, mas não a autenticação de certificado X. 509. 

Para conectar um dispositivo downstream a um gateway IoT Edge, você precisa de duas coisas:

* Um dispositivo ou aplicativo configurado com uma cadeia de conexão de dispositivo Hub IoT anexada com informações para conectá-lo ao gateway. 

    Esta etapa é explicada em [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).

* O dispositivo ou aplicativo precisa confiar no certificado de **autoridade de certificação raiz** do gateway para validar as conexões TLS com o dispositivo de gateway. 

    Esta etapa é explicada em detalhes no restante deste artigo. Essa etapa pode ser executada de duas maneiras: Instalando o certificado de autoridade de certificação no repositório de certificados do sistema operacional ou (para determinados idiomas) referenciando o certificado em aplicativos usando os SDKs do IoT do Azure.

## <a name="tls-and-certificate-fundamentals"></a>Conceitos básicos TLS e o certificado

O desafio de conectar com segurança dispositivos downstream ao IoT Edge é como qualquer outra comunicação cliente/servidor segura que ocorre na Internet. Um cliente e um servidor se comunicam com segurança pela Internet usando [ segurança do protocolo TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security). O TLS é construído usando as construções [padrão de infraestrutura de chave pública (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) chamadas de certificados. O TLS é uma especificação bastante envolvida e aborda uma ampla variedade de tópicos relacionados à proteção de dois pontos de extremidade. Esta seção resume os conceitos relevantes para você conectar dispositivos com segurança a um IoT Edge gateway.

Quando um cliente se conecta a um servidor, o servidor apresenta uma cadeia de certificados, chamada de *cadeia de certificados do servidor*. Uma cadeia de certificados normalmente inclui um certificado de autoridade de certificação raiz (AC), um ou mais certificados AC intermediários e, finalmente, o próprio certificado do servidor. Um cliente estabelece confiança com um servidor, verificando criptograficamente toda a cadeia de certificados do servidor. Essa validação de cliente da cadeia de certificados de servidor é chamada *validação de cadeia de servidores*. O cliente desafia criptograficamente o serviço a provar a posse da chave privada associada ao certificado do servidor em um processo chamado *prova de posse*. A combinação de validação de cadeia de servidores e prova de posse é chamada de *autenticação de servidor*. Para validar uma cadeia de certificados de servidor, um cliente precisa de uma cópia do certificado de autoridade de certificação raiz que foi usado para criar o certificado do servidor (ou emitir uma). Normalmente ao se conectar a sites, um navegador vem pré-configurado com certificados de autoridade de certificação comumente usados para que o cliente tem um processo contínuo. 

Quando um dispositivo se conecta ao Hub IoT do Azure, o dispositivo é o cliente e o serviço de nuvem do Hub IoT é o servidor. O serviço de nuvem Hub IoT é respaldado por um certificado de AC raiz chamado **Baltimore CyberTrust Root**, que está publicamente disponível e é amplamente usado. Como o certificado CA do Hub IoT já está instalado na maioria dos dispositivos, muitas implementações TLS (OpenSSL, Schannel, LibreSSL) o usam automaticamente durante a validação do certificado do servidor. Um dispositivo que pode se conectar com êxito ao Hub IoT pode ter problemas ao tentar se conectar a um gateway IoT Edge.

Quando um dispositivo se conecta a um gateway IoT Edge, o dispositivo downstream é o cliente e o dispositivo de gateway é o servidor. O Azure IoT Edge permite que os operadores (ou usuários) criem cadeias de certificados de gateway da maneira que acharem melhor. O operador pode optar por usar um certificado de autoridade de certificação público, como o Baltimore, ou usar um certificado de autoridade de certificação raiz autoassinado (ou interno). Os certificados públicos de CA costumam ter um custo associado a eles, então são normalmente usados em cenários de produção. Os certificados de CA autoassinados são preferidos para desenvolvimento e teste. Os artigos de configuração do gateway transparente listados na introdução usam certificados de AC raiz autoassinados. 

Quando você usa um certificado de autoridade de certificação AC raiz autoassinado para um gateway IoT Edge, ele precisa ser instalado ou fornecido a todos os dispositivos de recebimento de dados que tentam se conectar ao gateway. 

![Configuração do certificado de gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

Para saber mais sobre os certificados do IoT Edge e algumas implicações de produção, consulte [Detalhes do uso do certificado do IoT Edge](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Fornecer o certificado de autoridade de certificação raiz

Para verificar os certificados do dispositivo de gateway, o dispositivo downstream precisa de sua própria cópia do certificado de autoridade de certificação raiz. Se você usou os scripts fornecidos no repositório git IoT Edge para criar certificados de teste, o certificado de autoridade de certificação raiz será chamado **Azure-IOT-Test-only. root. ca. cert. pem**. Se você ainda não fez parte das outras etapas de preparação do dispositivo downstream, mova esse arquivo de certificado para qualquer diretório em seu dispositivo downstream. Você pode usar um serviço como [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como [protocolo de cópia segura](https://www.ssh.com/ssh/scp/) para mover o arquivo de certificado.

## <a name="install-certificates-in-the-os"></a>Instalar certificados no sistema operacional

A instalação do certificado de autoridade de certificação raiz no repositório de certificados do sistema operacional geralmente permite que a maioria dos aplicativos use o certificado de autoridade de certificação raiz. Há algumas exceções, como aplicativos NodeJS que não usam o repositório de certificados do sistema operacional, mas usam o repositório de certificados interno do tempo de execução do nó. Se você não puder instalar o certificado no nível do sistema operacional, pule para [usar certificados com SDKs de IOT do Azure](#use-certificates-with-azure-iot-sdks). 

### <a name="ubuntu"></a>Ubuntu

Os seguintes comandos são um exemplo de como instalar um certificado de CA em um host Ubuntu. Este exemplo pressupõe que você esteja usando o certificado **Azure-IOT-Test-only. root. ca. cert. pem** dos artigos de pré-requisitos e que você copiou o certificado em um local no dispositivo downstream.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Você deve ver uma mensagem que diz, "Atualizando certificados no /etc/ssl/certs... 1 adicionado, removido 0; feito."

### <a name="windows"></a>Windows

As etapas a seguir são um exemplo de como instalar um certificado de autoridade de certificação em um host do Windows. Este exemplo pressupõe que você esteja usando o certificado **Azure-IOT-Test-only. root. ca. cert. pem** dos artigos de pré-requisitos e que você copiou o certificado em um local no dispositivo downstream.

1. No menu Iniciar, pesquise e selecione **Gerenciar certificados de computador**. Um utilitário chamado **certlm** é aberto.
2. Navegue para **Certificados - Computador Local** > **Autoridades de Certificação Raiz Confiáveis**.
3. Clique com o botão direito do mouse em **Certificates** e selecione **All Tasks** > **Import**. O assistente para importação de certificados deve ser iniciado. 
4. Siga as etapas conforme direcionado e importe o arquivo de certificado `<path>/azure-iot-test-only.root.ca.cert.pem`. Quando concluído, você verá uma mensagem "Importada com êxito". 

Você também pode instalar certificados programaticamente usando APIs .NET, conforme mostrado na amostra .NET mais adiante neste artigo. 

Geralmente, os aplicativos usam a pilha TLS fornecida pelo Windows chamada [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) para se conectar com segurança através de TLS. O Schannel *requer* que quaisquer certificados sejam instalados no repositório de certificados do Windows antes de tentar estabelecer uma conexão TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Usar certificados com SDKs de IoT do Azure

Esta seção descreve como os SDKs do IoT do Azure se conectam a um dispositivo IoT Edge usando aplicativos de amostra simples. O objetivo de todas as amostras é conectar o cliente do dispositivo e enviar mensagens de telemetria ao gateway, em seguida, fechar a conexão e sair. 

Ter duas coisas prontas antes de usar os exemplos de nível de aplicativo:

* A cadeia de conexão do Hub IoT do dispositivo downstream foi modificada para apontar para o dispositivo de gateway e todos os certificados necessários para autenticar seu dispositivo downstream no Hub IoT. Para obter mais informações, consulte [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).

* O caminho completo para o certificado de AC raiz que você copiou e salvou em algum lugar em seu dispositivo downstream.

    Por exemplo, `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Esta seção fornece um aplicativo de amostra para conectar um cliente de dispositivo do Azure IoT NodeJS a um gateway do IoT Edge. Para aplicativos NodeJS, você deve instalar o certificado de autoridade de certificação raiz no nível do aplicativo, conforme mostrado aqui. Os aplicativos NodeJS não usam o repositório de certificados do sistema. 

1. Obter a amostra para **edge_downstream_device.js** da [repositório de exemplos do SDK do dispositivo IoT do Azure para Node. js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples). 
2. Confira o arquivo **readme.md** para verificar se você tem todos os pré-requisitos para executar o exemplo. 
3. No arquivo edge_downstream_device.js, atualize as variáveis **connectionString** e **edge_ca_cert_path**. 
4. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo. 

Para reconhecimento da amostra que você está executando, o snippet de código a seguir é como o SDK do cliente lê o arquivo de certificado e o utiliza para estabelecer uma conexão TLS segura: 

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Esta seção apresenta um aplicativo de exemplo para se conectar a um cliente do dispositivo IoT do Azure .NET para um gateway IoT Edge. No entanto, os aplicativos .NET são capazes de usar automaticamente quaisquer certificados instalados no repositório de certificados do sistema em hosts Linux e Windows.

1. Obtenha o exemplo para **EdgeDownstreamDevice** da [pasta de exemplos .NET do IoT Edge](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice). 
2. Confira o arquivo **readme.md** para verificar se você tem todos os pré-requisitos para executar o exemplo. 
3. No arquivo **Properties / launchSettings.json**, atualize as variáveis **DEVICE_CONNECTION_STRING** e **CA_CERTIFICATE_PATH**. Se você quiser usar o certificado instalado no repositório de certificados confiáveis no sistema host, deixe essa variável em branco. 
4. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo. 

Para instalar programaticamente um certificado confiável no repositório de certificados por meio de um aplicativo .NET, consulte a função **InstallCACert ()** no arquivo **EdgeDownstreamDevice / Program.cs**. Essa operação é idempotente, portanto, pode ser executada várias vezes com os mesmos valores sem efeito adicional. 

### <a name="c"></a>C

Esta seção apresenta um aplicativo de amostra para conectar um cliente de dispositivo do Azure IoT C a um gateway do IoT Edge. O C SDK pode operar com muitas bibliotecas TLS, incluindo OpenSSL, WolfSSL e Schannel. Para obter mais informações, consulte o [SDK do Azure IoT C](https://github.com/Azure/azure-iot-sdk-c). 

1. Obtenha o aplicativo **iotedge_downstream_device_sample** do [SDK do dispositivo IoT do Azure para amostras C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples). 
2. Confira o arquivo **readme.md** para verificar se você tem todos os pré-requisitos para executar o exemplo. 
3. No arquivo iotedge_downstream_device_sample.c, atualize as variáveis **connectionString** e **edge_ca_cert_path**. 
4. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo. 

O SDK do dispositivo IoT do Azure para C fornece uma opção para registrar um certificado de CA ao configurar o cliente. Essa operação não instala o certificado em nenhum lugar, mas usa um formato de cadeia de caracteres do certificado na memória. O certificado salvo é fornecido para a pilha TLS subjacente ao estabelecer uma conexão. 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Nos hosts do Windows, se você não estiver usando o OpenSSL ou outra biblioteca TLS, o padrão do SDK será usar o Schannel. Para que o Schannel funcione, o certificado da AC raiz da IoT Edge deve ser instalado no repositório de certificados do Windows, não definido usando a operação `IoTHubDeviceClient_SetOption`. 

### <a name="java"></a>Java

Esta seção apresenta um aplicativo de amostra para conectar um cliente de dispositivo Java IoT do Azure a um gateway IoT Edge. 

1. Obter a amostra para **evento de envio** da [SDK do dispositivo IoT do Azure para exemplos de Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples). 
2. Confira o arquivo **readme.md** para verificar se você tem todos os pré-requisitos para executar o exemplo. 
3. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo.

### <a name="python"></a>Python

Esta seção apresenta um aplicativo de exemplo para conectar um cliente de dispositivo Python do IoT do Azure a um gateway do IoT Edge. 

1. Obtenha o exemplo para **edge_downstream_client** do [SDK do dispositivo IoT do Azure para amostras do Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples). 
2. Confira o arquivo **readme.md** para verificar se você tem todos os pré-requisitos para executar o exemplo. 
3. No arquivo edge_downstream_client.py, atualize as variáveis **CONNECTION_STRING** e **TRUSTED_ROOT_CA_CERTIFICATE_PATH**. 
4. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo. 


## <a name="test-the-gateway-connection"></a>Testar a conexão de gateway

Este é um comando de exemplo que testa se tudo foi configurado corretamente. Você deve ver uma mensagem informando que "verificada OK".

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="troubleshoot-the-gateway-connection"></a>Solucionar problemas de conexão de gateway

Se o dispositivo de folha tiver uma conexão intermitente com seu dispositivo de gateway, tente as etapas a seguir para resolução. 

1. O nome do host do gateway na cadeia de conexão é igual ao valor do nome do host no arquivo IoT Edge config. YAML no dispositivo de gateway?
2. O nome do host do gateway é resolvido para um endereço IP? Você pode resolver conexões intermitentes usando DNS ou adicionando uma entrada de arquivo de host no dispositivo de folha.
3. As portas de comunicação são abertas em seu firewall? A comunicação baseada no protocolo usado (MQTTS: 8883/AMQPS: 5671/HTTPS: 433) deve ser possível entre o dispositivo downstream e o IoT Edge transparente.

## <a name="next-steps"></a>Próximas etapas

Saiba como o IoT Edge pode estender [recursos off-line](offline-capabilities.md) para dispositivos downstream. 
