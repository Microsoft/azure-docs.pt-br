---
title: Provisionar um pool em uma rede virtual
description: Como criar um pool de lotes em uma rede virtual do Azure para que os nós de computação possam se comunicar com segurança com outras VMs na rede, como um servidor de arquivos.
ms.topic: how-to
ms.date: 03/26/2021
ms.custom: seodec18
ms.openlocfilehash: 7213637e89cfccd1352861002c47a696d942d30f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629301"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Criar um pool do Lote do Azure em uma rede virtual

Quando você cria um pool do Lote do Azure, você pode provisionar o pool em uma sub-rede de uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) (VNet) que você especificar. Este artigo explica como configurar um pool do Lote em uma rede virtual.

## <a name="why-use-a-vnet"></a>Por que usar uma rede virtual?

Os nós de computação em um pool podem se comunicar entre si, por exemplo, para executar tarefas de várias instâncias, sem a necessidade de uma VNet separada. No entanto, por padrão, os nós em um pool não podem se comunicar com máquinas virtuais que estão fora do pool, como servidores de licença ou servidores de arquivos.

Para permitir que os nós de computação se comuniquem com segurança com outras máquinas virtuais, ou com uma rede local, você pode provisionar o pool em uma sub-rede de uma VNet do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- **Autenticação**. Para usar uma rede virtual do Azure, a API do cliente do Lote deverá usar a autenticação do Azure Active Directory (AD). O suporte ao Lote do Azure para o Azure AD está documentado em [Autenticar soluções do serviço Lote com o Active Directory](batch-aad-auth.md).

- **Uma rede virtual do Azure**. Consulte a seção a seguir para requisitos de rede virtual e a configuração. Para preparar uma rede virtual com uma ou mais sub-redes com antecedência, você pode usar o portal do Azure, o Azure PowerShell, a interface de linha de comando (CLI) do Azure ou outros métodos.
  - Para criar uma VNET baseada no Azure Resource Manager, consulte [Criar uma rede virtual](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Uma VNet baseada no Resource Manager é recomendada para novas implantações e tem suporte apenas em pools que usam a configuração de máquina virtual.
  - Para criar uma rede virtual clássica, consulte [Criar uma rede virtual (clássica) com várias sub-redes](/previous-versions/azure/virtual-network/create-virtual-network-classic). Há suporte para uma VNet clássica apenas em pools que usam a configuração de serviços de nuvem.

## <a name="vnet-requirements"></a>Requisitos de rede virtual

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-azure-portal"></a>Criar um pool com uma VNet no portal do Azure

Depois de criar sua rede virtual e ter atribuído uma sub-rede a ele, você pode criar um pool do Lote com essa rede virtual. Siga estas etapas para criar um pool no portal do Azure: 

1. Navegue até sua conta do Lote no portal do Azure. Esta conta deve estar na mesma assinatura e na mesma região que o grupo de recursos que contém a imagem que você deseja usar.
2. Na janela **Configurações** à esquerda, selecione o item de menu **Pools**.
3. Na janela **pools** , selecione **Adicionar**.
4. Na janela **Adicionar pool**, selecione a opção que você deseja usar na lista suspensa **Tipo de imagem**.
5. Selecione o **Editor/Oferta/SKU** correto para sua imagem personalizada.
6. Especifique o restante das configurações necessárias, incluindo o **Tamanho do nó**, os **Nós dedicados de destino** e os **Nós de baixa prioridade**, bem como qualquer configuração opcional desejada.
7. Em **Rede virtual**, selecione a rede virtual e a sub-rede que você deseja usar.

   ![Adicionar pool com a rede virtual](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Rotas definidas pelo usuário para o túnel forçado

Você pode ter requisitos em sua organização para redirecionar (forçar) o tráfego vinculado à Internet da sub-rede de volta para seu local para inspeção e registro em log. Além disso, você pode ter habilitado o túnel forçado para as sub-redes em sua VNet.

Para garantir que os nós em seu pool funcionem em uma VNet com túnel forçado habilitado, você deve adicionar as seguintes UDR ( [rotas definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md) ) para essa sub-rede:

- O serviço de lote precisa se comunicar com nós para tarefas de agendamento. Para habilitar essa comunicação, adicione uma UDR a cada endereço IP usado pelo serviço do Lote na região onde existe sua conta do Lote. Para obter a lista de endereços IP do serviço de lote, consulte [marcas de serviço locais](../virtual-network/service-tags-overview.md).

- Verifique se o tráfego de saída para o armazenamento do Azure (especificamente, as URLs do formulário `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` e `<account>.blob.core.windows.net` ) não está bloqueado pela sua rede local.

- Se você usar montagens de arquivo virtual, examine os [requisitos de rede](virtual-file-mount.md#networking-requirements) e certifique-se de que nenhum tráfego necessário esteja bloqueado.

Quando você adicionar uma UDR, defina a rota para cada prefixo de endereço IP de lote relacionado e defina **Próximo tipo de salto** como **Internet**.

![Rota definida pelo usuário](./media/batch-virtual-network/user-defined-route.png)

> [!WARNING]
> Os endereços IP do serviço de lote podem mudar ao longo do tempo. Para evitar interrupções devido a uma alteração de endereço IP, crie um processo para atualizar os endereços IP do serviço de lote automaticamente e mantenha-os atualizados na tabela de rotas.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Fluxo de trabalho e recursos primários do serviço de lote](batch-service-workflow-features.md) como pools, nós, trabalhos e tarefas.
- Saiba como [criar uma rota definida pelo usuário no portal do Azure](../virtual-network/tutorial-create-route-table-portal.md).
