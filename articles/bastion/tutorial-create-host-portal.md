---
title: 'Tutorial: Criar um host do Azure Bastion: VM do Windows: portal'
description: Neste artigo, você aprenderá a criar um host do Azure Bastion e a se conectar a uma VM do Windows.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: 3b365e347802824e855797afb8c68e5249bf0adb
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579627"
---
# <a name="tutorial-configure-bastion-and-connect-to-a-windows-vm-through-a-browser"></a>Tutorial: Configurar o Bastion e se conectar a uma VM do Windows por meio de um navegador

Este tutorial mostra como se conectar a uma máquina virtual por meio do navegador usando o Azure Bastion e o portal do Azure. No portal do Azure, implante o Bastion na sua rede virtual. Depois de implantar o Bastion, conecte-se a uma VM por meio do endereço IP privado usando o portal do Azure. Sua VM não precisará obter um endereço IP público nem um software especial. Depois que o serviço é provisionado, a experiência de RDP/SSH está disponível para todas as máquinas virtuais na mesma rede virtual. Para obter mais informações sobre o Azure Bastion, confira [O que é o Azure Bastion?](bastion-overview.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um bastion host para a sua VNet
> * Conectar-se a uma máquina virtual do Windows

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma rede virtual.
* Uma máquina virtual do Windows na rede virtual.
* As seguintes funções obrigatórias:
  * A função de leitor na máquina virtual.
  * A função de leitor na placa de interface de rede com endereço IP privado da máquina virtual.
  * Função de leitor no recurso do Azure Bastion.

* Portas: Para se conectar à VM do Windows, você precisará abrir as seguintes portas nela:
  * Portas de entrada: RDP (3389)

 >[!NOTE]
 >Não há suporte no momento para o uso do Azure Bastion com Zonas DNS Privadas do Azure. Antes de começar, verifique se a rede virtual em que você planeja implantar o recurso do Bastion não está vinculada a uma zona DNS privada.
 >

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Criar um bastion host

Esta seção ajuda você a criar o objeto do bastion na sua VNet. Isso é necessário para criar uma conexão segura com uma VM na VNet.

1. Na **Página Inicial**, selecione **+ Criar um recurso**.
1. Na página **Novo**, na caixa de Pesquisa, digite **Bastion** e clique em **Enter** para obter os resultados da pesquisa. No resultado de **Bastion**, verifique se o editor é a Microsoft.
1. Selecione **Criar**.
1. Na página **Criar um Bastion**, configure um novo recurso do Bastion.

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-basics.png" alt-text="Criar um Bastion host" lightbox="./media/tutorial-create-host-portal/bastion-basics.png":::

    * **Assinatura**: A assinatura do Azure que você deseja usar para criar um recurso do Bastion.
    * **Grupo de Recursos**: O grupo de recursos do Azure no qual esse recurso do Bastion será criado. Se você não tiver um grupo de recursos atualmente, crie um.
    * **Nome**: O nome do novo recurso do Bastion.
    * **Região**: A região pública do Azure na qual o recurso será criado.
    * **Rede virtual**: A rede virtual na qual o recurso do Bastion será criado. Você pode criar uma rede virtual no portal durante esse processo ou usar uma rede virtual existente. Se você estiver usando uma rede virtual existente, verifique se a rede virtual existente têm espaços de endereço livres suficientes para acomodar os requisitos de sub-rede do Bastion. Se você não vir a sua rede virtual no menu suspenso, verifique se você selecionou o Grupo de Recursos correto.
    * **Sub-rede**: Depois de criar ou selecionar uma rede virtual, o campo sub-rede será exibido. A sub-rede da rede virtual na qual o novo Bastion host será implantado. A sub-rede será dedicada ao Bastion host. Selecione **Gerenciar a configuração de sub-rede** e criar a sub-rede do Azure Bastion. Selecione **+ Sub-rede** e crie uma sub-rede usando as seguintes diretrizes:

         * A sub-rede precisa ser nomeada **AzureBastionSubnet**.
         * A sub-rede precisa ter pelo menos /27 ou maior.

      Você não precisa preencher campos adicionais. Selecione **OK** e, na parte superior da página, selecione **Criar um Bastion** para retornar à página de configuração do Bastion.
    * **Endereço IP público**: O IP público do recurso do Bastion no qual o RDP/SSH será acessado (pela porta 443). Criar um IP público. O endereço IP público precisa estar na mesma região que o recurso do Bastion que você está criando. Esse endereço IP não tem nada a ver com nenhuma das VMs às quais você deseja se conectar. É o IP público para o recurso do Bastion host.
    * **Nome do endereço IP público**: o nome do recurso de endereço IP público. Para este tutorial, você pode usar o padrão.
    * **SKU do endereço IP público**: Essa configuração é preenchida por padrão para **Standard**. O Azure Bastion usa/dá suporte apenas à SKU de IP público Standard.
    * **Atribuição**: Essa configuração é preenchida por padrão para **Estático**.

1. Quando terminar de especificar as configurações, selecione **Examinar + Criar**. Isso valida os valores. Depois que a validação for aprovada, você poderá criar o recurso do Bastion.
1. Selecione **Criar**.
1. Você verá uma mensagem informando que a implantação está em andamento. O status será exibido nessa página conforme os recursos são criados. Leva cerca de cinco minutos para o recurso do Bastion ser criado e implantado.

## <a name="connect-to-a-vm"></a>Como conectar-se a uma VM

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não quiser continuar usando este aplicativo, exclua os seus recursos seguindo as seguintes etapas:

1. Insira o nome do grupo de recursos na caixa **Pesquisa** na parte superior do portal. Quando ver o grupo de recursos nos resultados da pesquisa, selecione-o.
1. Selecione **Excluir grupo de recursos**.
1. Insira o nome do seu grupo de recursos para obter a opção **DIGITAR O NOME DO GRUPO DE RECURSOS** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um Bastion host e o associou a uma rede virtual. Em seguida, você o conectou a uma VM do Windows. Você pode usar os Grupos de Segurança de Rede com a sub-rede do Azure Bastion. Para fazer isso, confira:

> [!div class="nextstepaction"]
> [Trabalhar com NSGs](bastion-nsg.md)
