---
title: 'Início Rápido: criar um balanceador de carga interno – portal do Azure'
titleSuffix: Azure Load Balancer
description: Este guia de início rápido mostra como criar um balanceador de carga interno usando o portal do Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 9f987cd99007f66540fd826a6cd14c6427ea0893
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056378"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Início Rápido: Criar um balanceador de carga interno para balancear a carga de VMs usando o portal do Azure

Comece a usar o Azure Load Balancer no portal do Azure para criar um balanceador de carga interno e três máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-internal-load-balancer-standard)

>[!NOTE]
>O balanceador de carga de SKU Standard é recomendado para cargas de trabalho de produção.  Para obter mais informações sobre SKUs, confira **[SKUs do Azure Load Balancer](skus.md)** .

Nesta seção, você criará um balanceador de carga que faz o balanceamento da carga de máquinas virtuais. 

Quando você cria um balanceador de carga interno, uma rede virtual é configurada como a rede para o balanceador de carga. 

O seguinte diagrama mostra os recursos criados neste guia de início rápido:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Recursos do Standard Load Balancer criados para o guia de início rápido." border="false":::

Um endereço IP privado na rede virtual é configurado como o front-end (chamado **LoadBalancerFrontend** por padrão) para o balanceador de carga. 

O endereço IP do front-end pode ser **Estático** ou **Dinâmico**.

## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e uma sub-rede.

1. No canto superior esquerdo da tela, selecione **Criar um recurso > Rede > Rede virtual** ou pesquise por **Rede virtual** na caixa de pesquisa.

2. Em **Criar rede virtual**, insira ou selecione estas informações na guia **Básico**:

    | **Configuração**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscription     | Selecionar sua assinatura do Azure                                  |
    | Grupo de recursos   | Selecione **CreateIntLBQS-rg** |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Insira **myVNet**                                    |
    | Região           | Selecione **(Europa) Oeste da Europa** |

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

## <a name="create-load-balancer"></a>Criar um balanceador de carga

1. Selecione **Criar um recurso**. 
2. Na caixa de pesquisa, digite **Balanceador de carga**. Selecione **Balanceador de carga** nos resultados da pesquisa.
3. Na página **Balanceador de carga**, clique em **Criar**.
4. Na página **Criar balanceador de carga**, insira ou selecione as seguintes informações: 

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscription               | Selecione sua assinatura.    |    
    | Resource group         | Selecione **CreateIntLBQS-rg** criado na etapa anterior.|
    | Nome                   | Insira **myLoadBalancer**                                   |
    | Região         | Selecione **(Europa) Oeste da Europa**.                                        |
    | Type          | selecione **Interno**.                                        |
    | SKU           | Selecione **Standard** |
    | Rede virtual | Selecione **myVNet** criada na etapa anterior. |
    | Sub-rede  | Selecione **myBackendSubnet** criada na etapa anterior. |
    | Atribuição de endereço IP | selecione **Dinâmico**. |
    | Zona de disponibilidade | Selecione **Com redundância de zona** |

3. Aceite os padrões para as demais configurações e selecione **Examinar + criar**.

4. Na guia **Examinar + criar**, selecione **Criar**.   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-standard-internal-load-balancer.png" alt-text="Criar um balanceador de carga interno Standard." border="true":::
 
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
    | Redefinição de TCP | Selecione **Habilitado**. |
    
4. Deixe o restante dos padrões e selecione **OK**.

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, você:

* Crie três máquinas virtuais para o pool de back-end do balanceador de carga.
* Instale o IIS nas máquinas virtuais para testar o balanceador de carga.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Nesta seção, você criará três VMs (**myVM1**, **myVM2** e **myVM3**).

Essas VMs são adicionadas ao pool de back-end do balanceador de carga criado anteriormente.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Máquina virtual**. 
   
