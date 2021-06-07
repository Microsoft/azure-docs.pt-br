---
title: Configurar conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente-portal do Azure
description: Saiba como configurar um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente usando o portal do Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: cb7bfb9ac4b10b807ac186d087b0037953abd559
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91439517"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Configurar um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente usando o portal do Azure

Neste artigo, você aprenderá a configurar um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure.
- Um balanceador de carga de SKU padrão existente na assinatura em que o conjunto de dimensionamento de máquinas virtuais será implantado.
- Uma rede virtual do Azure para o conjunto de dimensionamento de máquinas virtuais.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>Implantar conjunto de dimensionamento de máquinas virtuais com o balanceador de carga existente

Nesta seção, você criará um conjunto de dimensionamento de máquinas virtuais no portal do Azure com um balanceador de carga do Azure existente.

> [!NOTE]
> As etapas a seguir pressupõem uma rede virtual chamada **myVNet** e um Azure Load Balancer chamado **myLoadBalancer** foi implantado anteriormente.

1. No canto superior esquerdo da tela, clique em **criar um recurso**  >  **computação**  >  **máquina virtual conjunto de dimensionamento** ou procure por **conjunto de dimensionamento de máquinas virtuais** na pesquisa do Marketplace.

2. Selecione **Criar**.

3. Em **criar um conjunto de dimensionamento de máquinas virtuais**, insira ou selecione essas informações na guia **noções básicas** :

    | Configuração                        | Valor                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **Detalhes do projeto**            |                                                                                                       |
    | Subscription                   | Selecionar sua assinatura do Azure                                                                        |
    | Grupo de recursos                 | Selecione criar novo, insira **MyResource** Group, selecione OK ou selecione um grupo de recursos existente. |
    | **Detalhes do conjunto de dimensionamento**          |                                                                                                       |
    | Nome do conjunto de dimensionamento de máquinas virtuais | Insira **myVMSS**                                                                                      |
    | Região                         | Selecione **Leste dos EUA 2**                                                                                    |
    | Zona de disponibilidade              | Selecione **Nenhum**                                                                                       |
    | **Detalhes da instância**           |                                                                                                       |
    | Imagem                          | Selecione **Ubuntu Server 18, 4 LTS**                                                                    |
    | Instância do Azure Spot            | Selecione **Não**                                                                                         |
    | Tamanho                           | Deixar no padrão                                                                                      |
    | **Conta de administrador**      |                                                                                                       |
    | Tipo de autenticação            | Selecionar **senha**                                                                                   |
    | Nome de Usuário                       | Insira o nome de usuário do administrador        |
    | Senha                       | Insira sua senha de administrador    |
    | Confirmar senha               | Insira novamente sua senha de administrador |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="Captura de tela mostra a guia criar noções básicas do conjunto de dimensionamento de máquinas virtuais." border="true":::

4. Selecione a guia **Rede**.

5. Insira ou selecione essas informações na guia **rede** :

     Configuração                           | Valor                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Configuração da rede virtual** |                                                          |
    | Rede virtual                   | Selecione **myVNet** ou sua rede virtual existente.      |
    | **Balanceamento de carga**                |                                                          |
    | Usar um balanceador de carga               | Selecione **Sim**                                           |
    | **Configurações de balanceamento de carga**       |                                                          |
    | Opções de balanceamento de carga            | Selecione **Azure Load Balancer**                           |
    | Selecionar um balanceador de carga            | Selecione **myLoadBalancer** ou seu balanceador de carga existente |
    | Selecionar um pool de back-end             | Selecione **myBackendPool** ou seu pool de back-end existente.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="Captura de tela mostra a guia criar rede do conjunto de dimensionamento de máquinas virtuais." border="true":::

6. Selecione a guia **Gerenciamento** .

7. Na guia **Gerenciamento** , defina **diagnóstico de inicialização** como **desativado**.

8. Selecione o botão **revisão azul + criar** .

9. Examine as configurações e selecione o botão **criar** .

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você implantou um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente.  Para saber mais sobre conjuntos de dimensionamento de máquinas virtuais e balanceador de carga, confira:

- [O que é o Azure Load Balancer?](load-balancer-overview.md)
- [O que são conjuntos de escala de máquina virtual?](../virtual-machine-scale-sets/overview.md)
