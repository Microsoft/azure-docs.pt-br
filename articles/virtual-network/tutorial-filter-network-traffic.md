---
title: Filtrar o tráfego de rede – Tutorial – Portal do Azure
titlesuffix: Azure Virtual Network
description: Neste tutorial, você aprenderá como filtrar o tráfego de rede para uma sub-rede, com um Grupo de Segurança de Rede, usando o portal do Azure.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: kumud
ms.openlocfilehash: cfbb499c79761e1f2014c834e65dac35fe09ef90
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057279"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Tutorial: Filtrar o tráfego de rede com um grupo de segurança de rede usando o portal do Azure

É possível usar um grupo de segurança de rede para filtrar o tráfego de entrada e saída de uma sub-rede de rede virtual.

Grupos de segurança de rede contêm regras de segurança que filtram o tráfego por endereço IP, porta e protocolo. As regras de segurança são aplicadas aos recursos implantados em uma sub-rede. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um grupo de segurança de rede e regras de segurança
> * Criar uma rede virtual e associar um grupo de segurança de rede a uma sub-rede
> * Implantar VMs (máquinas virtuais) em uma sub-rede
> * Testar filtros de tráfego

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

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

5. Selecione a guia **Examinar + criar** ou clique no botão azul **Examinar + criar** na parte inferior da página.

6. Selecione **Criar**.

## <a name="create-application-security-groups"></a>Criar grupos de segurança de aplicativos

Um grupo de segurança de aplicativos permite agrupar servidores com funções semelhantes, como servidores Web.

1. Selecione **Criar um recurso** no canto superior esquerdo do portal.

2. Na caixa de pesquisa, insira **Grupo de segurança do aplicativo**. Selecione **Grupo de segurança do aplicativo** nos resultados da pesquisa.

3. Na página **Grupo de segurança do aplicativo**, selecione **Criar**.

4. Em **Criar grupo de segurança do aplicativo**, insira ou selecione estas informações na guia **Básico**:

    | Configuração | Valor |
    | ------- | ----- |
    |**Detalhes do projeto** |  |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. |
    | **Detalhes da instância** |  |
    | Nome | Insira **myAsgWebServers**. |
    | Região | Selecione **(EUA) Leste dos EUA**. | 

5. Selecione a guia **Examinar + criar** ou clique no botão azul **Examinar + criar** na parte inferior da página.

6. Selecione **Criar**.

7. Repita a Etapa 4 novamente, especificando os seguintes valores:

    | Configuração | Valor |
    | ------- | ----- |
    |**Detalhes do projeto** |  |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. |
    | **Detalhes da instância** |  |
    | Nome | Insira **myAsgMgmtServers**. |
    | Região | Selecione **(EUA) Leste dos EUA**. |

8. Selecione a guia **Examinar + criar** ou clique no botão azul **Examinar + criar** na parte inferior da página.

9. Selecione **Criar**.

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Um grupo de segurança de rede protege o tráfego de rede na sua rede virtual.

1. Selecione **Criar um recurso** no canto superior esquerdo do portal.

2. Na caixa de pesquisa, insira **Grupo de segurança de rede**. Selecione o **Grupo de segurança de rede** nos resultados da pesquisa.

3. Na página **Grupo de segurança de rede**, selecione **Criar**.

4. Em **Criar grupo de segurança de rede**, insira ou selecione estas informações na guia **Básico**:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** |   |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. |
    | **Detalhes da instância** |   |
    | Nome | Insira **myNSG**. |
    | Local | Selecione **(EUA) Leste dos EUA**. | 

5. Selecione a guia **Examinar + criar** ou clique no botão azul **Examinar + criar** na parte inferior da página.

6. Selecione **Criar**.

## <a name="associate-network-security-group-to-subnet"></a>Associar o grupo de segurança de rede à sub-rede

Nesta seção, associaremos o grupo de segurança de rede à sub-rede da rede virtual que criamos anteriormente.

1. Na caixa **Pesquisar recursos, serviços e documentos** na parte superior do portal, comece digitando **myNsg**. Quando **myNsg** aparecer nos resultados da pesquisa, selecione-o.

2. Na página de visão geral do **myNSG**, selecione **Sub-redes** em **Configurações**.

3. Na página **Configurações**, selecione **Associar**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/associate-nsg-subnet.png" alt-text="Associar o NSG à sub-rede." border="true":::

3. Em **Associar sub-rede**, selecione **Rede virtual** e depois **myVNet**. 

4. Selecione **Sub-rede**, depois **padrão** e, por fim, **OK**.

## <a name="create-security-rules"></a>Criar regras de segurança

1. Em **Configurações** de **myNSG**, selecione **Regras de segurança de entrada**.

2. Em **Regras de segurança de entrada**, selecione **+ Adicionar**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/add-inbound-rule.png" alt-text="Adicione a regra de segurança de entrada." border="true":::

