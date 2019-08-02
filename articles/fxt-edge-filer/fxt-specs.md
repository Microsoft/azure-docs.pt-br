---
title: Especificações do Microsoft Azure FXT Edge Filer | Microsoft Docs
description: Especificações físicas e ambientais para o hardware do Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 0679bce8eae515aa6b90e34fcfd15ee9b4e56b31
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542884"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Especificações do Azure FXT Edge Filer

Este artigo explica as especificações de hardware exigidas para os nós de hardware do Azure FXT Edge Filer. Na prática, três ou mais nós são configurados em conjunto para formar o sistema de cache clusterizado.

## <a name="hardware-specifications"></a>Especificações de hardware

| Componente | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| Núcleos de CPU |  16 | 16 |
| DRAM  | 1536 GB | 768 GB |
| Portas de rede | 6 x 25/10 Gb + 2 x 1 Gb | 6 x 25/10 Gb + 2 x 1 Gb |
| Capacidade do SSD NVMe | 25,6 TB | 12,8 TB |

## <a name="drive-specifications"></a>Especificações da unidade

O sistema tem dez compartimentos de disco que podem ser acessados pela parte da frente. Cada unidade preenchida tem um rótulo à direita com informações sobre sua capacidade. 

Os números das unidades estão impressos no espaço entre elas. No Azure FXT Edge Filer, a unidade 0 é aquela no canto superior esquerdo e a unidade 1, a que fica imediatamente abaixo dela.

![foto de um compartimento de disco rígido no gabinete do FXT mostrando os números das unidades e os rótulos de capacidade](media/fxt-drives-photo.png)

| Números de unidade    |  Uso   |  Especificações |
|------------------|--------|-----------------|
| 0, 1             | SO     | SSD SATA de 480 GB |
| 2, 3, 4, 5, 6, 7, 8, 9 | Dados   | FXT 6600: SSD NVMe de 3,2 TB <br> FXT 6400: SSD NVMe de 1,6 TB |


## <a name="dimensions-and-weight"></a>Dimensões e peso

O Azure FXT Edge Filer foi projetado para caber em um rack de equipamento padrão de 19 pol. e tem a altura de uma unidade de rack (1U). 

<!-- 10x2.5 inches version -->

| Dimensões do Filer           |                          |
|-----------------------------|--------------------------|
| Altura                      | 42,8 mm (1,68 polegadas)    |
| Largura (incluindo suportes adicionais) | 482 mm (18,97 polegadas)  |
| Largura, compartimento principal      | 434 mm (17,08 polegadas) |
| Profundidade, do suporte adicional até a parte traseira do compartimento principal                   | 733,82 mm (29,61 polegadas) |
| Profundidade, do suporte adicional até a ponta traseira mais distante                 | 772,67 mm (30,42 polegadas) |
| Profundidade, do suporte adicional até a ponta dianteira mais distante, sem contar a borda | 22 mm (0,87 polegada)  |
| Profundidade, do suporte adicional até a ponta dianteira mais distante, contando a borda    | 35,84 mm (1,41 polegada) |

| Peso | |
|-----------------|----------------------|
| Peso do nó (sem embalagem, sem acessórios) | 40 lb (18,1 kg) |
| Peso líquido (sem embalagem, incluindo acessórios) | 51 lb (23,1 kg)|
| Peso bruto (como foi enviado, incluindo toda a embalagem) |  64 lb (29 kg) |

### <a name="shipping-dimensions"></a>Dimensões de envio

| Dimensão do pacote | Milímetros | Polegadas |
|-------------------|-------------|--------|
| Altura            | 311,2       | 12,25 |
| Largura             | 642,8       | 25,31 |
| Comprimento            | 1\.051,1     | 41,38 |

## <a name="power-and-thermal-specifications"></a>Potência e especificações térmicas

Esta seção indica as classificações e medidas de potência do Azure FXT Edge Filer.

### <a name="nameplate-ratings"></a>Corrente nominal

| Corrente nominal dos modelos da série FXT 6000 |
|----------------|
| 100 V a 240 V~    |
| 10 A a 5 A (X2)  |
| 50 Hz/60 Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Medidas térmicas e de potência 

Os nós do Azure FXT Edge Filer usam ventoinhas de diferentes velocidades, ou seja, a potência depende da temperatura e da carga. As ventoinhas podem atingir sua velocidade máxima em determinadas combinações de carga e temperaturas elevadas. 

Estes gráficos mostram o consumo de energia e as medidas térmicas em combinações de frequência e voltagem usadas comumente. 

