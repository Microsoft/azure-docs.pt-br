---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 51e1fd18b52d7e215ba43be540156199fb41778e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60482623"
---
#### <a name="to-attach-the-sas-cables"></a>Para conectar os cabos do SAS
1. Identifique os compartimentos principal e EBOD. Os dois compartimentos podem ser identificados pelos respectivos backplanes. Consulte a imagem abaixo para se orientar. 
   
    ![Backplane dos compartimentos principal e EBOD](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Para traseira dos compartimentos principal e EBOD**
   
   | Rótulo | DESCRIÇÃO |
   |:--- |:--- |
   | 1 |Compartimento principal |
   | 2 |Compartimento EBOD |
2. Localize os números de série nos compartimentos principal e EBOD. A etiqueta do número de série está fixada na orelha posterior de cada compartimento. Os números de série devem ser idênticos em ambos os compartimentos. [Contate o Suporte da Microsoft](../articles/storsimple/storsimple-contact-microsoft-support.md) imediatamente se os números de série não corresponderem. Consulte a ilustração a seguir para saber como localizar os números de série.
   
    ![Visão traseira do compartimento mostrando o local do número de série](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Posição da etiqueta do número de série**
   
   | Rótulo | DESCRIÇÃO |
   |:--- |:--- |
   | 1 |Orelha do compartimento |
3. Use os cabos SAS fornecidos para conectar o compartimento EBOD ao compartimento principal, conforme segue:
   
   1. Identifique as quatro portas do SAS no compartimento principal e o compartimento EBOD. As portas SAS são rotuladas como EBOD no compartimento principal e correspondem à porta A compartimento EBOD, conforme mostrado na ilustração da fiação SAS, abaixo.
   2. Use os cabos SAS fornecidos para conectar a porta EBOD à porta A.
   3. A porta EBOD no controlador 0 deve ser conectada à porta A no controlador 0 do EBOD. A porta EBOD no controlador 1 deve ser conectada à porta A no controlador 1 do EBOD. Consulte a ilustração a seguir como guia. 
      
      ![Fiação da SAS para o dispositivo](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **Cabeamento SAS**
      
      | Rótulo | DESCRIÇÃO |
      |:--- |:--- |
      | Uma |Compartimento principal |
      | b |Compartimento EBOD |
      | 1 |Controlador 0 |
      | 2 |Controlador 1 |
      | 3 |Controlador 0 do EBOD |
      | 4 |Controlador 1 do EBOD |
      | 5, 6 |Portas SAS no compartimento principal (rotuladas como EBOD) |
      | 7, 8 |Portas SAS no compartimento EBOD (Porta A) |

