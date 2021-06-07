---
title: Início Rápido – Criar um serviço de Link Privado usando o portal do Azure
titlesuffix: Azure Private Link
description: Learn how to create a Private Link service by using the Azure portal in this quickstart
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/18/2021
ms.author: allensu
ms.openlocfilehash: d394a475c5121607f70c03437382e104a5d0cbee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98746400"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Início Rápido: Criar um serviço de Link Privado usando o portal do Azure

Introdução à criação de um serviço de Link Privado referente ao seu serviço.  Conceda acesso do Link Privado ao serviço ou recurso implantado por trás de um Standard Load Balancer do Azure.  Os usuários do seu serviço têm acesso privado da rede virtual deles.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Criar um balanceador de carga interno

Nesta seção, você criará uma rede virtual e um Azure Load Balancer interno.

### <a name="virtual-network"></a>Rede virtual

Nesta seção, você criará uma rede virtual e uma sub-rede para hospedar o balanceador de carga que acessa o serviço de Link Privado.

1. No canto superior esquerdo da tela, selecione **Criar um recurso > Rede > Rede virtual** ou pesquise por **Rede virtual** na caixa de pesquisa.

2. Em **Criar rede virtual**, insira ou selecione estas informações na guia **Básico**:

    | **Configuração**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscription     | Selecionar sua assinatura do Azure                                  |
    | Grupo de recursos   | Selecione **CreatePrivLinkService-rg** |
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
    | Nome da sub-rede | Insira **mySubnet** |
    | Intervalo de endereços da sub-rede | Insira **10.1.0.0/24** |

7. Clique em **Salvar**.

8. Selecione a guia **Revisar + criar** ou o botão **Revisar + criar**.

9. Selecione **Criar**.

### <a name="create-a-standard-load-balancer"></a>Criar um balanceador de carga padrão

Use o portal para criar um balanceador de carga Standard interno. 

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Load Balancer.**

2. Na guia **Informações Básicas** da página **Criar balanceador de carga**, insira ou selecione as seguintes informações: 

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscription               | Selecione sua assinatura.    |    
    | Resource group         | Selecione o **CreatePrivLinkService-rg** criado na etapa anterior.|
    | Nome                   | Insira **myLoadBalancer**                                   |
    | Região         | Selecione **Leste dos EUA 2**.                                        |
    | Type          | selecione **Interno**.                                        |
    | SKU           | Selecione **Standard** |
    | Rede virtual | Selecione **myVNet** criada na etapa anterior. |
    | Sub-rede  | Selecione **mySubnet** criada na etapa anterior. |
    | Atribuição de endereço IP | selecione **Dinâmico**. |
    | Zona de disponibilidade | Selecione **Com redundância de zona** |

3. Aceite os padrões para as demais configurações e selecione **Examinar + criar**.

4. Na guia **Examinar + criar**, selecione **Criar**.   

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
    | Protocolo | selecione **TCP**. |
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

4. Deixe o restante dos padrões e selecione **OK**.

## <a name="create-a-private-link-service"></a>Criar um serviço de Link Privado

Nesta seção, você criará um serviço de Link Privado por trás de um balanceador de carga padrão.

1. Na parte superior esquerda da página no portal do Azure, selecione **Criar um recurso**.

2. Pesquise por **Link Privado** na caixa **Pesquisar no Marketplace**.

3. Selecione **Criar**.

4. Em **Visão geral**, em **Centro de Link Privado**, selecione o botão azul **Criar serviço de link privado**.

5. Na guia **Básico**, em **Criar serviço de link privado**, insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** |  |
    | Subscription | Selecione sua assinatura. |
    | Grupo de recursos | Selecione **CreatePrivLinkService-rg**. |
    | **Detalhes da instância** |  |
    | Nome | Insira **myPrivateLinkService**. |
    | Região | Selecione **Leste dos EUA 2**. |

6. Selecione a guia **Configurações de saída** ou selecione **Avançar: Configurações adicionais** na parte inferior da página.

7. Na guia **Configurações de saída**, insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Balanceador de carga | Selecione **myLoadBalancer**. |
    | Endereço IP de front-end do balanceador de carga | Selecione **LoadBalancerFrontEnd (10.1.0.4)** . |
    | Sub-rede da NAT de origem | Selecione **mySubnet (10.1.0.0/24)** . |
    | Habilitar o proxy TCP V2 | Mantenha o padrão **Não**. </br> Se o aplicativo estiver esperando o cabeçalho de proxy TCP v2, selecione **Sim**. |
    | **Configurações de endereço IP privado** |  |
    | Deixar as configurações padrão |  |

8. Selecione a guia **Segurança de acesso** ou selecione **Avançar: Segurança de acesso** na parte inferior da página.

9. Deixe o padrão de **Somente controle de acesso baseado em função** na guia **Segurança de acesso**.

10. Selecione a guia **Marcas** ou selecione **Avançar: Marcas** na parte inferior da página.

11. Selecione a guia **Examinar + criar** ou selecione **Avançar: Examinar + criar** na parte inferior da página.

12. Selecione **Criar** na guia **Examinar + criar**.

