---
title: Visão geral dos tipos de dispositivo IoT do Azure
description: Conheça os diferentes tipos de dispositivo com suporte do Azure IoT e as ferramentas disponíveis.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: aa99594fe3de98635e37d15beebf015f15dc4f64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654003"
---
# <a name="overview-of-azure-iot-device-types"></a>Visão geral dos tipos de dispositivo IoT do Azure
Os dispositivos IoT existem em uma ampla seleção de plataformas de hardware. Há pequenas MCUs de 8 bits até as CPUs x86 mais recentes, conforme encontradas em um computador desktop. Muitas variáveis fatoram a decisão de qual hardware você escolher para um dispositivo IoT e este artigo descreveu algumas das principais diferenças.

## <a name="key-hardware-differentiators"></a>Principais diferenciadores de hardware
Alguns fatores importantes ao escolher seu hardware são custo, consumo de energia, rede e entradas e saídas disponíveis.

* **Custo:** Dispositivos mais baratos são normalmente usados ao produzir o produto final em massa. No entanto, a desvantagem é que o desenvolvimento do dispositivo pode ser mais caro, considerando o dispositivo altamente restrito. O custo de desenvolvimento pode ser distribuído em todos os dispositivos produzidos para que o custo de desenvolvimento por unidade seja baixo.

* **Energia:** A quantidade de energia que um dispositivo consumirá é importante se o dispositivo estiver utilizando baterias e não estiver conectado à grade de energia. Os MCUs geralmente são projetados para cenários de energia menores e podem ser uma opção melhor para estender a vida útil da bateria.

* **Acesso à rede:** Há várias maneiras de conectar um dispositivo a um serviço de nuvem. Ethernet, Wi-Fi e celular e algumas das opções disponíveis. O tipo de conexão que você escolher dependerá do local em que o dispositivo está implantado e de como ele é usado. Por exemplo, o celular pode ser uma opção atraente, considerando a cobertura alta, no entanto, para dispositivos com alto tráfego, ele pode ser caro. A Ethernet conectada fornece custos de dados mais baratos, mas com a desvantagem de ser menos portátil.

* **Entrada e saídas:** As entradas e saídas disponíveis no dispositivo afetam diretamente os recursos operacionais dos dispositivos. Um microcontrolador normalmente terá muitas funções de e/s criadas diretamente no chip e fornecerá uma ampla variedade de sensores para se conectar diretamente.

## <a name="microcontrollers-vs-microprocessors"></a>Microcontroladores vs microprocessadores
Os dispositivos IoT podem ser separados em duas grandes categorias, microcontroladores (MCUs) e microprocessadores (MPUs).

**MCUs** são menos caros e mais simples de operar do que MPUs. Uma MCU conterá muitas das funções, como memória, interfaces e e/s no próprio chip. Uma MPU irá desenhar essa funcionalidade de componentes em chips com suporte. Uma MCU geralmente usará um sistema operacional (RTOS) em tempo real ou executará bare-metal (sem sistema operacional) e fornecerá resposta em tempo real e reações altamente determinísticas a eventos externos.

O **MPUs** geralmente executará um so de uso geral, como Windows, Linux ou MacOSX, que fornece uma resposta em tempo real não determinística. Normalmente, não há garantia de quando uma tarefa será concluída. 

:::image type="content" border="false" source="media/concepts-iot-device-types/mcu-vs-mpu.png" alt-text="MCU versus MPU":::

Veja abaixo uma tabela que mostra algumas das diferenças de definição entre uma MCU e um sistema baseado em MPU:

||Microcontroller (MCU)|Microprocessador (MPU)|
|-|-|-|
|**CPU**| Menor que | Mais |
|**RAM**| Menor que | Mais |
|**Flash**| Menor que | Mais |
|**SO**| Não ou RTOS | Uso Geral |
|**Dificuldade no desenvolvimento**| Força |  Fáceis |
|**Consumo de energia**| Mais baixo | Mais alto |
|**Custo**| Mais baixo | Mais alto |
|**Determinística**| Sim | Sem exceções|
|**Tamanho do dispositivo**| Menor | Influência |
