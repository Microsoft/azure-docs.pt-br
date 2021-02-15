---
title: Glossário de termos – Plug and Play de IoT | Microsoft Docs
description: Conceitos – um glossário de termos comuns relacionados a Plug and Play de IoT.
author: dominicbetts
ms.author: dobett
ms.date: 07/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d44866e2d04ab1bab5d2eca01374350a7d73a0ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577332"
---
# <a name="glossary-of-terms-for-iot-plug-and-play"></a>Glossário de termos para Plug and Play de IoT

Definições de termos comuns, conforme usado nos artigos do IoT Plug and Play.

## <a name="azure-iot-explorer-tool"></a>Ferramenta do explorador do Azure IoT

O explorador do Azure IoT é uma ferramenta gráfica que você pode usar para interagir com seus [dispositivos IoT Plug and Play](#iot-plug-and-play-device) e testá-los. Depois de instalar a ferramenta no computador local, você pode usá-la para:

- Exibir os dispositivos conectados ao seu [hub IoT](#azure-iot-hub).
- Conectar-se a um dispositivo IoT Plug and Play.
- Exiba os [componentes](#component)do dispositivo.
- Exibir a [telemetria](#telemetry) enviada pelo dispositivo.
- Trabalhar com [propriedades](#properties) do dispositivo.
- Chamar os comandos do [dispositivo](#commands).

## <a name="azure-iot-hub"></a>Hub IoT do Azure

O Hub IoT é um serviço gerenciado e hospedado na nuvem que atua como um hub central de mensagem para a comunicação bidirecional entre o aplicativo de IoT e os dispositivos que ele gerencia. [Dispositivos IoT Plug and Play](#iot-plug-and-play-device) podem se conectar a um hub IoT. Uma solução de IoT usa um hub IoT para permitir que:

- Dispositivos enviem telemetria a uma solução baseada em nuvem.
- Uma solução baseada em nuvem gerencie dispositivos conectados.

## <a name="azure-iot-device-sdk"></a>SDK do dispositivo IoT do Azure

Há SDKs de dispositivo para vários idiomas que você pode usar para criar aplicativos cliente de dispositivos IoT Plug and Play. Use **DeviceClient** para dispositivos e **ModuleClient** para módulos e módulos de IOT Edge.

## <a name="commands"></a>Comandos

Os comandos definidos em uma [interface](#interface) representam métodos que podem ser executados no [gêmeo digital](#digital-twin). Por exemplo, um comando para reinicializar um dispositivo.

## <a name="component"></a>Componente

Os componentes permitem criar uma [interface](#interface) de modelo como um assembly de outras interfaces. Um [modelo de dispositivo](#device-model) pode combinar várias interfaces como componentes. Por exemplo, um modelo pode incluir um componente de switch e um componente termostato. Vários componentes em um modelo também podem usar o mesmo tipo de interface. Por exemplo, um modelo pode incluir dois componentes termostato.

## <a name="connection-string"></a>Cadeia de conexão

Uma cadeia de conexão encapsula as informações necessárias para se conectar a um ponto de extremidade. Uma cadeia de conexão normalmente inclui o endereço das informações de segurança e de ponto de extremidade, mas os formatos da cadeia de conexão variam de acordo com os serviços. Há dois tipos de cadeia de conexão associados ao serviço do Hub IoT:

- As cadeias de conexão do dispositivo permitem que os [dispositivos IoT Plug and Play](#iot-plug-and-play-device) se conectem aos pontos de extremidade voltados para o dispositivo em um hub IoT. O código do cliente em um dispositivo usa a cadeia de conexão para estabelecer uma conexão segura com um hub IoT.
- Cadeias de conexão do Hub IoT permitem que as soluções e ferramentas de back-end se conectem com segurança aos pontos de extremidade voltados para o dispositivo em um hub IoT. Essas soluções e ferramentas gerenciam o hub IoT e os dispositivos conectados a ele.

## <a name="default-component"></a>Componente padrão

Todos os [modelos de dispositivo](#device-model) têm um componente padrão. Um modelo de dispositivo simples tem apenas um componente padrão-tal modelo também é conhecido como um dispositivo sem componente. Um modelo mais complexo tem vários componentes aninhados sob o componente padrão.

## <a name="device-certification"></a>Certificado de dispositivo

O programa de certificação de dispositivo do IoT Plug and Play verifica se um dispositivo atende aos requisitos de certificação do IoT Plug and Play. Você pode adicionar um dispositivo certificado ao [catálogo de dispositivos Azure Certified para IoT](https://aka.ms/devicecatalog).

## <a name="device-model"></a>Modelo do dispositivo

Um modelo de dispositivo descreve um [dispositivo Plug and Play IOT](#iot-plug-and-play-device) e define os [componentes](#component) que compõem o dispositivo. Um modelo de dispositivo simples não tem componentes separados e contém uma definição para uma única interface. A ferramenta Azure IoT Explorer mostra um modelo simples como tendo um único [componente padrão](#default-component).

Um modelo de dispositivo mais complexo inclui vários componentes. Um modelo de dispositivo geralmente corresponde a um dispositivo físico, produto ou SKU. Você usa a [linguagem de definição de gêmeos digital versão 2](#digital-twins-definition-language) para definir um modelo de dispositivo.

## <a name="device-builder"></a>Construtor de dispositivos

Um construtor de dispositivos usa um [modelo de dispositivo](#device-model) e [interfaces](#interface) ao implementar código para ser executado em um dispositivo de [plug and Play de IOT](#iot-plug-and-play-device). Os criadores de dispositivos normalmente usam um dos [SDKs do dispositivo IOT do Azure](#azure-iot-device-sdk) para implementar o cliente do dispositivo, mas isso não é necessário.

## <a name="device-modeling"></a>Modelagem de dispositivo

Um [Construtor de dispositivos](#device-builder) ou [Construtor de módulos](#module-builder)usa a linguagem de definição de [gêmeos digital](#digital-twins-definition-language) para modelar os recursos de um [dispositivo de plug and Play de IOT](#iot-plug-and-play-device). Um [Solution Builder](#solution-builder) pode configurar uma solução de IOT do modelo.

## <a name="digital-twin"></a>Gêmeo digital

Um gêmeo digital é um modelo de um [dispositivo IoT Plug and Play](#iot-plug-and-play-device). Uma teledigital é modelada usando a [linguagem de definição de gêmeos digital](#digital-twins-definition-language). É possível usar os [SDKs do dispositivo IoT do Azure](#azure-iot-device-sdk) para interagir com gêmeos digitais em tempo de execução. Por exemplo, é possível definir um valor da propriedade em um gêmeo digital em um dispositivo e o SDK comunica essa alteração à sua solução de IoT na nuvem.

## <a name="digital-twin-change-events"></a>Eventos de alteração de gêmeo digital

Quando um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) é conectado a um [hub IoT](#azure-iot-hub), o hub pode usar a funcionalidade de roteamento para enviar notificações de alterações de gêmeo digital. Por exemplo, sempre que um valor de [Propriedade](#properties) é alterado em um dispositivo, o Hub IOT pode enviar uma notificação para um ponto de extremidade, como um hub de eventos.

## <a name="digital-twins-definition-language"></a>Linguagem de definição de gêmeos digital

Uma linguagem para descrever modelos e interfaces para [dispositivos IoT Plug and Play](#iot-plug-and-play-device). Use a [linguagem de definição de gêmeos digital versão 2](https://github.com/Azure/opendigitaltwins-dtdl) para descrever os recursos de uma [teledigital](#digital-twin) e habilitar a plataforma IOT e as soluções de IOT para aproveitar a semântica da entidade.

## <a name="digital-twin-route"></a>Rota do gêmeo digital

Uma rota configurada em um [Hub IOT](#azure-iot-hub) para entregar [eventos de alteração de troca digital](#digital-twin-change-events) e ponto de extremidade, como um hub de eventos.

## <a name="interface"></a>Interface

Uma interface descreve as funcionalidades relacionadas implementadas por um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) ou pelo [gêmeo digital](#digital-twin). Você pode reutilizar interfaces em diferentes [modelos de dispositivo](#device-model). Quando uma interface é usada em um modelo de dispositivo, ela define um [componente](#component) do dispositivo. Um dispositivo simples contém apenas uma interface padrão.

## <a name="iot-hub-query-language"></a>Linguagem de consulta do Hub IoT

A linguagem de consulta do Hub IoT é usada para várias finalidades. Por exemplo, é possível usar a linguagem para pesquisar dispositivos registrados no Hub IoT ou refinar o comportamento do [roteamento de gêmeo digital](#digital-twin-route).

## <a name="iot-plug-and-play-bridge"></a>Ponte do IoT Plug and Play

O IoT Plug and Play Bridge é um aplicativo de software livre que permite que os sensores e periféricos existentes conectados aos gateways do Windows ou do Linux se conectem como [dispositivos de IoT plug and Play](#iot-plug-and-play-device).

## <a name="iot-plug-and-play-device"></a>Dispositivo IoT Plug and Play

Um dispositivo de Plug and Play IoT normalmente é um dispositivo de computação independente de pequena escala que coleta dados ou controla outros dispositivos, e que executa software ou firmware que implementa um [modelo de dispositivo](#device-model).  Por exemplo, um dispositivo IoT Plug and Play pode ser um dispositivo de monitoramento ambiental ou um controlador para um sistema de irrigação de agricultura inteligente. Um dispositivo IoT Plug and Play pode ser implementado diretamente ou como um módulo IoT Edge. É possível escrever uma solução de IoT hospedada na nuvem para comandar, controlar e receber dados de dispositivos IoT Plug and Play.

## <a name="iot-plug-and-play-conventions"></a>Convenções do IoT Plug and Play

Os [dispositivos](#iot-plug-and-play-device) de plug and Play IOT devem seguir um conjunto de [convenções](concepts-convention.md) quando trocam dados com uma solução.

## <a name="model-id"></a>ID do Modelo

Quando um dispositivo de Plug and Play de IoT se conecta a um hub IoT, ele envia a **ID de modelo** do modelo [DTDL](#digital-twins-definition-language) que ele implementa. Isso permite que a solução Localize o modelo do dispositivo.

## <a name="model-repository"></a>Repositório de modelos

Um [repositório de modelos](concepts-model-repository.md) armazena modelos e [interfaces](#interface)de [dispositivo](#device-model) .

## <a name="model-repository-rest-api"></a>API REST do repositório de modelos

Uma API para gerenciar e interagir com o repositório de modelos. Por exemplo, você pode usar a API para adicionar e Pesquisar [modelos de dispositivo](#device-model).

## <a name="module-builder"></a>Construtor de módulo

Um construtor de módulo usa um [modelo de dispositivo](#device-model) e [interfaces](#interface) ao implementar código para ser executado em um dispositivo de [plug and Play de IOT](#iot-plug-and-play-device). Os construtores de módulo implementam o código como um módulo ou um módulo IoT Edge para implantar no tempo de execução de IoT Edge em um dispositivo.

## <a name="properties"></a>Propriedades

As propriedades são campos de dados definidos em uma [interface](#interface) que representam algum estado de uma gêmeo digital. É possível declarar propriedades como somente leitura ou graváveis. Propriedades somente leitura, como número de série, são definidas pelo código em execução no próprio dispositivo [IoT Plug and Play](#iot-plug-and-play-device).  As propriedades graváveis, como um limite de alarme, normalmente são definidas com base na solução de IoT baseada em nuvem.

## <a name="shared-access-signature"></a>Assinatura de acesso compartilhado

As Assinaturas de acesso compartilhado são um mecanismo de autenticação baseado em hashes seguros SHA-256 ou URIs. A autenticação da assinatura de acesso compartilhado tem dois componentes: uma política de acesso compartilhado e uma assinatura de acesso compartilhado (normalmente chamada de token). Um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) usa uma assinatura de acesso compartilhado para se autenticar com um [hub IoT](#azure-iot-hub).

## <a name="solution-builder"></a>Solution Builder

Um Solution Builder cria o back-end da solução. Um Solution Builder normalmente funciona com recursos do Azure, como o [Hub IOT](#azure-iot-hub) e [repositórios de modelo](#model-repository).

## <a name="telemetry"></a>Telemetria

Os campos de telemetria definidos em uma [interface](#interface) representam medidas. Essas medidas são normalmente valores como leituras de sensor enviadas pelo [dispositivo IoT Plug and Play](#iot-plug-and-play-device) como um fluxo de dados.
