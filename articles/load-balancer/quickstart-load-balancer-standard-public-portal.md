---
title: 'Início Rápido: Criar um balanceador de carga público – portal do Azure'
titleSuffix: Azure Load Balancer
description: Este guia de início rápido mostra como criar um balanceador de carga usando o portal do Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 7c19c4963ad8c378e1282180800fd839e20edfdd
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056327"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Início Rápido: Criar um balanceador de carga público para balancear cargas de VMs usando o Portal do Azure

Comece a usar o Azure Load Balancer por meio do portal do Azure para criar um balanceador de carga público e três máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>O balanceador de carga de SKU Standard é recomendado para cargas de trabalho de produção.  Para obter mais informações sobre SKUs, confira **[SKUs do Azure Load Balancer](skus.md)** .

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="Recursos do Standard Load Balancer criados para o guia de início rápido." border="false":::

*Figura: recursos criados no guia de início rápido.*

Nesta seção, você criará um balanceador de carga que faz o balanceamento da carga de máquinas virtuais. 

Quando você criar um balanceador de carga público, crie um endereço IP público configurado como o front-end (chamado de **LoadBalancerFrontend** por padrão) para o balanceador de carga.

1. Selecione **Criar um recurso**. 
2. Na caixa de pesquisa, digite **Balanceador de carga**. Selecione **Balanceador de carga** nos resultados da pesquisa.
3. Na página **Balanceador de carga**, clique em **Criar**.
4. Na página **Criar balanceador de carga**, insira ou selecione as seguintes informações: 

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscription               | Selecione sua assinatura.    |    
    | Resource group         | Selecione **Criar novo** e insira **CreatePubLBQS-rg** na caixa de texto.|
    | Nome                   | Insira **myLoadBalancer**                                   |
    | Região         | Selecione **(Europa) Oeste da Europa**.                                        |
    | Type          | Selecione **Público**.                                        |
    | SKU           | Deixe o padrão **Standard**. |
    | Camada          | Deixe o padrão **Regional**. |
    | Endereço IP público | Selecione **Criar novo**. Se você tiver um IP público existente que deseja usar, selecione **Usar existente**. |
    | Nome do endereço IP público | Digite **myPublicIP** na caixa de texto.|
    | Zona de disponibilidade | Selecione **Com redundância de zona** para criar uma balanceador de carga resiliente. Para criar um balanceador de carga zonal, selecione uma zona específica de 1, 2 ou 3 |
    | Adicionar um endereço IPv6 público | Selecione **Não**. </br> Para obter mais informações sobre endereços IPv6 e o balanceador de carga, confira [O que é o IPv6 da Rede Virtual do Azure?](../virtual-network/ipv6-overview.md)  |
    | Preferência de roteamento | Deixe o padrão da **rede da Microsoft**. </br> Para obter mais informações sobre preferências de roteamento, confira [O que é uma preferência de roteamento (versão prévia)?](../virtual-network/routing-preference-overview.md). |

5. Aceite os padrões para as demais configurações e selecione **Examinar + criar**.

6. Na guia **Examinar + criar**, selecione **Criar**.   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png" alt-text="Criar um balanceador de carga padrão" border="true":::
 
## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta seção, você definirá:

* Configurações do balanceador de carga para um pool de endereços de back-end.
* Uma investigação de integridade.
* Uma regra do balanceador de carga.

### <a name="create-a-backend-pool"></a>Crie um pool de back-end

Um pool de endereços de back-end contém os endereços IP das (NICs) virtuais conectadas ao balanceador de carga. 

Crie o pool de endereços de back-end **myBackendPool** para incluir máquinas virtuais para balanceamento de carga de tráfego da Internet.

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.

2. Em **Configurações**, selecione **Pools de back-end** e, em seguida, **Adicionar**.

3. Na página **Adicionar um pool de back-end**, insira **myBackendPool** como o nome para o pool de back-end e depois selecione **Adicionar**.

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

O balanceador de carga monitora o status do seu aplicativo com uma investigação de integridade. 

