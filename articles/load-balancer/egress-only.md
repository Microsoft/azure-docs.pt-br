---
title: Configuração do balanceador de carga somente de saída
titleSuffix: Azure Load Balancer
description: Com este artigo, saiba mais sobre como criar um balanceador de carga interno com NAT de saída
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2020
ms.author: allensu
ms.openlocfilehash: ee264a22de5ce094e8a4c1335ace77cbbba49270
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94694986"
---
# <a name="outbound-only-load-balancer-configuration"></a>Configuração do balanceador de carga somente de saída

Use uma combinação de balanceadores de carga padrão internos e externos para criar conectividade de saída para VMs por trás de um balanceador de carga interno. 

Essa configuração fornece NAT de saída para um cenário de balanceador de carga interno, produzindo uma configuração de "egresso somente" para seu pool de back-end.


<p align="center">
  <img src="./media/egress-only/load-balancer-egress-only.png" alt="Figure depicts a egress only load balancer configuration." width="500" title="NAT de Rede Virtual">
</p>


<!-- 
:::image type="content" source="./media/egress-only/load-balancer-egress-only.png" alt-text="Figure depicts a egress only load balancer configuration" border="true":::
-->


*Figura: configuração do balanceador de carga somente de egresso*

As etapas necessárias são:

1. Crie uma rede virtual com um host de bastiões.
2. Crie uma máquina virtual com apenas um IP privado.
3. Crie balanceadores de carga padrão internos e públicos.
4. Adicione pools de back-end a ambos os balanceadores de carga e coloque a VM em cada pool.
5. Conecte-se à sua VM por meio do host de bastiões e:
    1. Testar a conectividade de saída, 
    2. Configure uma regra de saída no balanceador de carga público.
    3. Testar novamente a conectividade de saída.

## <a name="create-virtual-network-and-virtual-machine"></a>Criar rede virtual e máquina virtual

Crie uma rede virtual com duas sub-redes:

* Sub-rede primária
* Sub-rede de bastiões

Crie uma máquina virtual na nova rede virtual.

### <a name="create-the-virtual-network"></a>Criar a rede virtual