2. Em **Criar uma máquina virtual**, digite ou selecione os valores na guia **Informações Básicas**:

    | Configuração | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **CreateIntLBQS-rg** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **myVM1** |
    | Região | Selecione **(Europa) Oeste da Europa** |
    | Opções de disponibilidade | Selecione **Zonas de disponibilidade** |
    | Zona de disponibilidade | Selecione **1** |
    | Imagem | Selecione **Windows Server 2019 Datacenter** |
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
    | Rede virtual | **myVNet** |
    | Sub-rede | **myBackendSubnet** |
    | IP público | Selecione **Nenhum** |
    | Grupo de segurança de rede da NIC | Selecione **Avançado**|
    | Configurar um grupo de segurança de rede | Selecione **Criar novo**. </br> Em **Criar grupo de segurança de rede**, insira **myNSG** no **Nome**. </br> Selecione **OK** |
    | **Balanceamento de carga**  |
    | Colocar esta máquina virtual por trás de uma solução de balanceamento de carga existente? | Selecione **Sim** |
    | **Configurações de balanceamento de carga** |
    | Opções de balanceamento de carga | Selecione **Balanceamento de carga do Azure** |
    | Selecionar um balanceador de carga | Selecione **myLoadBalancer**  |
    | Selecionar um pool de back-end | Selecione **myBackendPool** |
   
5. Selecione **Examinar + criar**. 
  
6. Examine as configurações e selecione **Criar**.

7. Siga as etapas 1 a 8 para criar mais duas VMs com os seguintes valores e todas as outras configurações iguais à **myVM1**:

    | Configuração | VM 2 | VM 3 |
    | ------- | ----- | ---- |
    | Nome |  **myVM2** | **myVM3** |
    | Zona de disponibilidade | **2** | **3** |
    | Grupo de segurança de rede | Selecione o **myNSG** existente| Selecione o **myNSG** existente |


# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-1-create-internal-load-balancer-basic)

>[!NOTE]
>O balanceador de carga de SKU Standard é recomendado para cargas de trabalho de produção.  Para obter mais informações sobre SKUs, confira **[SKUs do Azure Load Balancer](skus.md)** .

Nesta seção, você criará um balanceador de carga que faz o balanceamento da carga de máquinas virtuais. 

Quando você cria um balanceador de carga interno, uma rede virtual é configurada como a rede para o balanceador de carga. 

O seguinte diagrama mostra os recursos criados neste guia de início rápido:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Recursos do balanceador de carga básico criados no guia de início rápido." border="false":::

Um endereço IP privado na rede virtual é configurado como o front-end (chamado **LoadBalancerFrontend** por padrão) para o balanceador de carga. 

O endereço IP do front-end pode ser **Estático** ou **Dinâmico**.

## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e uma sub-rede.

1. No canto superior esquerdo da tela, selecione **Criar um recurso > Rede > Rede virtual** ou pesquise por **Rede virtual** na caixa de pesquisa.

2. Em **Criar rede virtual**, insira ou selecione estas informações na guia **Básico**:

    | **Configuração**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscription     | Selecionar sua assinatura do Azure                                  |
    | Grupo de recursos   | Selecione **CreateIntLBQS-rg** |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Insira **myVNet**                                    |
    | Região           | Selecione **(Europa) Oeste da Europa** |

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

## <a name="create-load-balancer"></a>Criar um balanceador de carga

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Load Balancer.**

2. Na guia **Informações Básicas** da página **Criar balanceador de carga**, insira ou selecione as seguintes informações: 

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscription               | Selecione sua assinatura.    |    
    | Resource group         | Selecione **CreateIntLBQS-rg** criado na etapa anterior.|
    | Nome                   | Insira **myLoadBalancer**                                   |
    | Região         | Selecione **(Europa) Oeste da Europa**.                                        |
    | Type          | selecione **Interno**.                                        |
    | SKU           | Selecione **Básico** |
    | Rede virtual | Selecione **myVNet** criada na etapa anterior. |
    | Sub-rede  | Selecione **myBackendSubnet** criada na etapa anterior. |
    | Atribuição de endereço IP | selecione **Dinâmico**. |

3. Aceite os padrões para as demais configurações e selecione **Examinar + criar**.

4. Na guia **Examinar + criar**, selecione **Criar**.   

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-basic-internal-load-balancer.png" alt-text="Criar um balanceador de carga interno básico." border="true":::

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

3. Na página **Adicionar um pool de back-end**, insira ou selecione:
    
    | Setting | Valor |
    | ------- | ----- |
    | Nome | Insira **myBackendPool**. |
    | Rede virtual | Selecione **myVNet**. |
    | Associado a | Selecione **Máquinas virtuais** |

4. Selecione **Adicionar**.

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