A investigação de integridade adiciona ou remove VMs do balanceador de carga com base na resposta às verificações de integridade. 

Crie uma investigação de integridade chamada **myHealthProbe** para monitorar a integridade das VMs.

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.

2. Em **Configurações**, selecione **Investigações de integridade** e, em seguida, **Adicionar**.
    
    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira **myHealthProbe**. |
    | Protocolo | Selecione **HTTP**. |
    | Porta | Insira **80**.|
    | Intervalo | Insira **15** para o número de **Intervalo** em segundos entre tentativas de investigação. |
    | Limite não íntegro | Insira **2** para o número de **Limite não íntegro** ou falhas de investigação consecutivas que devem ocorrer antes que uma VM seja considerada não íntegra.|
    | | |

3. Deixe o restante dos padrões e selecione **OK**.

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra de balanceador de carga é usada para definir como o tráfego é distribuído para as VMs. Você define a configuração de IP de front-end para o tráfego de entrada e o pool de IPs de back-end para receber o tráfego. A porta de origem e de destino são definidas na regra. 

Nesta seção, você criará uma regra de balanceador de carga:

* Chamada **myHTTPRule**.
* No front-end chamado **LoadBalancerFrontEnd**.
* Escutando a **Porta 80**.
* Direciona o tráfego com balanceamento de carga para o back-end chamado **myBackendPool** na **Porta 80**.

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.

2. Em **Configurações**, selecione **Regras de balanceamento de carga** e **Adicionar**.

3. Use estes valores para configurar a regra de balanceamento de carga:
    
    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira **myHTTPRule**. |
    | Versão IP | Selecione **IPv4** |
    | Endereço IP de front-end | Selecione **LoadBalancerFrontEnd** |
    | Protocolo | selecione **TCP**. |
    | Porta | Insira **80**.|
    | Porta de back-end | Insira **80**. |
    | Pool de back-end | Selecione **myBackendPool**.|
    | Investigação de integridade | Selecione **myHealthProbe**. |
    | Tempo limite de ociosidade (minutos) | Mova o controle deslizante para **15** minutos. |
    | Redefinição de TCP | Selecione **Habilitado**. |
    | SNAT (conversão de endereços de rede de origem) de saída | Selecione **(Recomendado) Usar regras de saída para fornecer acesso à Internet aos membros do pool de back-ends.** |

4. Deixe o restante dos padrões e selecione **OK**.

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, você:

* Crie uma rede virtual.
* Crie três máquinas virtuais para o pool de back-end do balanceador de carga.
* Instale o IIS nas máquinas virtuais para testar o balanceador de carga.

## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e uma sub-rede.

1. No canto superior esquerdo da tela, selecione **Criar um recurso > Rede > Rede virtual** ou pesquise por **Rede virtual** na caixa de pesquisa.

2. Em **Criar rede virtual**, insira ou selecione estas informações na guia **Básico**:

    | **Configuração**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscription     | Selecionar sua assinatura do Azure                                  |
    | Grupo de recursos   | Selecione **CreatePubLBQS-rg** |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Insira **myVNet**                                    |
    | Região           | Selecione **Oeste da Europa** |

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

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Nesta seção, você criará três VMs (**myVM1**, **myVM2** e **myVM3**) em três zonas diferentes (**Zona 1**, **Zona 2** e **Zona 3**). 

Essas VMs são adicionadas ao pool de back-end do balanceador de carga criado anteriormente.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Máquina virtual**. 
   
2. Em **Criar uma máquina virtual**, digite ou selecione os valores na guia **Informações Básicas**:

    | Configuração | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **CreatePubLBQS-rg** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **myVM1** |
    | Região | Selecione **Oeste da Europa** |
    | Opções de disponibilidade | Selecione **Zonas de disponibilidade** |
    | Zona de disponibilidade | Selecione **1** |
    | Imagem | Selecione **Windows Server 2019 Datacenter** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho da VM ou use a configuração padrão |
    | **Conta de administrador** |  |
    | Nome de Usuário | Insira um nome de usuário |
    | Senha | Insira uma senha |
    | Confirmar senha | Insira novamente a senha |
    | **Regras de porta de entrada** |  |
    | Porta de entrada públicas | Selecione **Nenhum** |

