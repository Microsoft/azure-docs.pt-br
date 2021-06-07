---
title: 'Guia de início rápido: Criar uma rede virtual – portal do Azure'
titleSuffix: Azure Virtual Network
description: Neste guia de início rápido, você aprenderá a criar uma rede virtual usando o portal do Azure.
author: KumudD
ms.author: kumud
ms.date: 03/17/2021
ms.topic: quickstart
ms.service: virtual-network
ms.workload: infrastructure
ms.tgt_pltfrm: virtual-network
ms.devlang: na
tags:
- azure-resource-manager
ms.custom:
- mode-portal
ms.openlocfilehash: 43c45b43084656a45d2509ee2c7a4376cdc7c052
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531186"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Início Rápido: Criar uma Rede Virtual usando o portal do Azure

Neste início rápido, você aprenderá a criar uma rede virtual usando o portal do Azure. Você implantará duas VMs (máquinas virtuais). Em seguida, você se comunicará com segurança entre as VMs e se conectará com VMs da Internet. Uma rede virtual é o bloco de construção fundamental de sua rede privada no Azure. Ela permite que os recursos do Azure, como VMs, comuniquem-se de maneira segura uns com os outros e usando a Internet.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Selecione **Criar um recurso** no canto superior esquerdo do portal.

2. Na caixa de pesquisa, insira **Rede Virtual**. Selecione **Rede Virtual** nos resultados da pesquisa.

3. Na página **Rede Virtual**, selecione **Criar**.

4. Em **Criar rede virtual**, insira ou selecione estas informações na guia **Básico**:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** |   |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **Criar novo**.  </br> Insira **myResourceGroup**. </br> Selecione **OK**. |
    | **Detalhes da instância** |   |
    | Nome | Insira **myVNet**. |
    | Região | Selecione **(EUA) Leste dos EUA**. |

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="Criar uma rede virtual no portal do Azure" border="true":::

5. Selecione a guia **Endereços IP** ou escolha o botão **Avançar: Endereços IP** na parte inferior da página.

6. Em **Espaço de endereço IPv4**, selecione o espaço de endereço existente e altere-o para **10.1.0.0/16**.

7. Selecione **+ Adicionar sub-rede** e insira **MySubnet** em **Nome da sub-rede** e **10.1.0.0/24** em **Intervalo de endereços de sub-rede**.

8. Selecione **Adicionar**.

9. Selecione a guia **Segurança** ou escolha o botão **Avançar: Segurança** na parte inferior da página.

10. Em **BastionHost**, selecione **Habilitar**. Insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Nome do bastion | Insira **myBastionHost** |
    | Espaço de endereço da AzureBastionSubnet | Insira **10.1.1.0/24** |
    | Endereço IP público | Selecione **Criar novo**. </br> Em **Nome**, insira **myBastionIP**. </br> Selecione **OK**. |

11. Selecione a guia **Revisar + criar** ou o botão **Revisar + criar**.

12. Selecione **Criar**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual:

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Máquina virtual**. 
   
2. Em **Criar uma máquina virtual**, digite ou selecione os valores na guia **Informações Básicas**:

    | Setting | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **myResourceGroup** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **myVM1** |
    | Região | Selecione **(EUA) Leste dos EUA** |
    | Opções de disponibilidade | Selecione **Nenhuma redundância infraestrutura necessária** |
    | Imagem | Selecione **Windows Server 2019 Datacenter** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho da VM ou use a configuração padrão |
    | **Conta de administrador** |  |
    | Nome de Usuário | Insira um nome de usuário |
    | Senha | Insira uma senha |
    | Confirmar senha | Insira novamente a senha |
    | **Regras de porta de entrada** |    |
    | Porta de entrada públicas | Selecione **Nenhum**. |
    |

3. Selecione a guia **Rede** ou selecione **Avançar: Discos**, em seguida, **Avançar: Rede**.
  
4. Na guia Rede, selecione ou insira:

    | Setting | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **mySubnet** |
    | IP público | Selecione **Nenhum** |
    | Grupo de segurança de rede da NIC | Selecione **Básico**|
    | Rede das portas de entrada públicas | Selecione **Nenhum**. |
   
