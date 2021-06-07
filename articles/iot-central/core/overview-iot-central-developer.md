---
title: Desenvolvimento de dispositivos para o Azure IoT Central | Microsoft Docs
description: O Azure IoT Central é uma plataforma de aplicativo IoT que simplifica a criação de soluções de IoT. Este artigo fornece uma visão geral do desenvolvimento de dispositivos para se conectar ao seu aplicativo do IoT Central. Os dispositivos usam a telemetria para enviar propriedades e dados de streaming para relatar o estado do dispositivo. O IoT Central pode definir o estado do dispositivo usando propriedades graváveis e chamar comandos em um dispositivo.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: ebd2759d4dfb8ee79130f9b4876eba8d45226d04
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718783"
---
# <a name="iot-central-device-development-guide"></a>Guia de desenvolvimento de dispositivos para o IoT Central

*Este artigo se aplica a desenvolvedores de dispositivos.*

Um aplicativo do IoT Central permite que você monitore e gerencie milhões de dispositivos ao longo de seu ciclo de vida. Este guia destina-se a desenvolvedores de dispositivos que implementam código para execução em dispositivos que se conectam ao IoT Central.

Os dispositivos interagem com um aplicativo do IoT Central usando os seguintes primitivos:

- _Telemetria_ são dados que um dispositivo envia para o IoT Central. Por exemplo, um fluxo de valores de temperatura de um sensor integrado.
- _Propriedades_ são valores de estado que um dispositivo relata para o IoT Central. Por exemplo, a versão atual do firmware do dispositivo. Você também pode ter propriedades graváveis que o IoT Central pode atualizar no dispositivo como uma temperatura de destino.
- _Comandos_ são chamados do IoT Central para controlar o comportamento de um dispositivo. Por exemplo, seu aplicativo do IoT Central pode chamar um comando para reinicializar um dispositivo.

Um construtor de soluções é responsável por configurar painéis e exibições na interface do usuário Web do IoT Central para visualizar a telemetria, gerenciar propriedades e chamar comandos.

## <a name="types-of-device"></a>Tipos de dispositivo

As seções a seguir descrevem os principais tipos de dispositivo que você pode conectar a um aplicativo do IoT Central:

### <a name="standalone-device"></a>Dispositivo autônomo

Um dispositivo autônomo se conecta diretamente ao IoT Central. Um dispositivo autônomo normalmente envia telemetria de sensores integrados ou conectados a ele para o seu aplicativo do IoT Central. Dispositivos autônomos também podem relatar valores de propriedade, receber valores de propriedade graváveis e responder a comandos.

### <a name="gateway-device"></a>Dispositivos de gateway

Um dispositivo de gateway gerencia um ou mais dispositivos downstream que se conectam ao seu aplicativo do IoT Central. Você usa o IoT Central para configurar as relações entre os dispositivos downstream e o dispositivo de gateway. Para saber mais, confira [Definir um novo tipo de dispositivo de gateway de IoT no aplicativo Azure IoT Central](./tutorial-define-gateway-device-type.md).

### <a name="edge-device"></a>Dispositivo de borda

Um dispositivo de borda se conecta diretamente ao IoT Central, mas atua como um intermediário para outros dispositivos conhecidos como _dispositivos folha_. Um dispositivo de borda normalmente fica localizado próximo dos dispositivos folha para os quais está atuando como intermediário. Cenários que usam dispositivos de borda incluem:

- Habilitar dispositivos que não podem se conectar diretamente ao IoT Central para se conectarem por meio do dispositivo de borda. Por exemplo, um dispositivo folha pode usar Bluetooth para se conectar ao dispositivo de borda, que por sua vez se conecta pela Internet ao IoT Central.
- Agregar a telemetria antes do envio para o IoT Central. Essa abordagem pode ajudar a reduzir os custos de envio de dados ao IoT Central.
- Controlar dispositivos folha localmente para evitar a latência associada à conexão com o IoT Central pela Internet.

