---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/25/2019
ms.author: alkohli
ms.openlocfilehash: 1d52117440028c75b249f469f2b3576c2ab1c5c5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172586"
---
Etapa final é preparar o dispositivo para o envio. Nesta etapa, todos os compartilhamentos do dispositivo são colocados offline. As ações não poderão ser acessadas depois que você iniciar esse processo.

> [!IMPORTANT]
> A preparação para o envio é obrigatória, pois sinaliza os dados que não estão em conformidade com as convenções de nomenclatura do Azure. Ignorar essa etapa pode causar eventuais falhas no upload dos dados provocadas por dados fora de conformidade.

1. Vá para **Preparar para enviar** e clique em **Iniciar preparação**. Por padrão, as somas de verificação são computadas enquanto os dados estão sendo copiados. A Preparação para o envio conclui o cálculo da soma de verificação e cria a lista de arquivos ( *– arquivos de BOM*). O cálculo da soma de verificação pode levar de horas a dias dependendo do tamanho dos seus dados. 
   
    ![Preparar para o envio 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Se, por algum motivo, você desejar interromper a preparação do dispositivo, clique em **Parar a preparação**. É possível retomar a preparação para o envio mais tarde.
        
    ![Preparar para o envio 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. A preparação para o envio é iniciada e os compartilhamentos do dispositivo ficam offline. Você verá um lembrete para baixar a etiqueta de remessa depois que o dispositivo estiver pronto.

    ![Baixar o lembrete da etiqueta de remessa](media/data-box-prepare-to-ship/download-shipping-label-reminder.png)

3. O status do dispositivo é atualizado para *Pronto para envio* e o dispositivo será bloqueado depois que sua preparação for concluída.
        
    ![Preparar para o envio 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Se desejar copiar mais dados para o dispositivo, será possível desbloqueá-lo, copiar mais dados e executar a preparação para o envio novamente.

    Se houver erros nesta etapa, será necessário baixar o log de erros e resolvê-los. Depois de resolver os erros, execute **Preparar para o envio**.

4. Depois que a preparação para o envio for concluída com êxito (sem erros), baixe a lista de arquivos (também conhecida como o manifesto) copiada neste processo. Posteriormente, você poderá usar essa lista para verificar os arquivos carregados no Azure. Para obter mais informações, confira [Inspecionar arquivos de BOM durante a Preparação para o envio](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![Preparar para o envio 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

5. Desligar o dispositivo. Vá para **desligar ou reiniciar** da página e clique em **desligar**. Quando solicitado a confirmação, clique em **OK** para continuar.

6. Remova os cabos. A próxima etapa é enviar o dispositivo à Microsoft.
