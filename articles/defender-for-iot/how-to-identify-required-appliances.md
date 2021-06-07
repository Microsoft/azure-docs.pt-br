---
title: Identificar os dispositivos necessários
description: Saiba mais sobre hardware e dispositivos virtuais para sensores do Certified defender para IoT e o console de gerenciamento local.
ms.date: 01/13/2021
ms.topic: how-to
ms.openlocfilehash: 242e88e92d6197fd018c56fa55a4dd8166f5d027
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782751"
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

| Tipo de implantação | Corporativo | Enterprise | Montagem em rack de SMB| SMB resistente|
|--|--|--|--|--|
| Imagem | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="O modelo de nível corporativo."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="O modelo de nível empresarial."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="O modelo de nível SMB."::: | :::image type="content" source="media/how-to-prepare-your-network/office-ruggedized.png" alt-text="O modelo de nível resistente por SMB."::: |
| Modelo | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 | HPE EL300 |
| Portas de monitoramento | Até 15 RJ45 ou 8 aceitar | Até 8 RJ45 ou 6 aceitar | 4 RJ45 | Até 5 |
| Largura de banda máxima [1](#anchortext) | 3 GB/s | 1 GB/s | 200 MB/s | 100 MB/s |
| Máximo de dispositivos protegidos | 30,000 | 15,000 | 1,000 | 800 |

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

## <a name="smb-rugged-hpe-edgeline-el300"></a>SMB resistente: HPE Edge EL300

| Componente | Especificações técnicas |
|--|--|
| Construção | Alumínio, fanless & design à prova de poeira |
| Dimensões (altura x largura x profundidade) | 200.5 mm (7,9 ") de altura, 232mm (9,14") de largura por 100mm (3,9 ") de profundidade |
| Peso | 4,91 KG (10,83 lb.) |
| CPU | Intel Core i7-8650U (1,9 GHz/4-core/15W) |
| E7505 | Intel® Q170 Platform Controller Hub |
| Memória | 8 GB DDR4 2133MHz de temperatura ampla SODIMM |
| Armazenamento | SSD de 128 3ME3 de temperatura larga mSATA |
| Controlador de rede | portas 6 vezes Gigabit Ethernet por Intel® I219 |
| Acesso ao dispositivo  | 4 USBs: 2 Fronts; 2 traseiras; 1 interno |
| Adaptador de energia | 250V/10A |
| Montar | Kit de montagem, trilho de DIN |
| Temperatura operacional | 0C a + 70C  |
| Umidade | 10% ~ 90%, sem condensação |
| Vibração | 0,3 Grms 10Hz 300Hz, 15 minutos por eixo – trilho DIN   |
| Choque | 10G 10, semi-seno, três para cada eixo. (Pulso positivo & negativo) – trilho de DIN |

### <a name="appliance-bom"></a>BOM de dispositivo
| Produto | Descrição |
|--|--|
| P25828-B21 | Sistema de borda convergido do HPE EL300 v2 |
| P25828-B21 B19 | Sistema de borda convergido do HPE EL300 v2 |
| P25833-B21 | Intel Core i7-8650U (1.9 GHz/4-core/15W) FIO Basic Processor Kit para HPE Edge |
| P09176-B21 | HPE Edge de 8 GB (1x8GB) Dual Rank x8 DDR4-2666 SODIMM WT CAS-19-19-19 memória registrada kit FIO |
| P09188-B21 | HPE Edge 256 GB SATA 6G Read intensivo M. 2 2242 3yr WTY Wide Temp SSD |
| P04054-B21 | HPE Edge EL300 SFF para M. 2 Kit de habilitação |
| P08120-B21 | HPE Edge EL300 12VDC FIO transferência do tabuleiro |
| P08641-B21 | HPE Edge EL300 80W 12VDC fonte de energia |
| AF564A | HPE C13-SI-32 IL 250V 10Amp 1,83 m cabo de alimentação |
| P25835-B21 | Placa de operadora HPE EL300 v2 FIO |
| R1P49AAE | HPE EL300 iSM Avçd 3yr 24x7 Sup_Upd E-LTU |
| P08018-B21 opcional | Kit de colchetes do HPE Edge EL300 baixo  |
| P08019-B21 opcional | Kit de montagem do HPE Edge EL300 DIN |
| P08020-B21 opcional | Kit de montagem de parede do HPE Edge EL300 |
| P03456-B21 opcional | HPE Edge 1 GbE 4-Port TSN FIO filha Card |

## <a name="virtual-appliance-specifications"></a>Especificações de dispositivos virtuais

### <a name="sensors"></a>Sensores

| Type | Corporativo | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| Memória | 32 GB | 32 GB | 8 GB |
| Armazenamento | 5,6 TB | 1,8 TB | 500 GB |

### <a name="on-premises-management-console-appliance"></a>Dispositivo de console de gerenciamento local

| Type | Enterprise |
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

## <a name="next-steps"></a>Próximas etapas

[Sobre a instalação do Azure defender para IoT](how-to-install-software.md)

[Sobre a configuração de rede do Azure Defender para IoT](how-to-set-up-your-network.md)