O seu serviço de link privado é criado e pode receber tráfego. Se você quiser ver os fluxos de tráfego, configure o seu aplicativo por trás do Standard Load Balancer.


## <a name="create-private-endpoint"></a>Criar um ponto de extremidade privado

Nesta seção, você mapeará o serviço de link privado para um ponto de extremidade privado. Uma rede virtual contém o ponto de extremidade privado do serviço de link privado. Essa rede virtual contém os recursos que acessarão o serviço de link privado.

### <a name="create-private-endpoint-virtual-network"></a>Criar uma rede virtual do ponto de extremidade privado

1. No canto superior esquerdo da tela, selecione **Criar um recurso > Rede > Rede virtual** ou pesquise por **Rede virtual** na caixa de pesquisa.

2. Em **Criar rede virtual**, insira ou selecione estas informações na guia **Básico**:

    | **Configuração**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscription     | Selecionar sua assinatura do Azure                                  |
    | Grupo de recursos   | Selecione **CreatePrivLinkService-rg** |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Insira **myVNetPE**                                    |
    | Região           | Selecione **Leste dos EUA 2** |

3. Selecione a guia **Endereços IP** ou selecione o botão **Avançar: Endereços IP** na parte inferior da página.

4. Na guia **Endereços IP**, insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereço IPv4 | Insira **11.1.0.0/16** |

5. Em **Nome da sub-rede**, selecione a palavra **padrão**.

6. Em **Editar sub-rede**, insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Insira **mySubnetPE** |
    | Intervalo de endereços da sub-rede | Insira **11.1.0.0/24** |

7. Clique em **Salvar**.

8. Selecione a guia **Revisar + criar** ou o botão **Revisar + criar**.

9. Selecione **Criar**.

### <a name="create-private-endpoint"></a>Criar um ponto de extremidade privado

1. No lado superior esquerdo da tela no portal, selecione **Criar um recurso** > **Rede** > **Link Privado** ou, na caixa de pesquisa, digite **Link Privado**.

2. Selecione **Criar**.

3. Em **Centro de Link Privado**, selecione **Pontos de extremidade privados** no menu à esquerda.

4. Em **Pontos de extremidade privados**, selecione **+ Adicionar**.

5. Na guia **Noções básicas** de **Criar um ponto de extremidade privado**, digite ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **CreatePrivLinkService-rg**. Você criou esse grupo de recursos na seção anterior.|
    | **Detalhes da instância** |  |
    | Nome  | Insira **myPrivateEndpoint**. |
    | Região | Selecione **Leste dos EUA 2**. |

6. Selecione a guia **Recurso** ou o botão **Avançar: Recurso** na parte inferior da página.
    
7. Em **Recurso**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Método de conexão | Selecione **Conectar-se a um recurso do Azure em meu diretório**. |
    | Subscription | Selecione sua assinatura. |
    | Tipo de recurso | Selecione **Microsoft.Network/privateLinkServices**. |
    | Recurso | Selecione **myPrivateLinkService**. |

8. Selecione a guia **Configuração** ou o botão **Avançar: Configuração** na parte inferior da tela.

9. Em **Configuração**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Rede** |  |
    | Rede Virtual | Selecione **myVNetPE**. |
    | Sub-rede | Selecione **mySubnetPE**. |

10. Selecione a guia **Examinar + criar** ou o botão **Examinar + criar** na parte inferior da tela.

11. Selecione **Criar**.

### <a name="ip-address-of-private-endpoint"></a>Endereço IP do ponto de extremidade privado

Nesta seção, você encontra o endereço IP do ponto de extremidade privado que corresponde ao balanceador de carga e ao serviço de link privado.

1. Na coluna à esquerda do portal do Azure, selecione **Grupos de recursos**.

2. Selecione o grupo de recursos **CreatePrivLinkService-rg**.

3. No grupo de recursos **CreatePrivLinkService-rg**, selecione **myPrivateEndpoint**.

4. Na página **Visão geral** de **myPrivateEndpoint**, selecione o nome do adaptador de rede associado ao ponto de extremidade privado.  O nome do adaptador de rede começa com **myPrivateEndpoint.nic**.

5. Na página **Visão geral** do adaptador de rede do ponto de extremidade privado, o endereço IP do ponto de extremidade é exibido em **Endereço IP privado**.
    

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você terminar de usar o serviço de link privado, exclua o grupo de recursos para limpar os recursos usados neste guia de início rápido.

1. Insira **CreatePrivLinkService-rg** na caixa de pesquisa na parte superior do portal e selecione **CreatePrivLinkService-rg** nos resultados da pesquisa.
1. Selecione **Excluir grupo de recursos**.
1. Em **DIGITE O NOME DO GRUPO DE RECURSOS**, insira **CreatePrivLinkService-rg**.
1. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você:

* criou uma rede virtual e um Azure Load Balancer interno.
* Criou um serviço de link privado.
* Criou uma rede virtual e um ponto de extremidade privado para o serviço de link privado.

Para saber mais sobre o ponto de extremidade privado do Azure, acesse:
> [!div class="nextstepaction"]
> [Início Rápido: Criar um ponto de extremidade privado usando o portal do Azure](create-private-endpoint-portal.md)