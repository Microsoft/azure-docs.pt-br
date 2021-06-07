---
title: Criar um grupo de posicionamento por proximidade usando o portal
description: Saiba como criar um grupo de posicionamento por proximidade usando o portal do Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: daf844870670c14db5208f45fbd9c9adf46be985
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504619"
---
# <a name="create-a-proximity-placement-group-using-the-azure-portal"></a>Criar um grupo de posicionamento por proximidade usando o portal do Azure

Para colocar as VMs o mais próximo possível umas das outras, atingindo a menor latência possível, implante-as dentro de um [grupo de posicionamento por proximidade](../co-location.md#proximity-placement-groups).

Um grupo de posicionamento por proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos uns dos outros. Os grupos de posicionamento por proximidade são úteis para cargas de trabalho em que a baixa latência é um requisito.

> [!NOTE]
> Não é possível usar grupos de posicionamento por proximidade com hosts dedicados.
>
> Se você quiser usar zonas de disponibilidade juntamente com os grupos de posicionamento, será necessário verificar se as VMs do grupo de posicionamento também estão todas na mesma zona de disponibilidade.
>

## <a name="create-the-proximity-placement-group"></a>Criar o grupo de posicionamento por proximidade

1. Digite **grupo de posicionamento por proximidade** na pesquisa.
1. Em **Serviços**, nos resultados da pesquisa, selecione **grupos de posicionamento por proximidade**.
1. Na página **grupos de posicionamento por proximidade**, selecione **Adicionar**.
1. Na guia **Básico**, em **Detalhes do projeto**, verifique se a assinatura correta está selecionada.
1. Em **grupo de recursos**, selecione **Criar** para criar um grupo ou selecione um grupo de recursos vazio que já existe, na lista suspensa. 
1. Em **Região**, selecione a localização em que deseja que o grupo de posicionamento por proximidade seja criado.
1. Em **Nome do grupo de posicionamento por proximidade**, digite um nome e selecione **Examinar + criar**.
1. Depois da validação, selecione **Criar** para criar o grupo de posicionamento por proximidade.

    ![Captura de tela da criação de um grupo de posicionamento por proximidade](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Criar uma máquina virtual

1. Ao criar uma VM no portal, vá para a guia **Avançado**. 
1. Na seleção do **Grupo de posicionamento por proximidade**, escolha o grupo de posicionamento correto. 

    ![Captura de tela da seção do grupo de posicionamento por proximidade ao criar uma VM no portal](./media/ppg/vm-ppg.png)

1. Quando você terminar de fazer todas as outras seleções necessárias, escolha **Examinar + criar**.
1. Depois da validação, selecione **Criar** para implantar a VM no grupo de posicionamento.


## <a name="add-vms-in-an-availability-set-to-a-proximity-placement-group"></a>Adicionar as VMs de um conjunto de disponibilidade a um grupo de posicionamento por proximidade

Se a VM fizer parte do conjunto de disponibilidade, você precisará adicionar o conjunto de disponibilidade ao grupo de posicionamento antes de adicionar as VMs.

1. No [portal](https://portal.azure.com), pesquise por *Conjuntos de disponibilidade* e selecione o seu conjunto de disponibilidade nos resultados.
1. Pare\desaloque cada VM no conjunto de disponibilidade selecionando a VM e depois **Parar** na página da VM e clique em **OK** para interromper a VM.
1. Na página do conjunto de disponibilidade, verifique se todas as VMs têm o **Status** listado como **Parado (desalocado)** .
1. No menu à esquerda, selecione **Configuração**.
1. Em **Grupo de posicionamento por proximidade**, selecione um grupo de posicionamento na lista suspensa e depois escolha **Salvar**.
1. Selecione **Visão Geral** no menu à esquerda para ver a lista de VMs novamente. 
1. Selecione cada VM no conjunto de disponibilidade e depois escolha **Iniciar**, na página de cada VM. 


## <a name="add-existing-vm-to-placement-group"></a>Adicionar VM existente ao grupo de posicionamento 


1. Na página da VM, selecione **Parar**.
1. Depois que o status da VM for listado como **Parado (desalocado)** , selecione **Configuração** no menu à esquerda.
1. Em **Grupo de posicionamento por proximidade**, selecione um grupo de posicionamento na lista suspensa e depois escolha **Salvar**.
1. Selecione **Visão Geral** no menu à esquerda e depois escolha **Iniciar** para reiniciar a VM.

 

## <a name="next-steps"></a>Próximas etapas

Você também pode usar o [Azure PowerShell](proximity-placement-groups.md) para criar grupos de posicionamento por proximidade.