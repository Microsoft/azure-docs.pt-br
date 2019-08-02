---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: db2104020e9478b1fedf68e1c9467f75e16044e2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171959"
---
#### <a name="to-cable-for-power"></a>Para cabear o recebimento de energia
1. Certifique-se de que os interruptores de energia em cada PCMs (Módulos de Energia e Refrigeração) estejam na posição de DESLIGADO.
2. Conecte os cabos de energia a cada PCMs no compartimento principal.
3. Conecte os cabos de energia às PDUs, conforme mostrado na imagem a seguir. Verifique se os dois PCMs usam fontes de energia separadas.
   
   > [!IMPORTANT]
   > Para garantir a alta disponibilidade do seu sistema, recomendamos que siga fielmente o esquema de cabeamento de energia mostrado no diagrama que segue. 
   > 
   > 
   
    ![Cabear o dispositivo 2U para recebimento de energia](./media/storsimple-cable-8100-for-power/HCSCableYour2UDeviceforPower.png)
   
    **Fiação de energia em um dispositivo 8100**
   
   | Rotular | DESCRIÇÃO |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |Controlador 1 |
   | 3 |Controlador 0 |
   | 4 |PCM 1 |
   | 5 |PDUs |
4. Para ativar o sistema, inverta os comutadores de energia em ambos os PCMs para a posição LIGADO.

