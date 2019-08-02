---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172320"
---
| Identificador de limite | Limite | Comentários |
| --- | --- | --- |
| Número máximo de credenciais de conta de armazenamento |64 | |
| Número máximo de contêineres de volume |64 | |
| Número máximo de volumes |255 | |
| Número máximo de agendas por modelo de largura de banda |168 |Uma agenda para cada hora, todos os dias da semana. |
| Tamanho máximo de um volume em camadas em dispositivos físicos |64 TB para o StorSimple 8100 e StorSimple 8600 |StorSimple 8100 e StorSimple 8600 são dispositivos físicos. |
| Tamanho máximo de um volume em camadas em dispositivos virtuais no Azure |30 TB para o StorSimple 8010 <br></br> 64 TB para o StorSimple 8020 |StorSimple 8010 e o StorSimple 8020 são dispositivos virtuais no Azure que usam o armazenamento Standard e Premium, respectivamente. |
| Tamanho máximo de um volume localmente afixado em dispositivos físicos |9 TB para StorSimple 8100 <br></br> 24 TB para StorSimple 8600 |StorSimple 8100 e StorSimple 8600 são dispositivos físicos. |
| Número máximo de conexões iSCSI |512 | |
| Número máximo de conexões iSCSI dos iniciadores |512 | |
| Número máximo de registros de controle de acesso por dispositivo |64 | |
| Número máximo de volumes por política de backup |24 | |
| Número máximo de backups retidos por política de backup |64 | |
| Número máximo de agendas por política de backup |10 | |
| Número máximo de instantâneos de qualquer tipo que podem ser retidos por volume |256 |O valor inclui instantâneos locais e na nuvem. |
| Número máximo de instantâneos que podem estar presentes em qualquer dispositivo |10.000 | |
| Número máximo de volumes que podem ser processados paralelamente para backup, restauração e clonagem |16 |<ul><li>Se houver mais de 16 volumes, eles são processados sequencialmente conforme os slots de processamento ficarem disponíveis.</li><li>Novos backups de clonado ou um volume em camadas restaurado não pode ocorrer até que a operação seja concluída. Para um volume local, os backups são permitidos depois que o volume estiver online.</li></ul> |
| Tempo de recuperação de clonagem e restauração para volumes em camadas |< 2 minutos |<ul><li>O volume é disponibilizado em 2 minutos após uma operação de restauração ou clonagem, independentemente do tamanho do volume.</li><li>Inicialmente, o desempenho do volume pode ser mais lento do que o normal, pois a maioria dos dados e metadados ainda reside na nuvem. Pode aumentar o desempenho como fluxos de dados da nuvem para o dispositivo StorSimple.</li><li>O tempo total para baixar metadados depende do tamanho do volume alocado. Os metadados são automaticamente colocados no dispositivo em segundo plano a uma taxa de 5 minutos por TB de dados de volume alocados. Essa taxa pode ser afetada pela largura de banda de Internet para a nuvem.</li><li>A operação de restauração ou clonagem será concluída quando todos os metadados estiverem no dispositivo.</li><li>Operações de backup não podem ser executadas até que a restauração ou operação de clonagem seja totalmente concluída. |
| Tempo de recuperação de restauração para volumes localmente fixados |< 2 minutos |<ul><li>O volume é disponibilizado em até 2 minutos durante a operação de restauração, independentemente do tamanho do volume.</li><li>Inicialmente, o desempenho do volume pode ser mais lento do que o normal, pois a maioria dos dados e metadados ainda reside na nuvem. Pode aumentar o desempenho como fluxos de dados da nuvem para o dispositivo StorSimple.</li><li>O tempo total para baixar metadados depende do tamanho do volume alocado. Os metadados são automaticamente colocados no dispositivo em segundo plano a uma taxa de 5 minutos por TB de dados de volume alocados. Essa taxa pode ser afetada pela largura de banda de Internet para a nuvem.</li><li>Ao contrário de volumes em camadas, se houver volumes localmente afixados, os dados do volume também serão baixados localmente no dispositivo. A operação de restauração é concluída quando todos os dados de volume são colocados no dispositivo.</li><li>As operações de restauração podem ser longos e o tempo total para concluir a restauração dependerá do tamanho do volume local provisionado, largura de banda de Internet e os dados existentes no dispositivo. Operações de backup em volume localmente afixado são permitidas enquanto a operação de restauração está em andamento. |
| Disponibilidade de restauração fina |Último failover | |
| Taxa de transferência de leitura/gravação da máximo de clientes, quando servida da camada SSD * |920/720 MB/s com uma única interface de rede 10 gigabit Ethernet |Até duas vezes com MPIO e duas interfaces de rede. |
| Taxa de transferência de leitura/gravação da máximo de clientes, quando servida da camada HDD * |120/250 MB/sec | |
| Taxa de transferência de leitura/gravação da máximo de clientes, quando servida da camada de nuvem * |11/41 MB/s |A taxa de transferência de leitura depende dos clientes que geram e mantêm profundidade suficiente de fila de E/S. |

&#42;Taxa de transferência máxima por tipo de e/s foi medida com cenários de 100 por cento de gravação e leitura de 100 por cento. Taxa de transferência real pode ser menor e depende de e/s misturar e as condições da rede.

