---
title: "Solucionar problemas de grupos de segurança de rede – Portal | Microsoft Docs"
description: "Saiba como solucionar problemas de Grupos de segurança de rede no modelo de implantação do Azure Resource Manager usando o Portal do Azure."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: f01d3b43a7953697a6b03e176dace33448d95cd9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Solucionar problemas de grupos de segurança de rede usando o Portal do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

Se você configurou NSGs (grupos de segurança de rede) em sua VM (máquina virtual) e está tendo problemas de conectividade com a VM, este artigo fornece uma visão geral dos recursos de diagnóstico para NSGs a fim de ajudar a solucionar outros problemas.

Os NSGs permitem que você controle os tipos de tráfego que fluem para dentro e fora de suas VMs (máquinas virtuais). Os NSGs podem ser aplicados a sub-redes em uma VNet (rede virtual) do Azure, à NIC (adaptadores de rede) ou ambas. As regras em vigor aplicadas a uma NIC são uma agregação das regras que existem nos NSGs aplicados a uma NIC e à sub-rede à qual ela está conectada. As regras nesses NSGs podem, às vezes, entrar em conflito umas com as outras e afetar a conectividade de rede de uma VM.  

Você pode ver todas as regras de segurança em vigor em seus NSGs, conforme aplicado nas NICs da sua VM. Este artigo mostra como solucionar problemas de conectividade da VM usando essas regras no modelo de implantação do Azure Resource Manager. Se você não estiver familiarizado com conceitos de VNet e NSG, leia os artigos de visão geral de [Rede virtual](virtual-networks-overview.md) e [Grupos de segurança de rede](virtual-networks-nsg.md).

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Usando regras de segurança em vigor para solucionar problemas de fluxo de tráfego da VM
O cenário a seguir é um exemplo de um problema de conexão comum:

Uma VM denominada *VM1* faz parte de uma sub-rede denominada *Subnet1* em uma VNet denominada *WestUS-VNet1*. Falha em uma tentativa de conectar-se à VM usando o RDP por meio da porta TCP 3389. Os NSGs são aplicados tanto na NIC *VM1-NIC1* quanto na sub-rede *Subnet1*. O tráfego para a porta TCP 3389 é permitido no NSG associado ao adaptador de rede *VM1-NIC1*. No entanto, ocorre uma falha no ping do TCP para a porta 3389 da VM1.

Embora este exemplo use a porta TCP 3389, as etapas a seguir podem ser seguidas para determinar falhas de conexão de entrada e saída em qualquer porta.

### <a name="vm"></a>Exibir regras de segurança em vigor para uma máquina virtual
Siga as etapas a seguir para solucionar problemas de NSGs em uma VM:

Você pode exibir uma lista completa das regras de segurança em vigor em uma NIC, por meio da VM em si. Você também poderá adicionar, modificar e excluir tanto a NIC quanto as regras NSG da sub-rede da folha regras em vigor, se você tiver permissões para realizar essas operações.

1. Entre no Portal do Azure em https://portal.azure.com.
2. Clique em **Mais serviços** e em **Máquinas virtuais** na lista exibida.
3. Selecione uma VM para solucionar problemas na lista, e uma folha da VM com opções é exibida.
4. Clique em **Diagnosticar e resolver problemas** e selecione um problema comum. Neste exemplo, **Não é possível me conectar à minha VM do Windows** está selecionado. 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)
5. As etapas são exibidas embaixo do problema, conforme mostrado na imagem a seguir: 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)
   
    Clique em *regras efetivas do grupo de segurança* na lista de etapas recomendadas.
