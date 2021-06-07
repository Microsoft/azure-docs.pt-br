---
title: Configurar regras e ações no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra a você, como um construtor, como configurar regras e ações baseadas em telemetria em seu aplicativo do Azure IoT Central.
author: vavilla
ms.author: vavilla
ms.date: 12/23/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 378a1dbcfbc89cdf9b24dc2490db583f1135b9a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97796781"
---
# <a name="configure-rules"></a>Configurar regras

*Este artigo aplica-se a operadores, construtores e administradores.*

As regras no IoT Central atuam como ferramenta de resposta personalizável que aciona eventos ativamente monitorados nos dispositivos conectados. As seções a seguir descrevem como as regras são avaliadas.

## <a name="select-target-devices"></a>Selecionar dispositivo de destino

Use a seção Dispositivos de destino para selecionar a qual tipo de dispositivos essa regra será aplicada. Os filtros permitem refinar ainda mais os dispositivos que devem ser incluídos. Os filtros usam as propriedades no modelo de dispositivo para filtrar o conjunto de dispositivos. Os próprios filtros não disparam uma ação. Na captura de tela a seguir, os dispositivos direcionados são do tipo de modelo de dispositivo **Refrigerador**. O filtro indica que a regra deve incluir apenas os **Refrigeradores** em que a propriedade **Estado de fabricação** é igual a **Washington**.

![Condições](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Usar várias condições

As condições são as regras acionadas. Atualmente, quando você adiciona várias condições a uma regra, elas ficam logicamente juntas. Em outras palavras, todas as condições devem ser atendidas para que a regra seja avaliada como true.  

Na captura de tela a seguir, as condições verificam quando a temperatura é maior que 70&deg; F e a umidade é menor que 10. Quando ambas as instruções são true, a regra é avaliada como true e dispara uma ação.

![Captura de tela mostra um monitor de refrigerador com condições especificadas para temperatura e umidade.](media/howto-configure-rules/conditions.png)

### <a name="use-a-cloud-property-in-a-value-field"></a>Usar uma propriedade de nuvem em um campo de valor

Você pode fazer referência a uma propriedade de nuvem do modelo de dispositivo no campo **Valor** para uma condição. A propriedade de nuvem e o valor de telemetria devem ter tipos semelhantes. Por exemplo, se **Temperatura** for double, apenas as propriedades de nuvem do tipo double serão exibidas como opções no menu suspenso **Valor**.

Se você escolher um valor de telemetria do tipo de evento, a lista suspensa **Valor** incluirá a opção **Qualquer**. A opção **Qualquer** significa que a regra é acionada quando o aplicativo recebe um evento desse tipo, seja qual for a carga.

## <a name="use-aggregate-windowing"></a>Funções de janelas de agregação

As regras avaliam as janelas de tempo de agregação como janelas em cascata. Na captura de tela abaixo, a janela de tempo é de cinco minutos. A cada cinco minutos, a regra é avaliada nos últimos cinco minutos de dados. Os dados são avaliados apenas uma vez na janela à qual correspondem.

![Janelas em cascata](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Usar regras com os módulos do IoT Edge

Uma restrição se aplica às regras que são aplicadas aos módulos do IoT Edge. As regras na telemetria de diferentes módulos não são avaliadas como regras válidas. Considere o exemplo a seguir. A primeira condição da regra está em uma telemetria de temperatura do Módulo A. A segunda condição da regra está em uma telemetria de umidade no Módulo B. Como as duas condições são de módulos diferentes, esse é um conjunto de condições inválido. A regra não é válida e um erro é gerado ao tentar salvar a regra.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a configurar uma regra do aplicativo Azure IoT Central, pode aprender a [Configurar regras avançadas](howto-configure-rules-advanced.md) usando o Power Automate ou os Aplicativos Lógicos do Azure.
