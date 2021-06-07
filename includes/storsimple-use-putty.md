---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5c62c67cd401c043352b06e6e6070a7fc0f1296
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995414"
---
#### <a name="to-connect-through-the-serial-console"></a>Para conectar-se por meio do console serial
1. Conecte o cabo serial ao dispositivo (diretamente ou por meio de um adaptador serial USB).
2. Abra o **Painel de Controle** e, em seguida, abra o **Gerenciador de Dispositivos**.
3. Identifique a porta COM como mostrado na ilustração a seguir.
   
     ![Conectando por meio do console serial](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)
4. Inicie o PuTTY. 
5. No painel direito, altere o **Tipo de conexão** para **Serial**.
6. No painel à direita, digite a porta COM apropriada. Verifique se os parâmetros da configuração serial foram definidos da seguinte maneira:
   
   * Velocidade: 115.200
   * Bits de dados: 8
   * Bits de parada: 1
   * Paridade: nenhuma
   * Controle de fluxo: nenhum
     
     Essas configurações são mostradas na ilustração a seguir.
     
     ![Configurações do PuTTY](./media/storsimple-use-putty/HCS_PuttyConfig-include.png) 
     
     > [!NOTE]
     > Se a configuração de controle de fluxo padrão não funcionar, tente definir o controle de fluxo como XON/XOFF.
     > 
     > 
7. Clique em **Abrir** para iniciar uma sessão serial.