3. Criar uma regra de segurança que habilita as portas 80 e 443 ao grupo de segurança de aplicativo **myAsgWebServers**. Em **Adicionar regra de segurança de entrada**, insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Fonte | Mantenha o padrão de **Any**. |
    | Intervalos de portas de origem | Mantenha o padrão de **(*)** |
    | Destino | Selecione **Grupo de segurança do aplicativo**. |
    | Grupo de segurança do aplicativo de destino | Selecione **myAsgWebServers**. |
    | Serviço | Mantenha o padrão de **Custom**. |
    | Intervalos de portas de destino | Insira **80,443**. |
    | Protocolo | selecione **TCP**. |
    | Ação | Mantenha o padrão de **Allow**. |
    | Prioridade | Mantenha o padrão de **100**. |
    | Nome | Insira **Allow-Web-All**. |

    :::image type="content" source="./media/tutorial-filter-network-traffic/inbound-security-rule.png" alt-text="Regra de segurança de entrada." border="true":::

3. Conclua a etapa 2 novamente, usando os seguintes valores:

    | Configuração | Valor |
    | ------- | ----- |
    | Fonte | Mantenha o padrão de **Any**. |
    | Intervalos de portas de origem | Mantenha o padrão de **(*)** |
    | Destino | Selecione **Grupo de segurança do aplicativo**. |
    | Grupo de segurança do aplicativo de destino | Selecione **myAsgMgmtServers**. |
    | Serviço | Mantenha o padrão de **Custom**. |
    | Intervalos de portas de destino | Insira **3389**. |
    | Protocolo | selecione **TCP**. |
    | Ação | Mantenha o padrão de **Allow**. |
    | Prioridade | Mantenha o padrão de **110**. |
    | Nome | Insira **Allow-RDP-All**. |

    > [!CAUTION]
    > Neste artigo, o RDP (porta 3389) é exposto à Internet para a VM atribuída ao grupo de segurança do aplicativo **myAsgMgmtServers**. 
    >
    > Para ambientes de produção, em vez de expor a porta 3389 à Internet, é recomendável conectar-se aos recursos do Azure que você quer gerenciar usando uma VPN, conexão de rede privada ou Azure Bastion.
    >
    > Para obter mais informações sobre o Azure Bastion, confira [O que é o Azure Bastion?](../bastion/bastion-overview.md).

Depois de concluir as etapas 1 a 3, examine as regras que você criou. A sua lista deve ser semelhante à do seguinte exemplo:

:::image type="content" source="./media/tutorial-filter-network-traffic/security-rules.png" alt-text="Regras de segurança." border="true":::

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual.

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. Selecione **Criar um recurso** no canto superior esquerdo do portal.

2. Selecione **Computação** e **Máquina virtual**.

3. Em **Criar máquina virtual**, insira ou selecione estas informações na guia **Básico**:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** |  |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. |
    | **Detalhes da instância** |   |
    | Nome da máquina virtual | Insira **myVMWeb**. |
    | Região | Selecione **(EUA) Leste dos EUA**. |
    | Opções de disponibilidade | Deixe o padrão na opção nenhuma redundância é necessária. |
    | Imagem | Selecione **Windows Server 2019 Datacenter – Gen1**. |
    | Instância do Azure Spot | Deixe o padrão de desmarcado. |
    | Tamanho | Selecione **Standard_D2s_V3**. |
    | **Conta de administrador** |   |
    | Nome de Usuário | Digite um nome de usuário. |
    | Senha | Digite uma senha. |
    | Confirmar senha | Reinsira a senha. |
    | **Regras de porta de entrada** |   |
    | Porta de entrada públicas | Selecione **Nenhum**. |

4. Selecione a guia **Rede**.

5. Na guia **Rede**, insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Interface de rede** |   |
    | Rede virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **padrão (10.0.0.0/24)** . |
    | IP público | Deixe o padrão de um novo IP. |
    | Grupo de segurança de rede da NIC | Selecione **Nenhum**. | 

6. Selecione a guia **Examinar + criar** ou clique no botão azul **Examinar + criar** na parte inferior da página.

7. Selecione **Criar**.

### <a name="create-the-second-vm"></a>Criar a segunda VM

Conclua as Etapas de 1 a 7 novamente, mas, na Etapa 3, nomeie a VM como **myVMMgmt**. A VM demora alguns minutos para implantar. 

Não prossiga para a próxima etapa até que a VM seja implantada.

## <a name="associate-network-interfaces-to-an-asg"></a>Associar adaptadores de rede a um ASG

Quando o portal criou as VMs, ele criou e anexou uma interface de rede para cada VM. 

Adicione a interface de rede para cada VM para um dos grupos de segurança de aplicativo criados anteriormente:

1. Na caixa **Pesquisar recursos, serviços e documentos** na parte superior do portal, comece digitando **myVMWeb**. Quando a máquina virtual **myVMWeb** aparecer nos resultados da pesquisa, selecione-a.

