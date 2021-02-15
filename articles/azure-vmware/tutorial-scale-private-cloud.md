---
title: Tutorial – Escalar uma nuvem privada
description: Neste tutorial, você usará o portal do Azure para escalar uma nuvem privada da Solução VMware no Azure.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: d49d973cc6d97280dc0c7ea6681f2602b871e1ba
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791232"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>Tutorial: Escalar uma nuvem privada da Solução VMware no Azure

Para aproveitar ao máximo sua experiência com a nuvem privada da Solução VMware no Azure, escale os clusters e os hosts de maneira a refletir o que você precisa para as cargas de trabalho planejadas. Você pode escalar os clusters e hosts em uma nuvem privada conforme necessário para a carga de trabalho do aplicativo. As limitações de desempenho e disponibilidade para serviços específicos devem ser tratadas caso a caso. Os limites de cluster e de host são fornecidos no artigo sobre o [conceito de nuvem privada](concepts-private-clouds-clusters.md).

Neste tutorial, você usará o portal do Azure para:

> [!div class="checklist"]
> * Adicionar um cluster a uma nuvem privada existente
> * Adicionar hosts a um cluster existente

## <a name="prerequisites"></a>Pré-requisitos

Uma nuvem privada para concluir este tutorial. Se você não tiver criado uma nuvem privada, use o [tutorial para criar uma nuvem privada](tutorial-create-private-cloud.md). Configure a rede para sua nuvem privada do VMware no Azure para definir a rede virtual necessária.

## <a name="add-a-new-cluster"></a>Adicionar um cluster

1. Na página de visão geral de uma nuvem privada existente, em **Gerenciar** , selecione **Escalar nuvem privada**. Em seguida, selecione **+ Adicionar um cluster**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="selecione adicionar um cluster" border="true":::

1. Na página **Adicionar cluster** , use o controle deslizante para selecionar o número de hosts. Clique em **Salvar**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Na página Adicionar cluster, use o controle deslizante para selecionar o número de hosts. Clique em Salvar." border="true":::

   A implantação do novo cluster será iniciada.

## <a name="scale-a-cluster"></a>Dimensionar um cluster 

1. Na página de visão geral de uma nuvem privada existente, selecione **Dimensionar nuvem privada** e selecione o ícone de lápis para editar o cluster.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Selecione Escalar nuvem privada em visão geral" border="true":::

1. Na página **Editar cluster** , use o controle deslizante para selecionar o número de hosts. Clique em **Salvar**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Na página Editar Cluster, use o controle deslizante para selecionar o número de hosts. Clique em Salvar." border="true":::

   A adição de hosts ao cluster será iniciada.

## <a name="next-steps"></a>Próximas etapas

Se precisar de outra nuvem privada da Solução VMware no Azure, [crie outra nuvem privada](tutorial-create-private-cloud.md) seguindo os mesmos pré-requisitos de rede, cluster e limites de host.

<!-- LINKS - external-->

<!-- LINKS - internal -->
