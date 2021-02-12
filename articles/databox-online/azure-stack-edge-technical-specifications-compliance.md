---
title: Especificações técnicas e conformidade do Microsoft Azure Stack Edge pro | Microsoft Docs
description: Saiba mais sobre as especificações técnicas e a conformidade do Azure Stack Edge pro
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: 8b36bb34f4c9081d807998cb8287797443625a6b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460299"
---
# <a name="azure-stack-edge-pro-technical-specifications"></a>Especificações técnicas do Azure Stack Edge pro

Os componentes de hardware do seu dispositivo Microsoft Azure Stack Edge pro aderem às especificações técnicas e aos padrões regulatórios descritos neste artigo. As especificações técnicas descrevem as unidades de fonte de alimentação (PSUs), a capacidade de armazenamento, os compartimentos e os padrões ambientais.

## <a name="compute-memory-specifications"></a>Computação, especificações de memória

O dispositivo pro Edge Azure Stack tem as seguintes especificações para computação e memória:

| Especificação           | Valor                  |
|-------------------------|----------------------------|
| CPU    | CPU 2 X 10 Core Intel Xeon prata 4114 2.2 G                    |
| Memória              | 128 GB de RAM (8x 16 GB RDIMM)                 |

## <a name="fpga-specifications"></a>Especificações de FPGA

Uma FPGA (matriz de portão programável) de campo é incluída em cada dispositivo Azure Stack Edge pro que habilita cenários de Machine Learning (ML).

