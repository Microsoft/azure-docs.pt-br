---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/19/2020
ms.author: alkohli
ms.openlocfilehash: 97491000c63c966c3d8159b9361fcb60e062aa49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86208735"
---
Etapa final é preparar o dispositivo para o envio. Nesta etapa, todos os compartilhamentos do dispositivo são colocados offline. As ações não poderão ser acessadas depois que você iniciar esse processo. Você também pode optar por limpar o dispositivo por meio do qual os dados locais no dispositivo são apagados permanentemente. Quando a etapa for concluída, a tela de papel eletrônico mostrará a etiqueta de remessa de devolução.

> [!IMPORTANT]
> - Você pode optar por apagar os dados no dispositivo permanentemente nessa etapa e limpar o dispositivo. Os dados em sua conta de Armazenamento do Azure permanecerão e acumularão encargos. É recomendável que você exclua esses dados somente depois de ter verificado que a cópia dos dados em um servidor de dados local foi concluída.

1. Vá para **Preparar para o envio** e selecione **Iniciar preparação**. 
   
    ![Preparar para o envio 1](media/data-box-export-prepare-to-ship/prepare-to-ship1.png)

 
2. A preparação para o envio é iniciada e os compartilhamentos do dispositivo ficam offline. Por padrão, a limpeza do dispositivo é executada e os dados nos discos dele são apagados pelo Data Box. 


    ![Preparar para o envio 2](media/data-box-export-prepare-to-ship/prepare-to-ship2.png)

    Você pode recusar o procedimento de limpeza desmarcando a caixa de combinação. Nesse caso, os dados do dispositivo são apagados posteriormente no datacenter.

    ![Preparar para o envio 3](media/data-box-export-prepare-to-ship/prepare-to-ship3.png)


3. Quando a **Preparação para o envio** for iniciada, você verá uma notificação de progresso com uma barra de progresso.

    ![Preparar para o envio 4](media/data-box-export-prepare-to-ship/prepare-to-ship4.png)

4. Depois que a preparação para o envio e a limpeza do dispositivo (se selecionada) estiverem concluídas, a tela de papel eletrônico no dispositivo também exibirá o rótulo de remessa de devolução. 

    Você também verá um número de referência de conclusão que é usado para coisas diferentes, dependendo do país no qual você está localizado. Você também pode ver as instruções para a remessa de devolução. As instruções variam dependendo do tipo de envio escolhido, gerenciado pela Microsoft ou autogerenciado. 
        
    ![Preparar para o envio 5](media/data-box-export-prepare-to-ship/prepare-to-ship5.png)


5. Desligar o dispositivo. Localize o botão de energia na parte frontal do painel do dispositivo de operação. Desative o dispositivo.

    ![Botão de energia do Data Box](media/data-box-export-prepare-to-ship/data-box-powered-door-open.png)

6. Remova os cabos. A próxima etapa é enviar o dispositivo à Microsoft.