3. Selecione a guia **Rede** ou selecione **Avançar: Discos**, em seguida, **Avançar: Rede**.
  
4. Na guia Rede, selecione ou insira:

    | Configuração | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | **myVNet** |
    | Sub-rede | **myBackendSubnet** |
    | IP público | Selecione **Nenhum**. |
    | Grupo de segurança de rede da NIC | Selecione **Avançado**|
    | Configurar um grupo de segurança de rede | Selecione **Criar novo**. </br> Em **Criar grupo de segurança de rede**, insira **myNSG** no **Nome**. </br> Em **Regras de entrada**, selecione **+Adicionar uma regra de entrada**. </br> Em **Intervalos de porta de destino**, insira **80**. </br> Em **Prioridade**, insira **100**. </br> Em **Nome**, insira **myHTTPRule** </br> Selecione **Adicionar** </br> Selecione **OK** |
    | **Balanceamento de carga**  |
    | Colocar esta máquina virtual por trás de uma solução de balanceamento de carga existente? | Selecione **Sim** |
    | **Configurações de balanceamento de carga** |
    | Opções de balanceamento de carga | Selecione **Balanceamento de carga do Azure** |
    | Selecionar um balanceador de carga | Selecione **myLoadBalancer**  |
    | Selecionar um pool de back-end | Selecione **myBackendPool** |

5. Selecione a guia **Gerenciamento** ou selecione **Avançar** > **Gerenciamento**.

6. Na guia **Gerenciamento**, selecione ou insira:
    
    | Configuração | Valor |
    |-|-|
    | **Monitoring** |  |
    | Diagnóstico de inicialização | Selecione **Desativado** |
   
7. Selecione **Examinar + criar**. 
  
8. Examine as configurações e selecione **Criar**.

9. Siga as etapas 1 a 8 para criar duas VMs adicionais com os seguintes valores e todas as outras configurações iguais à **myVM1**:

    | Configuração | VM 2| VM 3|
    | ------- | ----- |---|
    | Nome |  **myVM2** |**myVM3**|
    | Zona de disponibilidade | **2** |**3**|
    | Grupo de segurança de rede | Selecione o **myNSG** existente| Selecione o **myNSG** existente|

## <a name="create-outbound-rule-configuration"></a>Criar configurações de regra de saída
As regras de saída do balanceador de carga configuram o SNAT de saída para as VMs no pool de back-end. 

Confira mais informações sobre conexões de saída em [Conexões de saída no Azure](load-balancer-outbound-connections.md).

### <a name="create-outbound-rule"></a>Criar uma regra de saída

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.

2. Em **Configurações**, selecione **Regras de saída** e **Adicionar**.

3. Use estes valores para configurar as regras de saída:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira **myOutboundRule**. |
    | Endereço IP de front-end | Selecione **Criar novo**. </br> Em **Nome**, insira **LoadBalancerFrontEndOutbound**. </br> Selecione **Endereço IP** ou **Prefixo de IP**. </br> Selecione **Criar** em **Endereço IP público** ou em **Prefixo de IP público**. </br> Em Nome, insira **myPublicIPOutbound** ou **myPublicIPPrefixOutbound**. </br> Selecione **Adicionar**.|
    | Tempo limite de ociosidade (minutos) | Mova o controle deslizante para **15 minutos**.|
    | Redefinição de TCP | Selecione **Habilitado**.|
    | Pool de back-end | Selecione **Criar novo**. </br> Digite **myBackendPoolOutbound** em **Nome**. </br> Selecione **Adicionar**. |
    | Alocação de porta -> Alocação de porta | Selecione **Escolher manualmente o número de portas de saída** |
    | Portas de saída-> Escolher por | Selecione **Portas por instância** |
    | Portas de saída -> Portas por instância | Digite **10000**. |