6. A folha **Obter regras de segurança em vigor** é exibida, conforme mostrado na imagem a seguir:
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)
   
    Observe as seguintes seções da imagem:
   
   * **Escopo:** defina a VM selecionada na etapa 3 como *VM1*.
   * **Adaptador de rede:** *VM1-NIC1* foi selecionado. Uma VM pode ter vários adaptadores de rede (NIC). Cada NIC pode ter regras de segurança em vigor únicas. Ao solucionar problemas, talvez seja necessário exibir as regras de segurança em vigor para cada NIC.
   * **NSGs associados:** os NSGs podem ser aplicados tanto à NIC quanto à sub-rede à qual a NIC está conectada. Na imagem, um NSG foi aplicado à NIC e à sub-rede na qual ela está conectada. Você pode clicar nos nomes do NSG para modificar diretamente as regras nos NSGs.
   * **Guia VM1-nsg:** a lista de regras exibidas na imagem é para o NSG aplicado à NIC. Várias regras padrão são criadas pelo Azure sempre que um NSG é criado. Não é possível remover as regras padrão, mas você pode substituí-las por regras com prioridade mais alta. Para saber mais sobre regras padrão, leia o artigo [Visão geral do NSG](virtual-networks-nsg.md#default-rules) .
   * **Coluna DESTINO:** algumas regras têm texto na coluna, enquanto outras têm prefixos de endereço. O texto é o nome de marcações padrão aplicadas à regra de segurança quando ela foi criada. As marcações são identificadores fornecidos pelo sistema que representam vários prefixos. A seleção de uma regra com uma marcação, como *AllowInternetOutBound*, lista os prefixos na folha **Prefixos de endereços** .
   * **Baixar:** a lista de regras pode ser longa. Você pode baixar um arquivo. csv das regras para análise offline clicando em **Baixar** e salvando o arquivo.
   * **AllowRDP** : essa regra possibilita conexões RDP com a VM.
7. Clique na guia **Subnet1-NSG** para exibir as regras em vigor do NSG aplicado à sub-rede, conforme mostrado na imagem a seguir: 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)
   
    Observe a regra de *entrada* **denyRDP** . As regras de entrada aplicadas na sub-rede são avaliadas antes das regras aplicadas no adaptador de rede. Como a regra negar é aplicada na sub-rede, a solicitação para se conectar à TCP 3389 falha, porque a regra permitir na NIC nunca é avaliada. 
   
    A regra *denyRDP* é a razão por que a conexão RDP está falhando. Removê-la deve resolver o problema.
   
   > [!NOTE]
   > Se a VM associada à NIC não estiver no estado de execução ou os NSGs não tiverem sido aplicados à NIC ou à sub-rede, nenhuma regra será mostrada.
   > 
   > 
8. Para editar as regras do NSG, clique em *Subnet1-NSG* na seção **NSGs associados** .
   Isso abre a folha **Subnet1-NSG** . Você pode editar as regras diretamente clicando em **Regras de segurança de entrada**.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)
9. Depois de remover a regra de entrada *denyRDP* na **Subnet1-NSG** e adicionar uma regra *allowRDP*, a lista de regras em vigor será semelhante à imagem a seguir:
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)
   
    Confirme que a porta TCP 3389 está aberta abrindo uma conexão RDP com a VM ou usando a ferramenta PsPing. Você pode saber mais sobre o PsPing lendo a [página de download do PsPing](https://technet.microsoft.com/sysinternals/psping.aspx).

### <a name="nic"></a>Exibir regras de segurança em vigor para um adaptador de rede
Se seu fluxo de tráfego da VM for afetado para uma NIC específica, você poderá ver uma lista completa das regras em vigor para a NIC no contexto de adaptadores de rede concluindo as seguintes etapas:

1. Entre no Portal do Azure em https://portal.azure.com.
2. Clique em **Mais serviços** e em **Adaptadores de rede** na lista exibida.
3. Selecione um adaptador de rede. Na imagem a seguir, uma NIC denominada *VM1-NIC1* foi selecionada.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)
   
    Observe que **Escopo** foi definido para o adaptador de rede selecionado. Para saber mais sobre as informações adicionais mostradas, leia a etapa 6 da seção **Solucionar problemas de NSGs para uma VM** deste artigo.
   
   > [!NOTE]
   > Se um NSG for removido em um adaptador de rede, o NSG da sub-rede ainda estará em vigor na NIC fornecida. Nesse caso, a saída só mostraria regras do NSG da sub-rede. As regras serão exibidas somente se a NIC estiver conectada a uma VM.
   > 
   > 
4. Você pode editar diretamente as regras para NSGs associados a uma NIC e a uma sub-rede. Para saber como fazer isso, leia a etapa 8 da seção **Exibir regras de segurança em vigor para uma máquina virtual** deste artigo.

