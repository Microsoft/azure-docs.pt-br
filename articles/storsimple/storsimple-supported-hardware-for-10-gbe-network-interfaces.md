---
title: Hardware para interfaces de 10 GbE do StorSimple | Microsoft Docs
description: Descreve os transceptores, cabos e switches SPF (small form-factor pluggable) com suporte para interfaces de rede de 10 GbE em seu dispositivo StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: db03b3cd668bf8e35913872ac4225de6d4d3edd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Suporte para hardware de interfaces de rede de 10 GbE em seu dispositivo StorSimple
## <a name="overview"></a>Visão geral
Este artigo dá informações sobre hardware suplementar que funciona com o seu dispositivo Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Lista de dispositivos testados pela Microsoft
A Microsoft testou os seguintes transceptores de fator forma pequeno conectáveis (SFP), cabos e comutadores, para garantir que funcionem de maneira ideal com dispositivos. As tabelas a seguir serão atualizadas enquanto o novo hardware é testado.

### <a name="sfp-transceivers"></a>Transceptores SFP +
| Faça | Modelo |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Cabos
| S. Nº | Faça | Modelo |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp-Lite |N820-05M (OM3) |

### <a name="switches"></a>Opções
| S. Nº | Faça | Modelo |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Lista de dispositivos testados em campo
Esta seção contém a lista dos dispositivos que têm sido implantados com êxito em campo por clientes do StorSimple. Não foram testados pela Microsoft, mas devem funcionar com o dispositivo StorSimple.

| Parâmetro | Valor |
| --- | --- |
| Comutador   marca |Juniper |
| Comutador   modelo |ex4550-32F |
| Comutador   versão do sistema operacional |JunOS 12.3R9.4 |
| Folha   modelo |Portas integradas (PIC 0) |
| Forma do transceptor |Juniper |
| Transceptor   modelo |Peça número 740-021308  <br></br> Peça número 740-030658 |
| Transceptor   versão do firmware |Rev 01 Versão 0.0 (reportada) |
| Cabo   modelo |Duplex jumper LC/LC 50/125µ,   OM3, LSZH |
| StorSimple   modelo |8600 |
| StorSimple   versão do software |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lista de dispositivos testados pelo provedor de OEM (Mellanox)
A Mellanox testou os seguintes transceptores de fator forma pequeno conectável (SFP), cabos e comutadores, para garantir que eles funcionem de maneira ideal com interfaces de rede Mellanox, como interfaces de rede 10 GbE em seu dispositivo StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Cabos e módulos compatíveis com Mellanox
A tabela a seguir lista os cabos e módulos compatíveis com Mellanox. Não foram testados pela Microsoft, mas devem funcionar com o dispositivo StorSimple.