4. Selecione **Adicionar**.

### <a name="add-virtual-machines-to-outbound-pool"></a>Adicionar máquinas virtuais a um pool de saída

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.

2. Em **Configurações**, selecione **Pools de back-end**.

3. Selecione **myBackendPoolOutbound**.

4. Em **Rede virtual**, selecione **myVNet**.

5. Em **Máquinas virtuais**, selecione **+ Adicionar**.

6. Marque as caixas ao lado de **myVM1**, **myVM2** e **myVM3**. 

7. Selecione **Adicionar**.

8. Clique em **Salvar**.

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>O balanceador de carga de SKU Standard é recomendado para cargas de trabalho de produção.  Para obter mais informações sobre SKUs, confira **[SKUs do Azure Load Balancer](skus.md)** .

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="Recursos do balanceador de carga básico criados no guia de início rápido." border="false":::

*Figura: recursos criados no guia de início rápido.*

Nesta seção, você criará um balanceador de carga que faz o balanceamento da carga de máquinas virtuais. 

Quando você criar um balanceador de carga público, crie um endereço IP público configurado como o front-end (chamado de **LoadBalancerFrontend** por padrão) para o balanceador de carga.

1. Selecione **Criar um recurso**. 
2. Na caixa de pesquisa, digite **Balanceador de carga**. Selecione **Balanceador de carga** nos resultados da pesquisa.
3. Na página **Balanceador de carga**, clique em **Criar**.
4. Na página **Criar balanceador de carga**, insira ou selecione as seguintes informações: 

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscription               | Selecione sua assinatura.    |    
    | Resource group         | Selecione **Criar novo** e digite **CreatePubLBQS-rg** na caixa de texto.|
    | Nome                   | Insira **myLoadBalancer**                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Type          | Selecione **Público**.                                        |
    | SKU           | Selecione **Básico** |
    | Endereço IP público | Selecione **Criar novo**. Se você tiver um IP público existente que deseja usar, selecione **Usar existente**. |
    | Nome do endereço IP público | Digite **myPublicIP** na caixa de texto.|
    | Atribuição | Selecione **Dinâmico** |
    | Adicionar um endereço IPv6 público | Selecione **Não**. </br> Para obter mais informações sobre endereços IPv6 e o balanceador de carga, confira [O que é o IPv6 da Rede Virtual do Azure?](../virtual-network/ipv6-overview.md)  |

5. Aceite os padrões para as demais configurações e selecione **Examinar + criar**.

6. Na guia **Examinar + criar**, selecione **Criar**.   

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-basic-load-balancer.png" alt-text="Criar um balanceador de carga básico" border="true":::

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta seção, você definirá:

* Crie uma rede virtual.
* Configurações do balanceador de carga para um pool de endereços de back-end.
* Uma investigação de integridade.
* Uma regra do balanceador de carga.

## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e uma sub-rede.

1. No canto superior esquerdo da tela, selecione **Criar um recurso > Rede > Rede virtual** ou pesquise por **Rede virtual** na caixa de pesquisa.

2. Em **Criar rede virtual**, insira ou selecione estas informações na guia **Básico**:

    | **Configuração**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscription     | Selecionar sua assinatura do Azure                                  |
    | Grupo de recursos   | Selecione **CreatePubLBQS-rg** |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Insira **myVNet**                                    |
    | Região           | Selecione **Oeste da Europa** |

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
### <a name="create-a-backend-pool"></a>Crie um pool de back-end

Um pool de endereços de back-end contém os endereços IP das (NICs) virtuais conectadas ao balanceador de carga. 

Crie o pool de endereços de back-end **myBackendPool** para incluir máquinas virtuais para balanceamento de carga de tráfego da Internet.

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.

2. Em **Configurações**, selecione **Pools de back-end** e, em seguida, **Adicionar**.

3. Na página **Adicionar um pool de back-end**, insira ou selecione:
    
    | Setting | Valor |
    | ------- | ----- |
    | Nome | Insira **myBackendPool**. |
    | Rede virtual | Selecione **myVNet**. |
    | Associado a | Selecione **Máquinas virtuais** |

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

