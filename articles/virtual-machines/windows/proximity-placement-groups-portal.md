---
title: Criar um grupo de posicionamento por proximidade usando o portal
description: Saiba como criar um grupo de posicionamento de proximidade usando o portal do Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/24/2020
ms.author: cynthn
ms.openlocfilehash: 45d7977dda4870be9a087b3277dd1db828c91178
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98871427"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Criar um grupo de posicionamento por proximidade usando o portal

Para obter as VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-las em um [grupo de posicionamento de proximidade](../co-location.md#proximity-placement-groups).

Um grupo de posicionamento por proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos uns dos outros. Os grupos de posicionamento por proximidade são úteis para cargas de trabalho em que a baixa latência é um requisito.

> [!NOTE]
> Os grupos de posicionamento de proximidade não podem ser usados com hosts dedicados.
>
> Se você quiser usar zonas de disponibilidade junto com grupos de posicionamento, precisará certificar-se de que as VMs no grupo de posicionamento também estejam todas na mesma zona de disponibilidade.
>

## <a name="create-the-proximity-placement-group"></a>Criar o grupo de posicionamento de proximidade

1. Digite **grupo de posicionamento de proximidade** na pesquisa.
1. Em **Serviços** nos resultados da pesquisa, selecione **grupos de posicionamento de proximidade**.
1. Na página **grupos de posicionamento de proximidade** , selecione **Adicionar**.
1. Na guia **Básico**, em **Detalhes do projeto**, verifique se a assinatura correta está selecionada.
1. Em **grupo de recursos** , selecione **criar novo** para criar um novo grupo ou selecione um grupo de recursos vazio que já existe, na lista suspensa. 
1. Em **região** , selecione o local onde você deseja que o grupo de posicionamento de proximidade seja criado.
1. Em **proximidade posição do grupo nome** , digite um nome e, em seguida, selecione **revisar + criar**.
1. Depois que a validação for aprovada, selecione **criar** para criar o grupo de posicionamento de proximidade.

    ![Captura de tela da criação de um grupo de posicionamento de proximidade](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Criar uma máquina virtual

1. Ao criar uma VM no portal, vá para a guia **avançado** . 
1. Na seleção de **grupo de posicionamento de proximidade** , selecione o grupo de posicionamento correto. 

    ![Captura de tela da seção grupo de posicionamento de proximidade ao criar uma nova VM no portal](./media/ppg/vm-ppg.png)

1. Quando terminar de fazer todas as outras seleções necessárias, selecione **revisar + criar**.
1. Depois de passar na validação, selecione **criar** para implantar a VM no grupo de posicionamento.


## <a name="add-vms-in-an-availability-set-to-a-proximity-placement-group"></a>Adicionar VMs em um conjunto de disponibilidade a um grupo de posicionamento de proximidade

Se a VM fizer parte do conjunto de disponibilidade, você precisará adicionar o conjunto de disponibilidade ao grupo de posicionamento antes de adicionar as VMs.

1. No [portal](https://portal.azure.com) , pesquise por *conjuntos de disponibilidade* e selecione seu conjunto de disponibilidade nos resultados.
1. Stop\deallocate cada VM no conjunto de disponibilidade selecionando a VM, em seguida, selecionando **parar** na página da VM e, em seguida, selecione **OK** para interromper a VM.
1. Na página de seu conjunto de disponibilidade, verifique se todas as VMs têm o **status** listado como **parado (desalocado)**.
1. No menu à esquerda, selecione **Configuração**.
1. Em **grupo de posicionamento de proximidade**, selecione um grupo de posicionamento na lista suspensa e, em seguida, selecione **salvar**.
1. Selecione **visão geral** no menu à esquerda para ver a lista de VMs novamente. 
1. Selecione cada VM no conjunto de disponibilidade e, em seguida, selecione **Iniciar** na página para cada VM. 


## <a name="add-existing-vm-to-placement-group"></a>Adicionar VM existente ao grupo de posicionamento 


1. Na página da VM, selecione **parar**.
1. Depois que o status da VM for listado como **parado (desalocado)**, selecione **configuração** no menu à esquerda.
1. Em **grupo de posicionamento de proximidade**, selecione um grupo de posicionamento na lista suspensa e, em seguida, selecione **salvar**.
1. Selecione **visão geral** no menu à esquerda e, em seguida, selecione **Iniciar** para reiniciar a VM.

 

## <a name="next-steps"></a>Próximas etapas

Você também pode usar o [Azure PowerShell](proximity-placement-groups.md) para criar grupos de posicionamento de proximidade.