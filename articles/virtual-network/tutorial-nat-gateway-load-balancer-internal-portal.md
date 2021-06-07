---
title: 'Tutorial: Integrar um gateway da NAT a um balanceador de carga interno – portal do Azure'
titleSuffix: Virtual Network NAT
description: Neste tutorial, saiba como integrar um gateway da NAT da Rede Virtual a um balanceador de carga interno usando o portal do Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: f25266f5a969514ca515e8cbbec959404428d6fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670676"
---
# <a name="tutorial-integrate-a-nat-gateway-with-an-internal-load-balancer-using-the-azure-portal"></a>Tutorial: Integrar um gateway da NAT a um balanceador de carga interno usando o portal do Azure

Neste tutorial, você aprenderá a integrar um gateway da NAT a um balanceador de carga interno.

Por padrão, um Standard Azure Load Balancer é seguro. A conectividade de saída é definida explicitamente pela habilitação do SNAT (conversão de endereços de rede de origem) de saída. 

O SNAT é habilitado para um pool de back-end interno por meio de outro balanceador de carga público, um roteamento de rede ou um IP público definido em uma máquina virtual.

A integração do gateway da NAT substitui a necessidade de implantação de um balanceador de carga público, um roteamento de rede ou um IP público definido em uma máquina virtual no pool de back-end.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um Azure Load Balancer
> * Criar duas máquinas virtuais para o pool de back-end do Azure Load Balancer
> * Criar um gateway da NAT
> * Validar a conectividade de saída das máquinas virtuais no pool de back-end do balanceador de carga

## <a name="prerequisites"></a>Pré-requisitos

Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e uma sub-rede.

