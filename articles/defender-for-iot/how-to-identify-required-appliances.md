---
title: Identificar os dispositivos necessários
description: Saiba mais sobre hardware e dispositivos virtuais para sensores do Certified defender para IoT e o console de gerenciamento local.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/13/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 92bf066c9769cc4b2525923b9e18ed3c0e9c577a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937137"
---
# <a name="identify-required-appliances"></a>Identificar os dispositivos necessários

Este artigo fornece informações sobre o Certified defender para dispositivos de sensor de IoT. O defender Fort IoT pode ser implantado em dispositivos físicos e virtuais.

Isso inclui dispositivos *pré-configurados* certificados, nos quais o software já está instalado, bem como dispositivos certificados não configurados nos quais você pode baixar e instalar o software necessário.

O artigo também fornece especificações para um dispositivo de console de gerenciamento local. O console de gerenciamento local não está disponível como um dispositivo pré-configurado.

- Se você quiser comprar um sensor pré-configurado, examine os modelos disponíveis na seção [dispositivos de sensor](#sensor-appliances) e, em seguida, continue com a compra.

- Se você quiser comprar seu próprio dispositivo, examine os modelos disponíveis na seção [dispositivos de sensor](#sensor-appliances) e na seção [dispositivos certificados adicionais](#additional-certified-appliances) . Depois de adquirir o dispositivo, você pode baixar e instalar o software.

- Se você quiser comprar o console de gerenciamento local, examine as informações na seção dispositivo do [console de gerenciamento local](#on-premises-management-console-appliance) . Depois de adquirir o dispositivo, você pode baixar e instalar o software.

Depois de concluir as tarefas aqui, você pode instalar o software e configurar sua rede.

## <a name="sensor-appliances"></a>Dispositivos de sensor

O defender para IoT dá suporte a implantações físicas e virtuais.

### <a name="physical-sensors"></a>Sensores físicos

Esta seção fornece uma visão geral dos modelos de sensor físico que estão disponíveis. Você pode comprar sensores com software pré-configurado ou sensores de compra que não são pré-configurados.

| Tipo de implantação | Corporativo | Enterprise | SMB |
|--|--|--|--|
| Imagem | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="O modelo de nível corporativo."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="O modelo de nível empresarial."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="O modelo de nível SMB."::: |
| Modelo | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 |
| Portas de monitoramento | Até 15 RJ45 ou 8 aceitar | Até 8 RJ45 ou 6 aceitar | 4 RJ45 |
| Largura de banda máxima [1](#anchortext) | 3 GB por segundo | 1 GB por segundo | 200 MB por segundo |
| Máximo de dispositivos protegidos | 30,000 | 15,000 | 1,000 |

Consulte [especificações do dispositivo](#appliance-specifications) para obter detalhes do fornecedor.

Sobre sensores pré-configurados: a Microsoft estabeleceu uma parceria com a seta para fornecer sensores pré-configurados. Para comprar um sensor pré-configurado, a seta de contato no seguinte endereço: <hardware.sales@arrow.com>

Sobre como trazer seu próprio dispositivo: examine os modelos com suporte descritos aqui. Depois de adquirir seu dispositivo, vá para **defender para**  >  **instalação ISO de sensores de rede** IOT  >   para baixar o software.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="ISO de sensores de rede.":::

<a id="anchortext">1</a> a capacidade da largura de banda pode variar, dependendo da distribuição de protocolos.

### <a name="virtual-sensors"></a>Sensores virtuais

Esta seção fornece uma visão geral dos sensores virtuais que estão disponíveis.

| Tipo de implantação | Corporativo | Enterprise | SMB |
|--|--|--|--|
| Largura de banda máxima | 2,5 GB/s | 800 MB/s | 160 MB/s |
| Máximo de dispositivos protegidos | 30,000 | 10.000 | 2\.500 |

## <a name="on-premises-management-console-appliance"></a>Dispositivo de console de gerenciamento local

O console de gerenciamento está disponível como uma implantação virtual.

| Tipo de implantação | Enterprise |
|--|--|
| Tipo de dispositivo | HPE DL20, VM |
| Número de sensores gerenciados | Até 300 |

Depois de adquirir um console de gerenciamento local, acesse **defender para IOT**  >  **local console de gerenciamento**  >  **instalação ISO** para baixar o ISO.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="Console de gerenciamento local.":::

## <a name="appliance-specifications"></a>Especificações do dispositivo

Esta seção descreve as especificações de hardware para os seguintes dispositivos:

- Implantação corporativa: HPE ProLiant DL360

- Implantação corporativa: HPE ProLiant DL20

- Implantação de SMB: HPE ProLiant DL20

- Especificações de dispositivos virtuais

## <a name="corporate-deployment-hpe-proliant-dl360"></a>Implantação corporativa: HPE ProLiant DL360

| Componente | Especificações técnicas |
|--|--|
| Chassi | servidor de rack 1U |
| Dimensões | 42,9 x 43,46 x 70,7 (cm)/1.69 "x 17,11" x 27,83 "(em) |
| Peso | Máx. 16,27 kg (35,86 lb) |
| Processador | Intel Xeon Silver 4215 R 3,2 GHz, cache 11 min, 8C/16T, 130 W |
| E7505 | Intel C621 |
| Memória | 32 GB = 2 x 16 GB 2666MT/s DDR4 ECC UDIMM |
| Armazenamento | 6 x 1,2-TB SAS 12G Enterprise 10K SFF (2,5) no disco rígido Hot-Plug-RAID 5 |
| Controlador de rede | Integrado: 2 x 1 GB de BCM5720 Broadcom<br>LOM integrado: placa de porta iDRAC de 1 GB, BCM5720 Broadcom<br><br>Externo: 1 x Intel Ethernet i350 QP de adaptador de servidor de 1 GB, baixo perfil |
| Gerenciamento | HPE iLO Advanced |
| Acesso ao dispositivo | Dois USB traseiros 3,0<br>Um USB frontal 2,0<br>Um USB interno 3,0 |
| Energia | 2 x HPE 500 W Flex slot Platinum Hot plug Halogen Power Supply kit |
| Suporte a rack | Kit de trilhos de instalação fácil do HPE 1U Gen10 SFF |

### <a name="appliance-bom"></a>BOM de dispositivo

| NP | Descrição | Quantidade |
|--|--|--|
| P19766-B21 | Servidor HPE DL360 Gen10 8SFF NC CTO | 1 |
| P19766-B21 | Europa-localização multilíngue | 1 |
| P24479-L21 | Kit Intel Xeon-S 4215 R FIO para DL360 G10 | 1 |
| P24479-B21 | Kit Intel Xeon-S 4215 R para DL360 Gen10 | 1 |
| P00922-B21 | HPE 16 GB 2Rx8 PC4-2933Y-R Smart Kit | 2 |
| 872479-B21 | HD DE HPE 1,2-TB SAS 10K PARA SC DS | 6 |
| 811546-B21 | HPE 1-GbE 4-p BASE-T i350 Adapter | 1 |
| P02377-B21 | Cabo do capacitor Smart híbrido HPE w \_ 145 mm | 1 |
| 804331-B21 | HPE Smart Array P408i-um controlador SR Gen10 | 1 |
| 665240-B21 | HPE 1-GbE 4-p FLR-T i350 Adapter | 1 |
| 871244-B21 | Kit de ventilador de alto desempenho HPE DL360 Gen10 | 1 |
| 865408-B21 | Kit de fonte de energia do HPE 500-W FS Plat Hot plug LH | 2 |
| 512485-B21 | HPE iLO Avçd 1-licença de servidor 1 ano de suporte | 1 |
| 874543-B21 | Kit de trilhos de instalação fácil do HPE 1U Gen10 SFF | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>Implantação corporativa: HPE ProLiant DL20

| Componente | Especificações técnicas |
|--|--|
| Chassi | servidor de rack 1U |
| Dimensões (altura x largura x profundidade) | 4,32 x 43,46 x 38,22 cm/1,70 x 17,11 x 15, 5 polegadas |
| Peso | 7,9 kg/17.41 lb |
| Processador | Intel Xeon E-2234, 3,6 GHz, 4C/8T, 71 W |
| E7505 | Intel C242 |
| Memória | 2 x 16 GB de fileira dupla x8 DDR4-2666 |
| Armazenamento | 3 x 1 TB SATA 6G Tabs no meio 7,2 K SFF (2,5) – RAID 5 com Smart Array P408i-um controlador SR |
| Controlador de rede | Integrado: 2 x 1 GB <br>Integrado: placa de porta iLO de 1 GB <br>Externo: 1 x HPE Ethernet 1-GB 4-porta 366FLR adaptador |
| Gerenciamento | HPE iLO Advanced |
| Acesso ao dispositivo | Front: 1 x USB 3,0, 1 x porta USB do serviço iLO <br>Parte traseira: 2 x USB 3,0 <br>Interno: 1 x USB 3,0 |
| Energia | Fontes de alimentação duplas de plugue duplo 500 W |
| Suporte a rack | Kit de trilhos de baixo-HPE 1U |

### <a name="appliance-bom"></a>BOM de dispositivo

| NP | Descrição: high-end | Quantidade |
|--|--|--|
| P06963-B21 | Servidor HPE DL20 Gen10 4SFF CTO | 1 |
| P06963-B21 | Servidor HPE DL20 Gen10 4SFF CTO | 1 |
| P17104-L21 | HPE DL20 Gen10 E-2234 FIO kit | 1 |
| 879507-B21 | HPE 16 GB 2Rx8 PC4-2666V-E STND kit | 2 |
| 655710-B21 | HDD DS 7,2 K SFF HPE DE 1 TB | 3 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM riser kit | 1 |
| 665240-B21 | Adaptador HPE Ethernet de 1 GB de 4 portas 366FLR | 1 |
| 782961-B21 | Bateria de armazenamento inteligente HPE 12 W | 1 |
| 869081-B21 | HPE Smart Array P408i-a SR G10 LH Controller | 1 |
| 865408-B21 | Kit de fonte de energia do HPE 500-W FS Plat Hot plug LH | 2 |
| 512485-B21 | HPE iLO Avçd 1-licença de servidor 1 ano de suporte | 1 |
| P06722-B21 | Kit de FIO de habilitação do HPE DL20 Gen10 RPS | 1 |
| 775612-B21 | Kit de trilhos de baixo-HPE 1U | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>Implantação de SMB: HPE ProLiant DL20

| Componente | Especificações técnicas |
|--|--|
| Chassi | servidor de rack 1U |
| Dimensões (altura x largura x profundidade) | 4,32 x 43,46 x 38,22 cm/1,70 x 17,11 x 15, 5 polegadas |
| Peso | 7,88 kg/17.37 lb |
| Processador | Intel Xeon E-2224, 3,4 GHz, 4C, 71 W |
| E7505 | Intel C242 |
| Memória | 1 x 8 GB de duas fileiras x8 DDR4-2666 |
| Armazenamento | 2 x 1 TB SATA 6G Tabs no meio 7,2 K SFF (2,5) – RAID 1 com Smart Array P208i-a |
| Controlador de rede | Integrado: 2 x 1 GB <br>Integrado: placa de porta iLO de 1 GB <br>Externo: 1 x HPE Ethernet 1-GB 4-porta 366FLR adaptador |
| Gerenciamento | HPE iLO Advanced |
| Acesso ao dispositivo | Front: 1 x USB 3,0, 1 x porta USB do serviço iLO <br>Parte traseira: 2 x USB 3,0 <br>Interno: 1 x USB 3,0 |
| Energia | Fonte de energia de plugue quente 290 W |
| Suporte a rack | Kit de trilhos de baixo-HPE 1U |

### <a name="appliance-bom"></a>BOM de dispositivo

| NP | Descrição | Quantidade |
|--|--|--|
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO Server | 1 |
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO Server | 1 |
| P17102-L21 | HPE DL20 Gen10 E-2224 FIO kit | 1 |
| 879505-B21 | HPE 8 GB 1Rx8 PC4-2666V-E STND kit | 1 |
| 801882-B21 | HPE DE 1 TB SATA 7,2 K LFF RW HDD | 2 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM riser kit | 1 |
| 665240-B21 | Adaptador HPE Ethernet de 1 GB de 4 portas 366FLR | 1 |
| 869079-B21 | HPE Smart Array E208i-a SR G10 LH Controller | 1 |
| P21649-B21 | HPE DL20 Gen10 Plat 290 W FIO PSU kit | 1 |
| P06683-B21 | HPE DL20 Gen10 M. 2 SATA/LFF AROC Cable kit | 1 |
| 512485-B21 | HPE iLO Avçd 1-licença de servidor 1 ano de suporte | 1 |
| 775612-B21 | Kit de trilhos de baixo-HPE 1U | 1 |

## <a name="virtual-appliance-specifications"></a>Especificações de dispositivos virtuais

### <a name="sensors"></a>Sensores

| Tipo | Corporativo | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| Memória | 32 GB | 32 GB | 8 GB |
| Armazenamento | 5,6 TB | 1,8 TB | 500 GB |

### <a name="on-premises-management-console-appliance"></a>Dispositivo de console de gerenciamento local

| Tipo | Enterprise |
|--|--|
| Descrição | Dispositivo virtual para tipos de implantação do Enterprise |
| vCPU | 8 |
| Memória | 32 GB |
| Armazenamento | 1,8 TB |

Hipervisores com suporte: VMware ESXi versão 5,0 e posterior, Hyper-V

## <a name="additional-certified-appliances"></a>Outros dispositivos certificados

Esta seção detalha os dispositivos adicionais que foram certificados pela Microsoft, mas que não são oferecidos como dispositivos pré-configurados.

| Tipo de implantação | Enterprise |
|--|--|
| Imagem | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="Tipo de implantação empresarial."::: |
| Modelo | Dell PowerEdge R340 XL |
| Portas de monitoramento | Até nove RJ45 ou seis aceitar |
| Largura de banda máxima [1](#anchortext2)| 1 GB/s |
| Máximo de dispositivos protegidos | 10.000 |

<a id="anchortext2">Um</a> A capacidade da largura de banda pode variar, dependendo da distribuição dos protocolos.

Depois de comprar o dispositivo, vá para **defender para**  >  instalação do **ISO de sensores de rede** IOT  >   para baixar o software.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="ISO de sensores de rede.":::

## <a name="enterprise-deployment-dell-poweredge-r340-xl"></a>Implantação corporativa: Dell PowerEdge R340 XL

| Componente | Especificações técnicas |
|--|--|
| Chassi | servidor de rack 1U |
| Dimensões | 42,8 x 434,0 x 596 (mm)/1,67 "x 17, 9" x 23,5 "(em) |
| Peso | Máx. 29,98 lb/345,44 kg |
| Processador | Intel Xeon E-2144G 3,6 GHz, cache de 8 m, 4C/8T, Turbo (71 W) |
| E7505 | Intel C246 |
| Memória | 32 GB = 2 x 16 GB 2666MT/s DDR4 ECC UDIMM |
| Armazenamento | 3 x 2-TB 7,2 K RPM SATA 6-Gbps 512n 3,5-in Hot-Plug disco rígido-RAID 5 |
| Controlador de rede | Integrado: 2 x 1 GB de BCM5720 Broadcom<br>LOM integrado: placa de porta iDRAC de 1 GB, BCM5720 Broadcom <br><br>Externo: 1 x Intel Ethernet i350 QP de adaptador de servidor de 1 GB, baixo perfil |
| Gerenciamento | iDRAC nove Enterprise |
| Acesso ao dispositivo | Dois USB traseiros 3,0 <br> Um USB frontal 3,0 |
| Energia | Fontes de alimentação duplas de plugue duplo 350 W |
| Suporte a rack | Os trilhos deslizantes do ReadyRails II para a montagem sem ferramentas em racks de 4 furos com buracos arredondados quadrados ou não-threads ou a montagem de ferramentas em racks de orifícios com 4 pós-post, com suporte para o ARM de gerenciamento de cabo opcional sem ferramentas. |

## <a name="dell-r340-bom"></a>BOM Dell R340

:::image type="content" source="media/how-to-prepare-your-network/enterprise-deployment-for-azure-defender-for-iot-dell-r340-bom.png" alt-text="BOM Dell R340.":::

## <a name="next-steps"></a>Próximas etapas

[Sobre a instalação do Azure defender para IoT](how-to-install-software.md)

[Sobre a configuração de rede do Azure Defender para IoT](how-to-set-up-your-network.md)