| Especificação           | Valor                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> Os modelos de DNN (rede neural profunda) disponíveis são os mesmos que os [suportados pelas instâncias do Cloud FPGA](../machine-learning/how-to-deploy-fpga-web-service.md#fpga-support-in-azure).|

## <a name="power-supply-unit-specifications"></a>Especificações da unidade de fonte de alimentação

O dispositivo pro Edge Azure Stack tem duas PSUs (unidades de alimentação de energia) de 100-240 V com ventiladores de alto desempenho. Essas duas PSUs oferecem uma configuração de alimentação redundante. Se uma PSU falhar, o dispositivo continuará a funcionar normalmente em outra PSU até que o módulo com falha seja substituído. A seguinte tabela lista as especificações técnicas associadas das PSUs.

| Especificação           | 750 W PSU                  |
|-------------------------|----------------------------|
| Alimentação de saída máxima    | 750 W                     |
| Frequência               | 50/60 Hz                   |
| Seleção de faixa de tensão | Variação automática: 100-240 V AC |
| Conectado com a máquina ligada           | Sim                        |

### <a name="azure-stack-edge-pro-power-cord-specifications-by-region"></a>Especificações do Azure Stack o cabo de alimentação do Edge pro por região

Seu dispositivo Azure Stack Edge pro precisa de um cabo de alimentação que varia de acordo com sua região do Azure.
Para obter especificações técnicas de todos os cabos de energia com suporte, consulte [especificações de Azure Stack o cabo de alimentação do Edge pro por região](azure-stack-edge-technical-specifications-power-cords-regional.md).

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Azure Stack Edge Pro device.-->

## <a name="network-interface-specifications"></a>Especificações da interface de rede

O dispositivo pro Edge Azure Stack tem seis interfaces de rede, PORT1-PORT6.

| Especificação           | Descrição                 |
|-------------------------|----------------------------|
|  Interfaces de rede    | 2 interfaces de 1 GbE – 1 gerenciamento, não configurável pelo usuário, usado para configuração inicial. A outra interface é configurável pelo usuário e pode ser usada para transferência de dados e é DHCP por padrão. <br>2 interfaces de 25 GbE – Elas também podem ser operadas como interfaces de 10 GbE. Essas interfaces de dados podem ser configuradas pelo usuário como DHCP (padrão) ou estáticas. <br> 2 interfaces de 25 GbE – Essas interfaces de dados podem ser configuradas pelo usuário como DHCP (padrão) ou estáticas.                  |

Os adaptadores de rede usados são: 

| Especificação           | Descrição                 |
|-------------------------|----------------------------|
|Placa auxiliar de rede (rNDC) |QLogic FastLinQ 41264 Dual Port 25GbE SFP +, Dual Port 1 GbE, rNDC|
|Adaptador de rede PCI |QLogic FastLinQ 41262 zwei portas 25Gbit/s SFP28 adaptador|

Consulte a lista de compatibilidade de hardware do Intel QLogic para o conversor de interface Gigabit compatível (GBIC). O conversor de interface Gigabit (GBIC) não está incluído na entrega do Azure Stack Edge. 

## <a name="storage-specifications"></a>Especificações do armazenamento

Os dispositivos Azure Stack Edge pro têm 9 X 2,5 "NVMe SSDs, cada um com uma capacidade de 1,6 TB. Desses SSDs, 1 é um disco do sistema operacional e os outros 8 são discos de dados. A capacidade utilizável total para o dispositivo é de aproximadamente 12,5 TB. A tabela a seguir tem os detalhes da capacidade de armazenamento do dispositivo.

|     Especificação                          |     Valor             |
|--------------------------------------------|-----------------------|
|    Número de SSDs (unidades de estado sólido)     |    8                  |
|    Capacidade de SSD único                     |    1,6 TB             |
|    Capacidade total                          |    12,8 TB            |
|    Capacidade total utilizável*                  |    ~ 12,5 TB            |

**Algum espaço está reservado para uso interno.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensões do compartimento e especificações de peso

As tabelas a seguir listam as diversas especificações de compartimento para dimensões e peso.

### <a name="enclosure-dimensions"></a>Dimensões do compartimento

A tabela a seguir lista as dimensões do compartimento em milímetros e em polegadas.

|     Compartimento     |     Milímetros     |     Polegadas     |
|-------------------|---------------------|----------------|
|    Altura         |    44,45            |    1,75"          |
|    Largura          |    434,1           |    17,09"          |
|    Comprimento          |    740,4           |    29,15"          |

A tabela a seguir lista as dimensões do pacote de remessa em milímetros e em polegadas.

|     Pacote     |     Milímetros     |     Polegadas     |
|-------------------|---------------------|----------------|
|    Altura         |    311,2            |    12,25          |
|    Largura          |    642,8          |    25,31          |
|    Comprimento          |   1\.051,1          |    41,38          |

### <a name="enclosure-weight"></a>Peso do compartimento

O pacote do dispositivo pesa 61 lbs. e requer duas pessoas para manipulá-lo. O peso do dispositivo depende da configuração do compartimento.

|     Compartimento                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Peso total, incluindo a embalagem       |    61 lbs.          |
|    Peso do dispositivo                       |    35 lbs.          |

## <a name="enclosure-environment-specifications"></a>Especificações do ambiente de compartimento

Esta seção lista as especificações relacionadas ao ambiente de compartimento, como temperatura, umidade e altitude.

### <a name="temperature-and-humidity"></a>Temperatura e umidade

|     Compartimento         |     Faixa de temperatura ambiente     |     Umidade relativa do ambiente     |     Ponto de condensação máximo     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operacional        |    10°C - 35°C (50°F - 86°F)         |    10% – 80% sem condensação         |    29° C (84° F)            |
|    Não operacional    |    -40°C a 65°C (-40°F - 149°F)     |    5% – 95% sem condensação.          |    33 °C (91 °F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Fluxo de ar, altitude, choque, vibração, orientação, segurança e EMC

|     Compartimento                           |     Especificações operacionais                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Fluxo de ar                              |    O ar do sistema flui da frente para a traseira. O sistema deve ser operado com uma instalação de exaustão traseira de baixa pressão. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Altitude operacional máxima        |    3048 metros (10.000 pés) com a temperatura de operação máxima desclassificada determinada pelo [especificações de temperatura operacional](#operating-temperature-de-rating-specifications).                                                                                |
|    Altitude máxima não operacional    |    12.000 metros (39.370 pés)                                                                                                                                                                                         |
|    Choque, operacional                   |    6 G por 11 milissegundos em 6 orientações                                                                                                                                                                         |
|    Choque, não operacional               |    71 G por 2 milissegundos em 6 orientações                                                                                                                                                                           |
|    Vibração, operacional               |    0,26 G<sub>RMS</sub> 5 Hz a 350 Hz aleatório                                                                                                                                                                                     |
|    Vibração, não operacional           |    1,88 G<sub>RMS</sub> 10 Hz a 500 Hz por 15 minutos (todos os seis lados testados)                                                                                                                                                  |
|    Orientação e montagem             |    19" montagem em rack                                                                                                                                                                                        |
|    Segurança e aprovações                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (classe D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energia             |    Regulamento da Comissão (UE) n.º 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>Especificações de desclassificação da temperatura operacional

|     Desclassificação da temperatura operacional     |     Faixa de temperatura ambiente                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Até 35°C (95°F)                       |    A temperatura máxima deve ser reduzida em 1 °C a cada 300 m (1 °F/547 pés) acima de 950 m (3.117 pés).    |
|    35°C a 40°C (95°F a 104°F)            |    A temperatura máxima deve ser reduzida em 1 °C a cada  1°C/175 m (1°F/319 pés) acima de 950 m (3.117 pés).    |
|    40°C a 45°C (104°F a 113°F)           |    A temperatura máxima deve ser reduzida em 1 °C/125 m a cada 125 m (1 °F/228 pés) acima de 950 m (3.117 pés).    |

## <a name="next-steps"></a>Próximas etapas

- [Implantar seu Azure Stack Edge pro](azure-stack-edge-deploy-prep.md)