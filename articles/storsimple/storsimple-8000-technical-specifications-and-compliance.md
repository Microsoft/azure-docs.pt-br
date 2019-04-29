---
title: Especificações técnicas do StorSimple | Microsoft Docs
description: Descreve as especificações técnicas e as informações de conformidade de padrões normativos para os componentes de hardware do StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: b36d721896bd7b4f95d831eded500a96969937c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631882"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Especificações técnicas e conformidade para o dispositivo StorSimple

## <a name="overview"></a>Visão geral

Os componentes de hardware do dispositivo Microsoft Azure StorSimple aderem às especificações técnicas e aos padrões regulatórios descritos neste artigo. As especificações técnicas descrevem os PCMs (Módulos de Alimentação e de Refrigeração), as unidades de disco, a capacidade de armazenamento e os compartimentos. As informações de conformidade abrangem itens como padrões internacionais, segurança e emissões e cabeamento.

## <a name="power-and-cooling-module-specifications"></a>Especificações do Módulo de Alimentação e de Refrigeração

O dispositivo StorSimple tem dois PCMs (Módulos de Alimentação e Refrigeração) em conformidade com SBB e com ventilador duplo de 100–240 V. Isso oferece uma configuração de alimentação redundante. Se um PCM falhar, o dispositivo continuará a funcionar normalmente em outro PCM até que o módulo com falha seja substituído.

O compartimento EBOD usa um PCM de 580 W e o compartimento principal usa um PCM de 764 W. As tabelas a seguir listam as especificações técnicas associadas aos PCMs.

| Especificação | PCM de 580 W (EBOD) | PCM de 764 W (Primário) |
| --- | --- | --- |
| Alimentação de saída máxima |580 W |764 |
| Frequência |50/60 Hz |50/60 Hz |
| Seleção de faixa de tensão |Variação automática: 90 – 264 V AC, 47/63 Hz |Variação automática: 90- 264 V AC, 47/63 Hz |
| Corrente de entrada máxima |20 A |20 A |
| Correção de fator de potência |Voltagem de entrada nominal >95% |Voltagem de entrada nominal >95% |
| Harmônicas |Atende EN61000-3-2 |Atende EN61000-3-2 |
| Saída |Tensão de espera de 5V \@ 2.0 A |Tensão de espera de 5V \@ 2.7 A |
| +5V \@ 42 A |+5V \@ 40 A | |
| +12V \@ 38 A |+12V \@ 38 A | |
| Conectado com a máquina ligada |Sim |Sim |
| Interruptores e LEDs |Interruptor LIGA/DESLIGA CA e quatro LEDs indicadores de status |Interruptor LIGA/DESLIGA CA e seis LEDs indicadores de status |
| Refrigeração do compartimento |Ventiladores de refrigeração axial com controle de velocidade variável de ventilador |Ventiladores de refrigeração axial com controle de velocidade variável de ventilador |

## <a name="power-consumption-statistics"></a>Estatísticas de consumo de energia

A tabela a seguir lista os dados de consumo de energia típicos (os valores reais podem variar desde a publicação) para os diversos modelos do dispositivo StorSimple.

| Condições | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Ventiladores lentos, unidades ociosas |1,45 A |0,31 kW |1057,76 BTU/h |3,19 A |0,34 kW |1160,13 BTU/h |
|  Ventiladores lentos, unidades acessando |1,54 A |0,33 kW |1126,01 BTU/h |3,27 A |0,36 kW |1228,37 BTU/h |
|  Ventiladores rápidos, unidades ociosas, dois PSUs ligados |2,14 A |0,49 kW |1671,95 BTU/h |4,99 A |0,54 kW |1842,56 BTU/h |
|  Ventiladores rápidos, unidades ociosas, um PSU ligado um ocioso |2,05 A |0,48 kW |1637,83 BTU/h |4,58 A |0,50 kW |1706,07 BTU/h |
|  Ventiladores rápidos, unidades acessando, dois PSUs ligados |2,26 A |0,51 kW |1740,19 BTU/h |4,95 A |0,54 kW |1842,56 BTU/h |
|  Ventiladores rápidos, unidades acessando, um PSU ligado um ocioso |2,14 A |0,49 kW |1671,95 BTU/h |4,81 A |0,53 kW |1808,44 BTU/h |

