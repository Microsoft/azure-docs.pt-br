---
title: Especificações técnicas e conformidade do Microsoft Azure Stack Microsoft Edge | Microsoft Docs
description: Saiba mais sobre as especificações técnicas e a conformidade para o dispositivo mini R do Azure Stack Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 3a0b87f04e60fd56d543c7c7a752cd788e087c78
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727474"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Especificações técnicas do Azure Stack Edge mini R

Os componentes de hardware de seu dispositivo de borda Microsoft Azure Stack mini R aderem às especificações técnicas e aos padrões regulatórios descritos neste artigo. As especificações técnicas descrevem a CPU, a memória, as unidades de fonte de alimentação (PSUs), a capacidade de armazenamento, as dimensões do compartimento e o peso.


## <a name="compute-memory-specifications"></a>Computação, especificações de memória

O dispositivo mini R do Azure Stack Edge tem as seguintes especificações para computação e memória:

| Especificação           | Valor                  |
|-------------------------|------------------------|
| CPU    | CPU de 16 núcleos, Intel Xeon-D 1577 |
| Memória              | 48 GB de RAM (2400 MT/s)                  |


## <a name="compute-acceleration-specifications"></a>Especificações de aceleração de computação

Uma VPU (unidade de processamento de visão) está incluída em cada dispositivo de borda de Azure Stack do Edge que habilita aplicativos baseados em kubernetes, profundas de rede neural e de pesquisa Visual computacional.

| Especificação           | Valor                  |
|-------------------------|------------------------|
| Cartão de aceleração de computação         | Intel Movidius infinidade X VPU <br> Para obter mais informações, consulte [Intel Movidius infinidade X VPU](https://www.movidius.com/MyriadX) |


## <a name="storage-specifications"></a>Especificações do armazenamento

O dispositivo mini R do Azure Stack Edge tem 1 disco de dados e um disco de inicialização (que funciona como armazenamento do sistema operacional). A tabela a seguir mostra os detalhes da capacidade de armazenamento do dispositivo.

|     Especificação                          |     Valor             |
|--------------------------------------------|-----------------------|
|    Número de SSDs (unidades de estado sólido)     |    2 discos de 1 TB <br> Um disco de dados e um disco de inicialização                  |
|    Capacidade de SSD único                     |    1 TB               |
|    Capacidade total (somente dados)              |    1 TB              |
|    Capacidade total utilizável*                  |    ~ 750 GB        |

**Algum espaço está reservado para uso interno.*

## <a name="network-specifications"></a>Especificações de rede

O dispositivo mini R do Azure Stack Edge tem as seguintes especificações para a rede:


|Especificação  |Valor  |
|---------|---------|
|Interfaces de rede    |2 x 10 GbE SFP + <br> Mostrado como a porta 3 e a porta 4 na interface do usuário local           |
|Interfaces de rede    |2 x 1 GbE RJ45 <br> Mostrado como a porta 1 e a porta 2 na interface do usuário local          |
|Wi-Fi   |802.11ac         |


## <a name="power-supply-unit-specifications"></a>Especificações da unidade de fonte de alimentação

O dispositivo mini R do Azure Stack Edge inclui um adaptador CA externo 85 W para fornecer energia e cobrar a bateria integrada.

A tabela a seguir mostra as especificações da unidade de fonte de alimentação:

| Especificação           | Valor                      |
|-------------------------|----------------------------|
| Alimentação de saída máxima    | 85 W                       |
| Frequência               | 50/60 Hz                   |
| Seleção de faixa de tensão | Variação automática: 100-240 V AC |



## <a name="included-battery"></a>Bateria incluída

O dispositivo mini R do Azure Stack Edge também inclui uma bateria integrada que é cobrada pela fonte de alimentação.

Um tipo adicional de [bateria 2590](https://www.bren-tronics.com/bt-70791ck.html) pode ser usado em conjunto com a bateria integrada para estender o uso do dispositivo entre os encargos. Essa bateria deve estar em conformidade com todas as regulamentações de segurança, transporte e ambientais aplicáveis no país de uso.


| Especificação           | Valor                      |
|-------------------------|----------------------------|
| Capacidade da bateria integrada | 73 WHr                    |

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensões do compartimento e especificações de peso

As tabelas a seguir listam as diversas especificações de compartimento para dimensões e peso.

### <a name="enclosure-dimensions"></a>Dimensões do compartimento

A tabela a seguir lista as dimensões do dispositivo e do USP com o caso resistente em milímetros e polegadas.

|     Compartimento     |     Milímetros     |     Polegadas     |
|-------------------|---------------------|----------------|
|    Altura         |    68            |    2.68          |
|    Largura          |    208          |      8,19          |
|    Comprimento          |   259           |    10,20          |


### <a name="enclosure-weight"></a>Peso do compartimento

A tabela a seguir lista o peso do dispositivo, incluindo a bateria.

|     Compartimento                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Peso total do dispositivo     |    7 kg.          |

## <a name="enclosure-environment-specifications"></a>Especificações do ambiente de compartimento


Esta seção lista as especificações relacionadas ao ambiente de compartimento, como temperatura, umidade e altitude.


|     Especificações             |     Descrição                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     Intervalo de temperatura          |     0 – 43 ° C (operacional)                                              |
|     Vibração                  |     MIL-método STD-810 514,7 *<br> Procedimento I CAT 4, 20                  |
|     Choque                      |     MIL-método STD-810 516,7 *<br> Procedimento IV, logística                 |
|     Altitude                   |     Operacional: 10.000 pés<br> Não operacional: 40.000 pés          |

**Todas as referências são para MIL-STD-810G alterar 1 (2014)*


## <a name="next-steps"></a>Próximas etapas

- [Implantar o seu Azure Stack Edge mini R](azure-stack-edge-placeholder.md)
