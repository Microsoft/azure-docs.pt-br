---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8c60e0275853f3c879db22f5414f0fbbbdb47b85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86050400"
---
## <a name="preparing-for-updates"></a>Preparar para atualizações
Você precisará executar as seguintes etapas antes de examinar e aplicar a atualização:

1. Tirar um instantâneo dos dados do dispositivo na nuvem.
2. Verifique se os IPs fixos do controlador são roteáveis e podem se conectar à Internet. Esses IPs fixo serão usado para atender às atualizações para o seu dispositivo. Você pode testar isso executando o seguinte cmdlet em cada controlador de interface do Windows PowerShell do dispositivo:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **Saída de amostra para testar conexão quando IPs fixos podem se conectar à Internet**

    ```output
    Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

    Source      Destination     IPV4Address      IPV6Address
    ----------------- -----------  -----------
    HCSNODE0  bing.com        204.79.197.200
    HCSNODE0  bing.com        204.79.197.200
    HCSNODE0  bing.com        204.79.197.200
    HCSNODE0  bing.com        204.79.197.200

    Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

    Source      Destination       IPV4Address    IPV6Address
    ----------------- -----------  -----------
    HCSNODE0  204.79.197.200  204.79.197.200
    HCSNODE0  204.79.197.200  204.79.197.200
    HCSNODE0  204.79.197.200  204.79.197.200
    HCSNODE0  204.79.197.200  204.79.197.200
    ```

Depois que essas pré-verificações manuais foi concluído com êxito, você poderá verificar e instalar as atualizações.

