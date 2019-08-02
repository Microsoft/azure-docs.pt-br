---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171958"
---
#### <a name="to-cable-your-device-for-power"></a>Para conectar seu dispositivo à alimentação
> [!NOTE]
> Ambos os compartimentos no seu dispositivo StorSimple incluem PCMs redundantes. Para cada compartimento, os PCMs devem ser instalados e conectados a diferentes fontes de energia para garantia de alta disponibilidade.
> 
> 

1. Verifique se os comutadores de energia em todos os PCMs estão na posição DESLIGADO.
2. No compartimento principal, conecte os cabos de energia a ambos os PCMs. Os cabos de energia são identificados em vermelho no diagrama de fiação de energia abaixo.
3. Verifique se os dois PCMs do compartimento principal usam fontes de energia separadas.
4. Conecte os cabos de energia às unidades de distribuição de energia do rack, conforme mostrado no diagrama de fiação a seguir.
5. Repita as etapas de 2 a 4 para o compartimento EBOD.
6. Ligue o compartimento EBOD invertendo o comutador de energia em cada PCM para a posição de LIGADO.
7. Verifique se o compartimento EBOD está ligado observando se os LEDs de cor verde na parte posterior do controlador do EBOD ACENDERAM.
8. Ligue o compartimento principal invertendo cada comutador do PCM para a posição de LIGADO.
9. Verifique se o sistema está ligado garantindo que os LEDs do controlador do dispositivo ACENDERAM.
10. Verifique se a conexão entre o controlador do EBOD e o controlador do dispositivo está ativa observando que os quatro LEDs do lado da porta do SAS, no controlador do EBOD esteja verde.
    
    > [!IMPORTANT]
    > Para garantir a alta disponibilidade do seu sistema, recomendamos que siga fielmente o esquema de cabeamento de energia mostrado no diagrama que segue.
    > 
    > 
    
    ![Cabeamento do dispositivo 4U para alimentação](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Cabeamento de energia**
    
    | Rotular | DESCRIÇÃO |
    |:--- |:--- |
    | 1 |Compartimento principal |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |Controlador 0 |
    | 5 |Controlador 1 |
    | 6 |Controlador 0 do EBOD |
    | 7 |Controlador 1 do EBOD |
    | 8 |Compartimento EBOD |
    | 9 |PDUs |

