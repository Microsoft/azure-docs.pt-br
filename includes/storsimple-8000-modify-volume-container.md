---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 71f18cf8448060385ea38be9b2719b1ed545c5d2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545195"
---
> [!NOTE] 
> Você não pode modificar as configurações de criptografia e as credenciais da conta de armazenamento associadas a um contêiner de volume depois que ele é criado.

#### <a name="to-modify-a-volume-container"></a>Para modificar um contêiner de volume

1. Vá para o serviço de Gerenciador de Dispositivos do StorSimple e navegue até **gerenciamento > contêineres de volume**.

2. Na lista tabular de contêineres de volume, selecione o contêiner de volume que você deseja modificar. Na página **Dispositivos**, selecione o dispositivo, dê um clique duplo nele e, em seguida, clique na guia **Contêineres de volume**.

3. Na listagem de tabela dos contêineres de volume, selecione o contêiner de volume que você deseja modificar. Na folha que será aberta, clique em **Modificar** na barra de comandos.

    ![Modificar um contêiner de volume](./media/storsimple-8000-modify-volume-container/modify-volume-container-01.png)

4. Na folha **Modificar contêiner de volume** , execute as seguintes etapas:
   
   1. O nome, a chave de criptografia e a conta de armazenamento associados ao contêiner de volume não poderão ser alterados depois de especificados. Altere a configuração de largura de banda associada.<!--STEPS NEED WORK. Updated screen doesn't show alternative to Unlimited or subsequent steps if they customize bandwidth. Can we talk them through this (briefly)?-->
      
       ![Alterar configuração de largura de banda](./media/storsimple-8000-modify-volume-container/modify-volume-container-02.png)<!--New graphic based on: modify-volume-container-bw-setting.png-->

   1.  Clique em **OK**.<!--If they choose Custom, do they still click OK, or are there more steps?-->

5. Na próxima página da caixa de diálogo **Modificar Contêiner de Volume**:<!--This step happens only if they choose Custom bandwidth? Are the steps similar to those in "Add volume container," step 3f, above?"-->
   
   1. Na lista suspensa, escolha um modelo existente de largura de banda.
   1. Examine as configurações de agendamento para o modelo de largura de banda especificado.
   1. Clique em **Salvar** e confirme as alterações.
      
       ![Confirmar alterações](./media/storsimple-8000-modify-volume-container/modify-volume-container-03.png)

      A folha **Contêineres de volume** será atualizada para refletir as alterações.
