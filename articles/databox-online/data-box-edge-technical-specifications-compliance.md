---
title: Conformidade e especificações técnicas de borda da caixa de dados do Microsoft Azure | Microsoft Docs
description: Saiba mais sobre as especificações técnicas e conformidade para a borda da caixa de dados do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 52fb32a8b34c62fe94ab35e2c051d996ab8bef10
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60755127"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Especificações técnicas de borda da caixa de dados do Azure

Os componentes de hardware do seu dispositivo de borda da caixa de dados do Microsoft Azure seguem as especificações técnicas e aos padrões regulatórios descritos neste artigo. As especificações técnicas descrevem as unidades de fonte de alimentação (PSUs), capacidade de armazenamento, compartimentos e padrões ambientais. 

## <a name="power-supply-unit-specifications"></a>Especificações de unidade de fonte de alimentação

O dispositivo de borda da caixa de dados tem dois 100 a 240 V Power unidades de fonte (PSUs) com os fãs de alto desempenho. Dois PSUs fornecem uma configuração de alimentação redundante. Se uma fonte de alimentação falhar, o dispositivo continuará a funcionar normalmente em outra fonte de alimentação até que o módulo com falha seja substituído. A tabela a seguir lista as especificações técnicas dos PSUs.

| Especificação           | 750 W PSU                  |
|-------------------------|----------------------------|
| Alimentação de saída máxima    | 750 W                     |
| Frequência               | 50/60 Hz                   |
| Seleção de faixa de tensão | Variação automática: 100 A 240 V CA |
| Conectado com a máquina ligada           | Sim                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>Especificações do armazenamento

Os dispositivos de borda da caixa de dados têm 10 X 2,5" NVMe SSDs, cada um com uma capacidade de 1,6 TB. Esse SSDs, 2 são discos do sistema operacional e os outros 8 são discos de dados. A capacidade útil total para o dispositivo é de aproximadamente 12,5 TB. A tabela a seguir tem os detalhes para a capacidade de armazenamento do dispositivo.

|     Especificação                          |     Value             |
|--------------------------------------------|-----------------------|
|    Número de SSDs (unidades de estado sólido)     |    8                  |
|    Capacidade de SSD único                     |    1,6 TB             |
|    Capacidade total                          |    12,8 TB            |
|    Capacidade total utilizável*                  |    ~ 12,5 TB            |

**Algum espaço é reservado para uso interno.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensões do compartimento e especificações de peso

As tabelas a seguir listam as diversas especificações de compartimento para dimensões e peso.

### <a name="enclosure-dimensions"></a>Dimensões do compartimento

A tabela a seguir lista as dimensões do compartimento em milímetros e em polegadas.

|     Compartimento     |     Milímetros     |     Polegadas     |
|-------------------|---------------------|----------------|
|    Altura         |    44.45            |    1.75"          |
|    Largura          |    434.1           |    17.09"          |
|    Comprimento          |    740.4           |    29.15"          |

A tabela a seguir lista as dimensões do pacote de envio em milímetros e em polegadas.

|     Pacote     |     Milímetros     |     Polegadas     |
|-------------------|---------------------|----------------|
|    Altura         |    311.2            |    12.25"          |
|    Largura          |    642.8          |    25.31"          |
|    Comprimento          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>Peso do compartimento

O pacote do dispositivo pondera 66 lbs. e requer duas pessoas para manuseá-lo. O peso do dispositivo depende da configuração do compartimento.

|     Compartimento                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Peso total, incluindo o empacotamento       |    61 lbs.          |
|    Peso do dispositivo                       |    35 lbs.          |

## <a name="enclosure-environment-specifications"></a>Especificações do ambiente de compartimento

Esta seção lista as especificações relacionadas ao ambiente de compartimento, como temperatura, umidade e altitude.

### <a name="temperature-and-humidity"></a>Temperatura e umidade

|     Compartimento         |     Faixa de temperatura ambiente     |     Umidade relativa do ambiente     |     Ponto de Orvalho máximo     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operacional        |    10°C - 35°C (50°F - 86°F)         |    10% a 80% sem condensação.         |    29° C (84° F)            |
|    Não operacional    |    -40 ° C a 65 ° C (-40 ° F - 149 ° F)     |    5% a 95% sem condensação.          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Fluxo de ar, altitude, choque, vibração, orientação, segurança e EMC

|     Compartimento                           |     Especificações operacionais                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Fluxo de ar                              |    O ar do sistema flui da frente para a traseira. O sistema deve ser operado com uma instalação de exaustão traseira de baixa pressão. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Máximo altitude, operacional        |    3048 metros (10.000 pés) com desprovisionar classificada como temperatura máxima é determinado pela [desprovisionar especificações de classificação de temperatura operacional](#operating-temperature-de-rating-specifications).                                                                                |
|    Máximo altitude, não operacional    |    medidores de 12.000 (39.370 pés)                                                                                                                                                                                         |
|    Choque, operacional                   |    6G por 11 milissegundos em 6 orientações                                                                                                                                                                         |
|    Choque, não operacional               |    71 G para 2 milissegundos em 6 orientações                                                                                                                                                                           |
|    Vibração, operacional               |    G 0,26<sub>RMS</sub> 5 Hz a 350 Hz aleatório                                                                                                                                                                                     |
|    Vibração, não operacional           |    1,88 G<sub>RMS</sub> Hz 10 a 500 Hz por 15 minutos (todos os seis lados testados.)                                                                                                                                                  |
|    Orientação e montagem             |    montagem de rack 19"                                                                                                                                                                                        |
|    Segurança e aprovações                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014 / IEC 61000-3-2:2014 (classe D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energia             |    N º de regulamento (UE) de comissão 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Temperatura operacional desprovisionar especificações de classificação

|     Operação de eliminação da classificação de temperatura     |     Faixa de temperatura ambiente                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Até 35 ° C (95° F)                       |    Temperatura máxima é reduzida por 1° C/300 m (de 1° F/547) acima m 950 (3,117 pés).    |
|    35° C a 40° C (95° F a 104° F)            |    Temperatura máxima é reduzida por 1° C/175 m (1° F/319 pés) acima m 950 (3,117 pés).    |
|    40° C a 45° C (104° F a 113° F)           |    Temperatura máxima é reduzida por 1° C/125 m (1° F/228 pés) acima m 950 (3,117 pés).    |


## <a name="next-steps"></a>Próximas etapas

- [Implante seu Azure Data Box Edge](data-box-edge-deploy-prep.md)
