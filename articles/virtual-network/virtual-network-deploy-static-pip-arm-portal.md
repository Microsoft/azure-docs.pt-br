---
title: Criar uma VM com um endereço IP público estático-portal do Azure
description: Saiba como criar uma VM com um endereço IP público estático usando o Portal do Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/12/2020
ms.author: allensu
ms.openlocfilehash: d416af3d3a8eb8ab8057f13cc0d9a133adcb849a
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221149"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Crie uma máquina virtual com um endereço IP público estático usando o portal do Azure

Um endereço IP público permite que você se comunique com uma máquina virtual pela Internet. 

Atribua um endereço IP público estático, em vez de um endereço dinâmico, para garantir que o endereço nunca seja alterado.   

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Máquina virtual** ou pesquise **Máquina virtual** na caixa de pesquisa.
   
2. Em **Criar uma máquina virtual**, digite ou selecione os valores na guia **Informações Básicas**:

    | Configuração | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **Criar novo**. </br> Em **nome**, insira **MyResource**. </br> Selecione **OK**. |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **myVM** |
    | Região | Selecione **Leste dos EUA** |
    | Opções de disponibilidade | Selecione **Nenhuma redundância infraestrutura necessária** |
    | Imagem | Selecione **Windows Server 2019 Datacenter – Gen1** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho da VM ou use a configuração padrão |
    | **Conta de administrador** |  |
    | Nome de Usuário | Insira um nome de usuário |
    | Senha | Insira uma senha |
    | Confirmar senha | Insira novamente a senha |

3. Selecione a guia **Rede** ou selecione **Avançar: Discos**, em seguida, **Avançar: Rede**.
  
4. Na guia Rede, selecione ou insira:

    | Configuração | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | Aceite o nome de rede padrão. |
    | Sub-rede | Aceite a configuração de sub-rede padrão. |
    | IP público | Selecione **Criar novo**. </br> Em **criar endereço IP público**, em nome, insira **myPublicIP**. </br> Para **SKU**, selecione **padrão**. </br> **Atribuição**, selecione **estático**. </br> Selecione **OK**.  |
    | Grupo de segurança de rede da NIC | Selecione **Básico**|
    | Porta de entrada públicas | Selecione **Permitir portas selecionadas**. |
    | Selecione as portas de entrada | Selecionar **RDP (3389)** |

    > [!WARNING]
    > O Portal 3389 está selecionado para permitir o acesso remoto à máquina virtual do Windows Server a partir da Internet. Não é recomendável abrir a porta 3389 para a Internet para gerenciar as cargas de trabalho de produção. </br> Para obter acesso seguro às máquinas virtuais do Azure, consulte **[o que é a bastiões do Azure?](../bastion/bastion-overview.md)**
   
5. Selecione **Examinar + criar**. 
  
6. Examine as configurações e selecione **Criar**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos e todos os recursos que ele contém:

1. Insira **myResourceGroup** na caixa **Pesquisar** na parte superior do portal. Quando aparecer **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Insira **myResourceGroup** para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Consulte [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md):

* Para alterar um endereço IP público de dinâmico para estático.
* Trabalhar com endereços IP privados.

Os endereços IP públicos têm um [custo nominal](https://azure.microsoft.com/pricing/details/ip-addresses). Há um [limite](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para o número de endereços IP públicos que você pode usar por assinatura.

A SKU do endereço IP público da máquina virtual deve corresponder ao SKU de IP público de Azure Load Balancer quando adicionada a um pool de back-end. Para obter detalhes, consulte [balanceador de carga do Azure](../load-balancer/skus.md).

Você pode baixar a lista de intervalos (prefixos) para as nuvens [pública](https://www.microsoft.com/download/details.aspx?id=56519), do [governo dos EUA](https://www.microsoft.com/download/details.aspx?id=57063), da [China](https://www.microsoft.com/download/details.aspx?id=57062) e da [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.

- Saiba mais sobre os [endereços IP públicos estáticos](./public-ip-addresses.md#allocation-method).
- Saiba mais sobre os [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) no Azure.
- Saiba mais sobre todas as [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Saiba mais sobre [endereços IP privados](./private-ip-addresses.md) e atribuindo um [endereço IP privado estático](virtual-network-network-interface-addresses.md#add-ip-addresses) a uma máquina virtual do Azure.