## <a name="disk-drive-specifications"></a>Especificações da unidade de disco

Seu dispositivo StorSimple oferece suporte a até 12 unidades de disco SAS (Serial Attached SCSI) fator forma de 3,5 polegadas. As unidades reais podem ser uma combinação de unidades de estado sólido (SSDs) ou de unidades de disco rígido (HDDs), dependendo da configuração do produto. Os 12 slots de unidade de disco estão localizados em uma configuração de 3 a 4 na frente do compartimento. O compartimento EBOD permite armazenamento adicional para outras 12 unidades de disco. Elas sempre serão HDDs.

## <a name="storage-specifications"></a>Especificações do armazenamento

Os dispositivos StorSimple têm uma mescla de unidades de disco rígido e de estado sólido para o 8100 e o 8600. A capacidade utilizável total para o 8100 e 8600 é cerca de 15 TB e 38 TB, respectivamente. A tabela a seguir documenta os detalhes da SSD, da HDD e da capacidade de nuvem no contexto da capacidade da solução StorSimple.

| Modelo/capacidade do dispositivo | 8100 | 8600 |
| --- | --- | --- |
| Número de HDDs (unidades de disco rígido) |8 |19 |
| Número de SSDs (unidades de estado sólido) |4 |5 |
| Capacidade de HDD único |4 TB |4 TB |
| Capacidade de SSD único |400 GB |800 GB |
| Capacidade reserva |4 TB |4 TB |
| Capacidade utilizável do HDD |14 TB |36 TB |
| Capacidade utilizável do SSD |800 GB |2 TB |
| Capacidade total utilizável* |~ 15 TB |~ 38 TB |
| Capacidade máxima da solução (incluindo a nuvem) |200 TB |500 TB |

<sup>* </sup>- *A capacidade útil total inclui a capacidade disponível para dados, metadados e buffers. Você pode provisionar volumes localmente fixados de até 8,5 TB no dispositivo 8100 ou até 22,5 TB no dispositivo 8600 maior. Para obter mais informações, vá para [StorSimple locally pinned volumes](storsimple-8000-local-volume-faq.md) (Volumes localmente fixados do StorSimple).*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensões do compartimento e especificações de peso

As tabelas a seguir listam as diversas especificações de compartimento para dimensões e peso.

### <a name="enclosure-dimensions"></a>Dimensões do compartimento

A tabela a seguir lista as dimensões do compartimento em milímetros e em polegadas.

| Compartimento | Milímetros | Polegadas |
| --- | --- | --- |
| Altura |87,9 |3,46 |
| Largura do flange de montagem |483 |19,02 |
| Largura do corpo do compartimento |443 |17,44 |
| Profundidade do flange de montagem frontal para a extremidade do corpo do compartimento |577 |22,72 |
| Profundidade do painel de controle para a extremidade mais distante do compartimento |630,5 |24,82 |
| Profundidade do flange de montagem até a extremidade mais distante do compartimento |603 |23,74 |

### <a name="enclosure-weight"></a>Peso do compartimento

Dependendo da configuração, um compartimento primário totalmente carregado pode pesar de 21 a 33 kg e requer duas pessoas para carregá-lo.

| Compartimento | Peso |
| --- | --- |
| Peso máximo (depende da configuração) |30 kg – 33 kg |
| Vazio (nenhuma unidade ajustada) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Especificações do ambiente de compartimento

Esta seção lista as especificações relacionadas ao ambiente de compartimento. A temperatura, a umidade, a altitude, o choque, a vibração, a orientação, a segurança e a Compatibilidade Eletromagnética (EMC) estão incluídos nesta categoria.

### <a name="temperature-and-humidity"></a>Temperatura e umidade

