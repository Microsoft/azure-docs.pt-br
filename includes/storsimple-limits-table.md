---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "67172320"
---
| Identificador de limite | Limite | Comentários |
| --- | --- | --- |
| Número máximo de credenciais de conta de armazenamento |64 | |
| Número máximo de contêineres de volume |64 | |
| Número máximo de volumes |255 | |
| Número máximo de agendas por modelo de largura de banda |168 |Um agendamento para cada hora e dia da semana. |
| Tamanho máximo de um volume em camadas em dispositivos físicos |64 TB para o StorSimple 8100 e o StorSimple 8600 |O StorSimple 8100 e o StorSimple 8600 são dispositivos físicos. |
| Tamanho máximo de um volume em camadas em dispositivos virtuais no Azure |30 TB para o StorSimple 8010 <br></br> 64 TB para o StorSimple 8020 |O StorSimple 8010 e o StorSimple 8020 são dispositivos virtuais no Azure que usam os armazenamentos Standard e Premium, respectivamente. |
| Tamanho máximo de um volume localmente afixado em dispositivos físicos |9 TB para o StorSimple 8100 <br></br> 24 TB para o StorSimple 8600 |O StorSimple 8100 e o StorSimple 8600 são dispositivos físicos. |
| Número máximo de conexões iSCSI |512 | |
| Número máximo de conexões iSCSI dos iniciadores |512 | |
| Número máximo de registros de controle de acesso por dispositivo |64 | |
| Número máximo de volumes por política de backup |24 | |
| Número máximo de backups retidos por política de backup |64 | |
| Número máximo de agendas por política de backup |10 | |
| Número máximo de instantâneos de qualquer tipo que podem ser retidos por volume |256 |Essa quantidade inclui instantâneos locais e na nuvem. |
| Número máximo de instantâneos que podem estar presentes em qualquer dispositivo |10.000 | |
| Número máximo de volumes que podem ser processados paralelamente para backup, restauração e clonagem |16 |<ul><li>Caso haja mais de 16 volumes, eles serão processados de modo sequencial conforme os slots de processamento ficarem disponíveis.</li><li>Os novos backups de um volume clonado ou restaurado em camadas não deverão ocorrer até que a operação seja finalizada. No entanto, para um volume local, os backups serão permitidos depois que o volume estiver online.</li></ul> |
| Tempo de recuperação de clonagem e restauração para volumes em camadas |< 2 minutos |<ul><li>O volume será disponibilizado em até 2 minutos após uma operação de restauração ou clonagem, independentemente do tamanho do volume.</li><li>O desempenho do volume inicialmente poderá ser mais lento do que o normal, pois a maioria dos dados e metadados ainda residirá na nuvem. O desempenho poderá aumentar conforme houver um fluxo de dados da nuvem para o dispositivo StorSimple.</li><li>O tempo total para baixar metadados depende do tamanho do volume alocado. Os metadados são automaticamente colocados no dispositivo em segundo plano a uma taxa de 5 minutos por TB de dados de volume alocados. Essa taxa poderá ser afetada pela largura de banda da Internet para a nuvem.</li><li>A operação de restauração ou clonagem será concluída quando todos os metadados estiverem no dispositivo.</li><li>As operações de backup não poderão ser executadas até que a operação de restauração ou clonagem seja totalmente concluída. |
| Tempo de recuperação de restauração para volumes localmente fixados |< 2 minutos |<ul><li>O volume é disponibilizado em até 2 minutos durante a operação de restauração, independentemente do tamanho do volume.</li><li>O desempenho do volume inicialmente poderá ser mais lento do que o normal, pois a maioria dos dados e metadados ainda residirá na nuvem. O desempenho poderá aumentar conforme houver um fluxo de dados da nuvem para o dispositivo StorSimple.</li><li>O tempo total para baixar metadados depende do tamanho do volume alocado. Os metadados são automaticamente colocados no dispositivo em segundo plano a uma taxa de 5 minutos por TB de dados de volume alocados. Essa taxa poderá ser afetada pela largura de banda da Internet para a nuvem.</li><li>Diferente dos volumes em camadas, caso haja volumes fixados localmente, os dados do volume também serão baixados localmente no dispositivo. A operação de restauração é concluída quando todos os dados de volume são colocados no dispositivo.</li><li>As operações de restauração poderão ser longas e o tempo total para concluir a restauração dependerá do tamanho do volume local provisionado, da largura de banda da Internet e dos dados existentes no dispositivo. Operações de backup em volume localmente afixado são permitidas enquanto a operação de restauração está em andamento. |
| Disponibilidade de restauração fina |Último failover | |
| Taxa de transferência máxima de leitura/gravação do cliente quando fornecida da camada SSD* |920/720 MB/s com um adaptador de rede Ethernet de 10 gigabits |Até duas vezes com MPIO e dois adaptadores de rede. |
| Taxa de transferência máxima de leitura/gravação do cliente quando fornecida da camada HDD* |120/250 MB/s | |
| Taxa de transferência máxima de leitura/gravação do cliente quando fornecida da camada de nuvem* |11/41 MB/s |A taxa de transferência de leitura depende dos clientes que geram e mantêm profundidade suficiente de fila de E/S. |

&#42;A taxa de transferência máxima por tipo de E/S foi medida com cenários de 100% de leitura e 100% de gravação. A taxa de transferência real poderá ser menor e dependerá da combinação de E/S com as condições da rede.

