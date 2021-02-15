---
title: Suporte da plataforma de SDKs do dispositivo IoT do Azure | Microsoft Docs
description: Os SDKs de dispositivo de software livre estão disponíveis no GitHub em C, .NET (C#), Java, Node.js e Python, para conectar dispositivos ao Hub IoT do Azure e ao DPS (serviço de provisionamento de dispositivos).
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: b8f8403617a5fc8b442e8a3f74ab9226fe1479fe
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588753"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Suporte à plataforma de SDKs do dispositivo IoT do Azure

A Microsoft se esforça para expandir continuamente o universo de dispositivos compatíveis com o Hub IoT do Azure. A Microsoft publica SDKs de dispositivo de software livre no GitHub para ajudar a conectar dispositivos ao Hub IoT do Azure e ao serviço de provisionamento de dispositivos. Os SDKs do dispositivo estão disponíveis para C, .NET (C#), Java, Node.js e Python. A Microsoft testa cada SDK para garantir que ele seja executado nas configurações com suporte detalhadas para ele na seção [Microsoft SDKs e suporte à plataforma de dispositivo](#microsoft-sdks-and-device-platform-support) .

Além dos SDKs do dispositivo, a Microsoft fornece vários outros caminhos para capacitar clientes e desenvolvedores a conectarem seus dispositivos à IoT do Azure:

* A Microsoft colabora com várias empresas parceiras para ajudá-las a publicar kits de desenvolvimento, com base no SDK do Azure IoT C, para suas plataformas de hardware.

* A Microsoft trabalha com parceiros confiáveis da Microsoft para fornecer um conjunto em constante expansão de dispositivos que foram testados e certificados para o Azure IoT. Para obter uma lista atual desses dispositivos, consulte o [Catálogo de dispositivos Azure Certified para IOT](https://catalog.azureiotsolutions.com/).

* A Microsoft fornece uma PAL (camada de abstração de plataforma) no SDK do dispositivo C do Hub IoT do Azure que ajuda os desenvolvedores a portar facilmente o SDK para sua plataforma. Para saber mais, Confira as [diretrizes de portabilidade do SDK do C](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

Este tópico fornece informações sobre os SDKs da Microsoft e as configurações de plataforma às quais eles dão suporte, bem como cada uma das outras opções listadas acima.

## <a name="microsoft-sdks-and-device-platform-support"></a>Microsoft SDKs e suporte a plataforma de dispositivo

A Microsoft publica SDKs de código aberto no GitHub para os seguintes idiomas: C, .NET (C#), Node.js, Java e Python. Os SDKs e suas dependências são listados nesta seção. Os SDKs têm suporte em qualquer plataforma de dispositivo que satisfaça essas dependências.

Para cada um dos SDKs listados, Microsoft:

* Compila e executa continuamente testes de ponta a ponta em relação à ramificação principal do SDK relevante no GitHub em várias plataformas populares.  Para fornecer cobertura de teste entre diferentes versões do compilador, geralmente testamos a versão mais recente do LTS e a versão mais popular.

* Fornece diretrizes de instalação ou pacotes de instalação, se aplicável.

* Dá suporte total aos SDKs no GitHub com código de software livre, um caminho para contribuições de clientes e envolvimento da equipe de produto com problemas do GitHub.

### <a name="c-sdk"></a>SDK C

O [SDK do dispositivo C do Hub IOT do Azure](https://github.com/Azure/azure-iot-sdk-c) é testado com o e oferece suporte às seguintes configurações.

| Sistema operacional                  | Biblioteca TLS                  | Requisitos adicionais                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL ou BearSSL | Berkeley Sockets</br></br>Interface do sistema operacional portátil (POSIX)                       |
| iOS 12,2            | OpenSSL                      | XCode emulado no OSX 10.13.4                                                               |
| Família Windows 10   | SChannel                     |                                                                                             |
| Mbed OS 5,4         | Mbed TLS 2                   | [Kit de desenvolvimento de IoT MXChip](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Sistema operacional Azure Sphere     | WolfSSL                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | BearSSL                      | [ESP32 ou ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>SDK do Python

O [SDK do dispositivo Python do Hub IOT do Azure](https://github.com/Azure/azure-iot-sdk-python) é testado com o e oferece suporte às seguintes configurações.

| Sistema operacional                  | Compilador                          |
|---------------------|-----------------------------------|
| Linux               | Python 2,7. *, 3,5 ou posterior |
| macOS High Sierra   | Python 2,7. *, 3,5 ou posterior |
| Família Windows 10   | Python 2,7. *, 3,5 ou posterior |

Somente o Python versão 3.5.3 ou posterior dá suporte às APIs assíncronas, recomendamos o uso da versão 3,7 ou posterior.

### <a name="net-sdk"></a>SDK .NET

O [SDK do dispositivo .net (C#) do Hub IOT do Azure](https://github.com/Azure/azure-iot-sdk-csharp) é testado com o e oferece suporte às seguintes configurações.

| Sistema operacional                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| SKUs do Windows 10 desktop e Server   | .NET Core 2,1, .NET Framework 4.5.1 ou .NET Framework 4,7 |

O SDK do .NET também pode ser usado com o Windows IoT core com o [agente de dispositivo do Azure](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) ou [um NTService personalizado que pode usar o RPC para se comunicar com os aplicativos UWP](/samples/microsoft/windows-iotcore-samples/ntservice-rpc/).

### <a name="nodejs-sdk"></a>SDK do Node.js

O [SDK do dispositivo Node.js Hub IOT do Azure](https://github.com/Azure/azure-iot-sdk-node) é testado com o e oferece suporte às seguintes configurações.

| Sistema operacional                  | Versão do nó    |
|---------------------|-----------------|
| Linux               | LTS e atual |
| Família Windows 10   | LTS e atual |

### <a name="java-sdk"></a>Java SDK

O [SDK do dispositivo Java do Hub IOT do Azure](https://github.com/Azure/azure-iot-sdk-java) é testado com o e oferece suporte às seguintes configurações.

| Sistema operacional                     | Versão Java |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Família Windows 10 x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>Kits de desenvolvimento com suporte para parceiros

A Microsoft trabalha com vários parceiros para fornecer kits de desenvolvimento para várias arquiteturas de microprocessador. Esses parceiros portaram o SDK do Azure IoT C para sua plataforma. Os parceiros criam e mantêm a PAL (camada de abstração de plataforma) do SDK. A Microsoft trabalha com esses parceiros para fornecer suporte estendido.

| Parceiro             | Dispositivos                            | Link                     | Suporte |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT Modem     | [Qualcomm LTE para SDK de IoT](https://developer.qualcomm.com/software/lte-iot-sdk) | [Fórum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | Série STM32L4 <br/> Série STM32F4 <br/>  Série STM32F7 <br/>  Kit de descoberta STM32L4 para nó de IoT    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Suporte](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | LaunchPad CC3220SF </br> LaunchPad CC3220S </br> LaunchPad CC3235SF </br> LaunchPad CC3235S </br> LaunchPad MSP432E4 | [Plugin do IoT do Azure para SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [Fórum de TI E2E](https://e2e.ti.com) <br/> [Fórum de TI E2E para CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [Fórum de TI E2E para MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>Portando o SDK do Microsoft Azure IoT C

Se a plataforma do dispositivo não estiver coberta por uma das seções anteriores, considere a possibilidade de portar o SDK do Azure IoT C. Portar o SDK do C envolve principalmente a implementação da PAL (camada de abstração de plataforma) do SDK. A PAL define primitivos que fornecem a cola entre o dispositivo e as funções de nível superior no SDK. Para obter mais informações, consulte [diretrizes de portabilidade](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Parceiros da Microsoft e dispositivos do Azure IoT certificados

A Microsoft trabalha com vários parceiros para expandir continuamente o universo IoT do Azure com os dispositivos testados e certificados pelo IoT do Azure.

* Para procurar dispositivos do Azure IoT Certified, consulte [Microsoft Azure Certified para IOT catálogo de dispositivos](https://catalog.azureiotsolutions.com/).

* Para saber mais sobre o ecossistema certificado pelo Azure para IoT, confira [ingressar no ecossistema certificado para IOT](https://catalog.azureiotsolutions.com/register).

## <a name="connecting-to-iot-hub-without-an-sdk"></a>Conectando-se ao Hub IoT sem um SDK

Se não for possível usar um dos SDKs de dispositivo do Hub IoT, você poderá se conectar diretamente ao Hub IoT usando um protocolo de comunicação com suporte. Para saber mais, confira [desenvolver sem um SDK do Azure IOT](iot-hub-devguide-no-sdk.md).

## <a name="support-and-other-resources"></a>Suporte e outros recursos

Se você tiver problemas ao usar os SDKs do dispositivo IoT do Azure, há várias maneiras de buscar o suporte resumido abaixo. Para obter informações completas sobre todas as suas opções de suporte, consulte [suporte do Azure IOT e opções de ajuda](../iot-fundamentals/iot-support-help.md). 

**Relatando bugs** – os bugs nos SDKs do dispositivo podem ser relatados na página problemas do projeto do GitHub relevante. As correções passam rapidamente do projeto para as atualizações do produto.

* [Problemas do SDK C do Hub IoT do Azure](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Problemas do SDK do .NET (C#) do Hub IoT do Azure](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Problemas do SDK do Java do Hub IoT do Azure](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Problemas do SDK Node.js do Hub IoT do Azure](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Problemas do SDK do Python no Hub IoT do Azure](https://github.com/Azure/azure-iot-sdk-python/issues)

**Perguntas técnicas** – você pode fazer perguntas técnicas no [Microsoft Q&A](/answers/topics/azure-iot-sdk.html) e [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-sdk) usando a marca *Azure-IOT-SDK*.

**Equipe de suporte ao cliente da Microsoft** – os usuários que têm um [plano de suporte](https://azure.microsoft.com/support/plans/) podem envolver a equipe de suporte ao cliente da Microsoft criando uma nova solicitação de suporte diretamente do [portal do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

**Solicitações de recursos** – as solicitações de recurso do Azure IOT são controladas por meio da [página de voz do usuário](https://feedback.azure.com/forums/321918-azure-iot)do produto.

## <a name="next-steps"></a>Próximas etapas

* [SDKs de serviço e dispositivo](iot-hub-devguide-sdks.md)
* [Diretrizes de portabilidade](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)