1. [Entre](https://portal.azure.com) no portal do Azure.

2. No canto superior esquerdo da tela, selecione **Criar um recurso > Rede > Rede virtual** ou pesquise por **Rede virtual** na caixa de pesquisa.

2. Em **Criar rede virtual**, insira ou selecione estas informações na guia **Básico**:

    | **Configuração**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscription     | Selecionar sua assinatura do Azure                                  |
    | Grupo de recursos   | Selecione **Criar novo**. </br> Insira **myResourceGroupLB**. </br> Selecione **OK**. |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Insira **myVNet**                                    |
    | Região           | Selecione **Leste dos EUA 2** |

3. Selecione a guia **Endereços IP** ou selecione o botão **Avançar: Endereços IP** na parte inferior da página.

4. Na guia **Endereços IP**, insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereço IPv4 | Insira **10.1.0.0/16** |

5. Em **Nome da sub-rede**, selecione a palavra **padrão**.

6. Em **Editar sub-rede**, insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Insira **myBackendSubnet** |
    | Intervalo de endereços da sub-rede | Insira **10.1.0.0/24** |

7. Clique em **Salvar**.

8. Selecione a guia **Segurança**.

9. Em **BastionHost**, selecione **Habilitar**. Insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Nome do bastion | Insira **myBastionHost** |
    | Espaço de endereço da AzureBastionSubnet | Insira **10.1.1.0/24** |
    | Endereço IP público | Selecione **Criar novo**. </br> Em **Nome**, insira **myBastionIP**. </br> Selecione **OK**. |


8. Selecione a guia **Revisar + criar** ou o botão **Revisar + criar**.

9. Selecione **Criar**.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Máquina virtual**. 
   
2. Em **Criar uma máquina virtual**, digite ou selecione os valores na guia **Informações Básicas**:

    | Configuração | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **myResourceGroupLB** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **myVM** |
    | Região | Selecione **Leste dos EUA 2** |
    | Opções de disponibilidade | Selecione **Nenhuma redundância infraestrutura necessária** |
    | Imagem | Selecione **Windows Server 2019 Datacenter** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho da VM ou use a configuração padrão |
    | **Conta de administrador** |  |
    | Nome de Usuário | Insira um nome de usuário |
    | Senha | Insira uma senha |
    | Confirmar senha | Insira novamente a senha |
    | **Regras de porta de entrada** |  |
    | Porta de entrada públicas | Selecionar **permitir portas selecionadas** |
    | Selecione as portas de entrada | Selecionar **RDP (3389)** |

3. Selecione a guia **Rede** ou selecione **Avançar: Discos**, em seguida, **Avançar: Rede**.
  
4. Na guia Rede, selecione ou insira:

    | Configuração | Valor |
    |-----|------------|
    | **Interface de rede** |  |
    | Rede virtual | **myVNet** |
    | Sub-rede | **myBackendSubnet** |
    | IP público | Selecione **Nenhum**. |
    | Grupo de segurança de rede da NIC | Selecione **Nenhum**|
    | Coloque esta máquina virtual por trás de uma solução de balanceamento de carga existente? | Selecione **Não** |
   
5. Selecione a guia **Gerenciamento** ou selecione **Avançar** > **Gerenciamento**.

6. Na guia **Gerenciamento**, selecione ou insira:
    
    | Configuração | Valor |
    |-|-|
    | **Monitoring** |  |
    | Diagnóstico de inicialização | Selecione **Desativado** |
   
7. Selecione **Examinar + criar**. 
  
8. Examine as configurações e selecione **Criar**.

## <a name="create-load-balancers-and-test-connectivity"></a>Criar balanceadores de carga e conectividade de teste

Use o portal do Azure para criar:

* Balanceador de carga interno
* Balanceador de carga público
 
Adicione sua VM criada ao pool de back-end de cada.  Em seguida, você definirá uma configuração para permitir somente a conectividade de saída de sua VM, testando antes e depois.

### <a name="create-internal-load-balancer"></a>Criar balanceador de carga interno

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Load Balancer.**

2. Na guia **Informações Básicas** da página **Criar balanceador de carga**, insira ou selecione as seguintes informações: 

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscription               | Selecione sua assinatura.    |    
    | Resource group         | Selecione **myResourceGroupLB** criado na etapa anterior.|
    | Nome                   | Insira **myInternalLoadBalancer**                                   |
    | Região         | Selecione **Leste dos EUA 2**.                                        |
    | Type          | selecione **Interno**.                                        |
    | SKU           | Selecione **Standard** |
    | Rede virtual | Selecione **myVNet** criada na etapa anterior. |
    | Sub-rede  | Selecione **myBackendSubnet** criada na etapa anterior. |
    | Atribuição de endereço IP | selecione **Dinâmico**. |

3. Aceite os padrões para as demais configurações e selecione **Examinar + criar**.

4. Na guia **Examinar + criar**, selecione **Criar**.   

### <a name="create-public-load-balancer"></a>Criar balanceador de carga público

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Load Balancer.**

2. Na guia **Informações Básicas** da página **Criar balanceador de carga**, insira ou selecione as seguintes informações: 

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscription               | Selecione sua assinatura.    |    
    | Resource group         | Selecione **Criar** e insira **myResourceGroupLB** na caixa de texto.|
    | Nome                   | Insira **myPublicLoadBalancer**                                   |
    | Região         | Selecione **Leste dos EUA 2**.                                        |
    | Type          | Selecione **Público**.                                        |
    | SKU           | Selecione **Standard** |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público | Insira **myFrontendIP** na caixa de texto.|
    | Zona de disponibilidade | Selecione **Com redundância de zona** |
    | Adicionar um endereço IPv6 público | Selecione **Não**. |

3. Aceite os padrões para as demais configurações e selecione **Examinar + criar**.

4. Na guia **Examinar + criar**, selecione **Criar**.   
   
### <a name="create-internal-backend-address-pool"></a>Criar pool de endereços de back-end interno

Crie o pool de endereços de back-end **myInternalBackendPool**:

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos** e, em seguida, selecione **myInternalLoadBalancer** na lista de recursos.

2. Em **Configurações**, selecione **Pools de back-end** e, em seguida, **Adicionar**.

3. Na página **Adicionar um pool de back-end** , em nome, digite **myInternalBackendPool**, como o nome do pool de back-end.
 
4. Em **Rede virtual**, selecione **myVNet**.

5. Em **máquinas virtuais**, selecione **Adicionar** e escolha **myVM**.
 
6. Selecione **Adicionar**.

### <a name="create-public-backend-address-pool"></a>Criar pool de endereços de back-end público

Crie o pool de endereços de back-end **myPublicBackendPool**:

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos** e, em seguida, selecione **myPublicLoadBalancer** na lista de recursos.

2. Em **Configurações**, selecione **Pools de back-end** e, em seguida, **Adicionar**.

3. Na página **Adicionar um pool de back-end** , em nome, digite **myPublicBackendPool**, como o nome do pool de back-end.

4. Em **Rede virtual**, selecione **myVNet**.
 
5. Em **máquinas virtuais**, selecione **Adicionar** e escolha **myVM**.
 
6. Selecione **Adicionar**.

### <a name="test-connectivity-before-outbound-rule"></a>Testar a conectividade antes da regra de saída

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos** e, em seguida, na lista recursos, selecione **myVM** que está localizado no grupo de recursos **myResourceGroupLB** .

2. Na página **Visão Geral**, selecione **Conectar** e **Bastion**.

4. Insira o nome de usuário e a senha fornecidos durante a criação da VM.

5. Selecione **Conectar**.

6. Abra o Internet Explorer.

7. Digite **https://whatsmyip.org** na barra de endereços.

8. A conexão deve falhar. Por padrão, o Load Balancer público padrão [não permite o tráfego de saída sem uma regra de saída definida](load-balancer-overview.md#securebydefault).
 
### <a name="create-a-public-load-balancer-outbound-rule"></a>Criar uma regra de saída do balanceador de carga público

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos** e, em seguida, selecione **myPublicLoadBalancer** na lista de recursos.

2. Em **Configurações**, selecione **Regras de saída** e **Adicionar**.

3. Use estes valores para configurar as regras de saída:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira **myOutboundRule**. |
    | Endereço IP de front-end | Selecione **LoadBalancerFrontEnd**.|
    | Tempo limite de ociosidade (minutos) | Mova o controle deslizante para **15 minutos**.|
    | Redefinição de TCP | Selecione **Habilitado**.|
    | Pool de back-end | Selecione **myPublicBackendPool**.| |
    | Alocação de porta -> Alocação de porta | Selecione **usar o número padrão de portas de saída** |

4. Selecione **Adicionar**.

### <a name="test-connectivity-after-outbound-rule"></a>Testar a conectividade após a regra de saída

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos** e, em seguida, na lista recursos, selecione **myVM** que está localizado no grupo de recursos **myResourceGroupLB** .

2. Na página **Visão Geral**, selecione **Conectar** e **Bastion**.

4. Insira o nome de usuário e a senha fornecidos durante a criação da VM.

5. Selecione **Conectar**.

6. Abra o Internet Explorer.

7. Digite **https://whatsmyip.org** na barra de endereços.

8. A conexão deve ter sucesso.

9. O endereço IP exibido deve ser o endereço IP de front-end de **myPublicLoadBalancer**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos, balanceadores de carga, VM e todos os recursos relacionados. 

Para fazer isso, selecione o grupo de recursos **myResourceGroupLB** e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma configuração de "saída somente" com uma combinação de balanceadores de carga públicos e internos.  

Essa configuração permite balancear a carga do tráfego interno de entrada para seu pool de back-end enquanto ainda impede qualquer conexão de entrada pública.

- Saiba mais sobre [Azure Load Balancer](load-balancer-overview.md).
- Saiba mais sobre [as conexões de saída no Azure](load-balancer-outbound-connections.md).
- [Perguntas frequentes](load-balancer-faqs.md)do balanceador de carga.
- Saiba mais sobre a [bastiões do Azure](../bastion/bastion-overview.md)