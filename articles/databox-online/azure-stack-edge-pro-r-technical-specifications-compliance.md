---
title: Especificações técnicas e conformidade do Microsoft Azure Stack Edge pro R | Microsoft Docs
description: Saiba mais sobre as especificações técnicas e a conformidade do seu dispositivo pro R Azure Stack Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: aa1b861555cff65c9e432ea711af3f7c6e410625
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109158"
---
# <a name="azure-stack-edge-pro-r-technical-specifications"></a>Especificações técnicas do Azure Stack Edge pro R

Os componentes de hardware do seu dispositivo Azure Stack Edge pro R aderem às especificações técnicas descritas neste artigo. As especificações técnicas descrevem as unidades de fonte de alimentação (PSUs), a capacidade de armazenamento, os compartimentos e os padrões ambientais.


## <a name="compute-memory-specifications"></a>Computação, especificações de memória

O dispositivo R Azure Stack Edge Pro tem as seguintes especificações para computação e memória:

| Especificação       | Valor                  |
|---------------------|------------------------|
| CPU    | 2 X Intel Xeon prata 4114 CPU<br>20 núcleos de físicos (10 por CPU)<br>40 núcleos lógicos (vCPUs) (20 por CPU)  |
| Memória              | 256 GB de RAM (2666 MT/s)     |


## <a name="compute-acceleration-specifications"></a>Especificações de aceleração de computação

Uma GPU (unidade de processamento gráfico) está incluída em cada dispositivo que habilita cenários de kubernetes, aprendizado profundo e aprendizado de máquina.

| Especificação           | Valor                  |
|-------------------------|----------------------------|
| GPU   | Uma GPU nVidia T4 <br> Para obter mais informações, consulte [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).| 

## <a name="power-supply-unit-specifications"></a>Especificações da unidade de fonte de alimentação

O dispositivo R Azure Stack Edge Pro tem duas PSUs (unidades de alimentação de energia) de 100-240 V com ventiladores de alto desempenho. Essas duas PSUs oferecem uma configuração de alimentação redundante. Se uma PSU falhar, o dispositivo continuará a funcionar normalmente em outra PSU até que o módulo com falha seja substituído. A seguinte tabela lista as especificações técnicas associadas das PSUs.

| Especificação           | 550 W PSU                  |
|-------------------------|----------------------------|
| Alimentação de saída máxima    | 550 W                      |
| Dissipação de calor (máxima)                   | 2891 BTU/h                |
| Frequência               | 50/60 Hz                   |
| Seleção de faixa de tensão | Variação automática: 115-230 V AC |
| Conectado com a máquina ligada           | Sim                        |

## <a name="network-specifications"></a>Especificações de rede

O dispositivo R Azure Stack Edge Pro tem quatro interfaces de rede, PORT1-PORT4. 


|Especificação  |Descrição                              |
|----------------------|----------------------------------|
|Interfaces de rede    |**2 x 1 GbE RJ45** <br> A porta 1 é usada como interface de gerenciamento para a configuração inicial e é estática por padrão. Depois que a configuração inicial for concluída, você poderá usar a interface para dados com qualquer endereço IP. No entanto, ao redefinir, a interface reverte de volta para o IP estático. <br>A outra porta 2 da interface é configurável pelo usuário, pode ser usada para transferência de dados e é DHCP por padrão.     |
|Interfaces de rede    |**2 x 25 GbE SFP28** <br> Essas interfaces de dados porta 3 e porta 4 podem ser configuradas como DHCP (padrão) ou estática.            |

O dispositivo R Azure Stack Edge Pro tem o seguinte hardware de rede:

* **Adaptador de rede do canal dual port 25g ConnectX-4** -porta 3 e porta 4 da Mellanox. 

<!--Here are the details for the Mellanox card: MCX4421A-ACAN

| Parameter           | Description                 |
|-------------------------|----------------------------|
| Model    | ConnectX®-4 Lx EN network interface card                      |
| Model Description               | 25GbE dual-port SFP28; PCIe3.0 x8; ROHS R6                    |
| Device Part Number (XR2) | MCX4421A-ACAN  |
| PSID (R640)           | MT_2420110034                         |-->
<!-- confirm w/ Ravi what is this-->

Para obter uma lista completa de cabos, comutadores e transceptores com suporte para essas placas de rede, acesse: [Mellanox Dual Port 25g ConnectX-4 Channel adaptador de rede de canais Compatible Products](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).