1. Entre no [portal do Azure](https://portal.azure.com).

1. No canto superior esquerdo da tela, selecione **Criar um recurso > Rede > Rede virtual** ou pesquise por **Rede virtual** na caixa de pesquisa.

2. Selecione **Criar**. 

3. Em **Criar rede virtual**, insira ou selecione estas informações na guia **Básico**:

    | **Configuração**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscription     | Selecionar sua assinatura do Azure                                  |
    | Grupo de recursos   | Selecione **Criar novo**. Insira **TutorIntLBNAT-rg**. </br> Selecione **OK**. |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Insira **myVNet**                                    |
    | Região           | Selecione **Leste dos EUA** |

4. Selecione a guia **Endereços IP** ou selecione o botão **Avançar: Endereços IP** na parte inferior da página.

5. Na guia **Endereços IP**, insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereço IPv4 | Insira **10.1.0.0/16** |

6. Em **Nome da sub-rede**, selecione a palavra **padrão**.

7. Em **Editar sub-rede**, insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Insira **myBackendSubnet** |
    | Intervalo de endereços da sub-rede | Insira **10.1.0.0/24** |

8. Clique em **Salvar**.

9. Selecione a guia **Segurança**.

10. Em **BastionHost**, selecione **Habilitar**. Insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Nome do bastion | Insira **myBastionHost** |
    | Espaço de endereço da AzureBastionSubnet | Insira **10.1.1.0/24** |
    | Endereço IP público | Selecione **Criar novo**. </br> Em **Nome**, insira **myBastionIP**. </br> Selecione **OK**. |


11. Selecione a guia **Revisar + criar** ou o botão **Revisar + criar**.

12. Selecione **Criar**.

## <a name="create-load-balancer"></a>Criar um balanceador de carga

Nesta seção, você criará um Standard Azure Load Balancer. 

1. Selecione **Criar um recurso**. 

2. Na caixa de pesquisa, digite **Balanceador de carga**. Selecione **Balanceador de carga** nos resultados da pesquisa.

3. Na página **Balanceador de carga**, clique em **Criar**.
4. Na página **Criar balanceador de carga**, insira ou selecione as seguintes informações: 

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | **Detalhes do projeto** |   |
    | Subscription               | Selecione sua assinatura.    |    
    | Resource group         | Selecione **TutorIntLBNAT-rg**.|
    | **Detalhes da instância** |    |
    | Nome                   | Insira **myLoadBalancer**                                   |
    | Região         | Selecione **(EUA) Leste dos EUA**.                                        |
    | Type          | selecione **Interno**.                                        |
    | SKU           | Deixe o padrão **Standard**. |
    | **Configurar rede virtual** |    |
    | Rede virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **myBackendSubnet**. |
    | Atribuição de endereço IP | selecione **Dinâmico**. |
    | Zona de disponibilidade | Selecione **Com redundância de zona** para criar uma balanceador de carga resiliente. </br> Para criar um balanceador de carga zonal, selecione uma zona específica de 1, 2 ou 3 |
    

5. Aceite os padrões para as demais configurações e selecione **Examinar + criar**.

6. Na guia **Examinar + criar**, selecione **Criar**.

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

3. Na página **Adicionar um pool de back-end**, insira **myBackendPool** como o nome do pool de back-end e selecione **Adicionar**.

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

O balanceador de carga monitora o status do seu aplicativo com uma investigação de integridade. 

A investigação de integridade adiciona ou remove VMs do balanceador de carga com base na resposta às verificações de integridade. 

Crie uma investigação de integridade chamada **myHealthProbe** para monitorar a integridade das VMs.

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.

2. Em **Configurações**, selecione **Investigações de integridade** e, em seguida, **Adicionar**.
    
    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira **myHealthProbe**. |
    | Protocolo | selecione **TCP**. |
    | Porta | Insira **80**.|
    | Intervalo | Insira **15** para o número de **Intervalo** em segundos entre tentativas de investigação. |
    | Limite não íntegro | Insira **2** para o número de **Limite não íntegro** ou falhas de investigação consecutivas que devem ocorrer antes que uma VM seja considerada não íntegra.|
   
3. Mantenha o restante com os padrões e selecione **Adicionar**.

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
    | Tempo limite de ociosidade (minutos) | Insira **15** minutos. |
    | Redefinição de TCP | Selecione **Habilitado**. |

4. Deixe o restante dos padrões e selecione **OK**.


## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Nesta seção, você criará duas VMs (**myVM1** e **myVM2**) em duas zonas diferentes (**Zona 1** e **Zona 2**).

Essas VMs são adicionadas ao pool de back-end do balanceador de carga criado anteriormente.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Máquina virtual**. 
   
2. Em **Criar uma máquina virtual**, digite ou selecione os valores na guia **Informações Básicas**:

    | Configuração | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **TutorIntLBNAT-rg** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **myVM1** |
    | Região | Selecione **Leste dos EUA** |
    | Opções de disponibilidade | Selecione **Zonas de disponibilidade** |
    | Zona de disponibilidade | Selecione **1** |
    | Imagem | Selecione **Windows Server 2019 Datacenter** |
    | Instância do Azure Spot | Deixar o padrão |
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
    | Proteger esta máquina virtual com uma solução de balanceamento de carga existente? | Marque a caixa de seleção. |
    | **Configurações de balanceamento de carga** |
    | Opções de balanceamento de carga | Selecione **Azure Load Balancer** |
    | Selecionar um balanceador de carga | Selecione **myLoadBalancer**  |
    | Selecionar um pool de back-end | Selecione **myBackendPool** |
   
5. Selecione **Examinar + criar**. 
  
6. Examine as configurações e selecione **Criar**.

7. Siga as etapas 1 a 7 para criar uma VM com os seguintes valores e todas as outras configurações iguais à da **myVM1**:

    | Configuração | VM 2 |
    | ------- | ----- |
    | Nome |  **myVM2** |
    | Zona de disponibilidade | **2** |
    | Grupo de segurança de rede | Selecione o **myNSG** existente| 

## <a name="create-nat-gateway"></a>Criar gateway NAT

Nesta seção, você criará um gateway da NAT e o atribuirá à sub-rede na rede virtual criada anteriormente.

1. No lado superior esquerdo da tela, clique em **Criar um recurso > Rede > gateway da NAT** ou pesquise a opção **gateway da NAT** na caixa de pesquisa.

2. Selecione **Criar**. 

3. Em **Criar gateway da NAT (conversão de endereços de rede)** , insira ou selecione as seguintes informações na guia **Básico**:

    | **Configuração**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscription     | Selecione sua assinatura do Azure.                                  |
    | Grupo de recursos   | Selecione **TutorIntLBNAT-rg**. |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Insira **myNATGateway**                                    |
    | Região           | Selecione **(EUA) Leste dos EUA**  |
    | Zona de disponibilidade | Selecione **Nenhum**. |
    | Tempo limite de ociosidade (minutos) | Insira **10**. |

4. Selecione a guia **IP de saída** ou clique no botão **Avançar: IP de saída** na parte inferior da página.

5. Na guia **IP de saída**, insira ou selecione as seguintes informações:

    | **Configuração** | **Valor** |
    | ----------- | --------- |
    | Endereços IP públicos | Clique em **Criar um endereço IP público**. </br> Em **Nome**, insira **myPublicIP-NAT**. </br> Selecione **OK**. |

6. Selecione a guia **Sub-rede** ou clique no botão **Avançar: sub-rede** na parte inferior da página.

7. Na guia **Sub-rede**, selecione a opção **myVNet** no menu suspenso **Rede virtual**.

8. Marque a caixa ao lado de **myBackendSubnet**.

9. Selecione a guia **Examinar + criar** ou clique no botão azul **Examinar + criar** na parte inferior da página.

10. Selecione **Criar**.

## <a name="test-nat-gateway"></a>Testar um gateway da NAT

Nesta seção, testaremos um gateway da NAT. Primeiro, descobriremos o IP público do gateway da NAT. Depois nos conectaremos à máquina virtual de teste e verificaremos a conexão de saída por meio do gateway da NAT.
    
1. Localize o endereço IP público do gateway da NAT na tela **Visão Geral**. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myPublicIP**.

2. Anote o endereço IP público:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-internal-portal/find-public-ip.png" alt-text="Captura de tela da descoberta do endereço IP público do gateway da NAT." border="true":::

3. Selecione **Todos os serviços** no menu à esquerda, **Todos os recursos** e, na lista de recursos, selecione **myVM1**, localizada no grupo de recursos **TutorIntLBNAT-rg**.

4. Na página **Visão Geral**, selecione **Conectar** e **Bastion**.

5. Selecione o botão azul **Usar Bastion**.

6. Insira o nome de usuário e a senha fornecidos durante a criação da VM.

7. Abra o **Internet Explorer** em **myVM1**.

8. Digite **https://whatsmyip.com** na barra de endereços.

9. Verifique se o endereço IP exibido corresponde ao endereço do gateway da NAT anotado na etapa anterior:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-internal-portal/my-ip.png" alt-text="Captura de tela do Internet Explorer mostrando um IP de saída externo." border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

Caso não pretenda continuar usando esse aplicativo, exclua a rede virtual, a máquina virtual e o gateway da NAT usando as seguintes etapas:

1. No menu esquerdo, selecione **Grupos de recursos**.

2. Selecione o grupo de recursos **TutorIntLBNAT-rg**.

3. Selecione **Excluir grupo de recursos**.

4. Insira **TutorIntLBNAT-rg** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a NAT da Rede Virtual do Azure, confira:
> [!div class="nextstepaction"]
> [Visão geral da NAT da Rede Virtual](nat-overview.md)
