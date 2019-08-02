---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171953"
---
| **Identificador de limite** | **Limite** | **Comentários** |
| --- | --- | --- |
| Capacidade total (incluindo nuvem) |Até 64 TB por dispositivo virtual |Você pode fazer o failover de uma Matriz Virtual do StorSimple completa para outra matriz vazia. Se você tentar restaurar os compartilhamentos, verifique se tem espaço suficiente no dispositivo para concluir a operação. Depois de exceder 32 TB, você não poderá restaurar no mesmo dispositivo. |
| Número máximo de credenciais de conta de armazenamento por dispositivo |1 | |
| Número máximo de volumes/compartilhamentos |16 | |
| Tamanho mínimo de um compartilhamento em camadas |500 GB | |
| Tamanho mínimo de um volume em camadas |500 GB | |
| Tamanho máximo de um compartilhamento em camadas |20 TB | |
| Tamanho máximo de um volume em camadas |5 TB | |
| Tamanho mínimo de um compartilhamento fixado localmente |50 GB | |
| Tamanho mínimo de um volume fixado localmente |50 GB | |
| Tamanho máximo de um compartilhamento fixado localmente |2 TB | |
| Tamanho máximo de um volume fixado localmente |200 GB | |
| Número máximo de conexões iSCSI dos iniciadores |512 | |
| Número máximo de registros de controle de acesso por dispositivo |64 | |
| Número máximo de backups retidos pelo dispositivo virtual na pasta *.backups* do servidor de arquivos |5 |Isso inclui os backups manuais e os backups agendados mais recentes (gerados pela política de backup padrão). |
| Número máximo de backups agendados mantidos pelo dispositivo |55 |30 backups diários<br>12 backups mensais<br>13 backups anuais |
| Número máximo de backups manuais mantidos pelo dispositivo |45 | |
| O número máximo de arquivos por compartilhamento para um servidor de arquivos |1 milhão |Ao ser realizada uma restauração de dispositivo, os tempos de restauração são proporcionais ao número de arquivos em todos os compartilhamentos no dispositivo. |
| Número máximo de arquivos por volume para um servidor iSCSI |1 milhão | |
| Número máximo de arquivos por matriz virtual |4 milhões | |
| Tempo de recuperação de restauração |Restauração rápida |A restauração é baseada no mapa de calor e depende do tamanho do volume.<br>Operações de backup podem ocorrer enquanto uma operação de restauração está em andamento. |