| S. Nº | Velocidade | Modelo | Descrição | Faça |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |cabo de cobre passivo SFP+ 10 Gb/s 1m |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2M |cabo de cobre passivo SFP+ 10 Gb/s 2m |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3M |cabo de cobre passivo SFP+ 10 Gb/s 3m |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5M |cabo de cobre passivo SFP+ 10 Gb/s 5m |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Cabo SFP + Cisco |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Cabo SFP + Cisco |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Cabo SFP + Cisco |Cisco |
| 8. |10 GbE |J9281B HP X242 10G |Conexão direta de cabo de cobre SFP+ a SFP+ 1m |HP |
| 9. |10 GbE |455883-B21 HP BLc |10Gb SR SFP+ Opt |HP |
| 10. |10 GbE |455886-B21 HP BLc |10Gb LR SFP+ Opt |HP |
| 11. |10 GbE |487649-B21 HP BLc |Cabo de cobre SFP+ 0.5m 10GbE |HP |
| 12. |10 GbE |487652-B21 HP BLc |Cabo de cobre SFP+ 1m 10GbE |HP |
| 13. |10 GbE |487655-B21 HP BLc |Cabo de cobre SFP+ 3m 10GbE |HP |
| 14. |10 GbE |487658-B21 HP BLc |Cabo de cobre SFP+ 7m 10GbE |HP |
| 15. |10 GbE |537963-B21 HP BLc |Cabo de cobre SFP+ 5m 10GbE |HP |
| 16. |10 GbE |AP784A HP |Cabo de cobre passivo série C SFP + 3m |HP |
| 17. |10 GbE |HP AP785A |Cabo de cobre passivo série C SFP + 5m |HP |
| 18. |10 GbE |AP818A HP |Cabo de cobre passivo série B SFP + 1m |HP |
| 19. |10 GbE |AP819A HP |Cabo de cobre passivo série B SFP + 3m |HP |
| 20. |10 GbE |J9150A HP |X132 10G SFP+ transceptor LC SR |HP |
| 21. |10 GbE |J9151A HP |X132 10G SFP+ transceptor LC LR |HP |
| 22. |10 GbE |J9283B HP |X242 10G SFP+ SFP+ cabo 3m DAC |HP |
| 23. |10 GbE |J9285B HP |X242 10G SFP+ SFP+ cabo 7m DAC |HP |
| 24. |10 GbE |JD095B HP |X240 10G SFP+ SFP+ cabo 0,65m DAC |HP |
| 25. |10 GbE |JD096B HP |X240 10G SFP+ SFP+ cabo 1,2m DAC |HP |
| 26. |10 GbE |JD097B HP |X240 10G SFP+ SFP+ cabo 3m DAD |HP |
| 27. |10 GbE |MAM1Q00A-QSA Mellanox |Adaptador QSFP a SFP + |Mellanox Technologies |
| 28. |10 GbE |MC2309124-006 Mt |Cabo de cobre passivo 1x SFP+ To QSFP 10Gb/s 24awg 7m |Mellanox Technologies |
| 29. |10 GbE |MC2309124-007 Mt |Cabo de cobre passivo 1x SFP+ To QSFP 10Gb/s 24awg 7m |Mellanox Technologies |
| 30. |10 GbE |MC2309130-003 Mt |Cabo de cobre passivo 1x SFP+ a QSFP 10Gb/s 30awg 3m |Mellanox Technologies |
| 31. |10 GbE |MC2309130-00A Mt |Cabo de cobre passivo 1x SFP+ a QSFP 10Gb/s 30awg 0.5m |Mellanox Technologies |
| 32. |10 GbE |MC3309124-005 Mt |Cabo de cobre passivo 1x SFP+ 10Gb/s 24awg 5m |Mellanox Technologies |
| 33. |10 GbE |MC3309124-007 Mt |Cabo de cobre passivo 1x SFP+ 10Gb/s 24awg 7m |Mellanox Technologies |
| 34. |10 GbE |MC3309130-003 Mt |Cabo de cobre passivo 1x SFP+ 10Gb/s 30awg 3m |Mellanox Technologies |
| 35. |10 GbE |MC3309130-00A Mt |Cabo de cobre passivo 1x SFP+ 10Gb/s 30awg 0,5m |Mellanox Technologies |

### <a name="switches-supported-by-mellanox"></a>Opções compatíveis com o Mellanox
A tabela a seguir lista as opções compatíveis com Mellanox. Não foram testados pela Microsoft, mas devem funcionar com o dispositivo StorSimple.

| S. Nº | Velocidade | Modelo | Descrição | Faça |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |516733-B21 |Blade Switch de Ethernet de 10 GbE HP ProCurve 6120XG |HP |
| 2. |10 GbE |538113-B21 |Módulo de passagem de 10 GbE HP (PTM) |HP |
| 3. |10 GbE |EN4093 |Módulo de Switch escalonável IBM PureFlex System Fabric EN4093 10 Gigabit |IBM |
| 4. |1 GbE |3020 |Switch blade Cisco Catalyst 3020 1 GbE |Cisco |
| 5. |1 GbE |3020X |Switch blade Cisco Catalyst 3020X 1GbE |Cisco |
| 6. |1 GbE |438030-B21 |Módulo de switch HP de 1 GbE - GbE2c camada 2/3 Switch blade Ethernet |HP |
| 7. |1 GbE |6120G |Switch blade de 1 GbE HP ProCurve 6120G/XG |HP |

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre os componentes de hardware e o status do StorSimple](storsimple-monitor-hardware-status.md).