| Potência do FXT 6600 em temperatura ambiente <br />(22 °C, 71,6 °F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Voltagem (V) | 100 | 120 | 208 | 230 | 240 | 
| Frequência (Hz) | 60 | 60 | 60 | 50 | 50 |
| Corrente (A) | 5,02 | 4,16 |2,40 | 2,20 | 2.16 |
| Potência aparente (VA) | 502 | 499 | 499 | 506 | 518|
| Fator de potência | 0.99 | 0.99 |0.98 | 0.98 | 0.98 |
| Potência real (W) | 497 |494 | 489 | 496 | 508 |
| Dissipação térmica (BTU/h) |1\.696 | 1\.686 | 1\.669 | 1\.692 | 1\.733 |

| Potência do FXT 6600 com a ventoinha em velocidade máxima | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Voltagem (V) | 100 |120 | 208 | 230 | 240| 
| Frequência (Hz) | 60 | 60 | 60 | 50 | 50 |
| Corrente (A) | 5,98 | 5,01 | 2.81 | 2.55 | 2,48 |
| Potência aparente (VA) | 598 | 601 | 584 | 587 | 595 |
| Fator de potência | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 |
| Potência real (W) | 592 | 595 | 573 | 575 | 583 |
| Dissipação térmica (BTU/h) | 2020 |2031 | 1\.954 | 1\.961 | 1990 |

| Potência do FXT 6400 em temperatura ambiente <br />(22 °C, 71,6 °F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Voltagem (V) | 100 | 120 | 208 | 230 | 240 |
| Frequência (Hz) |60 | 60 | 60 | 50 | 50 |
| Corrente (A) | 4,63 | 3,86 | 2,24 | 2,04 | 1,94 |
| Potência aparente (VA) | 463 | 463 | 466 | 469 | 466 |
| Fator de potência | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 | 
| Potência real (W) | 458 | 459 | 457 | 460 | 456 |
| Dissipação térmica (BTU/h) | 1\.564 | 1\.565 | 1\.558 | 1\.569 | 1\.557 |

| Potência do FXT 6400 com a ventoinha em velocidade máxima | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Voltagem (V) | 100 | 120 | 208 | 230 | 240 |
| Frequência (Hz) | 60 | 60 | 60 | 50 | 50 |
| Corrente (A) | 5.15 | 4,28 | 2,48 | 2,28 | 2,13 |
| Potência aparente (VA) | 515 | 514 | 516 | 524 | 511 |
| Fator de potência | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 |
| Potência real (W) | 510 | 508 | 506 | 514 | 501 |
| Dissipação térmica (BTU/h) | 1\.740 | 1\.735 | 1\.725 | 1753 | 1\.709 |

## <a name="environmental-requirements"></a>Requisitos ambientais

Esta seção fornece especificações para o ambiente do hardware.

### <a name="temperature-and-humidity"></a>Temperatura e umidade

| Atributo ambiental   | Intervalo operacional                   | Intervalo não operacional         |
|---------------------------|-----------------------------------|-----------------------------|
| Faixa de temperatura ambiente | 10 °C a 35 °C (50 – 86 °F)          | -40 °C a 65 °C (-40 – 149 °F) |
| Umidade relativa do ambiente | 10% – 80% sem condensação          | 5% – 95% sem condensação     |
| Ponto de condensação máximo         | 29° C (84° F)                       | 33 °C (91 °F)                 |
| Altitude                  | até 3.048 metros (10.000 pés), sujeito à revogação da classificação de temperatura indicada abaixo | até 12.000 metros (39.370 pés) |

> [!NOTE] 
> **Revogação da classificação de temperatura da altitude:** a temperatura máxima deve ser reduzida em 1 °C a cada 300 m (1 °F/547 pés) acima de 950 m (3.117 pés).

### <a name="airflow-shock-and-vibration"></a>Fluxo de ar, choque e vibração 

| Atributo         | Especificação |
|-------------------|---------------|
| Fluxo de ar                    | O ar do sistema flui da frente para a traseira. O sistema deve ser operado com uma instalação de exaustão traseira de baixa pressão. |
| Choque, operacional         | 6 G por 11 milissegundos (testados em 6 orientações) |
| Choque, não operacional     | 71 G por 2 milissegundos (testados em 6 orientações) |
| Vibração, operacional     | 0,26 G<sub>RMS</sub> 5 Hz a 350 Hz aleatório         |
| Vibração, não operacional | 1,88 G<sub>RMS</sub> 10 Hz a 500 Hz por 15 minutos (todos os seis lados testados)  |

## <a name="safety-regulation-compliance"></a>Conformidade com a norma de segurança 

O Azure FXT Edge Filer está em conformidade com as regulamentações listadas. 

| Categoria       | Especificação regulatória | 
|----------------|--------------------------|
| Segurança geral | EN 60950-1:2006 + A1:2010 + A2:2013 + A11:2009 + A12:2011/IEC 60950-1:2005 ed2 + A1:2009 +A2: 2013 <br>EN 62311:2008 | 
| EMC            | FCC A, ICES-003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (classe D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energia         | Regulamento da Comissão (UE) n.º 617/2013  |
| RoHS           |    EN 50581:2012   |