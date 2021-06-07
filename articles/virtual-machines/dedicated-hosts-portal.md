---
title: Implantar hosts dedicados do Azure usando o portal do Azure
description: Implante VMs e conjuntos de dimensionamento em hosts dedicados usando o portal do Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: 3bc7909f38e63256d7d708ec189c628662cf8837
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667325"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts-using-the-portal"></a>Implantar VMs e conjuntos de dimensionamento em hosts dedicados usando o portal 

Este artigo orienta como criar um [host dedicado](dedicated-hosts.md) do Azure para hospedar suas máquinas virtuais (VMs). 


## <a name="limitations"></a>Limitações

- Os tamanhos e tipos de hardware disponíveis para hosts dedicados variam de acordo com a região. Consulte a [página de preços](https://aka.ms/ADHPricing) do host para saber mais.

## <a name="create-a-host-group"></a>Criar um grupo de hosts

Um **grupo de hosts** é um recurso que representa uma coleção de hosts dedicados. Você cria um grupo de hosts em uma região e uma zona de disponibilidade e adiciona hosts a ele. Ao planejar a alta disponibilidade, há opções adicionais. Você pode usar uma ou ambas as opções a seguir com hosts dedicados: 
- Alcance de várias zonas de disponibilidade. Nesse caso, é necessário ter um grupo de hosts em cada uma das zonas que você quer usar.
- Alcance de vários domínios de falha que são mapeados para racks físicos. 
 
Em ambos os casos, você precisa fornecer a contagem de domínios de falha ao seu grupo de hosts. Se você não quiser abranger domínios de falha no seu grupo, use uma contagem de domínio de falha de 1. 

Você também pode optar por usar tanto zonas de disponibilidade quanto domínios de falha. 

Neste exemplo, criaremos um grupo de hosts usando uma zona de disponibilidade e dois domínios de falha. 


1. Abra o [portal](https://portal.azure.com) do Azure. 
1. Selecione **Criar um recurso** no canto superior esquerdo.
1. Procure por **Grupo de hosts** e selecione **Grupos de hosts** nos resultados.
1. Na página **Grupos de hosts**, selecione **Criar**.
1. Selecione a assinatura que você deseja usar e escolha **Criar** para criar um grupo de recursos.
1. Digite *myDedicatedHostsRG* como o **Nome** e selecione **OK**.
1. Para **Nome do grupo de hosts**, digite *myHostGroup*.
1. Em **Localização**, selecione **Leste dos EUA**.
1. Para **Zona de Disponibilidade**, selecione **1**.
1. Para **Contagem de domínios de falha**, selecione **2**.
1. Selecione **Posicionamento automático** para atribuir automaticamente as VMs e as instâncias do conjunto de dimensionamento a um host disponível no grupo.
1. Selecione **Examinar + criar** e aguarde a validação.
1. Depois de ver a mensagem **Validação aprovada**, selecione **Criar** para criar o grupo de hosts.

Só deve levar alguns minutos para criar o grupo de hosts.


## <a name="create-a-dedicated-host"></a>Criar um host dedicado

Criamos um host dedicado no grupo de hosts. Além de um nome para o host, você deve fornecer o SKU para o host. O SKU do host captura a série de VMs com suporte, bem como a geração de hardware para o host dedicado.

Para mais informações sobre preços e SKUs do host, consulte [Preços do Host Dedicado do Azure](https://aka.ms/ADHPricing).

Ao definir uma contagem de domínios de falha para seu grupo de hosts, você será solicitado a especificar o domínio de falha para o host.  

1. Selecione **Criar um recurso** no canto superior esquerdo.
1. Pesquise **Host dedicado** e selecione **Hosts dedicados** nos resultados.
1. Na página **Hosts dedicados**, selecione **Criar**.
1. Selecione a assinatura que você deseja usar.
1. Selecione *myDedicatedHostsRG* como o **Grupo de recursos**.
1. Em **Detalhes da instância**, digite *myhost* para o **Nome** e selecione *Leste dos EUA* para o local.
1. Em **Perfil de hardware**, selecione *Família Standard Es3 – Tipo 1* para a **Família de tamanho**, selecione *myHostGroup* para o **Grupo de hosts** e *1* para o **Domínio de falha**. Deixe os padrões no restante dos campos.
1. Quando terminar, selecione **Examinar + criar** e aguarde a validação.
1. Depois de ver a mensagem **Validação aprovada**, selecione **Criar** para criar o host.

## <a name="create-a-vm"></a>Criar uma máquina virtual

1. Escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Na caixa de pesquisa acima da lista de recursos do Azure Marketplace, procure e selecione a imagem que deseja usar e escolha **Criar**.
1. Na guia **Básico**, em **Detalhes do projeto**, garanta que a assinatura correta esteja selecionada e selecione *myDedicatedHostsRG* como o **Grupo de recursos**. 
1. Em **Detalhes da instância**, digite *myVM* para o **Nome da máquina virtual** e escolha *Leste dos EUA* para **Localização**.
1. Em **Opções de disponibilidade**, selecione **Zona de disponibilidade** e *1* na lista suspensa.
1. Para o tamanho, selecione **Alterar tamanho**. Na lista de tamanhos disponíveis, escolha um da série Esv3, como **Standard E2s v3**. Talvez seja necessário limpar o filtro para ver todos os tamanhos disponíveis.
1. Conclua o restante dos campos na guia **Básico**, conforme necessário.
1. Se você quiser especificar qual host deve ser usado para sua VM, na parte superior da página, selecione a guia **Avançado** e, na seção **Host**, selecione *myHostGroup* para **Grupo de hosts** e *myHost* para o **Host**. Caso contrário, sua VM será colocada automaticamente em um host com capacidade.
    ![Selecionar grupo de hosts e host](./media/dedicated-hosts-portal/advanced.png)
1. Deixe os padrões restantes e, em seguida, selecione o botão **Examinar + criar** na parte inferior da página.
1. Quando você receber a mensagem informando que a validação foi aprovada, selecione **Criar**.

Levará alguns minutos para que sua VM seja implantada.

## <a name="create-a-scale-set"></a>Criar um conjunto de escala 

Ao implantar um conjunto de dimensionamento, você especificará o grupo de hosts.

1. Procure por *Conjunto de dimensionamento* e selecione **Conjuntos de dimensionamento de máquinas virtuais** na lista.
1. Selecione **Adicionar** para criar um conjunto de dimensionamento.
1. Preencha os campos na guia **Básico** como faria normalmente, mas selecione um tamanho de VM que seja da série escolhida para o host dedicado, como **Standard E2s v3**.
1. Na guia **Avançado**, para **Algoritmo de difusão**, selecione **Difusão máxima**.
1. Em **Grupo de hosts**, selecione o grupo de hosts na lista suspensa. Se você criou recentemente o grupo, poderá levar um minuto para ele ser adicionado à lista.

## <a name="add-an-existing-vm"></a>Adicionar uma VM existente 

Você pode adicionar uma VM existente a um host dedicado, mas a VM deverá primeiro ser Parada\Desalocada. Antes de mover uma VM para um host dedicado, verifique se a configuração dela tem suporte:

- O tamanho da VM deve estar na mesma família de tamanho que o host dedicado. Por exemplo, se o host dedicado for DSv3, o tamanho da VM poderá ser Standard_D4s_v3, mas não poderá ser Standard_A4_v2. 
- A VM precisa estar localizada na mesma região que o host dedicado.
- A VM não pode fazer parte de um grupo de posicionamento por proximidade. Remova a VM do grupo de posicionamento por proximidade antes de movê-la para um host dedicado. Para saber mais, confira [Remover uma VM de um grupo de posicionamento por proximidade](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- A VM não pode estar em um conjunto de disponibilidade.
- Se a VM estiver em uma zona de disponibilidade, deverá estar na mesma que o grupo de hosts. As configurações da zona de disponibilidade para a VM e o grupo de hosts devem corresponder.

Mova a VM para um host dedicado usando o [portal](https://portal.azure.com).

1. Abra a página da VM.
1. Selecione **Parar** para parar\desalocar a VM.
1. Selecione **Configuração** no menu esquerdo.
1. Selecione um grupo de hosts e um host nos menus suspensos.
1. Quando terminar, selecione **Salvar** na parte superior da página.
1. Depois que a VM tiver sido adicionada ao host, selecione **Visão geral** no menu esquerdo.
1. Na parte superior da página, selecione **Iniciar** para reiniciar a VM.

## <a name="next-steps"></a>Próximas etapas

- Para mais informações, consulte a visão geral de [hosts dedicados](dedicated-hosts.md).

- Há um exemplo de modelo, [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), que usa zonas e domínios de falha para obter resiliência máxima em uma região.

- Você também pode implantar um host dedicado usando a [CLI do Azure](./linux/dedicated-hosts-cli.md) ou o [PowerShell](./windows/dedicated-hosts-powershell.md).
