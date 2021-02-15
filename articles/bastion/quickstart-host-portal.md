---
title: 'Início Rápido: Configurar o Azure Bastion e conectar-se a uma VM por meio de um endereço IP privado e um navegador'
titleSuffix: Azure Bastion
description: Neste artigo de início rápido, saiba como criar um host do Azure Bastion por meio de uma máquina virtual e se conectar à VM com segurança pelo navegador usando um endereço IP privado.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 325f39b695d80c14ed7097d071380b937458546c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021479"
---
# <a name="quickstart-connect-to-a-vm-securely-through-a-browser-via-private-ip-address"></a>Início Rápido: Conectar-se a uma VM com segurança por meio de um navegador usando um endereço IP privado

Você pode se conectar a uma VM (máquina virtual) pelo navegador usando o portal do Azure e o Azure Bastion. Este artigo de início rápido mostra como configurar o Azure Bastion com base nas configurações da VM e se conectar a ela por meio do portal. A VM não precisa ter um endereço IP público, um software cliente, um agente nem uma configuração especial. Depois que o serviço é provisionado, a experiência de RDP/SSH está disponível para todas as máquinas virtuais na mesma rede virtual. Para obter mais informações sobre o Azure Bastion, confira [O que é o Azure Bastion?](bastion-overview.md).

## <a name="prerequisites"></a><a name="prereq"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. Caso não tenha uma, [crie uma gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Para se conectar a uma VM pelo navegador usando o Bastion, você precisa conseguir entrar no portal do Azure.

* Uma máquina virtual do Windows em uma rede virtual. Caso não tenha uma VM, crie uma usando o [Guia de Início Rápido: Criar uma VM](../virtual-machines/windows/quick-create-portal.md).

  * Se precisar de valores de exemplo, confira os [Valores de exemplo](#values) fornecidos.
  * Caso já tenha uma rede virtual, lembre-se de selecioná-la na guia Rede ao criar sua VM.
  * Se ainda não tiver uma rede virtual, crie uma ao mesmo tempo que criar a VM.
  * Você não precisa ter um endereço IP público para essa VM para se conectar por meio do Azure Bastion.

* Funções VM necessárias:
  * A função de leitor na máquina virtual.
  * A função de leitor na placa de interface de rede com endereço IP privado da máquina virtual.
  
* Portas de VM necessárias:
  * Portas de entrada: RDP (3389)

### <a name="example-values"></a><a name="values"></a>Valores de exemplo

Use os valores de exemplo a seguir ao criar essa configuração ou substitua-os por valores próprios.

**Valores básicos da VNet e da VM:**

|**Nome** | **Valor** |
| --- | --- |
| Máquina virtual| TestVM |
| Resource group | TestRG |
| Região | Leste dos EUA |
| Rede virtual | TestVNet1 |
| Espaço de endereço | 10.0.0.0/16 |
| Sub-redes | FrontEnd: 10.0.0.0/24 |

**Valores do Azure Bastion:**

|**Nome** | **Valor** |
| --- | --- |
| Nome | TestVNet1-bastion |
| + Nome da Sub-rede | AzureBastionSubnet |
| Endereços da AzureBastionSubnet | Uma sub-rede no espaço de endereço da VNet com uma máscara de sub-rede /27. Por exemplo, 10.0.1.0/27.  |
| Endereço IP público |  Criar |
| Nome do endereço IP público | VNet1BastionPIP  |
| SKU do endereço IP público |  Standard  |
| Atribuição  | Estático |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Criar um bastion host

Há algumas maneiras diferentes de configurar um bastion host. Nas etapas a seguir, você criará um bastion host no portal do Azure diretamente por meio da sua VM. Quando você cria um host por meio de uma VM, várias configurações correspondentes à máquina virtual e/ou à rede virtual são preenchidas automaticamente.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Procure a VM à qual você deseja se conectar e selecione **Conectar**.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="configurações da máquina virtual" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. Na lista suspensa, selecione **Bastion**.
1. Na **página TestVM | Conectar**, selecione **Usar o Bastion**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="Selecionar o Bastion" border="false":::

1. Na página **Bastion**, preencha os seguintes campos de configurações:

   * **Nome**: nomeie o bastion host.
   * **Sub-rede**: esse é o espaço de endereço de rede virtual no qual o recurso do Bastion será implantado. A sub-rede precisa ser criada com o nome **AzureBastionSubnet**. Use uma sub-rede de pelo menos /27 ou maior (/27,/26, /25 e assim por diante).
   * Clique em **Gerenciar a configuração da sub-rede**.
1. Na página **Sub-redes**, selecione **+Sub-rede**.

   :::image type="content" source="./media/quickstart-host-portal/subnet.png" alt-text="+ Sub-rede":::
    
1. Na página **Adicionar sub-rede**, em **Nome**, digite **AzureBastionSubnet**.
   * Para o intervalo de endereços de sub-rede, escolha um endereço de sub-rede que esteja dentro do seu espaço de endereço de rede virtual.
   * Não ajuste nenhuma outra configuração. Selecione **OK** para aceitar e salvar as alterações da sub-rede.

   :::image type="content" source="./media/quickstart-host-portal/add-subnet.png" alt-text="Adicionar sub-rede":::
1. Clique no botão Voltar no navegador para voltar à página **Bastion** e continuar especificando os valores.
   * **Endereço IP público**: mantenha essa opção como **Criar**.
   * **Nome do endereço IP público**: o nome do recurso de endereço IP público.
   * **Atribuição**: usa Estática como padrão. Não é possível usar uma atribuição Dinâmica para o Azure Bastion.
   * **Grupo de recursos**: o mesmo grupo de recursos da VM.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="Criar um bastion host":::
1. Clique em **Criar** para criar o bastion host. O Azure validará suas configurações e criará o host. O host e seus respectivos recursos levarão cerca de 5 minutos para serem criados e implantados.

## <a name="connect"></a><a name="connect"></a>Conectar

Depois que o Bastion tiver sido implantado na rede virtual, a tela será alterada para a página Conectar.

1. Insira o nome de usuário e a senha da sua máquina virtual. Em seguida, selecione **Conectar**.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="A captura de tela mostra a caixa de diálogo Conectar usando o Azure Bastion.":::
1. A conexão RDP com essa máquina virtual será aberta diretamente no portal do Azure (via HTML5) usando a porta 443 e o serviço Bastion.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="Conexão RDP":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de usar a rede virtual e as máquinas virtuais, exclua o grupo de recursos e todos os recursos que ele contém:

1. Insira o nome do grupo de recursos na caixa **Pesquisar** na parte superior do portal e selecione-o nos resultados da pesquisa.

1. Selecione **Excluir grupo de recursos**.

1. Insira seu grupo de recursos para obter a opção **DIGITAR O NOME DO GRUPO DE RECURSOS** e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um bastion host para a sua rede virtual e se conectou a uma máquina virtual com segurança por meio do Bastion. Depois será possível continuar com a etapa a seguir caso queira se conectar a um conjunto de dimensionamento de máquinas virtuais.

> [!div class="nextstepaction"]
> [Como se conectar a um conjunto de dimensionamento de máquinas virtuais usando o Azure Bastion](bastion-connect-vm-scale-set.md)