Um dispositivo de borda também pode enviar a própria telemetria, relatar suas propriedades e responder a atualizações de propriedade e comandos graváveis.

O IoT Central vê apenas o dispositivo de borda, não os dispositivos folha conectados a ele.

Para saber mais, confira [Adicionar um dispositivo Azure IoT Edge ao aplicativo do Azure IoT Central](./tutorial-add-edge-as-leaf-device.md).

## <a name="connect-a-device"></a>Conectar um dispositivo

O Azure IoT Central usa o [DPS (Serviço de Provisionamento de Dispositivos no Hub IoT do Azure)](../../iot-dps/about-iot-dps.md) para gerenciar todo o registro e a conexão do dispositivo.

O uso do DPS permite que:

- O IoT Central dê suporte à integração e à conexão de dispositivos em escala.
- Você gere credenciais de dispositivo e configure os dispositivos offline sem registrar os dispositivos por meio da interface do usuário do IoT Central.
- Você use as próprias identificações do dispositivo para registrar dispositivos no IoT Central. O uso das próprias identificações do dispositivo simplifica a integração aos sistemas de back-office existentes.
- Uma só maneira consistente de conectar dispositivos ao IoT Central.

Para saber mais, confira [Conectar-se ao Azure IoT Central](./concepts-get-connected.md) e [Melhores práticas](concepts-best-practices.md).

### <a name="security"></a>Segurança