## <a name="storage-specifications"></a>Especificações do armazenamento

Os dispositivos R do Azure Stack Edge pro têm 8 discos de dados e 2 M. 2 discos SATA que funcionam como discos do sistema operacional. Para obter mais informações, acesse [M. 2 discos SATA](https://en.wikipedia.org/wiki/M.2).

#### <a name="storage-for-1-node-device"></a>Armazenamento para um dispositivo de 1 nó

A tabela a seguir tem os detalhes da capacidade de armazenamento do dispositivo de 1 nó.

|     Especificação                          |     Valor             |
|--------------------------------------------|-----------------------|
|    Número de SSDs (unidades de estado sólido)     |    8                  |
|    Capacidade de SSD único                     |    8 TB               |
|    Capacidade total                          |    64 TB              |
|    Capacidade total utilizável*                  |    ~ 42 TB          |

**Algum espaço está reservado para uso interno.*

<!--#### Storage for 4-node device

The following table has the details for the storage capacity of the 4-node device.

|     Specification                          |     Value             |
|--------------------------------------------|-----------------------|
|    Number of solid-state drives (SSDs)     |    32 (4 X 8 disks for 4 devices)                |
|    Single SSD capacity                     |    8 TB               |
|    Total capacity                          |    256 TB              |
|    Total usable capacity*                  |    ~ 163 TB          |

**After mirroring and parity, and reserving some space for internal use.* -->


## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensões do compartimento e especificações de peso

As tabelas a seguir listam as diversas especificações de compartimento para dimensões e peso.

### <a name="enclosure-dimensions"></a>Dimensões do compartimento 

A tabela a seguir lista as dimensões do dispositivo e o UPS com o caso resistente em milímetros e polegadas.

|     Compartimento     |     Milímetros     |     Polegadas     |
|-------------------|---------------------|----------------|
|    Altura         |    301,2            |    11,86       |
|    Largura          |    604,5            |    23,80       |
|    Comprimento         |    740,4            |    35,50       |

<!--#### For the 4-node system

For the 4-node system, the servers and the heater are shipped in a 5U case and the UPS are shipped in a 4U case.

The following table lists the dimensions of the 5U device case:  

|     Enclosure     |     Millimeters   |     Inches     |
|-------------------|-------------------|----------------|
|    Height         |    387.4          |    15.25       |
|    Width          |    604.5          |    23.80       |
|    Length         |    901.7          |    35.50       |

The following table lists the dimensions of the 4U UPS case: 

|     Enclosure     |     Millimeters   |     Inches    |
|-------------------|-------------------|---------------|
|    Height         |    342.9          |    13.5       |
|    Width          |    604.5          |   23.80       |
|    Length         |    901.7          |   35.50       |
-->

### <a name="enclosure-weight"></a>Peso do compartimento 

O peso do dispositivo depende da configuração do compartimento.

|     Compartimento                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Peso total do dispositivo de 1 nó + caso resistente com extremidades finais     |    ~ 114 lbs.          |

<!--#### For the 4-node system

|     Enclosure                                 |     Weight          |
|-----------------------------------------------|---------------------|
|   Approximate weight of fully populated 4 devices + heater in 5U case     |    ~200 lbs.          |
|   Approximate weight of fully populated 4 UPS in 4U case    |    ~145 lbs.          |
-->

## <a name="enclosure-environment-specifications"></a>Especificações do ambiente de compartimento

Esta seção lista as especificações relacionadas ao ambiente de compartimento, como temperatura, vibração, choque e altitude.


|     Especificação              |     Valor    |
|--------------------------------|-------------------------------------------------------------------|
|     Intervalo de temperatura          |     0 – 43 ° C (operacional)    |
|     Vibração                  |     MIL-método STD-810 514,7 *<br>Procedimento I CAT 4, 20                  |
|     Choque                      |     MIL-método STD-810 516,7 *<br>Procedimento IV, logística                 |
|     Altitude                   |     Operacional: 10.000 pés<br>Não operacional: 40.000 pés          |

**Todas as referências são para MIL-STD-810G alterar 1 (2014)*

## <a name="next-steps"></a>Próximas etapas

- [Implantar seu Azure Stack Edge](azure-stack-edge-placeholder.md)
