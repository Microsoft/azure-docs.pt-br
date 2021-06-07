---
title: 'Tutorial: Criar um gateway da NAT – portal do Azure'
titlesuffix: Azure Virtual Network NAT
description: Neste tutorial, saiba como criar e validar um gateway da NAT usando o portal do Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 82b5892b027627871e5492e3c6cd3776a923632b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553435"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Tutorial: Criar um gateway da NAT usando o portal do Azure

Este tutorial mostra como usar o serviço NAT da Rede Virtual do Azure. Você criará um gateway da NAT para fornecer conectividade de saída para uma máquina virtual no Azure. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie uma rede virtual.
> * Crie uma máquina virtual.
> * Crie um gateway da NAT e o associe à rede virtual.
> * Conecte-se à máquina virtual e verifique o endereço IP de NAT.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="virtual-network"></a>Rede virtual

Antes de implantar uma VM e usar o gateway da NAT, precisamos criar o grupo de recursos e a rede virtual.

1. Entre no [portal do Azure](https://portal.azure.com).

2. No canto superior esquerdo da tela, selecione **Criar um recurso > Rede > Rede virtual** ou pesquise por **Rede virtual** na caixa de pesquisa.

3. Selecione **Criar**.

4. Em **Criar rede virtual**, insira ou selecione estas informações na guia **Básico**:

    | **Configuração**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscription     | Selecionar sua assinatura do Azure                                  |
    | Grupo de recursos   | Selecione **Criar novo**. </br> Insira a opção **myResourceGroupNAT**. </br> Selecione **OK**. |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Insira **myVNet**                                    |
    | Região           | Selecione **(Europa) Oeste da Europa** |

5. Selecione a guia **Endereços IP** ou selecione o botão **Avançar: Endereços IP** na parte inferior da página.

6. Na guia **Endereços IP**, insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereço IPv4 | Insira **10.1.0.0/16** |

7. Clique em **+Adicionar sub-rede**. 

8. Em **Editar sub-rede**, insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Insira **mySubnet** |
    | Intervalo de endereços da sub-rede | Insira **10.1.0.0/24** |

9. Selecione **Adicionar**.

10. Selecione a guia **Segurança**.

11. Em **BastionHost**, selecione **Habilitar**. Insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Nome do bastion | Insira **myBastionHost** |
    | Espaço de endereço da AzureBastionSubnet | Insira **10.1.1.0/24** |
    | Endereço IP público | Selecione **Criar novo**. </br> Em **Nome**, insira **myBastionIP**. </br> Selecione **OK**. |

12. Selecione a guia **Revisar + criar** ou o botão **Revisar + criar**.

13. Selecione **Criar**.

## <a name="nat-gateway"></a>Gateway da NAT

Você pode usar um ou mais recursos de endereço IP público, prefixos IP público ou ambos. Adicionaremos um recurso de IP público e um recurso de gateway da NAT.

1. No lado superior esquerdo da tela, clique em **Criar um recurso > Rede > gateway da NAT** ou pesquise a opção **gateway da NAT** na caixa de pesquisa.

2. Selecione **Criar**. 

3. Em **Criar gateway da NAT (conversão de endereços de rede)** , insira ou selecione as seguintes informações na guia **Básico**:

    | **Configuração**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscription     | Selecione sua assinatura do Azure.                                  |
    | Grupo de recursos   | Selecione **myResourceGroupNAT**. |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Insira a opção **myNATgateway**                                    |
    | Região           | Selecione **Oeste da Europa**  |
    | Zona de disponibilidade | Selecione **Nenhum**. |
    | Tempo limite de ociosidade (minutos) | Insira **10**. |

4. Selecione a guia **IP de saída** ou clique no botão **Avançar: IP de saída** na parte inferior da página.

5. Na guia **IP de saída**, insira ou selecione as seguintes informações:

    | **Configuração** | **Valor** |
    | ----------- | --------- |
    | Endereços IP públicos | Clique em **Criar um endereço IP público**. </br> Em **Nome**, insira a opção **myPublicIP**. </br> Selecione **OK**. |

6. Selecione a guia **Sub-rede** ou clique no botão **Avançar: sub-rede** na parte inferior da página.

7. Na guia **Sub-rede**, selecione a opção **myVNet** no menu suspenso **Rede virtual**.

8. Marque a caixa ao lado de **mySubnet**.

9. Selecione a guia **Examinar + criar** ou clique no botão azul **Examinar + criar** na parte inferior da página.

10. Selecione **Criar**.
    
## <a name="virtual-machine"></a>Máquina virtual

Nesta seção, você criará uma máquina virtual para testar um gateway da NAT, bem como verificará o endereço IP público da conexão de saída.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Máquina virtual**. 

2. Na página **Criar máquina virtual**, insira ou selecione as seguintes informações na guia **Básico**:

    | **Configuração** | **Valor** |
    | ----------- | --------- |
    | **Detalhes do projeto** |   |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroupNAT**. |
    | **Detalhes da instância** |   |
    | Nome da máquina virtual | Insira a opção **myVM**. |
    | Região | Selecione **Oeste da Europa**. |
    | Opções de disponibilidade | Deixe o padrão na opção nenhuma redundância é necessária. |
    | Imagem | Selecione **Windows Server 2019 Datacenter – Gen1**. |
    | Tamanho | Selecione **Standard_DS1_v2**. |
    | **Conta de administrador** |   |
    | Nome de Usuário | Insira um nome de usuário para a máquina virtual. |
    | Senha | Digite uma senha. |
    | Confirmar senha | Confirme a senha. |
    | **Regras de porta de entrada** |    |
    | Porta de entrada públicas | Selecione **Nenhum**. |

3. Selecione a guia **Discos** ou clique no botão **Avançar: discos** na parte inferior da página.

4. Deixe o padrão na guia **Discos**.

5. Selecione a guia **Rede** ou clique no botão **Avançar: rede** na parte inferior da página.

6. Na guia **Rede**, insira ou selecione as seguintes informações:

    | **Configuração** | **Valor** |
    | ----------- | --------- |
    | **Interface de rede** |   |
    | Rede virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **mySubnet**. |
    | IP público | Selecione **Nenhum**. |
    | Grupo de segurança de rede da NIC | Selecione **Básico**. |
    | Porta de entrada públicas | Selecione **Nenhum**. |

7. Selecione a guia **Examinar + criar** ou clique no botão azul **Examinar + criar** na parte inferior da página.

8. Selecione **Criar**.

## <a name="test-nat-gateway"></a>Testar um gateway da NAT

Nesta seção, testaremos um gateway da NAT. Primeiro, descobriremos o IP público do gateway da NAT. Depois nos conectaremos à máquina virtual de teste e verificaremos a conexão de saída por meio do gateway da NAT.
    
1. Localize o endereço IP público do gateway da NAT na tela **Visão Geral**. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myPublicIP**.

2. Anote o endereço IP público:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Descobrir o endereço IP público do gateway da NAT" border="true":::

3. Selecione **Todos os serviços** no menu esquerdo, depois **Todos os recursos**. Além disso, na lista de recursos, selecione a opção **myVM** que está localizada no grupo de recursos **myResourceGroupNAT**.

4. Na página **Visão Geral**, selecione **Conectar** e **Bastion**.

5. Selecione o botão azul **Usar Bastion**.

6. Insira o nome de usuário e a senha fornecidos durante a criação da VM.

7. Abra o **Internet Explorer** em **myTestVM**.

8. Digite **https://whatsmyip.com** na barra de endereços.

9. Verifique se o endereço IP exibido corresponde ao endereço do gateway da NAT anotado na etapa anterior:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="O Internet Explorer mostrando um IP de saída externo" border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

Caso não pretenda continuar usando esse aplicativo, exclua a rede virtual, a máquina virtual e o gateway da NAT usando as seguintes etapas:

1. No menu esquerdo, selecione **Grupos de recursos**.

2. Selecione o grupo de recursos **myResourceGroupNAT**.

3. Selecione **Excluir grupo de recursos**.

4. Insira a opção **myResourceGroupNAT** e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a NAT da Rede Virtual do Azure, confira:
> [!div class="nextstepaction"]
> [Visão geral da NAT da Rede Virtual](nat-overview.md)
