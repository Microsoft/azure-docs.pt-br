---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 467af776af95cf035121250fdcadd2fee65d9805
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171967"
---
#### <a name="to-create-a-volume-container"></a>Para criar um contêiner de volume
1. Acesse o serviço Gerenciador de Dispositivo StorSimple e clique em **Dispositivos**. Na listagem tabular dos dispositivos, selecione e clique em um dispositivo. 

    ![Folha Contêiner de volume](./media/storsimple-8000-create-volume-container/createvolumecontainer1.png)

2. No painel do dispositivo, clique em **+ Adicionar contêiner de volume**

    ![Folha Contêiner de volume](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

3. Na folha **Adicionar contêiner de volume**:
   
   1. O dispositivo é selecionado automaticamente.
   2. Fornecer um **Nome** para o seu contêiner do volume. O nome deve ter entre 3 a 32 caracteres. Você não pode renomear um contêiner de volume quando ele é criado.
   3. Selecione **Habilitar Criptografia de Armazenamento em Nuvem** para habilitar a criptografia dos dados enviados do dispositivo para a nuvem.
   4. Forneça e confirme uma **Chave de Criptografia de Armazenamento em Nuvem** que possui entre 8 a 32 caracteres. Essa chave é usada pelo dispositivo para acessar dados criptografados.
   5. Selecione uma **Conta de Armazenamento** para associar a esse contêiner de volume. Você pode escolher uma conta de armazenamento existente ou a conta padrão gerada no momento da criação do serviço. Você também pode usar a opção **Adicionar novo** para especificar uma conta de armazenamento que não está vinculada a essa assinatura de serviço.
   6. Selecione **Ilimitada** na lista suspensa **Especificar largura de banda** se desejar consumir toda a largura de banda disponível. Você também pode definir essa opção para **Personalizada** para empregar os controles de largura de banda, e especifique um valor entre 1 Mbps e 1.000 Mbps.
      Se você tiver informações de uso da largura de banda disponíveis, você poderá alocar a largura de banda com base em uma agenda especificando **Selecionar um modelo de largura de banda**. Para obter um procedimento passo a passo, vá para [Adicionar um modelo de largura de banda](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template).

      ![Folha Contêiner de volume](./media/storsimple-8000-create-volume-container/createvolumecontainer6b.png)
   7. Clique em **Criar**.

        ![Folha Contêiner de volume](./media/storsimple-8000-create-volume-container/createvolumecontainer6.png)
   
       Você será notificado quando o contêiner de volume for criado com êxito.

       ![Notificação de criação do contêiner de volume](./media/storsimple-8000-create-volume-container/createvolumecontainer8.png)

   O contêiner de volume criado recentemente é listado na lista de contêineres de volume para o dispositivo.

   ![Folha Adicionar contêiner de volume](./media/storsimple-8000-create-volume-container/createvolumecontainer9.png)