| Compartimento | Faixa de temperatura ambiente | Umidade relativa do ambiente | Umidade máxima da lâmpada |
| --- | --- | --- | --- |
| Operacional |5° C - 35° C (41° F - 95° F) |20-80% sem condensação- |28° C (82° F) |
| Não operacional |-40° C - 70° C (40° F - 158° F) |5% - 100% sem condensação |29° C (84° F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Fluxo de ar, altitude, choque, vibração, orientação, segurança e EMC

| Compartimento | Especificações operacionais |
| --- | --- |
| Fluxo de ar |O ar do sistema flui da frente para a traseira. O sistema deve ser operado com uma instalação de exaustão traseira de baixa pressão. Pressão traseira criada por portas e obstáculos no rack não devem exceder 5 pascals (medidor de água de 0,5 mm). |
| Altitude, operacional |-30 metros a 3045 metros (-100 pés a 10.000 pés) com a temperatura operacional máxima reduzida em 5°C acima dos 7000 pés. |
| Altitude, não operacional |-305 metros a 12.192 metros (-1.000 pés a 40.000 pés) |
| Choque, operacional |5 g em 10 ms a ½ seno |
| Choque, não operacional |30 g em 10 ms a ½ seno |
| Vibração, operacional |0,21g RMS 5-500 Hz aleatório |
| Vibração, não operacional |1,04g RMS 2-200 Hz aleatório |
| Vibração, realocação |Seno de 3g 2-200 Hz |
| Orientação e montagem |Montagem de rack de 19"(2 unidades EIA) |
| Trilhos do rack |Para ajustar racks com profundidade mínima de 700 mm (31,50 polegadas) compatíveis com o IEC 297 |
| Segurança e aprovações |CE e UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| EMC |EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Conformidade com padrões internacionais

O dispositivo Microsoft Azure StorSimple está em conformidade com os seguintes padrões internacionais:  

* CE - EN 60950 - 1
* Relatório CB para IEC 60950-1
* UL e cUL para UL 60950-1

## <a name="safety-compliance"></a>Conformidade de segurança

O dispositivo Microsoft Azure StorSimple atende às seguintes classificações de segurança:

* Aprovação de tipo de produto do sistema: UL, cUL, CE
* Conformidade de segurança: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>Conformidade com EMC

O dispositivo Microsoft Azure StorSimple atende às classificações EMC a seguir.

### <a name="emissions"></a>Emissões

O dispositivo é compatível com EMC em relação aos níveis de emissões conduzidas e irradiadas.

* Níveis de limite de emissões de realizada: CFR 47 Parte 15B classe A EN55022 Classe A CISPR classe A
* Por radiação níveis de limite de emissões: CFR 47 Parte 15B classe A EN55022 Classe A CISPR classe A

### <a name="harmonics-and-flicker"></a>Harmônicas e cintilação

O dispositivo é compatível com EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Níveis de limite de imunidade

O dispositivo é compatível com EN55024.

## <a name="ac-power-cord-compliance"></a>Conformidade do cabo de alimentação de CA

O plugue e o conjunto completo do cabo de alimentação devem atender aos padrões adequados ao país em que o dispositivo está sendo usado, e devem ter aprovações de segurança aceitáveis nesse país. As tabelas a seguir listam os padrões para os EUA e a Europa.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Cabos de alimentação de CA - EUA (devem estar listados no NRTL)

| Componente | Especificação |
| --- | --- |
| Tipo de cabo |SV ou SVT, 18 AWG (mínimo), 3 condutores, 2,0 metros de comprimento máximo |
| Plugue |Plugue de conexão do tipo aterramento NEMA 5-15P com taxa de 120 V, 10 A ou IEC 320 C14, 250 V, 10 A |
| Soquete |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>Cabos de alimentação de CA - Europa

| Componente | Especificação |
| --- | --- |
| Tipo de cabo |Harmonizadas, H05-VVF-3G1.0 |
| Soquete |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Cabos de rede com suporte

Para as interfaces de rede 10 GbE, DATA 2 e DATA 3, consulte a [lista de módulos e cabos de rede com suporte](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Próximas etapas

Agora, você está pronto para implantar um dispositivo StorSimple em seu datacenter. Para saber mais, confira [Implantando o dispositivo local](storsimple-8000-deployment-walkthrough-u2.md).

