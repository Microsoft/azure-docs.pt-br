---
title: Criar uma biblioteca de conteúdo para implantar VMs na solução VMware do Azure
description: Crie uma biblioteca de conteúdo para implantar uma VM em uma nuvem privada da solução Azure VMware.
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: a50b12ef8e139bf7de171398fd28f74fc3f310c9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381997"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Criar uma biblioteca de conteúdo para implantar VMs na solução VMware do Azure

Uma biblioteca de conteúdo armazena e gerencia o conteúdo na forma de itens de biblioteca. Um item de biblioteca consiste em um ou mais arquivos que você usa para implantar VMs (máquinas virtuais). 

Neste artigo, vamos examinar o procedimento para criar uma biblioteca de conteúdo.  Em seguida, vamos examinar a implantação de uma VM usando uma imagem ISO da biblioteca de conteúdo.

## <a name="prerequisites"></a>Pré-requisitos

Um segmento NSX-T (comutador lógico) e um serviço DHCP gerenciado são necessários para concluir este tutorial.  Para obter mais informações, consulte o artigo [como gerenciar o DHCP no Azure VMware Solution](manage-dhcp.md) .

## <a name="create-a-content-library"></a>Criar uma biblioteca de conteúdo

1. No cliente vSphere local, selecione **Menu > Bibliotecas de Conteúdo**.

   ![Selecione Menu -> Bibliotecas de Conteúdo](./media/content-library/vsphere-menu-content-libraries.png)

1. Selecione o botão **Adicionar** para criar uma nova biblioteca de conteúdo.

   ![Selecione o botão Adicionar para criar uma nova biblioteca de conteúdo.](./media/content-library/create-new-content-library.png)

1. Especifique um nome e confirme o endereço IP do vCenter Server e selecione **Próximo**.

   ![Especifique um nome e as notas de sua escolha e clique em Avançar.](./media/content-library/new-content-library-step1.png)

1. Selecione a **Biblioteca de conteúdo local** e selecione **Próximo**.

   ![Para este exemplo, vamos criar uma biblioteca de conteúdo local. Selecione Próximo.](./media/content-library/new-content-library-step2.png)

1. Selecione o armazenamento de dados que armazenará sua biblioteca de conteúdo e clique em **Avançar**.

   ![Selecione o armazenamento de dados em que você deseja hospedar a biblioteca de conteúdo e selecione próximo.](./media/content-library/new-content-library-step3.png)

1. Examine e verifique as configurações da biblioteca de conteúdo e clique em **Concluir**.

   ![Verifique suas Configurações e selecione Concluir.](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>Carregar uma imagem ISO na biblioteca de conteúdo

Agora que a biblioteca de conteúdo foi criada, é possível adicionar uma imagem ISO para implantar uma VM em um cluster de nuvem privada. 

1. No cliente vSphere, selecione **Menu > Bibliotecas de Conteúdo**.

1. Clique com o botão direito do mouse na biblioteca de conteúdo que você deseja usar para o novo ISO e selecione **Importar Item**.

1. Importe um item de biblioteca para a origem seguindo um destes procedimentos e selecione **Importar**:
   1. Selecione a URL e forneça uma URL para baixar um ISO.

   1. Selecione **Arquivo Local** para carregar do seu sistema local.

   > [!TIP]
   > Opcional; é possível definir um nome de item personalizado e as notas para o destino.

1. Abra a biblioteca e selecione a guia **Outros Tipos** para verificar se o ISO foi carregado com êxito.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Implantar uma VM em um cluster de nuvem privada

1. No cliente vSphere, selecione **Menu > Hosts e Clusters**.

1. No painel esquerdo, expanda a árvore e selecione um cluster.

1. Selecione **Ações > Nova Máquina Virtual**.

1. Percorra o assistente e modifique as configurações desejadas.

1. Selecione **Nova Unidade de CD/DVD > Dispositivo Cliente > Arquivo ISO da Biblioteca de Conteúdo**.

1. Selecione o ISO carregado na seção anterior e clique em **OK**.

1. Marque a caixa de seleção **Conectar** para que o ISO seja montado no momento da ativação.

1. Selecione **Nova Rede > Selecione a lista suspensa > Procurar**.

1. Selecione o **comutador lógico (segmento)** e selecione **OK**.

1. Modifique outras configurações de hardware e selecione **Próximo**.

1. Verifique as configurações e selecione **Concluir**.


## <a name="next-steps"></a>Próximas etapas

Agora que você já abordou a criação de uma biblioteca de conteúdo para implantar VMs na solução VMware do Azure, talvez queira saber mais sobre:

- [Implantando e Configurando o VMware HCX](tutorial-deploy-vmware-hcx.md) para migrar cargas de trabalho de VM para sua nuvem privada.
- [Gerenciamento do ciclo de vida de VMs de solução do Azure VMware](lifecycle-management-of-azure-vmware-solution-vms.md).

<!-- LINKS - external-->

<!-- LINKS - internal -->
