---
title: 'Início Rápido: Configurar o Azure Bastion e conectar-se a uma VM por meio de um endereço IP privado e um navegador'
titleSuffix: Azure Bastion
description: Neste artigo de início rápido, saiba como criar um host do Azure Bastion por meio de uma máquina virtual e se conectar à VM com segurança pelo navegador usando um endereço IP privado.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 02/18/2021
ms.author: cherylmc
ms.openlocfilehash: 53f09eed89f9667611ed4d5e0268c889609d560a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553559"
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

 >[!NOTE]
 >Não há suporte no momento para o uso do Azure Bastion com Zonas DNS Privadas do Azure. Antes de começar, verifique se a rede virtual em que você planeja implantar o recurso do Bastion não está vinculada a uma zona DNS privada.
 >

### <a name="example-values"></a><a name="values"></a>Valores de exemplo

Use os valores de exemplo a seguir ao criar essa configuração ou substitua-os por valores próprios.

**Valores básicos da VNet e da VM:**

|**Nome** | **Valor** |
| --- | --- |
| Máquina virtual| TestVM |
| Resource group | TestRG1 |
| Região | Leste dos EUA |
| Rede virtual | VNet1 |
| Espaço de endereço | 10.1.0.0/16 |
| Sub-redes | FrontEnd: 10.1.0.0/24 |

**Valores do Azure Bastion:**

|**Nome** | **Valor** |
| --- | --- |
| Nome | VNet1-bastion |
| + Nome da Sub-rede | AzureBastionSubnet |
| Endereços da AzureBastionSubnet | Uma sub-rede no espaço de endereço da VNet com uma máscara de sub-rede /27. Por exemplo, 10.1.1.0/27.  |
| Endereço IP público |  Criar |
| Nome do endereço IP público | VNet1-ip  |
| SKU do endereço IP público |  Standard  |
| Atribuição  | Estático |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Criar um bastion host

Há algumas maneiras diferentes de configurar um bastion host. Nas etapas a seguir, você criará um bastion host no portal do Azure diretamente por meio da sua VM. Quando você cria um host por meio de uma VM, várias configurações correspondentes à máquina virtual e/ou à rede virtual são preenchidas automaticamente.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Procure a VM à qual você deseja se conectar e selecione **Conectar**.

   :::image type="content" source="./media/quickstart-host-portal/vm-connect.png" alt-text="Captura da tela das configurações da máquina virtual." lightbox="./media/quickstart-host-portal/vm-connect.png":::
1. Na lista suspensa, selecione **Bastion**.

   :::image type="content" source="./media/quickstart-host-portal/bastion.png" alt-text="Captura de tela da lista suspensa do Bastion." lightbox="./media/quickstart-host-portal/bastion.png":::
1. Na **página TestVM | Conectar**, selecione **Usar o Bastion**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="Captura de tela de Usar o Bastion.":::

1. Na página **Conectar usando o Azure Bastion**, configure os valores.

   * **Etapa 1:** os valores são preenchidos previamente porque você está criando o bastion host diretamente de sua VM.

   * **Etapa 2:** o espaço de endereço é preenchido previamente com um espaço de endereço sugerido. AzureBastionSubnet deve ter um espaço de endereço de /27 ou maior (/26,/25 etc.).

   :::image type="content" source="./media/quickstart-host-portal/create-subnet.png" alt-text="Captura de tela da criação da sub-rede do Bastion.":::

1. Clique em **Criar sub-rede** para criar a AzureBastionSubnet.
1. Após a criação da sub-rede, a página avança automaticamente para a **Etapa 3**. Para a Etapa 3, use os seguintes valores:

   * **Nome**: dê um nome ao bastion host.
   * **Endereço IP público:** selecione **Criar novo**.
   * **Nome do endereço IP público**: o nome do recurso de endereço IP público.
   * **SKU do endereço IP público:** pré-configurado como **Standard**
   * **Atribuição:** pré-configurado como **Estático**. Não é possível usar uma atribuição Dinâmica para o Azure Bastion.
   * **Grupo de recursos**: o mesmo grupo de recursos da VM.

   :::image type="content" source="./media/quickstart-host-portal/create-bastion.png" alt-text="Captura de tela da Etapa 3.":::
1. Depois de preencher os valores, selecione **Criar Azure Bastion usando os padrões**. O Azure validará suas configurações e criará o host. O host e seus respectivos recursos levarão cerca de 5 minutos para serem criados e implantados.

## <a name="connect"></a><a name="connect"></a>Conectar

Depois que o Bastion tiver sido implantado na rede virtual, a tela será alterada para a página Conectar.

1. Insira o nome de usuário e a senha da sua máquina virtual. Em seguida, selecione **Conectar**.

   :::image type="content" source="./media/quickstart-host-portal/connect.png" alt-text="A captura de tela mostra a caixa de diálogo Conectar usando o Azure Bastion.":::
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