Nesta seção, você criará três VMs (**myVM1**, **myVM2**, **myVM3**).

As duas VMs serão adicionadas a um conjunto de disponibilidade chamado **myAvailabilitySet**.

Essas VMs são adicionadas ao pool de back-end do balanceador de carga criado anteriormente.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Máquina virtual**. 
   
2. Em **Criar uma máquina virtual**, digite ou selecione os valores na guia **Informações Básicas**:

    | Configuração | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **CreateIntLBQS-rg** |
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
    | Configurar um grupo de segurança de rede | Selecione **Criar novo**. </br> Em **Criar grupo de segurança de rede**, insira **myNSG** no **Nome**. </br> Selecione **OK** |
    | **Balanceamento de carga**  |
    | Colocar esta máquina virtual por trás de uma solução de balanceamento de carga existente? | Selecione **Não** |

5. Selecione **Examinar + criar**. 
  
6. Examine as configurações e selecione **Criar**.

7. Siga as etapas 1 a 8 para criar mais duas VMs com os seguintes valores e todas as outras configurações iguais à **myVM1**:

    | Configuração | VM 2 | VM 3 |
    | ------- | ----- | ---- |
    | Nome |  **myVM2** | **myVM3** |
    | Conjunto de disponibilidade | Selecione **myAvailabilitySet** | Selecione **myAvailabilitySet** |
    | Grupo de segurança de rede | Selecione o **myNSG** existente | Selecione o **myNSG** existente |

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

## <a name="create-test-virtual-machine"></a>Criar máquina virtual de teste

Nesta seção, você criará uma VM chamada **myTestVM**.  Essa VM será usada para testar a configuração do balanceador de carga.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Máquina virtual**. 
   
2. Em **Criar uma máquina virtual**, digite ou selecione os valores na guia **Informações Básicas**:

    | Configuração | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **CreateIntLBQS-rg** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **myTestVM** |
    | Região | Selecione **Oeste da Europa** |
    | Opções de disponibilidade | Selecione **Nenhuma redundância infraestrutura necessária** |
    | Imagem | Selecione **Windows Server 2019 Datacenter** |
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
    | Rede virtual | **myVNet** |
    | Sub-rede | **myBackendSubnet** |
    | IP público | Selecione **Nenhum**. |
    | Grupo de segurança de rede da NIC | Selecione **Avançado**|
    | Configurar um grupo de segurança de rede | Selecione **MyNSG** criado na etapa anterior.|
       
5. Selecione **Examinar + criar**. 
  
6. Examine as configurações e selecione **Criar**.

## <a name="install-iis"></a>Instalar o IIS

1. Selecione **Todos os serviços** no menu à esquerda, em seguida, **Todos os recursos** e, na lista de recursos, escolha **myVM1**, que está no grupo de recursos **CreateIntLBQS-rg**.

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

1. Encontre o endereço IP privado para o balanceador de carga na tela **Visão Geral**. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer**.

2. Anote ou copie o endereço ao lado de **Endereço IP Privado** na **Visão geral** do **myLoadBalancer**.

3. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, na lista de recursos, escolha **myTestVM**, que está no grupo de recursos **CreateIntLBQS-rg**.

4. Na página **Visão Geral**, selecione **Conectar** e **Bastion**.

6. Insira o nome de usuário e a senha fornecidos durante a criação da VM.

7. Abra o **Internet Explorer** em **myTestVM**.

8. Insira o endereço IP da etapa anterior na barra de endereços do navegador. A página padrão do servidor Web do IIS é exibida no navegador.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="A captura de tela mostra uma janela do navegador que exibe a página padrão, conforme esperado." border="true":::
   
Para ver o balanceador de carga distribuir o tráfego entre as duas VMs, personalize a página padrão do servidor Web do IIS de cada VM e force a atualização do navegador da Web no computador cliente.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos **CreateIntLBQS-rg** que contém os recursos e escolha **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você:

* Criou um balanceador de carga interno Standard ou Básico do Azure
* Anexou três VMs ao balanceador de carga.
* Configurou a regra de tráfego do balanceador de carga e a investigação de integridade e testou o balanceador de carga. 

Para saber mais sobre o Azure Load Balancer, vá para:
> [!div class="nextstepaction"]
> [O que é o Azure Load Balancer?](load-balancer-overview.md)