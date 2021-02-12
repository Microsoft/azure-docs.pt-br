---
title: Filtrar o tráfego de rede – tutorial – portal do Azure
titlesuffix: Azure Virtual Network
description: Neste tutorial, você aprenderá como filtrar o tráfego de rede para uma sub-rede, com um Grupo de Segurança de Rede, usando o portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.service: virtual-network
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: kumud
ms.openlocfilehash: 97690618de5d58fa4022d01fa36a872f9d220083
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221673"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Tutorial: Filtrar o tráfego de rede com um Grupo de Segurança de Rede usando o portal do Azure

É possível filtrar o tráfego para entrada e saída de uma sub-rede de rede virtual com um grupo de segurança de rede. Grupos de segurança de rede contêm regras de segurança que filtram o tráfego por endereço IP, porta e protocolo. As regras de segurança são aplicadas aos recursos implantados em uma sub-rede. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um grupo de segurança de rede e regras de segurança
> * Criar uma rede virtual e associar um grupo de segurança de rede a uma sub-rede
> * Implantar VMs (máquinas virtuais) em uma sub-rede
> * Testar filtros de tráfego

Se preferir, você pode concluir este tutorial usando a [CLI do Azure](tutorial-filter-network-traffic-cli.md) ou o [PowerShell](tutorial-filter-network-traffic-powershell.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**. 
2. Selecione **Rede** e, sem seguida, selecione **Rede Virtual**.
3. Insira, ou selecione, as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **Criar**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Nome                    | myVirtualNetwork                                   |
    | Espaço de endereço           | 10.0.0.0/16                                        |
    | Subscription            | Selecione sua assinatura.                          |
    | Resource group          | Selecione **Criar novo** e insira *myResourceGroup*. |
    | Location                | Selecione **Leste dos EUA**.                                |
    | Sub-rede – Nome            | mySubnet                                           |
    | Sub-rede – Intervalo de endereços  | 10.0.0.0/24                                        |

## <a name="create-application-security-groups"></a>Criar grupos de segurança de aplicativos

Um grupo de segurança de aplicativos permite agrupar servidores com funções semelhantes, como servidores Web.

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**. 
2. Na caixa **Pesquisar no Marketplace**, insira *Grupo de segurança do aplicativo*. Quando **Grupo de segurança do aplicativo** for exibido nos resultados da pesquisa, selecione-o, selecione **Grupo de segurança do aplicativo** novamente em **Tudo** e, em seguida, selecione **Criar**.
3. Insira ou selecione as seguintes informações e selecione **Criar**:

    | Configuração        | Valor                                                         |
    | ---            | ---                                                           |
    | Nome           | myAsgWebServers                                               |
    | Subscription   | Selecione sua assinatura.                                     |
    | Resource group | Selecione **Usar existente** e depois **myResourceGroup**. |
    | Location       | Leste dos EUA                                                       |

4. Conclua a etapa 3 novamente, especificando os seguintes valores:

    | Configuração        | Valor                                                         |
    | ---            | ---                                                           |
    | Nome           | myAsgMgmtServers                                              |
    | Subscription   | Selecione sua assinatura.                                     |
    | Resource group | Selecione **Usar existente** e depois **myResourceGroup**. |
    | Location       | Leste dos EUA                                                       |

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**. 
2. Selecione **Rede** e **Grupo de segurança de rede**.
3. Insira ou selecione as seguintes informações e selecione **Criar**:

    |Configuração|Valor|
    |---|---|
    |Nome|myNsg|
    |Subscription| Selecione sua assinatura.|
    |Resource group | Clique em **Usar existente** e selecione *myResourceGroup*.|
    |Location|Leste dos EUA|

## <a name="associate-network-security-group-to-subnet"></a>Associar o grupo de segurança de rede à sub-rede

1. Na caixa *Pesquisar recursos, serviços e documentos* na parte superior do portal, comece digitando *myNsg*. Quando **myNsg** aparecer nos resultados da pesquisa, selecione-o.
2. Em **CONFIGURAÇÕES**, selecione **Sub-redes** e, em seguida, selecione **+ Associar**, conforme mostrado na imagem a seguir:

    ![Associar o NSG à sub-rede](./media/tutorial-filter-network-traffic/associate-nsg-subnet.png)

3. Em **Associar sub-rede**, selecione **Rede virtual** e depois **myVirtualNetwork**. Selecione **Sub-rede**, depois **mySubnet** e, por fim, **OK**.

## <a name="create-security-rules"></a>Criar regras de segurança

1. Em **CONFIGURAÇÕES**, selecione **Regras de segurança inbound** e, em seguida, selecione **+ Adicionar**, como mostrado na imagem a seguir:

    ![Adicionar uma regra de segurança de entrada](./media/tutorial-filter-network-traffic/add-inbound-rule.png)

2. Criar uma regra de segurança que habilita as portas 80 e 443 ao grupo de segurança de aplicativo **myAsgWebServers**. Em **Adicionar regra de segurança de entrada**, insira ou selecione os valores a seguir, aceite os padrões restantes e, em seguida, selecione **Adicionar**:

    | Configuração                 | Valor                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Destino             | Selecione **Grupo de segurança de aplicativo** e, em seguida, selecione **myAsgWebServers** para **Grupo de segurança de aplicativo**.  |
    | Intervalos de portas de destino | Insira 80,443                                                                                                    |
    | Protocolo                | Selecione TCP                                                                                                      |
    | Nome                    | Allow-Web-All                                                                                                   |

3. Conclua a etapa 2 novamente, usando os seguintes valores:

    | Configuração                 | Valor                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Destino             | Selecione **Grupo de segurança de aplicativo** e, em seguida, selecione **myAsgMgmtServers** para **Grupo de segurança de aplicativo**. |
    | Intervalos de portas de destino | Insira 3389                                                                                                      |
    | Protocolo                | Selecione TCP                                                                                                      |
    | Prioridade                | Insira 110                                                                                                       |
    | Nome                    | Allow-RDP-All                                                                                                   |

    Neste tutorial, o RDP (porta 3389) é exposto à internet para a VM atribuída ao grupo de segurança de aplicativo *myAsgMgmtServers*. Para ambientes de produção, em vez de expor a porta 3389 à Internet, é recomendável conectar os recursos do Azure que você quer gerenciar utilizando uma conexão de rede VPN ou privada.

Depois de concluir as etapas 1 a 3, examine as regras que você criou. Sua lista deve ser semelhante à da seguinte figura:

![Regras de segurança](./media/tutorial-filter-network-traffic/security-rules.png)

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual.

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**. 
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**.
3. Insira ou selecione as seguintes informações e aceite os padrões para as configurações restantes:

    |Configuração|Valor|
    |---|---|
    |Subscription| Selecione sua assinatura.|
    |Resource group| Selecione **Usar existente** e, em seguida, **myResourceGroup**.|
    |Nome|myVmWeb|
    |Location| Selecione **Leste dos EUA**.|
    |Nome de usuário| Insira um nome de usuário de sua escolha.|
    |Senha| Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|

   

4. Selecione um tamanho para a VM e selecione **Selecionar**.
5. Em **Rede**, selecione os seguintes valores e aceite os padrões restantes:

    |Configuração|Valor|
    |---|---|
    |Rede virtual |Selecione **myVirtualNetwork**.|
    |Grupo de segurança de rede da NIC |Selecione **Nenhum**.|
  

6. Selecione **Examinar + Criar** no canto inferior esquerdo e selecione **Criar** para iniciar a implantação da VM.

### <a name="create-the-second-vm"></a>Criar a segunda VM

Conclua as etapas de 1 a 6 novamente, mas, na etapa 3, nomeie a VM como *myVmMgmt*. A VM demora alguns minutos para implantar. Não prossiga para a próxima etapa até que a VM seja implantada.

## <a name="associate-network-interfaces-to-an-asg"></a>Associar adaptadores de rede a um ASG

Quando o portal criou as VMs, ele criou e anexou uma interface de rede para cada VM. Adicione a interface de rede para cada VM para um dos grupos de segurança de aplicativo criados anteriormente:

1. Na caixa *Pesquisar recursos, serviços e documentos* na parte superior do portal, comece digitando *myVmWeb*. Quando a VM **myVmWeb** aparecer nos resultados da pesquisa, selecione-a.
2. Em **CONFIGURAÇÕES**, selecione **Rede**.  Selecione **Configurar os grupos de segurança do aplicativo**, selecione **myAsgWebServers** para **Grupos de segurança do aplicativo** e, em seguida, selecione **Salvar**, conforme mostrado na imagem a seguir:

    ![Associar ao ASG](./media/tutorial-filter-network-traffic/associate-to-asg.png)

3. Conclua as etapas 1 e 2 novamente, procurando a VM **myVmMgmt** e selecionando o ASG **myAsgMgmtServers**.

## <a name="test-traffic-filters"></a>Testar filtros de tráfego

1. Conecte-se à VM *myVmMgmt*. Insira *myVmMgmt* na caixa de pesquisa na parte superior do portal. Quando **myVmMgmt** aparecer na caixa de resultados, selecione-o. Selecione o botão **Conectar**.
2. Selecione **Baixar Arquivo RDP**.
3. Abra o arquivo RDP baixado e selecione **Conectar**. Insira o nome de usuário e senha que você especificou ao criar a VM. Talvez seja necessário selecionar **Mais escolhas** e, em seguida, **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM.
4. Selecione **OK**.
5. Você pode receber um aviso do certificado durante o processo de logon. Se você receber o aviso, selecione **Sim** ou **Continuar**, para prosseguir com a conexão.

    É uma conexão com êxito porque a porta 3389 tem permissão de entrada da Internet para o grupo de segurança de aplicativo *myAsgMgmtServers* ao qual o adaptador de rede conectado à VM *myVmMgmt* está.

6. Conecte-se à VM *myVmWeb* a partir da VM *myVmMgmt* inserindo o seguinte comando em uma sessão do PowerShell:

    ``` 
    mstsc /v:myVmWeb
    ```

    É possível se conectar à VM myVmWeb a partir da VM myVmMgmt porque as VMs na mesma rede virtual podem se comunicar entre si em qualquer porta, por padrão. Porém, não é possível criar uma conexão de área de trabalho remota para a VM *myVmWeb* da Internet porque a regra de segurança para *myAsgWebServers* não permite a entrada da porta 3389 da Internet e o tráfego de entrada da Internet é negado a todos os recursos, por padrão.

7. Para instalar os IIS da Microsoft na VM *myVmWeb*, insira o comando a seguir a partir de uma sessão do PowerShell na VM *myVmWeb*:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Após a conclusão da instalação dos Serviços de Informações da Internet, desconecte-se da VM *myVmWeb*, que direciona para a conexão da área de trabalho remota da VM *myVmMgmt*.
9. Desconecte a VM *myVmMgmt*.
10. Na caixa *Pesquisar recursos, serviços e documentos* na parte superior do portal do Azure, comece digitando *myVmWeb* no seu computador. Quando **myVmWeb** apareces nos resultados da pesquisa, selecione-o. Anote o **endereço IP público** da VM. O endereço mostrado na imagem a seguir é 137.135.84.74, mas seu endereço é diferente:

    ![Endereço IP público](./media/tutorial-filter-network-traffic/public-ip-address.png)
  
11. Para confirmar se é possível acessar o servidor Web *myVmWeb* na Internet, abra um navegador da Internet no seu computador e navegue até `http://<public-ip-address-from-previous-step>`. Você verá uma tela de boas-vindas dos IIS porque a porta 80 tem permissão de entrada da Internet para o grupo de segurança de aplicativo *myAsgWebServers* no qual está anexado o adaptador de rede à VM *myVmWeb*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos e todos os recursos que ele contém:

1. Insira *myResourceGroup* na caixa **Pesquisar** na parte superior do portal. Quando aparecer **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Insira *myResourceGroup* para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um grupo de segurança de rede e o associou a uma sub-rede de rede virtual. Para saber mais sobre grupos de segurança de rede, consulte [Visão geral do grupo de segurança de rede](./network-security-groups-overview.md) e [Gerenciar um grupo de segurança de rede](manage-network-security-group.md).

O Azure roteia o tráfego entre sub-redes por padrão. Em vez disso, é possível escolher rotear o tráfego entre sub-redes por meio de uma VM, servindo como um firewall, por exemplo. Para saber mais sobre como criar uma tabela de rotas, avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Criar uma tabela de rotas](./tutorial-create-route-table-portal.md)