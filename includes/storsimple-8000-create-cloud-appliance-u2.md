---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d1ca6d37d6133786aff7ad3156fea2a0c22dfb97
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171986"
---
#### <a name="to-create-a-cloud-appliance"></a>Para criar um dispositivo de nuvem

1. No Portal do Azure, vá até o serviço **StorSimple Device Manager** .
2. Acesse a folha **Dispositivos**. Na barra de comandos na folha de resumo do serviço, clique em **Criar dispositivo de nuvem**.
    ![Criar dispositivo de nuvem StorSimple](./media/storsimple-8000-create-cloud-appliance-u2/sca-create1.png)
3. Na folha **Criar dispositivo de nuvem**, especifique os detalhes a seguir.
   
    ![Criar dispositivo de nuvem StorSimple](./media/storsimple-8000-create-cloud-appliance-u2/sca-create2m.png)
   
   1. **Nome** – Um nome exclusivo para seu dispositivo de nuvem.
   2. **Modelo** – Escolha o modelo do dispositivo de nuvem. Um dispositivo 8010 oferece 30 TB de armazenamento padrão enquanto 8020 tem 64 TB de armazenamento Premium. Especifique 8010 para implantar cenários de recuperação de nível de item de backups. Selecione 8020 para implantar cargas de trabalho de baixa latência e alto desempenho, ou use como um dispositivo secundário para recuperação de desastre.
   3. **Versão** – Escolha a versão do dispositivo de nuvem. A versão corresponde à versão da imagem do disco virtual que é usada para criar o dispositivo de nuvem. A versão do dispositivo de nuvem determinará em qual dispositivo físico você pode aplicar o failover ou clonar. É importante que você crie uma versão apropriada do dispositivo de nuvem.
   4. **Rede virtual** – Especifique uma rede virtual que você deseja usar com este dispositivo de nuvem. Se estiver usando o Armazenamento Premium, você deverá selecionar uma rede virtual compatível com a conta de Armazenamento Premium. As redes virtuais sem suporte ficam desabilitadas na lista suspensa. Você receberá um aviso se selecionar uma rede virtual sem suporte.
   5. **Subrede** - Com base na rede virtual selecionada, a lista suspensa exibe as sub-redes associadas. Atribua uma sub-rede ao dispositivo de nuvem.
   6. **Conta de armazenamento** – Selecione uma conta de armazenamento para manter a imagem do dispositivo de nuvem durante o provisionamento. Esta conta de armazenamento deve estar na mesma região do dispositivo de nuvem e da rede virtual. Ela não deve ser usada para armazenamento de dados do dispositivo de nuvem. Por padrão, uma nova conta de armazenamento é criada para essa finalidade. No entanto, se você souber que você já tem uma conta de armazenamento adequada para esse uso, poderá selecioná-la da lista. Se você criar um dispositivo de nuvem premium, a lista suspensa exibirá apenas contas de armazenamento Premium.
      
      > [!NOTE]
      > O dispositivo de nuvem só pode trabalhar com as contas de armazenamento do Azure.
    
   7. Marque a caixa de seleção para indicar que você entende que os dados armazenados no dispositivo de nuvem serão hospedados em um datacenter da Microsoft.
       * Quando você usa apenas um dispositivo físico, sua chave de criptografia é mantida com seu dispositivo; portanto, a Microsoft não pode descriptografá-la.

       * Quando você usa um dispositivo de nuvem, a chave de criptografia e a chave de descriptografia são armazenadas no Microsoft Azure. Para saber mais, consulte [Considerações de segurança para usar um dispositivo de nuvem](../articles/storsimple/storsimple-security.md).
   8. Clique em **Criar** para provisionar o dispositivo de nuvem. O dispositivo pode levar cerca de 30 minutos para ser provisionado. Você receberá uma notificação quando dispositivo de nuvem for criado com êxito. Acesse a folha Dispositivos, e a lista de dispositivos será atualizada para exibir o dispositivo de nuvem. O status do dispositivo é **Pronto para configurar**.
      
      ![Pronto para configurar o Dispositivo de nuvem StorSimple](./media/storsimple-8000-create-cloud-appliance-u2/sca-create3.png)