A conexão entre um dispositivo e seu aplicativo do IoT Central é protegida usando [assinaturas de acesso compartilhado](./concepts-get-connected.md#sas-group-enrollment) ou [certificados X.509](./concepts-get-connected.md#x509-group-enrollment) padrão do setor.

### <a name="communication-protocols"></a>Protocolos de comunicação

Os protocolos de comunicação que um dispositivo pode usar para se conectar ao IoT Central incluem MQTT, AMQP e HTTPS. Internamente, o IoT Central usa um hub IoT para habilitar a conectividade do dispositivo. Para obter mais informações sobre os protocolos de comunicação a que o Hub IoT dá suporte para conectividade de dispositivo, confira [Escolher um protocolo de comunicação](../../iot-hub/iot-hub-devguide-protocols.md).

## <a name="implement-the-device"></a>Implementar o dispositivo

Um modelo de dispositivo IoT Central inclui um _modelo_ que especifica os comportamentos que um dispositivo desse tipo deve implementar. Os comportamentos incluem telemetria, propriedades e comandos.

> [!TIP]
> Você pode exportar o modelo do IoT Central como um arquivo JSON da [DTDL (Linguagem de Definição de Gêmeos Digitais) v2](https://github.com/Azure/opendigitaltwins-dtdl).

Cada modelo tem um _DTMI_ (identificador de modelo de dispositivo gêmeo) exclusivo, como `dtmi:com:example:Thermostat;1`. Quando um dispositivo se conecta ao IoT Central, ele envia o DTMI do modelo que ele implementa. O IoT Central pode associar o modelo de dispositivo correto ao dispositivo.

O [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) define um conjunto de convenções que um dispositivo deve seguir ao implementar um modelo DTDL.

Os [SDKs do dispositivo IoT do Azure](#languages-and-sdks) incluem suporte para as convenções do IoT Plug and Play.

### <a name="device-model"></a>Modelo do dispositivo

Um modelo de dispositivo é definido usando a [DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Essa linguagem permite que você defina:

- A telemetria enviada pelo dispositivo. A definição inclui o nome e o tipo de dados da telemetria. Por exemplo, um dispositivo envia a telemetria de temperatura como um double.
- As propriedades que o dispositivo relata ao IoT Central. Uma definição de propriedade inclui o nome e tipo de dados dela. Por exemplo, um dispositivo relata o estado de uma válvula como um booliano.
- As propriedades que o dispositivo pode receber do IoT Central. Opcionalmente, você pode marcar uma propriedade como gravável. Por exemplo, IoT Central envia uma temperatura de destino como um double para um dispositivo.
- Os comandos aos quais um dispositivo responde. A definição inclui o nome do comando e os nomes e tipos de dados de parâmetros. Por exemplo, um dispositivo responde a um comando de reinicialização que especifica o número de segundos de espera antes da reinicialização.

Um modelo de DTDL pode ser um modelo com _nenhum componente_ ou _vários componentes_:

- Modelo com nenhum componente: um modelo simples não usa componentes inseridos nem em cascata. Toda a telemetria, propriedades e comandos são definidos como um _componente padrão_. Para obter um exemplo, confira o modelo [Termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json).
- Modelo com vários componentes. Um modelo mais complexo que inclui dois ou mais componentes. Esses componentes incluem um componente padrão e um ou mais componentes aninhados adicionais. Para obter um exemplo, confira o modelo [Controlador de Temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

Para saber mais, confira o [Guia de modelagem do IoT Plug and Play](../../iot-pnp/concepts-modeling-guide.md).

### <a name="conventions"></a>Convenções

Um dispositivo deve seguir as convenções de IoT Plug and Play quando troca dados com o IoT Central. As convenções incluem:

- Enviar o DTMI quando ele se conectar ao IoT Central.
- Enviar os metadados e o conteúdo JSON formatado corretamente para o IoT Central.
- Responder corretamente a propriedades e comandos graváveis do IoT Central.
- Seguir as convenções de nomenclatura para comandos de componente.

> [!NOTE]
> Atualmente, o IoT Central não dá suporte total aos tipos de dados **Matriz** e **Geoespacial** da DTDL.

Para saber mais sobre o formato das mensagens JSON que um dispositivo troca com o IoT Central, confira [Telemetria, propriedade e conteúdos de comando](concepts-telemetry-properties-commands.md).

Para saber mais sobre as convenções do IoT Plug and Play, confira [Convenções do IoT Plug and Play](../../iot-pnp/concepts-convention.md).

### <a name="device-sdks"></a>SDKs de dispositivo

Use um dos [SDKs do dispositivo IoT do Azure](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) para implementar o comportamento de seu dispositivo. O código deve:

- Registrar o dispositivo com o DPS e usar as informações do DPS para se conectar ao Hub IoT interno em seu aplicativo do IoT Central.
- Comunicar o DTMI do modelo implementado pelo dispositivo.
- Enviar a telemetria no formato especificado pelo modelo de dispositivo. O IoT Central usa o modelo no modelo de dispositivo para determinar como usar a telemetria para visualizações e análises.
- Sincronizar valores de propriedade entre o dispositivo e o IoT Central. O modelo especifica os nomes de propriedade e os tipos de dados para que o IoT Central possa exibir as informações.
- Implementar manipuladores de comandos para os comandos especificados no modelo. O modelo especifica os nomes de comando e parâmetros que o dispositivo deve usar.

Para obter mais informações sobre a função de modelos de dispositivo, confira [O que são modelos de dispositivo?](./concepts-device-templates.md).

Para obter um código de exemplo, confira [Criar e conectar um aplicativo cliente](./tutorial-connect-device.md).

### <a name="languages-and-sdks"></a>Linguagens e SDKs

Para obter mais informações sobre os idiomas e SDKs com suporte, confira [Entender e usar os SDKs de dispositivo do Hub IoT do Azure](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks).

## <a name="next-steps"></a>Próximas etapas

Se você é um desenvolvedor de dispositivos e deseja se aprofundar em algum código, a próxima etapa sugerida é [Criar e conectar um aplicativo cliente ao seu aplicativo do Azure IoT Central](./tutorial-connect-device.md).

Se quiser saber mais sobre como usar o IoT Central, as próximas etapas sugeridas são experimentar os guias de início rápido, começando em [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md).