## <a name="nsg"></a>Exibir regras de segurança em vigor para um NSG (grupo de segurança de rede)
Ao modificar as regras do NSG, convém analisar o impacto das regras que estão sendo adicionadas em uma VM específica. Agora você pode ver uma lista completa das regras de segurança em vigor para todas as NICs às quais um NSG fornecido é aplicado, sem precisar mudar o contexto da folha de NSG fornecida. Para solucionar problemas de regras em vigor em um NSG, siga as etapas a seguir:

1. Entre no Portal do Azure em https://portal.azure.com.
2. Clique em **Mais serviços** e em **Grupos de segurança de rede** na lista exibida.
3. Selecione um NSG. Na imagem a seguir, um NSG denominado VM1-nsg foi selecionado.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)
   
    Observe as seguintes seções da imagem anterior:
   
   * **Escopo:** defina para o NSG selecionado.
   * **Máquina Virtual:** quando um NSG é aplicado a uma sub-rede, ele é aplicado a todos os adaptadores de rede conectados a todas as VMs conectadas à sub-rede. Esta lista mostra todas as VMs às quais este NSG foi aplicado. Você pode selecionar qualquer VM na lista.
     
     > [!NOTE]
     > Se um NSG for aplicado a apenas uma sub-rede vazia, as VMs não serão listadas. Se um NSG for aplicado a uma NIC não associada a uma VM, essas NICs também não serão listadas. 
     > 
     > 
   * **Adaptador de rede:** uma VM pode ter vários adaptadores de rede. Você pode selecionar um adaptador de rede conectado à VM selecionada.
   * **AssociatedNSGs:** a qualquer momento, uma NIC pode ter até dois NSGs em vigor, um aplicado à NIC e o outro, à sub-rede. Embora o escopo tenha sido selecionado como VM1-nsg, se a NIC tiver um NSG de sub-rede em vigor, a saída mostrará os dois NSGs.
4. Você pode editar diretamente as regras para NSGs associados a uma NIC ou a uma sub-rede. Para saber como fazer isso, leia a etapa 8 da seção **Exibir regras de segurança em vigor para uma máquina virtual** deste artigo.

Para saber mais sobre as outras informações mostradas, leia a etapa 6 da seção **Exibir regras de segurança em vigor para uma máquina virtual** deste artigo.

> [!NOTE]
> Embora uma sub-rede e a NIC possam ter apenas um NSG aplicado a elas, um NSG pode ser associado a várias NICs e a várias sub-redes.
> 
> 

## <a name="considerations"></a>Considerações
Considere os seguintes pontos ao solucionar problemas de conectividade:

* As regras NSG padrão bloquearão o acesso de entrada da Internet e permitirão apenas o tráfego de entrada da VNet. As regras devem ser adicionadas explicitamente para permitir acesso de entrada da Internet, conforme necessário.
* Se não houver nenhuma regra de segurança NSG que faça a conectividade de rede da VM falhar, o problema poderá ser devido a:
  * Software de firewall em execução no sistema operacional da VM
  * Rotas configuradas para soluções de virtualização ou tráfego local. O tráfego da Internet pode ser redirecionado para o local por meio do túnel forçado. Uma conexão RDP/SSH da Internet para sua VM pode não funcionar com essa configuração, dependendo de como o hardware de rede local trata esse tráfego. Leia o artigo [Solucionando problemas de rotas](virtual-network-routes-troubleshoot-powershell.md) para saber como diagnosticar problemas de rotas que podem estar impedindo o fluxo de tráfego de entrada e saída da VM. 
* Se você tiver VNets emparelhadas, por padrão, a marcação VIRTUAL_NETWORK expandirá automaticamente para incluir prefixos para VNets emparelhadas. Você pode exibir esses prefixos na lista **ExpandedAddressPrefix** , para solucionar os problemas relacionados à conectividade de emparelhamento da VNet. 
* As regras de segurança em vigor serão mostradas apenas se houver um NSG associado à NIC e/ou sub-rede da VM. 
* Se não houver nenhum NSG associado à NIC ou à sub-rede e você tiver um endereço IP público atribuído à sua VM, todas as portas estarão abertas para acesso de entrada e saída. Se a VM tem um endereço IP público, recomenda-se aplicar NSGs à NIC ou à sub-rede.

