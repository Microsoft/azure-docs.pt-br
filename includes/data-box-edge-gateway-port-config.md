---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 71460af42b4da97a578ae5a3e23a714577e71867
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86218412"
---
| Nº da porta| Entrada ou saída | Escopo da porta| Obrigatório|   Observações |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|Saída|WAN |Não|A porta de saída é usada para acesso à internet para recuperar atualizações. <br>O proxy Web de saída é configurável pelo usuário. |
| TCP 443 (HTTPS)|Saída|WAN|Sim|A porta de saída é usada para acessar dados na nuvem.<br>O proxy Web de saída é configurável pelo usuário.|
| UDP 123 (NTP)|Saída|WAN|Em alguns casos<br>Consulte as observações|Esta porta só será necessária se você estiver usando um servidor NTP baseado na internet.  |   
| UDP 53 (DNS)|Saída|WAN|Em alguns casos<br>Consulte as observações|Esta porta só será necessária se você estiver usando um servidor DNS baseado na internet.<br>É recomendado usar um servidor DNS local. |
| TCP 5985 (WinRM)|Entrada/saída|LAN|Em alguns casos<br>Consulte as observações|Esta porta é necessária para se conectar ao dispositivo por meio do PowerShell remoto via HTTP.  |
| UDP 67 (DHCP)|Saída|LAN|Em alguns casos<br>Consulte as observações|Essa porta será necessária apenas se você estiver usando um servidor DHCP local.  |
| TCP 80 (HTTP)|Entrada/saída|LAN|Sim|Essa porta é a porta de entrada da interface do usuário local no dispositivo para gerenciamento local. <br>O acesso à interface do usuário local por HTTP será redirecionado automaticamente para HTTPS.  |
| TCP 443 (HTTPS)|Entrada/saída|LAN|Sim|Essa porta é a porta de entrada da interface do usuário local no dispositivo para gerenciamento local. |
| TCP 445 (SMB)|Em|LAN|Em alguns casos<br>Consulte as observações|Essa porta será necessária apenas se você estiver se conectando via SMB. |
| TCP 2049 (NFS)|Em|LAN|Em alguns casos<br>Consulte as observações|Essa porta será necessária apenas se você estiver se conectando via NFS. |
