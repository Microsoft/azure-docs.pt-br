---
title: Conectividade de dispositivo no Azure IoT Central | Microsoft Docs
description: Este artigo apresenta os principais conceitos relacionados à conectividade de dispositivo no Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 9e1e85d1ab1c5e7ce0cbd96c64137309c2e2916a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60887433"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Conectividade de dispositivo no Azure IoT Central | Microsoft Docs

Este artigo apresenta os principais conceitos relacionados à conectividade de dispositivo no Microsoft Azure IoT Central.

Azure IoT Central usa o [serviço de provisionamento de dispositivos do Azure IoT Hub (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) para gerenciar todo o registro de dispositivo e conexão.

Usar o DPS permite:

- Central da IoT para dar suporte a dispositivos de conexão em grande escala e integração.
- A geração de dispositivo de credenciais e configurar os dispositivos offline sem registrar os dispositivos por meio de IoT Central da interface do usuário.
- Dispositivos se conectem usando compartilhado (SAS) de assinaturas de acesso.
- Dispositivos para se conectar usando certificados X.509 padrão da indústria.
- Você usar seu próprio dispositivo IDs para registrar dispositivos em IoT Central. Usar seu próprio dispositivo IDs simplifica a integração com sistemas de back-office existentes.
- Uma maneira única e consistente para conectar dispositivos à IoT Central.

Este artigo descreve os casos de uso de quatro seguintes:

1. [Conectar-se rapidamente um único dispositivo usando SAS](#connect-a-single-device)
1. [Conectar dispositivos em escala usando SAS](#connect-devices-at-scale-using-sas)
1. [Conectar dispositivos em escala usando certificados x. 509](#connect-devices-using-x509-certificates) essa é a abordagem recomendada para ambientes de produção.
1. [Conectar sem primeiro registrar dispositivos](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>Conectar um único dispositivo

Essa abordagem é útil quando você estiver experimentando a IoT Central ou dispositivos de teste. Você pode usar as informações de conexão do dispositivo do seu aplicativo de Central da IoT para gerar a cadeia de conexão para um dispositivo. Para obter etapas detalhadas, consulte [como gerar uma cadeia de caracteres de conexão de dispositivo para se conectar a um aplicativo do Azure IoT Central](howto-generate-connection-string.md).

## <a name="connect-devices-at-scale-using-sas"></a>Conectar dispositivos em escala usando SAS

Para conectar dispositivos IoT Central em escala usando SAS, você precisa registrar e, em seguida, configurar os dispositivos:

### <a name="register-devices-in-bulk"></a>Registrar dispositivos em massa

Para registrar um grande número de dispositivos com o seu aplicativo IoT Central, use um arquivo CSV para [importar identificações de dispositivo e nomes de dispositivo](howto-manage-devices.md#import-devices).

Para recuperar as informações de conexão para os dispositivos importados, [exportar um arquivo CSV de seu aplicativo IoT Central](howto-manage-devices.md#export-devices).

> [!NOTE]
> Para saber como você pode conectar dispositivos sem primeiro registrá-los no Central da IoT, consulte [Connect sem primeiro registro de dispositivos](#connect-without-registering-devices).

### <a name="set-up-your-devices"></a>Configurar seus dispositivos

Use as informações de conexão do arquivo de exportação no seu código de dispositivo para permitir que os dispositivos para se conectar e enviar dados ao IoT para seu aplicativo de IoT Central. Para obter mais informações sobre como conectar dispositivos, consulte [próximas etapas](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Conectar dispositivos usando certificados X.509

Em um ambiente de produção, usando certificados X.509 é o mecanismo de autenticação de dispositivo recomendadas para a IoT Central. Para saber mais, consulte [Autenticação de dispositivo usando certificados de AC X.509](../iot-hub/iot-hub-x509ca-overview.md).

As etapas a seguir descrevem como conectar dispositivos IoT Central usando certificados X.509:

1. Em seu aplicativo IoT Central, _adicionar e verificar o intermediário ou raiz do certificado x. 509_ você está usando para gerar certificados de dispositivo:

    - Navegue até **Administração > Conexão do dispositivo > certificados (x. 509)** e adicionar raiz X.509 ou certificado intermediário que você está usando para gerar os certificados de dispositivo de folha.

      ![Configurações de conexão](media/concepts-connectivity/connection-settings.png)

      Se você tiver uma violação de segurança ou o certificado primário é definido para expirar, use o certificado secundário para reduzir o tempo de inatividade. Você pode continuar a provisionar dispositivos usando o certificado secundário enquanto você atualiza o certificado primário.

    - Verificando a propriedade do certificado garante que o carregador do certificado tem a chave privada do certificado. Para verificar o certificado:
        - Selecione o botão lado **código de verificação** para gerar um código.
        - Crie um certificado de verificação X.509 com o código de verificação que você gerou na etapa anterior. Salve o certificado como um arquivo. cer.
        - Carregue o certificado de verificação assinado e selecione **Verify**.

          ![Configurações de conexão](media/concepts-connectivity/verify-cert.png)

1. Usar um arquivo CSV para _importação e registrar dispositivos_ em seu aplicativo IoT Central.

1. _Configure seus dispositivos._ gere os certificados secundários usando o certificado raiz carregado. Use o **ID do dispositivo** como o valor CNAME em certificados de folha. A ID do dispositivo deve ter apenas letras minúsculas. Em seguida, seus dispositivos com informações de serviço de provisionamento do programa. Quando um dispositivo é ativado para o primeiro, ele recupera as informações de conexão para seu aplicativo IoT Central do DPS.

### <a name="further-reference"></a>Referência adicional

- Exemplo de implementação para [RaspberryPi.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [Exemplo de cliente de dispositivo em C.](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Para fins de teste somente

Somente para teste, você pode usar esses utilitários para gerar certificados de autoridade de certificação e certificados de dispositivo.

- Se você estiver usando um dispositivo de kit de desenvolvimento, isso [ferramenta de linha de comando](https://aka.ms/iotcentral-docs-dicetool) gera um certificado de autoridade de certificação que você pode adicionar ao seu aplicativo de IoT Central para verificar os certificados.

- Use esta [ferramenta de linha de comando](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) para:
  - Crie uma cadeia de certificados. Siga a etapa 2 do artigo do GitHub.
  - Salve os certificados como arquivos. cer para carregar seu aplicativo IoT Central.
  - Use o código de verificação do aplicativo Central da IoT para gerar o certificado de verificação. Siga a etapa 3 do artigo do GitHub.
  - Crie certificados de folha para seus dispositivos usando suas IDs de dispositivo como um parâmetro para a ferramenta. Execute a etapa 4 no artigo do GitHub.

## <a name="connect-without-registering-devices"></a>Conectar-se sem registro de dispositivos

Um cenário fundamental que habilita a IoT Central é para OEMs fabricar em massa de dispositivos que podem se conectar a um aplicativo Central da IoT sem primeiro que está sendo registrado. Um fabricante deve gerar as credenciais adequadas e configurar os dispositivos na fábrica. Quando um dispositivo liga pela primeira vez, ele se conecta automaticamente a um aplicativo de IoT Central. Um operador de IoT Central deve aprovar o dispositivo antes que ele possa enviar dados stat.

O diagrama a seguir descreve este fluxo:

![Configurações de conexão](media/concepts-connectivity/device-connection-flow.png)

As etapas a seguir descrevem esse processo em mais detalhes. As etapas diferem ligeiramente dependendo se você estiver usando certificados X.509 ou SAS para autenticação de dispositivo:

1. Defina as configurações de conexão:

    - **Certificados x. 509:** [Adicionar e verificar o certificado raiz/intermediário](#connect-devices-using-x509-certificates) e usá-lo para gerar os certificados de dispositivo na etapa a seguir.
    - **SAS:** Copie a chave primária. Essa chave é a chave de SAS de grupo para o aplicativo de IoT Central. Use a chave para gerar as chaves SAS do dispositivo na etapa a seguir.
    ![SAS de configurações de conexão](media/concepts-connectivity/connection-settings-sas.png)

1. Gerar suas credenciais do dispositivo
    - **Certificados x. 509:** Gere os certificados de folha para seus dispositivos usando o certificado raiz ou intermediário que é adicionado ao seu aplicativo de IoT Central. Verifique se você usar o minúsculo **ID do dispositivo** como o CNAME nos certificados de folha. Para fins de teste apenas, usam isso [ferramenta de linha de comando](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) para gerar certificados de dispositivo.
    - **SAS:** Use esta [ferramenta de linha de comando](https://www.npmjs.com/package/dps-keygen) para gerar as chaves SAS do dispositivo. Use o grupo **Primary Key** da etapa anterior. A ID do dispositivo deve ser em letras minúscula.

      Para instalar o [utilitário do gerador de chave](https://github.com/Azure/dps-keygen), execute o seguinte comando:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Para gerar uma chave de dispositivo da chave primária de grupo SAS, execute o seguinte comando:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Para configurar seus dispositivos, flash cada dispositivo com o **ID do escopo**, **ID do dispositivo**, e **certificado de dispositivo X.509** ou **chave SAS**.

1. Em seguida, ative o dispositivo para que ele se conectar ao seu aplicativo IoT Central. Quando você alternar em um dispositivo, ele se conecta pela primeira vez para pontos de distribuição para recuperar suas informações de registro de IoT Central.

1. O dispositivo conectado aparece inicialmente como um **dispositivo não associado** sobre o **Device Explorer** página. O status de provisionamento do dispositivo é **Registrado**. **Associar** o dispositivo para o modelo de dispositivo apropriado e aprovar o dispositivo se conecte ao seu aplicativo IoT Central. O dispositivo pode recuperar uma cadeia de caracteres de conexão do IoT Hub e comece a enviar dados. Provisionamento de dispositivos foi concluído e o status de provisionamento é agora **provisionado**.

## <a name="provisioning-status"></a>Status de provisionamento

Quando um dispositivo real se conecta ao seu aplicativo IoT Central, suas alterações de status de provisionamento da seguinte maneira:

1. Status de provisionamento de dispositivos é o primeiro **registrado**. Esse status significa que o dispositivo é criado no Central da IoT e tem uma ID de dispositivo. Um dispositivo é registrado quando:
    - Um novo dispositivo real é adicionado na **Device Explorer** página.
    - Um conjunto de dispositivos é adicionado usando **importação** sobre o **Device Explorer** página.
    - Um dispositivo não tiver sido registrado manualmente na **Device Explorer** da página, mas conectado com credenciais válidas e está visível como um **não associado** dispositivo na **Device Explorer**página.

1. O status de provisionamento de dispositivo muda para **provisionado** quando o dispositivo conectado ao seu aplicativo de Central da IoT com credenciais válidas conclui a etapa de provisionamento. Nesta etapa, o dispositivo recupera uma cadeia de caracteres de conexão do IoT Hub. Agora, o dispositivo pode se conectar ao IoT Hub e comece a enviar dados.

1. Um operador pode bloquear um dispositivo. Quando um dispositivo é bloqueado, ele não é possível enviar dados ao seu aplicativo IoT Central. Dispositivos bloqueados têm um status de provisionamento de **bloqueado**. Um operador deve redefinir o dispositivo que ele possa continuar enviando dados. Quando um operador desbloqueia um dispositivo que o status de provisionamento retorna para seu valor anterior, **registrado** ou **provisionado**.

## <a name="sdk-support"></a>Suporte a SDK

A oferta de SDKs de dispositivo do Azure a maneira mais fácil para você implementar seu código de dispositivo. Estão disponíveis os SDKs do dispositivo a seguir:

- [SDK do IoT do Azure para C](https://github.com/azure/azure-iot-sdk-c)
- [SDK do IoT do Azure para Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK do IoT do Azure para Node.js](https://github.com/azure/azure-iot-sdk-node)
- [SDK do IoT do Azure para Java](https://github.com/azure/azure-iot-sdk-java)
- [SDK do IoT do Azure para .NET](https://github.com/azure/azure-iot-sdk-csharp)

Cada dispositivo conecta usando uma cadeia de conexão única que identifica o dispositivo. Um dispositivo só pode se conectar ao hub IoT em que ele está registrado. Quando você cria um dispositivo real em seu aplicativo do Azure IoT Central, o aplicativo gera as informações necessárias construir uma cadeia de caracteres de conexão usando `dps-keygen`.

### <a name="sdk-features-and-iot-hub-connectivity"></a>Recursos de SDK e conectividade do Hub IoT

Toda a comunicação do dispositivo com o Hub IoT usa as opções de conectividade do Hub IoT a seguir:

- [Mensagens de dispositivo para nuvem](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Dispositivos gêmeos](../iot-hub/iot-hub-devguide-device-twins.md)

A tabela a seguir resume como os recursos do dispositivo Azure IoT Central são mapeados para os recursos do Hub IoT:

| Azure IoT Central | Hub IoT do Azure |
| ----------- | ------- |
| Medida: Telemetria | Mensagens do dispositivo para a nuvem |
| Propriedades do dispositivo | Propriedades relatadas do dispositivo gêmeo |
| Configurações | Propriedades desejadas e relatadas do dispositivo gêmeo |

Para saber mais sobre como usar os SDKs do Dispositivo, consulte um dos artigos a seguir para exemplo de código:

- [Conectar um cliente Node.js genérico ao aplicativo Azure IoT Central](howto-connect-nodejs.md)
- [Conectar um dispositivo Raspberry Pi ao aplicativo Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Conectar um dispositivo do kit de DevDiv ao aplicativo Azure IoT Central](howto-connect-devkit.md).

### <a name="protocols"></a>Protocolos

Os SDKs do Dispositivo dão suporte aos protocolos de rede a seguir para conectar um Hub IoT:

- MQTT
- AMQP
- HTTPS

Para obter informações sobre esses diferentes protocolos e as diretrizes sobre como escolher um deles, consulte [Escolher um protocolo de comunicação](../iot-hub/iot-hub-devguide-protocols.md).

Se o dispositivo não puder usar nenhum dos protocolos com suporte, será possível usar o Azure IoT Edge para fazer a conversão de protocolo. O IoT Edge dá suporte a outros cenários de inteligência na borda para transferir o processamento para a borda do aplicativo Azure IoT Central.

## <a name="security"></a>Segurança

Todos os dados trocados entre dispositivos e o Azure IoT Central são criptografados. O Hub IoT autentica todas as solicitações de um dispositivo que conecta a qualquer ponto de extremidade do Hub IoT voltado para o dispositivo. Para evitar a troca de credenciais pela rede, um dispositivo usa tokens assinados para autenticar. Para obter mais informações, consulte [Controlar o acesso ao Hub IoT](../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre a conectividade do dispositivo no Azure IoT Central, aqui estão as próximas etapas sugeridas:

- [Preparar e conectar um dispositivo DevKit](howto-connect-devkit.md)
- [Preparar e conectar um Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Conectar um cliente Node.js genérico ao aplicativo Azure IoT Central](howto-connect-nodejs.md)
- [SDK DE C: SDK do cliente de dispositivo de provisionamento](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
