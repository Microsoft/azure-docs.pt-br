---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2abfa29671bd804ee75194ef621fe07f06c015e9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171975"
---
#### <a name="to-create-a-volume"></a>Para criar um volume
1. Na lista tabular de dispositivos na folha **Dispositivos**, selecione seu dispositivo. Clique em **+ Adicionar volume**.

    ![Adicionar um novo volume](./media/storsimple-8000-create-volume-u2/step5createvol1.png)

2. Na folha **Adicionar um volume**:
   
   1. O campo **Selecionar dispositivo** é automaticamente preenchido com o dispositivo atual.

   2. Na lista suspensa, selecione o contêiner de volume em que você precisa adicionar um volume. 

   3. Digite uma **Nome** para o seu volume. Você não pode renomear um volume quando ele é criado.

   4. Na lista suspensa, selecione o **Tipo** para o seu volume. Para cargas de trabalho que exigem garantias locais, menos latências e um melhor desempenho, selecione um volume **Fixado localmente** . Para todos os outros dados, selecione um volume **Em camadas** . Se estiver usando esse volume para dados de arquivamento, marque **Usar este volume para dados de arquivamento acessados com menos frequência**.
      
       Um volume em camadas é provisionado por completo e pode ser criado rapidamente. Se você estiver usando o volume em camadas para dados de arquivamento, a seleção de **Usar este volume para dados de arquivamento acessados com menos frequência** altera o tamanho do bloco de eliminação de duplicação para o volume para 512 KB. Se esse campo não estiver marcado, o volume em camadas correspondente usará um tamanho de bloco de 64 KB. Um tamanho maior de bloco de eliminação de duplicação permite que o dispositivo acelere a transferência de dados de arquivo grandes para a nuvem.
       
       Um volume fixado localmente é provisionado estaticamente e garante que os dados primários no volume permanecem como locais para o dispositivo e não são divulgados na nuvem.  Se você criar um volume fixado localmente, o dispositivo verificará o espaço disponível nas camadas locais para provisionar o volume do tamanho solicitado. A operação de criação de um volume fixado localmente pode envolver a perda de dados existentes do dispositivo para a nuvem e o tempo necessário para criar o volume pode ser longo. O tempo total depende do tamanho do volume provisionado, da largura de banda disponível e dos dados no dispositivo.

   5. Especifique o **Capacidade Provisionada** para o seu volume. Anote a capacidade disponível com base no tipo de volume selecionado. O tamanho do volume especificado não deve exceder o espaço disponível.
      
       É possível provisionar volumes fixados localmente de até 8,5 TB ou volumes em camadas de até 200 TB no dispositivo 8100. No dispositivo 8600, que é maior, você pode provisionar volumes localmente afixados de até 22,5 TB ou volumes em camadas de até 500 TB. Como o espaço local no dispositivo é necessário para hospedar o conjunto de trabalho de volumes em camadas, a criação de volumes fixados localmente afetará o espaço disponível para o provisionamento de volumes em camadas. Portanto, se você criar um volume fixado localmente, o espaço disponível para a criação de volumes em camadas será reduzido. Da mesma forma, se um volume em camadas é criado, o espaço disponível para a criação de volumes localmente fixados será reduzido.
      
       Se você provisionar um volume fixado localmente de 8.5 TB (tamanho máximo permitido) em seu dispositivo 8100, você esgotará todo o espaço local disponível no dispositivo. Você não pode criar um volume em camadas desse ponto em diante, pois não há espaço local no dispositivo para hospedar o conjunto de trabalho do volume em camadas. Os volumes existentes em camadas também afetam o espaço disponível. Por exemplo, se você tiver um dispositivo 8100 que já tem volumes em camadas de 106 TB, somente 4 TB de espaço estarão disponíveis para volumes fixados localmente.

      1. No campo **Hosts conectados**, clique na seta. 

         ![Hosts conectados](./media/storsimple-8000-create-volume-u2/step5createvol2.png)

      1. Na folha **Hosts conectados**, escolha um ACR existente ou adicionar um novo ACR executando as seguintes etapas:

         1. Dê um **Nome** para o seu ACR.
         2. Em **Nome do Iniciador iSCSI**, forneça o iSCSI IQN (nome qualificado) do host Windows. Se você não tiver o IQN, vá para [Obter o IQN de um host do Windows Server](#get-the-iqn-of-a-windows-server-host).

      1. Clique em **Criar**. Será criado um volume com as configurações especificadas.

         ![Clique em Criar.](./media/storsimple-8000-create-volume-u2/step5createvol3.png)

         > [!NOTE]
         > Lembre-se de que o volume que você criou aqui não está protegido. Você precisará criar e associar políticas de backup a este volume para fazer backups agendados. 

