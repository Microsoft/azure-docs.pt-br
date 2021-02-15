---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 07/01/2020
ms.author: alkohli
ms.openlocfilehash: a78bce9f6e714aa5564791f6bb63996554beabf1
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375903"
---
Etapa final é preparar o dispositivo para o envio. Nesta etapa, todos os compartilhamentos do dispositivo são colocados offline. As ações não poderão ser acessadas depois que você iniciar esse processo.

> [!IMPORTANT]
> A preparação para o envio é obrigatória, pois sinaliza os dados que não estão em conformidade com as convenções de nomenclatura do Azure. Esta etapa impede possíveis falhas de upload de dados devido a dados não conformes.

1. Vá para **Preparar para enviar** e clique em **Iniciar preparação**. Por padrão, as somas de verificação são computadas enquanto os dados estão sendo copiados. A Preparação para o envio conclui o cálculo da soma de verificação e cria a lista de arquivos ( *– arquivos de BOM* ). O cálculo da soma de verificação pode levar de horas a dias dependendo do tamanho dos seus dados. 
   
    ![Preparar para o envio 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Se, por algum motivo, você desejar interromper a preparação do dispositivo, clique em **Parar a preparação**. É possível retomar a preparação para o envio mais tarde.
        
    ![Preparar para o envio 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. A preparação para o envio é iniciada e os compartilhamentos do dispositivo ficam offline. <!--You see a reminder to download the shipping label once the device is ready.--> O status do dispositivo é atualizado para *Pronto para envio* e o dispositivo será bloqueado depois que sua preparação for concluída.
        
    ![Preparar para o envio 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Se desejar copiar mais dados para o dispositivo, será possível desbloqueá-lo, copiar mais dados e executar a preparação para o envio novamente.

    Se houver erros nesta etapa, o status será atualizado para *Verificação concluída com erros*. Desbloqueie o dispositivo e vá para a página **Conectar e copiar** , baixe a lista de problemas e resolva os erros.

    ![Preparar para o envio 4](media/data-box-prepare-to-ship/prepare-to-ship4.png)

    Depois de resolver os erros, execute **Preparar para o envio**.

4. Depois que a preparação para o envio for concluída com êxito (sem erros), siga estas etapas:

    1. Anote o número de referência de conclusão. Dependendo do país em que você está, esse número pode ser necessário para operações diferentes.
    2. Baixe a lista de arquivos (também conhecidos como o manifesto) copiados nesse processo. Posteriormente, você poderá usar essa lista para verificar os arquivos carregados no Azure. Para obter mais informações, confira [Inspecionar arquivos de BOM durante a Preparação para o envio](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
        ![Preparar para o envio 5](media/data-box-prepare-to-ship/prepare-to-ship5.png)
    3. Selecione e baixe as instruções de envio para o dispositivo. As instruções de envio são diferentes dependendo do país no qual você está.
    4. Se E-Ink não estiver exibindo a etiqueta de remessa, você poderá baixar a etiqueta de remessa reversa aqui. 

5. Desligar o dispositivo. Vá para **desligar ou reiniciar** da página e clique em **desligar**. Quando solicitado a confirmação, clique em **OK** para continuar.

6. Remova os cabos. A próxima etapa é enviar o dispositivo à Microsoft.
