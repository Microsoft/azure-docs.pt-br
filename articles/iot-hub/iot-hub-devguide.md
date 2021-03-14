---
title: Guia do desenvolvedor para o Hub IoT do Azure | Microsoft Docs
description: O guia do desenvolvedor do Hub IoT do Azure aborda os pontos de extremidade, segurança, Registro de identidade, gerenciamento de dispositivos, métodos diretos, dispositivos gêmeos, uploads de arquivos, trabalhos, linguagem de consulta do Hub IoT e mensagens.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom: mqtt
ms.openlocfilehash: 1fcf560ba1fef137a28ab1872635cc2182cd07e1
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009191"
---
# <a name="azure-iot-hub-developer-guide"></a>Guia do desenvolvedor do Hub IoT do Azure

O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a permitir comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de solução.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O Hub IoT do Azure oferece:

* Proteja as comunicações usando as credenciais de segurança e o controle de acesso por dispositivo.

* Várias opções de comunicação em larga escala do dispositivo para a nuvem e da nuvem para o dispositivo.

* Armazenamento que podem ser consultado com informações de estado por dispositivo e metadados.

* Facilitar a conectividade do dispositivo com as bibliotecas de dispositivo a fim de obter as plataformas e os idiomas mais populares.

Este guia do desenvolvedor do Hub IoT inclui os seguintes artigos:

* [Orientação de comunicação do dispositivo para a nuvem](iot-hub-devguide-d2c-guidance.md) ajuda você a escolher entre mensagens do dispositivo para a nuvem, propriedades reportadas do dispositivo gêmeo ou carregamento do arquivo.

* [Diretrizes de comunicação de nuvem para dispositivo](iot-hub-devguide-c2d-guidance.md) que ajudam você a escolher entre métodos diretos, propriedades desejadas do dispositivo gêmeo e mensagens de nuvem para dispositivo.

* [Mensagens de dispositivo para nuvem e nuvem para dispositivo com IoT Hub](iot-hub-devguide-messaging.md) que descreve os recursos de mensagens (dispositivo para nuvem e nuvem para dispositivo) que o Hub IoT expõe.

  * [Enviar mensagens de dispositivo para nuvem para Hub IoT](iot-hub-devguide-messages-d2c.md).

  * [Ler mensagens do dispositivo para a nuvem do ponto de extremidade interno](iot-hub-devguide-messages-read-builtin.md).

  * [Use pontos de extremidade personalizados e regras de roteamento para mensagens do dispositivo para a nuvem](iot-hub-devguide-messages-read-custom.md).

  * [Enviar mensagens da nuvem para o dispositivo do Hub IOT](iot-hub-devguide-messages-c2d.md).

  * [Criar e ler mensagens do Hub IOT](iot-hub-devguide-messages-construct.md).

* [Carregar arquivos de um dispositivo](iot-hub-devguide-file-upload.md) descreve como você pode carregar arquivos de um dispositivo. O artigo também inclui informações sobre tópicos como as notificações que podem ser enviadas pelo processo de carregamento.

* [Gerenciar identidades do dispositivo no Hub IoT](iot-hub-devguide-identity-registry.md) descreve as informações armazenadas pelo registro de identidade de cada Hub IoT. O artigo também descreve como você pode acessar e modificá-lo.

* [Controlar o acesso ao Hub IoT](iot-hub-devguide-security.md) descreve o modelo de segurança usado para conceder acesso à funcionalidade do Hub IoT para componentes de dispositivos e da nuvem. O artigo inclui informações sobre como usar tokens e certificados X.509, e os detalhes das permissões que você pode conceder.

* [Usar dispositivos gêmeos para sincronizar o estado e as configurações](iot-hub-devguide-device-twins.md) descreve o conceito de *dispositivos gêmeos*. O artigo também descreve a funcionalidade que o dispositivo gêmeos expõe, como a sincronização de um dispositivo com seu dispositivo. O artigo inclui informações sobre os dados armazenados em um dispositivo gêmeo.

* [Invocar um método direto em um dispositivo](iot-hub-devguide-direct-methods.md) descreve o ciclo de vida de um método direto. O artigo descreve como invocar métodos em um dispositivo de seu aplicativo de back-end e manipular o método direto no seu dispositivo.

* [Agendar trabalhos em vários dispositivos](iot-hub-devguide-jobs.md) descreve como você pode agendar trabalhos em vários dispositivos. O artigo descreve como enviar trabalhos que executam tarefas, por exemplo, execução de um método direto, atualização de um dispositivo usando um dispositivo gêmeo. Ele também descreve como consultar o status de um trabalho.

* [Referência - escolher um protocolo de comunicação](iot-hub-devguide-protocols.md) descreve os protocolos de comunicação que o Hub IoT dá suporte para comunicação de dispositivo e lista as portas que devem ser abertas.

* [Referência - Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md) descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de runtime e de gerenciamento. O artigo também descreve como você pode criar mais pontos de extremidade adicionais em seu Hub IoT e como usar um gateway de campo para habilitar a conectividade para seus pontos de extremidade de Hub IoT em cenários não padrão.

* [Referência - Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens](iot-hub-devguide-query-language.md) descreve que a linguagem de consulta do Hub IoT permite que você recupere informações do seu Hub sobre seus dispositivos gêmeos e trabalhos.

* [Referência – cotas e limitação](iot-hub-devguide-quotas-throttling.md) resume as cotas definidas no serviço do Hub IoT e a limitação que ocorre ao exceder a cota.

* [Referência - preços](iot-hub-devguide-pricing.md) fornece informações gerais sobre diferentes SKUs e preços do Hub IoT e os detalhes sobre como as várias funcionalidades do Hub IoT são medidas como mensagens pelo Hub IoT.

* [Referência – SDKs de dispositivo e serviço](iot-hub-devguide-sdks.md) lista os SDKs do IoT do Azure para o desenvolvimento de aplicativos de dispositivo e de serviço que interagem com o Hub IoT. O artigo inclui links para documentação online da API.

* [Referência - Suporte ao MQTT do Hub IoT](iot-hub-mqtt-support.md) fornece informações detalhadas sobre como o Hub IoT oferece suporte ao protocolo MQTT. O artigo descreve o suporte para o protocolo MQTT interno para os SDKs do IoT do Azure e fornece informações sobre como usar o protocolo MQTT diretamente.
