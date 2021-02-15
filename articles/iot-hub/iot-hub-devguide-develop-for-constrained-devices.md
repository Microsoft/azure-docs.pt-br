---
title: O Hub IoT do Azure desenvolve para dispositivos restritos usando o SDK do Hub IoT C
description: Guia do desenvolvedor - diretrizes sobre como desenvolver usando SDKs do IoT do Azure para dispositivos restritos.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 5a43eb2537ebc09ffcb524a4426d7a8c9bec560b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499995"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Desenvolver para dispositivos restritos usando SDKs C de IoT do Azure

O SDK C do Hub IoT é gravado em ANSI C (C99), tornando-o adequado para operar uma variedade de plataformas com pequeno volume de memória e disco. A RAM recomendada é de pelo menos 64 KB, mas o tamanho exato do volume de memória depende do protocolo usado, do número de conexões abertas e da plataforma de destino.
> [!NOTE]
> * O SDK do Azure IoT C publica regularmente informações de consumo de recursos para ajudar no desenvolvimento.  Visite nosso [repositório do GitHub](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) e examine o parâmetro de comparação mais recente.
>

O SDK C está disponível em formulário de pacote do apt-get, NuGet e MBED. Para direcionar dispositivos restritos, convém compilar o SDK localmente para a plataforma de destino. Esta documentação demonstra como remover determinados recursos para reduzir o volume do SDK C usando [cmake](https://cmake.org/). Além disso, esta documentação aborda os modelos de programação de melhores práticas para trabalhar com dispositivos restritos.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Compilar o SDK C para dispositivos restritos

Crie o SDK C para dispositivos restritos.

> [!NOTE]
> O SDK do C inserido é uma alternativa para dispositivos restritos que dão suporte à abordagem BYON (traga sua própria rede). Os desenvolvedores de IoT têm a liberdade de trazer o cliente MQTT, o TLS e o soquete de sua escolha para criar uma solução de dispositivo. [Saiba mais sobre o SDK do C inserido](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot).

### <a name="prerequisites"></a>Pré-requisitos

Siga este [Guia de instalação do SDK C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) para preparar o ambiente de desenvolvimento para criar o SDK C. Antes de chegar à etapa de compilação com cmake, é possível invocar sinalizadores cmake para remover recursos não utilizados.

### <a name="remove-additional-protocol-libraries"></a>Remover bibliotecas de protocolo adicionais

Atualmente, o SDK C dá suporte a cinco protocolos: MQTT, MQTT sobre WebSocket, AMQPs, AMQP sobre WebSocket e HTTPS. A maioria dos cenários requer um ou dois protocolos em execução em um cliente, portanto, é possível remover a biblioteca de protocolos que não está usando do SDK. Informações adicionais sobre como escolher o protocolo de comunicação apropriado para seu cenário podem ser localizadas em [Escolher um protocolo de comunicação do Hub IoT](iot-hub-devguide-protocols.md). Por exemplo, o MQTT é um protocolo leve que geralmente é mais adequado para dispositivos restritos.

Você pode remover as bibliotecas AMQP e HTTP usando o seguinte comando do cmake:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Remover o recurso de log do SDK

O SDK C fornece log extensivo para ajudar na depuração. É possível remover o recurso de log para dispositivos de produção usando o comando cmake a seguir:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Remover upload para o recurso de blob

É possível carregar arquivos grandes no Armazenamento do Microsoft Azure usando o recurso interno do SDK. O Hub IoT atua como um dispatcher para uma conta de Armazenamento do Microsoft Azure associada. É possível usar esse recurso para enviar arquivos de mídia, grandes lotes de telemetria e logs. Você pode obter mais informações em [carregando arquivos com o Hub IoT](iot-hub-devguide-file-upload.md). Se o aplicativo não exigir essa funcionalidade, você poderá remover esse recurso usando o comando cmake a seguir:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Execução de faixa no ambiente Linux

Se os binários executarem no sistema Linux, você poderá utilizar o [comando strip](https://en.wikipedia.org/wiki/Strip_(Unix)) para reduzir o tamanho do aplicativo final após a compilação.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Modelos de programação para dispositivos restritos

Em seguida, examine os modelos de programação para dispositivos restritos.

### <a name="avoid-using-the-serializer"></a>Evite usar o Serializador

O SDK C tem um [serializador do SDK C](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer) opcional que permite usar tabelas de mapeamento declarativas para definir métodos e propriedades de dispositivo gêmeo. O serializador foi projetado para simplificar o desenvolvimento, mas adiciona sobrecarga, o que não é ideal para dispositivos restritos. Nesse caso, considere o uso de APIs de cliente primitivas e a análise do JSON usando um analisador leve, como o [Parson](https://github.com/kgabis/parson).

### <a name="use-the-lower-layer-_ll_"></a>Use a camada inferior (_LL_)

O SDK C dá suporte a dois modelos de programação. Um conjunto tem APIs com um infixo _LL_, que significa camada inferior. Esse conjunto de APIs é mais leve e não ativa threads de trabalho, o que significa que o usuário precisa controlar manualmente o agendamento. Por exemplo, para o cliente de dispositivo, as APIs _LL_ podem ser encontradas neste [arquivo de cabeçalho](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

Outro conjunto de APIs sem o índice _LL_ é chamado de camada de conveniência, em que um thread de trabalho é girado automaticamente. Por exemplo, as APIs da camada de conveniência do cliente do dispositivo podem ser encontradas neste [arquivo de cabeçalho do cliente do dispositivo de IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h). Para dispositivos restritos em que cada thread extra pode receber uma porcentagem substancial de recursos do sistema, considere o uso de APIs _LL_.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a arquitetura de SDK C do IoT do Azure:
-    [Código-fonte do C SDK do Azure IoT](https://github.com/Azure/azure-iot-sdk-c/)
-    [SDK do dispositivo IoT do Azure para introdução C](iot-hub-device-sdk-c-intro.md)
