---
title: "Limites do sistema do StorSimple série 8000 | Microsoft Docs"
description: "Descreve os limites do sistema e os tamanhos recomendados para componentes e conexões do StorSimple série 8000."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: c7392678-0924-46c6-9c59-1665cb9b6586
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/28/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cc3c0ad193af7625c8c4c1c2e82b6bdc8be33310
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>O que são limites do sistema do StorSimple série 8000?

## <a name="overview"></a>Visão geral

O StorSimple fornece armazenamento escalonável e flexível para seu datacenter. No entanto, há algumas limitações que você deve ter em mente ao planejar, implantar e operar a solução StorSimple. A tabela a seguir descreve esses limites e fornece algumas recomendações para que você possa aproveitar ao máximo sua solução StorSimple.

| Identificador de limite | Limite | Comentários |
| --- | --- | --- |
| Número máximo de credenciais de conta de armazenamento |64 | |
| Número máximo de contêineres de volume |64 | |
| Número máximo de volumes |255 | |
| Número máximo de volumes afixados localmente |32 | |
| Número máximo de agendas por modelo de largura de banda |168 |Uma agenda para cada hora, cada dia da semana (24x7). |
| Tamanho máximo de um volume em camadas em dispositivos físicos |64 TB para 8100 e 8600 |8100 e 8600 são dispositivos físicos. |
| Tamanho máximo de um volume em camadas em dispositivos virtuais no Azure |30 TB para 8010  <br></br> 64 TB para 8020 |8010 e 8020 são dispositivos virtuais no Azure que usam armazenamento Standard e Premium, respectivamente. |
| Tamanho máximo de um volume localmente afixado em dispositivos físicos |8,5 TB para 8100 <br></br> 22,5 TB para 8600 |8100 e 8600 são dispositivos físicos. |
| Número máximo de conexões iSCSI |512 | |
| Número máximo de conexões iSCSI dos iniciadores |512 | |
| Número máximo de registros de controle de acesso por dispositivo |64 | |
| Número máximo de volumes por política de backup |20 | |
| Número máximo de backups retidos por programação (em uma política de backup) |64 | |
| Número máximo de agendas por política de backup |10 | |
| Número máximo de instantâneos de qualquer tipo que podem ser retidos por volume |256 |Esse número inclui instantâneos locais e na nuvem. |
| Número máximo de instantâneos que podem estar presentes em qualquer dispositivo |10.000 | |
| Número máximo de volumes que podem ser processados paralelamente para backup, restauração e clonagem |16 |<ul><li>Se houver mais de 16 volumes, eles serão processados sequencialmente conforme os slots de processamento ficarem disponíveis.</li><li>Novos backups de um volume clonado ou restaurado em camadas não poderão ocorrer até que a operação seja finalizada. No entanto, para um volume local, há permissão para backups quando o volume estiver online.</li></ul> |
| Tempo de recuperação de clonagem e restauração para volumes em camadas |Menos de 2 minutos |<ul><li>O volume é disponibilizado em até 2 minutos durante a operação de restauração ou clonagem, independentemente do tamanho do volume.</li><li>Inicialmente, o desempenho do volume pode ser mais lento do que o normal, pois a maioria dos dados e metadados ainda reside na nuvem. O desempenho pode aumentar à medida que os dados fluem da nuvem para o dispositivo StorSimple.</li><li>O tempo total para baixar metadados depende do tamanho do volume alocado. Os metadados são automaticamente colocados no dispositivo em segundo plano a uma taxa de 5 minutos por TB de dados de volume alocados. Essa taxa pode ser afetada pela largura de banda da Internet para a nuvem.</li><li>A operação de restauração ou clonagem será concluída quando todos os metadados estiverem no dispositivo.</li><li>As operações de backup não podem ser executadas até que a operação de restauração ou clonagem seja totalmente concluída. |
| Tempo de recuperação de restauração para volumes localmente fixados |Menos de 2 minutos |<ul><li>O volume é disponibilizado em até 2 minutos durante a operação de restauração, independentemente do tamanho do volume.</li><li>Inicialmente, o desempenho do volume pode ser mais lento do que o normal, pois a maioria dos dados e metadados ainda reside na nuvem. O desempenho pode aumentar à medida que os dados fluem da nuvem para o dispositivo StorSimple.</li><li>O tempo total para baixar metadados depende do tamanho do volume alocado. Os metadados são automaticamente colocados no dispositivo em segundo plano a uma taxa de 5 minutos por TB de dados de volume alocados. Essa taxa pode ser afetada pela largura de banda da Internet para a nuvem.</li><li>Ao contrário de volumes em camadas, para volumes localmente afixados, os dados do volume também serão baixados localmente no dispositivo. A operação de restauração é concluída quando todos os dados de volume são colocados no dispositivo.</li><li>As operações de restauração podem ser longas. O tempo total para concluir a restauração dependerá do tamanho do volume local provisionado, da largura de banda da Internet e dos dados existentes no dispositivo. Operações de backup em volume localmente afixado são permitidas enquanto a operação de restauração está em andamento. |
| Taxa de processamento de instantâneos de nuvem |15 minutos/TB |<ul><li>O tempo mínimo para preparar o instantâneo de nuvem para upload, de acordo com os TB dos dados do volume alocado no backup. </li><li> O tempo total do instantâneo de nuvem é calculado pela adição desse tempo ao tempo de upload do instantâneo, que é afetado pela largura de banda da Internet para a nuvem. |
| Taxa de transferência máxima de leitura/gravação do cliente (quando servida da camada SSD)* |920/720 MB/s com uma única interface de rede de 10 GbE |Até 2 vezes com MPIO e duas interfaces de rede. |
| Taxa de transferência máxima de leitura/gravação do cliente (quando servida da camada HDD)* |120/250 MB/s | |
| Taxa de transferência máxima de leitura/gravação do cliente (quando originada da camada de nuvem)* para a Atualização 3 e posterior** |40/60 MB/s para volumes em camadas<br><br>60/80 MB/s para volumes em camadas com a opção de arquivamento selecionada durante a criação do volume |A taxa de transferência de leitura depende dos clientes que geram e mantêm profundidade suficiente de fila de E/S. <br><br>A velocidade obtida depende da velocidade da conta de armazenamento subjacente usada. |

&#42; A taxa de transferência máxima por tipo de E/S foi medida com cenários 100% de gravação e 100% de leitura. A taxa de transferência real pode ser menor e depende da combinação de E/S e das condições da rede.

&#42;&#42; Os números de desempenho antes da Atualização 3 podem ser menores.

## <a name="next-steps"></a>Próximas etapas
Analise os [requisitos de sistema do StorSimple](storsimple-8000-system-requirements.md).

