---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: fbc77b960cac0db2d077345c74d64485bd7ad8bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994479"
---
Uma URL usada para notificar o cliente (usando uma solicitação POST) sobre os eventos a seguir:

- Alteração de estado de indexação: 
    - Propriedades:    
    
        |Nome|Descrição|
        |---|---|
        |id|A ID do vídeo|
        |state|O estado do vídeo|  
    - Exemplo: https: \/ /test.com/notifyme?projectName=MyProject&ID = 1234abcd&estado = processado
- Pessoa identificada no vídeo:
  - Propriedades
    
      |Nome|Descrição|
      |---|---|
      |id| A ID do vídeo|
      |faceId|A identificação de face que aparece no índice de vídeo|
      |knownPersonId|A ID da pessoa que é exclusiva dentro de um modelo de detecção facial|
      |personName|O nome da pessoa|
        
    - Exemplo: https: \/ /test.com/notifyme?projectName=MyProject&ID = 1234abcd&faceid = 12&knownPersonId = CCA84350-89B7-4262-861C-3CAC796542A5&PersonName = Inigo_Montoya 