5. Selecione a guia **Examinar + criar** ou clique no botão azul **Examinar + criar** na parte inferior da página.
  
6. Examine as configurações e selecione **Criar**.

### <a name="create-the-second-vm"></a>Criar a segunda VM

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Máquina virtual**. 
   
2. Em **Criar uma máquina virtual**, digite ou selecione os valores na guia **Informações Básicas**:

    | Setting | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **myResourceGroup** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **myVM2** |
    | Região | Selecione **(EUA) Leste dos EUA** |
    | Opções de disponibilidade | Selecione **Nenhuma redundância infraestrutura necessária** |
    | Imagem | Selecione **Windows Server 2019 Datacenter** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho da VM ou use a configuração padrão |
    | **Conta de administrador** |  |
    | Nome de Usuário | Insira um nome de usuário |
    | Senha | Insira uma senha |
    | Confirmar senha | Insira novamente a senha |
    | **Regras de porta de entrada** |    |
    | Porta de entrada públicas | Selecione **Nenhum**. |
    |

3. Selecione a guia **Rede** ou selecione **Avançar: Discos**, em seguida, **Avançar: Rede**.
  
4. Na guia Rede, selecione ou insira:

    | Setting | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **mySubnet** |
    | IP público | Selecione **Nenhum** |
    | Grupo de segurança de rede da NIC | Selecione **Básico**|
    | Rede das portas de entrada públicas | Selecione **Nenhum**. |
   
5. Selecione a guia **Examinar + criar** ou clique no botão azul **Examinar + criar** na parte inferior da página.
  
6. Examine as configurações e selecione **Criar**.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="connect-to-myvm1"></a>Conectar-se à myVM1

1. Acesse o [portal do Azure](https://portal.azure.com) para gerenciar a VM privada. Pesquise por **Máquinas virtuais** e selecione essa opção.

2. Escolha o nome da sua máquina virtual privada **myVM1**.

3. Na barra de menus da VM, selecione **Conectar** e escolha **Bastion**.

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="Conectar-se à myVM1 com o Azure Bastion" border="true":::

4. Na página **Conectar**, selecione o botão azul **Usar o Bastion**.

5. Na página do **Bastion**, insira o nome de usuário e a senha que você criou para a máquina virtual anteriormente.

6. Selecione **Conectar**.

## <a name="communicate-between-vms"></a>Comunicação entre VMs

1. Na conexão do bastion da **myVM1**, abra o PowerShell.

2. Digite `ping myvm2`.

    Você receberá uma mensagem semelhante a esta saída:

    ```powershell
    Pinging myvm2.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.5] with 32 bytes of data:
    Reply from 10.1.0.5: bytes=32 time=3ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.5:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 3ms, Average = 1ms
    ```

3. Feche a conexão do bastion com a **myVM1**.

4. Conclua as etapas descritas em [Conectar-se à myVM1](#connect-to-myvm1), mas conecte-se à **myVM2**.

5. Abra o PowerShell na **myVM2** e insira `ping myvm1`.

    Você receberá algo parecido com esta mensagem:

    ```powershell
    Pinging myvm1.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 1ms, Average = 1ms
    ```

7. Feche a conexão do bastion com a **myVM2**.

## <a name="clean-up-resources"></a>Limpar os recursos

Neste início rápido, você criou uma rede virtual padrão e duas VMs. 

Você se conectou a uma VM pela Internet e executou uma comunicação entre duas VMs de maneira segura.

Quando terminar de usar a rede virtual e as VMs, exclua o grupo de recursos e todos os recursos que ele contém:

1. Pesquise e selecione **myResourceGroup**.

1. Selecione **Excluir grupo de recursos**.

1. Insira **myResourceGroup** para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre configurações de rede virtual, confira [Criar, alterar ou excluir uma rede virtual](manage-virtual-network.md).

Para saber mais sobre os tipos de comunicações de rede de VMs, confira [Filtrar o tráfego de rede](tutorial-filter-network-traffic.md).