O balanceador de carga monitora o status do seu aplicativo com uma investigação de integridade. 

A investigação de integridade adiciona ou remove VMs do balanceador de carga com base na resposta às verificações de integridade. 

Crie uma investigação de integridade chamada **myHealthProbe** para monitorar a integridade das VMs.

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.

2. Em **Configurações**, selecione **Investigações de integridade** e, em seguida, **Adicionar**.
    
    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira **myHealthProbe**. |
    | Protocolo | Selecione **HTTP**. |
    | Porta | Insira **80**.|
    | Caminho | Insira **/** |
    | Intervalo | Insira **15** para o número de **Intervalo** em segundos entre tentativas de investigação. |
    | Limite não íntegro | Insira **2** para o número de **Limite não íntegro** ou falhas de investigação consecutivas que devem ocorrer antes que uma VM seja considerada não íntegra.|

3. Selecione **OK**.

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra de balanceador de carga é usada para definir como o tráfego é distribuído para as VMs. Você define a configuração de IP de front-end para o tráfego de entrada e o pool de IPs de back-end para receber o tráfego. A porta de origem e de destino são definidas na regra. 

Nesta seção, você criará uma regra de balanceador de carga:

* Chamada **myHTTPRule**.
* No front-end chamado **LoadBalancerFrontEnd**.
* Escutando a **Porta 80**.
* Direciona o tráfego com balanceamento de carga para o back-end chamado **myBackendPool** na **Porta 80**.

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.

2. Em **Configurações**, selecione **Regras de balanceamento de carga** e, em seguida, **Adicionar**.

3. Use estes valores para configurar a regra de balanceamento de carga:
    
    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira **myHTTPRule**. |
    | Versão IP | Selecione **IPv4** |
    | Endereço IP de front-end | Selecione **LoadBalancerFrontEnd** |
    | Protocolo | selecione **TCP**. |
    | Porta | Insira **80**.|
    | Porta de back-end | Insira **80**. |
    | Pool de back-end | Selecione **myBackendPool**.|
    | Investigação de integridade | Selecione **myHealthProbe**. |
    | Tempo limite de ociosidade (minutos) | Mova o controle deslizante para **15** minutos. |
 
4. Deixe o restante dos padrões e selecione **OK**.

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, você:

* Crie três máquinas virtuais para o pool de back-end do balanceador de carga.
* Crie um conjunto de disponibilidade para as máquinas virtuais.
* Instale o IIS nas máquinas virtuais para testar o balanceador de carga.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Nesta seção, você criará três VMs (**myVM1**, **myVM2** e **myVM3**) com um endereço IP público básico.  

As três VMs serão adicionadas a um conjunto de disponibilidade chamado **myAvailabilitySet**.

Essas VMs são adicionadas ao pool de back-end do balanceador de carga criado anteriormente.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Máquina virtual**. 
   
2. Em **Criar uma máquina virtual**, digite ou selecione os valores na guia **Informações Básicas**:

    | Configuração | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **CreatePubLBQS-rg** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **myVM1** |
    | Região | Selecione **Oeste da Europa** |
    | Opções de disponibilidade | Selecione **Conjunto de disponibilidade** |
    | Conjunto de disponibilidade | Selecione **Criar novo**. </br> Insira **myAvailabilitySet** em **Nome**. </br> Selecione **OK** |
    | Imagem | **Windows Server 2019 Datacenter** |
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
    | Rede virtual | Selecione **myVNet** |
    | Sub-rede | Selecione **myBackendSubnet** |
    | IP público | Selecione **Nenhum** |
    | Grupo de segurança de rede da NIC | Selecione **Avançado**|
    | Configurar um grupo de segurança de rede | Selecione **Criar novo**. </br> Em **Criar grupo de segurança de rede**, insira **myNSG** no **Nome**. </br> Em **Regras de entrada**, selecione **+Adicionar uma regra de entrada**. </br> Em **Intervalos de porta de destino**, insira **80**. </br> Em **Prioridade**, insira **100**. </br> Em **Nome**, insira **myHTTPRule** </br> Selecione **Adicionar** </br> Selecione **OK** |
    | **Balanceamento de carga**  |
    | Colocar esta máquina virtual por trás de uma solução de balanceamento de carga existente? | Selecione **Não** |
 
5. Selecione a guia **Gerenciamento** ou selecione **Avançar** > **Gerenciamento**.

6. Na guia **Gerenciamento**, selecione ou insira:
    
    | Configuração | Valor |
    |---|---|
    | **Monitoring** | |
    | Diagnóstico de inicialização | Selecione **Desativado** |

7. Selecione **Examinar + criar**. 
  
8. Examine as configurações e selecione **Criar**.

9. Siga as etapas 1 a 8 para criar duas VMs adicionais com os seguintes valores e todas as outras configurações iguais à **myVM1**:

    | Configuração | VM 2| VM 3|
    | ------- | ----- |---|
    | Nome |  **myVM2** |**myVM3**|
    | Conjunto de disponibilidade| Selecione **myAvailabilitySet** | Selecione **myAvailabilitySet**|
    | Grupo de segurança de rede | Selecione o **myNSG** existente| Selecione o **myNSG** existente|

### <a name="add-virtual-machines-to-the-backend-pool"></a>Adicionar máquinas virtuais ao pool de back-end

As VMs criadas nas etapas anteriores devem ser adicionadas ao pool de back-end de **myLoadBalancer**.

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.

2. Em **Configurações**, selecione **Pools de back-end** e escolha **myBackendPool**.

3. Selecione **Máquinas virtuais** em **Associado a**.

4. Na seção **Máquinas virtuais**, selecione **+Adicionar**.

5. Selecione as caixas ao lado de **myVM1**, **myVM2** e **myVM3**.

6. Selecione **Adicionar**.

7. Clique em **Salvar**.

---

## <a name="install-iis"></a>Instalar o IIS

1. Selecione **Todos os serviços** no menu à esquerda, **Todos os recursos** e, na lista de recursos, selecione **myVM1** localizada no grupo de recursos **CreatePubLBQS-rg**.

2. Na página **Visão Geral**, selecione **Conectar** e **Bastion**.

4. Insira o nome de usuário e a senha fornecidos durante a criação da VM.

5. Selecione **Conectar**.

6. Na área de trabalho do servidor, navegue até **Ferramentas Administrativas do Windows** > **Windows PowerShell**.

7. Na janela do PowerShell, execute os seguintes comandos para:

    * Instalar o servidor IIS
    * Remover o arquivo padrão iisstart.htm
    * Adicionar um novo arquivo iisstart.htm que exiba o nome da VM:

   ```powershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Feche a sessão do Bastion com **myVM1**.

9. Repita as etapas de 1 a 6 para instalar o IIS e o arquivo iisstart.htm atualizado em **myVM2** e **myVM3**.

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

1. Encontre o endereço IP público para o balanceador de carga na tela **Visão Geral**. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myPublicIP**.

2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador. A página padrão do servidor Web do IIS é exibida no navegador.

   ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver o balanceador de carga distribuir o tráfego entre as três VMs, personalize a página padrão do servidor Web do IIS de cada VM e force a atualização do navegador da Web no computador cliente.

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua o grupo de recursos, o balanceador de carga e todos os recursos relacionados quando eles não forem mais necessários. Para fazer isso, selecione o grupo de recursos **CreatePubLBQS-rg** que contém os recursos e escolha **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você:

* Criou um Azure Standard ou Basic Load Balancer
* Anexou três VMs ao balanceador de carga.
* Configurou a regra de tráfego do balanceador de carga e a investigação de integridade e testou o balanceador de carga. 

Para saber mais sobre o Azure Load Balancer, vá para:
> [!div class="nextstepaction"]
> [O que é o Azure Load Balancer?](load-balancer-overview.md)