2. Em **Configurações**, selecione **Rede**.  

3. Selecione a guia **Grupos de segurança do aplicativo** e **Configurar os grupos de segurança do aplicativo**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/configure-app-sec-groups.png" alt-text="Configure os grupos de segurança do aplicativo." border="true":::

4. Em **Configurar os grupos de segurança de aplicativo**, selecione **myAsgWebServers**. Selecione **Salvar**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/select-asgs.png" alt-text="Selecione os grupos de segurança do aplicativo." border="true":::

5. Conclua as Etapas 1 e 2 novamente, procurando a máquina virtual **myVMMgmt** e selecionando o ASG **myAsgMgmtServers**.

## <a name="test-traffic-filters"></a>Testar filtros de tráfego

1. Conecte-se à VM **myVMMgmt**. Insira **myVMMgmt** na caixa de pesquisa na parte superior do portal. Quando **myVMMgmt** aparecer nos resultados da pesquisa, selecione-o. Selecione o botão **Conectar**.

2. Selecione **Baixar Arquivo RDP**.

3. Abra o arquivo RDP baixado e selecione **Conectar**. Insira o nome de usuário e senha que você especificou ao criar a VM.

4. Selecione **OK**.

5. Você pode receber um aviso de certificado durante o processo de conexão. Caso receba o aviso, clique em **Sim** ou **Continuar** para prosseguir com a conexão.

    A conexão é bem-sucedida porque a porta 3389 tem permissão de entrada da Internet para o grupo de segurança do aplicativo **myAsgMgmtServers**. 
    
    O adaptador de rede para **myVMMgmt** está associado ao grupo de segurança do aplicativo **myAsgMgmtServers** e permite a conexão.

6. Abra uma sessão do PowerShell em **myVMMgmt**. Conecte-se ao **myVMWeb** usando o seguinte exemplo: 

    ```powershell
    mstsc /v:myVmWeb
    ```

    A conexão RDP de **myVMMgmt** para **myVMWeb** é realizada com sucesso porque as máquinas virtuais na mesma rede podem se comunicar com cada uma em qualquer porta por padrão.
    
    Não é possível criar uma conexão RDP com a máquina virtual **myVMWeb** da Internet. A regra de segurança do **myAsgWebServers** impede conexões com a porta 3389 de entrada por meio da Internet. O tráfego de entrada por meio da Internet é negado a todos os recursos por padrão.

7. Para instalar o IIS da Microsoft na máquina virtual **myVMWeb**, insira o seguinte comando por meio de uma sessão do PowerShell na máquina virtual **myVMWeb**:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Após a conclusão da instalação do IIS, desconecte-se da máquina virtual **myVMWeb**, que direciona para a conexão de área de trabalho remota da máquina virtual **myVMMgmt**.

9. Desconecte a VM **myVMMgmt**.

10. Na caixa **Pesquisar recursos, serviços e documentos** na parte superior do portal do Azure, comece digitando **myVMWeb** no seu computador. Quando **myVMWeb** apareces nos resultados da pesquisa, selecione-o. Anote o **endereço IP público** da VM. O endereço mostrado no seguinte exemplo é 23.96.39.113, mas o seu endereço é diferente:

    :::image type="content" source="./media/tutorial-filter-network-traffic/public-ip-address.png" alt-text="Endereço IP público." border="true":::
    
11. Para confirmar se é possível acessar o servidor Web **myVMWeb** na Internet, abra um navegador da Internet no seu computador e navegue até `http://<public-ip-address-from-previous-step>`. 

Você verá uma tela de boas-vindas do IIS porque a porta 80 tem permissão de entrada da Internet para o grupo de segurança do aplicativo **myAsgWebServers**. 

O adaptador de rede anexado do **myVMWeb** está associado ao grupo de segurança do aplicativo **myAsgWebServers** e permite a conexão. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos e todos os recursos que ele contém:

1. Insira **myResourceGroup** na caixa **Pesquisar** na parte superior do portal. Quando aparecer **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Insira **myResourceGroup** para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

* Foi criado um grupo de segurança de rede e ele foi associado a uma sub-rede de rede virtual. 
* Foram criados grupos de segurança do aplicativo para Web e gerenciamento.
* Crie duas máquinas virtuais.
* A filtragem de rede do grupo de segurança do aplicativo foi testada.

Para saber mais sobre grupos de segurança de rede, consulte [Visão geral do grupo de segurança de rede](./network-security-groups-overview.md) e [Gerenciar um grupo de segurança de rede](manage-network-security-group.md).

O Azure roteia o tráfego entre sub-redes por padrão. Em vez disso, é possível escolher rotear o tráfego entre sub-redes por meio de uma VM, servindo como um firewall, por exemplo. 

Para saber mais sobre como criar uma tabela de rotas, avance para o próximo tutorial.
> [!div class="nextstepaction"]
> [Criar uma tabela de rotas](./tutorial-create-route-table-portal.md)