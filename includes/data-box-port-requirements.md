---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839759"
---
| Nº da porta| Entrada ou saída | Escopo da porta| Obrigatório| Observações |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|No|LAN|Sim|Esta porta é usada para se conectar ao armazenamento de Blog de caixa de dados APIs REST sobre HTTP. Se não estiver se conectando para APIs REST, isso redireciona automaticamente a interface do usuário da web local mais 8443. |
| TCP 443 (HTTPS)|No|LAN|Sim|Esta porta é usada para se conectar ao armazenamento de Blog de caixa de dados APIs REST sobre HTTPS. Se não estiver se conectando para APIs REST, isso redireciona automaticamente a interface do usuário da web local mais 8443. |
| TCP 8443 (Alt-HTTPS)|No|LAN|Sim|Isso é uma porta alternativa para HTTPS e é usado ao se conectar a interface web local para o gerenciamento de dispositivo. |
| TCP 445 (SMB)|Entrada/saída|LAN|Em alguns casos<br>Consulte as observações|Esta porta é necessária somente se você estiver se conectando por meio do SMB. |
| TCP 2049 (NFS)|Entrada/saída|LAN|Em alguns casos<br>Consulte as observações|Esta porta é necessária somente se você estiver se conectando por meio de NFS. |
| TCP 111 (NFS)|Entrada/saída|LAN|Em alguns casos<br>Consulte as observações|Esta porta é usada para o mapeamento de porta/rpcbind e necessário somente se você estiver se conectando por meio de NFS.  |
