---
title: Disponibilidade de região StorSimple | Microsoft Docs
description: Explica as regiões do Azure na qual vários modelos de dispositivo StorSimple estão disponíveis.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: ff2ea89c257a4941c801f3056450e005a9b37039
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628857"
---
# <a name="available-regions-for-your-storsimple"></a>Regiões disponíveis para o StorSimple

## <a name="overview"></a>Visão geral

Os datacenters do Azure operam em várias regiões geográficas do mundo para atender às demandas do cliente por desempenho, requisitos e preferências de local dos dados. Uma geografia do Azure é uma área definida do mundo que contém, pelo menos, uma Região do Azure. Uma região do Azure é uma área dentro de uma região geográfica que contém um ou mais datacenters.

A escolha de uma região do Azure é muito importante e é influenciada por fatores como a residência de dados e soberania, disponibilidade do serviço, desempenho, custo e redundância. Para obter mais informações sobre como escolher uma região, vá para [Qual região do Azure é a certa para mim?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

Para solução StorSimple, a escolha da região é determinada especificamente pelos seguintes fatores:

- Regiões onde o serviço do Gerenciador de Dispositivos do StorSimple está disponível.
- Países onde o StorSimple físico, a nuvem ou o dispositivo virtual está disponível.
- Regiões onde as contas de armazenamento que armazenam os dados do StorSimple devem ser localizadas para render desempenho ideal.

Este tutorial descreve a disponibilidade de região para o serviço do Gerenciador de Dispositivos do StorSimple, os dispositivos físicos locais e na nuvem. As informações contidas neste artigo aplicam-se a dispositivos StorSimple das séries 8000 e 1200.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Disponibilidade de região do serviço do Gerenciador de Dispositivos do StorSimple

Atualmente, há suporte para o serviço do Gerenciador de Dispositivos do StorSimple em 12 regiões públicas e 2 regiões do Azure Governamental.

Defina uma região ou um local ao criar o serviço do Gerenciador de Dispositivos do StorSimple. Em geral, escolha um local próximo da região geográfica onde o dispositivo está implantado. Porém, o dispositivo e o serviço também podem ser implantados em diferentes locais.

Veja uma lista de regiões onde o serviço do Gerenciador de Dispositivos do StorSimple está disponível para a nuvem pública do Azure e pode ser implantado.

![storsimple-device-manager-service-regions](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Para a nuvem do Azure Governamental, o serviço do Gerenciador de Dispositivos do StorSimple está disponível nos datacenters US Gov Iowa e US Gov Virginia.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Disponibilidade de região de dados armazenados no StorSimple

Os dados do StorSimple são armazenados fisicamente nas contas de armazenamento do Azure e essas contas estão disponíveis em todas as regiões do Azure. Quando você cria uma conta de armazenamento do Azure, o local primário dessa conta é escolhido e determina a região onde os dados residem.

Quando você cria um serviço do Gerenciador de Dispositivos do StorSimple e associa uma conta de armazenamento a ele, o serviço do Gerenciador de Dispositivos do seu StorSimple e o armazenamento do Azure podem ficar em dois locais separados. Nesse caso, é necessário criar a conta de armazenamento do Azure e do StorSimple Device Manager separadamente.

Em geral, escolha a região mais próxima do seu serviço para a conta de armazenamento. No entanto, a região mais próxima do Microsoft Azure pode não ser realmente a região com a menor latência. A latência determina o desempenho do serviço de rede e, portanto, o desempenho da solução. Assim, se você escolher uma conta de armazenamento em uma região diferente, saiba quais são as latências entre o seu serviço e a região associada à sua conta de armazenamento.

Se você está usando um Dispositivo de Nuvem StorSimple, é recomendável que o serviço e a conta de armazenamento associada estejam na mesma região. Contas de armazenamento em regiões diferentes podem resultar em baixo desempenho.

## <a name="availability-of-storsimple-device"></a>Disponibilidade do dispositivo StorSimple

Dependendo do modelo, os dispositivos StorSimple podem estar disponíveis em regiões ou países diferentes.

### <a name="storsimple-physical-device-models-81008600"></a>Dispositivo físico StorSimple (modelos 8100/8600)

Se usar um dispositivo físico StorSimple 8100 ou 8600, o dispositivo estará disponível nos seguintes países.

| #  | País/Região        | #  | País/Região     | #  | País/Região      | #  | País/Região              |
|----|----------------|----|-------------|----|--------------|----|----------------------|
| 1  | Austrália      | 16 | RAE de Hong Kong| 31 | Nova Zelândia  | 46 | África do Sul         |
| 2  | Áustria        | 17 | Hungria     | 32 | Nigéria      | 47 | Coreia do Sul          |
| 3  | Bahrein        | 18 | Islândia     | 33 | Noruega       | 48 | Espanha                |
| 4  | Bélgica        | 19 | Índia       | 34 | Peru         | 49 | Sri Lanka            |
| 5  | Brasil         | 20 | Indonésia   | 35 | Filipinas  | 50 | Suécia               |
| 6  | Canadá         | 21 | Irlanda     | 36 | Polônia       | 51 | Suíça          |
| 7  | Chile          | 22 | Israel      | 37 | Portugal     | 52 | Taiwan               |
| 8  | Colômbia       | 23 | Itália       | 38 | Porto Rico  | 53 | Tailândia             |
| 9  | República Tcheca | 24 | Japão       | 11,8 | Catar        | 54 | Turquia               |
| 10 | Dinamarca        | 25 | Quênia       | 40 | Romênia      | 55 | Ucrânia              |
| 11 | Egito          | 26 | Kuwait      | 41 | Rússia       | 56 | Emirados Árabes Unidos |
| 12 | Finlândia        | 27 | RAE de Macau   | 42 | Arábia Saudita | 57 | Reino Unido       |
| 13 | França         | 28 | Malásia    | 43 | Singapura    | 58 | Estados Unidos        |
| 14 | Alemanha        | 29 | México      | 44 | Eslováquia     | 59 | Vietnã              |
| 15 | Grécia         | 30 | Países Baixos | 45 | Eslovênia     | 60 | Croácia              |

Esta lista muda à medida que mais países são adicionados. Para obter uma lista mais atualizada de regiões geográficas, vá para o apêndice de termos de matriz de armazenamento em [Termos do produto](https://www.microsoft.com/en-us/licensing/product-licensing/products).

A Microsoft pode enviar o hardware físico e substituir peças de reposição de hardware para o StorSimple para regiões geográficas na lista anterior.

> [!IMPORTANT]
> Não coloque um dispositivo físico StorSimple em uma região onde não há suporte para o StorSimple. A Microsoft não poderá enviar peças de reposição para países onde não há suporte para o StorSimple.

### <a name="storsimple-cloud-appliance-models-80108020"></a>Dispositivo de Nuvem StorSimple (Modelos 8010/8020)

Se usar um Dispositivo de Nuvem StorSimple 8010 ou 8020, haverá suporte disponível em todas as regiões onde há suporte para a VM de base. O 8010 usa uma VM _Standard_A3_ com suporte em todas as regiões do Azure.

O 8020 usa o armazenamento premium e a VM _Standard_DS3_ para criar um dispositivo de nuvem. Há suporte para o 8020 em todas as regiões do Azure com suporte ao Armazenamento Premium e às VMs _Standard_DS3_ do Azure. Use [esta lista](https://azure.microsoft.com/regions/services/) para ver se as **Máquinas Virtuais > série DS** e o **Armazenamento > Armazenamento em disco** estão disponíveis em sua região.

### <a name="storsimple-virtual-array-model-1200"></a>Matriz Virtual StorSimple (Modelo 1200)

Se usar a Matriz Virtual StorSimple série 1200, haverá suporte para a imagem do disco virtual em todas as regiões do Azure.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os [preços de vários modelos de StorSimple](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* [Saiba mais sobre como gerenciar sua conta de armazenamento do StorSimple](storsimple-8000-manage-storage-accounts.md).
* Saiba mais sobre como [usar o serviço StorSimple Device Manager para administrar dispositivos StorSimple](storsimple-8000-manager-service-administration.md).
