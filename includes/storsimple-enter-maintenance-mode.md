---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67171949"
---
#### <a name="to-enter-maintenance-mode"></a>Para entrar no modo de Manutenção
1. No menu do console serial, selecione a opção 1, **Log in com acesso completo**.
2. Digite a senha. A senha padrão é **Senha1**.
3. No prompt de comando, digite
   
     `Enter-HcsMaintenanceMode`
4. Você verá uma mensagem de aviso informando que o modo de Manutenção interromperá todas as solicitações de E/S e a conexão com o portal clássico do Azure, e você será solicitado a confirmar. Digite **Y** para entrar no modo Manutenção.
   
    Ambos os controladores serão reiniciados. Quando a reinicialização estiver concluída, outra mensagem será exibida indicando que o dispositivo está em modo de Manutenção.

