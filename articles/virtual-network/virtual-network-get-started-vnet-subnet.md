---
title: Criar sua primeira rede virtual do Azure | Microsoft Docs
description: "Aprenda a criar uma VNet (rede virtual) do Azure, a conectar duas VMs (máquinas virtuais) à VNet e a se conectar às VMs."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2016
ms.author: jdial
ms.openlocfilehash: e653764d7cb514d50b44fadd0cc5963dd404d99e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-virtual-network"></a>Criar sua primeira rede virtual

Saiba como criar uma VNet (rede virtual) com duas sub-redes, como criar duas VMs (máquinas virtuais) e como conectar cada VM a uma das sub-redes, conforme mostrado na figura abaixo:

![Diagrama de rede virtual](./media/virtual-network-get-started-vnet-subnet/vnet-diagram.png)

Uma rede virtual do Azure (VNet) é uma representação da sua própria rede na nuvem. Você pode controlar as configurações de rede do Azure e definir blocos de endereço DHCP,  configurações de DNS, políticas de segurança e roteamento. Para saber mais sobre os conceitos de rede virtual, leia o artigo [Visão geral da Rede Virtual](virtual-networks-overview.md). Conclua as seguintes etapas para criar os recursos mostrados na figura:

1. [Criar uma rede virtual com duas sub-redes](#create-vnet)
2. [Criar duas VMs, cada uma com uma NIC (interface de rede)](#create-vms)e associar um NSG (grupo de segurança de rede) a cada NIC
3. [Conectar-se entre VMs](#connect-to-from-vms)
4. [Excluir todos os recursos](#delete-resources). Existem encargos para alguns dos recursos criados neste exercício enquanto eles são provisionados. Para minimizar os encargos, conclua as etapas nesta seção depois de concluir o exercício para excluir os recursos criados.

Você aprenderá as noções básicas de como usar uma rede virtual depois de concluir as etapas neste artigo. Outras etapas são fornecidas para que você saiba mais sobre como usar VNets em um nível mais profundo.

## <a name="create-vnet"></a>Criar uma rede virtual com duas sub-redes

Para criar uma rede virtual com duas sub-redes, conclua as etapas a seguir. Normalmente, são usadas sub-redes diferentes para controlar o fluxo do tráfego entre sub-redes.

1. Faça logon no [Portal do Azure](<https://portal.azure.com>). Caso ainda não tenha uma conta, você pode se inscrever para obter uma [avaliação gratuita por um mês](https://azure.microsoft.com/free). 
2. No painel **Favoritos** do portal, clique em **Novo**.
3. Na folha **Novo**, clique em **Rede**. Na folha **Rede**, clique em **Rede virtual** conforme mostrado na figura abaixo:

    ![Diagrama de rede virtual](./media/virtual-network-get-started-vnet-subnet/virtual-network.png)

4.  Na folha **Rede virtual**, deixe o modelo de implantação *Resource Manager* selecionado como modelo de implantação e clique em **Criar**.
5.  Na folha **Criar Rede virtual** surgida, digite os seguintes valores e clique em **Criar**:

    |**Configuração**|**Valor**|**Detalhes**|
    |---|---|---|
    |**Nome**|*MyVNet*|O nome deve ser exclusivo no grupo de recursos selecionado.|
    |**Espaço de endereço**|*10.0.0.0/16*|Você pode especificar qualquer espaço de endereço desejado em notação CIDR.|
    |**Nome da sub-rede**|*Front-end*|O nome da sub-rede deve ser exclusivo na rede virtual.|
    |**Intervalo de endereços da sub-rede**|*10.0.0.0/24*| O intervalo que você especificar deve existir no espaço de endereço definido para a rede virtual.|
    |**Assinatura**|*[Sua assinatura]*|Selecione uma assinatura na qual a VNet será criada. Uma VNet existe em uma única assinatura.|
    |**Grupo de recursos**|**Criar novo:** *MyRG*|Crie um grupos de recursos. O nome do grupo de recursos deve ser exclusivo na assinatura selecionada. Para saber mais sobre grupos de recursos, leia o artigo [Visão geral do Gerenciador de Recursos](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups).|
    |**Localidade**|*Oeste dos EUA*| Normalmente, o local mais próximo de sua localização física é selecionado.|

    A rede virtual demora alguns segundos para ser criada. Depois de criado, você pode ver o painel do portal do Azure.

6. Com a rede virtual criada, no painel **Favoritos** do portal do Azure, clique em **Todos os recursos**. Clique na rede virtual **MyVNet** na folha **Todos os recursos**. Se a assinatura que você selecionou já contém vários recursos, você pode inserir *MyVNet* na caixa **Filtrar por nome...** para acessar a VNet facilmente.
7. A folha **MyVNet** é aberta e exibe informações sobre a VNet, conforme mostrado na figura abaixo:

    ![Diagrama de rede virtual](./media/virtual-network-get-started-vnet-subnet/myvnet.png)

8. Conforme mostrado na figura anterior, clique em **Sub-redes** para exibir uma lista das sub-redes na VNet. A única sub-rede que existe é a **Front-end**, a sub-rede que você criou na etapa 5.
9. Na folha MyVNet - Sub-redes, clique em **+ Sub-rede** para criar uma sub-rede com as informações abaixo e clique em **OK** para criar a sub-rede:

    |**Configuração**|**Valor**|**Detalhes**|
    |---|---|---|
    |**Nome**|*Back-end*|O nome deve ser exclusivo na rede virtual.|
    |**Intervalo de endereços**|*10.0.1.0/24*|O intervalo que você especificar deve existir no espaço de endereço definido para a rede virtual.|
    |**Grupo de segurança de rede** e **Tabela de rotas**|*None* (padrão)|Os NSGs (Grupos de segurança de rede) serão abordados mais adiante neste artigo. Para saber mais sobre rotas definidas pelo usuário, leia o artigo [Rotas definidas pelo usuário](virtual-networks-udr-overview.md).|

10. Depois que a nova sub-rede é adicionada à rede virtual, você pode fechar a folha **MyVNet – Sub-redes** e fechar a folha **Todos os recursos**.

## <a name="create-vms"></a>Criar máquinas virtuais

Com a rede virtual e as sub-redes criadas, você pode criar as máquinas virtuais. Para este exercício, as duas VMs executam o sistema operacional Windows Server, embora elas possam executar qualquer sistema operacional com suporte pelo Azure, incluindo várias distribuições do Linux diferentes.

### <a name="create-web-server-vm"></a>Criar a VM do servidor Web

Para criar a VM do servidor Web, conclua as seguintes etapas:

1. No painel Favoritos do portal do Azure, clique em **Novo**, **Computação** e em **Datacenter do Windows Server 2016**.
2. Na folha **Datacenter do Windows Server 2016**, clique em **Criar**.
3. Na folha **Básico** surgida, digite ou selecione os valores abaixo e clique em **OK**:

    |**Configuração**| **Valor**|**Detalhes**|
    |---|---|---|
    |**Nome**|*MyWebServer*|Essa VM serve como um servidor Web ao qual os recursos da Internet se conectam.|
    |**Tipo de disco da VM**|*SSD*|
    |**Nome de usuário**|*Sua escolha*|
    |**Senha e Confirmar senha**|*Sua escolha*|
    | **Assinatura**|*<Your subscription>*|A assinatura deve ser a mesma especificada na etapa 5 da seção [Criar uma rede virtual com duas sub-redes](#create-vnet) neste artigo. A VNet à qual você conecta uma VM deve existir na mesma assinatura da VM.|
    |**Grupo de recursos**|**Usar existente:** selecione *MyRG*|Embora estejamos estamos usando o mesmo grupo de recursos usado para a rede virtual, os recursos não têm que existir no mesmo grupo de recursos.|
    |**Localidade**|*Oeste dos EUA*|O local deve ser o mesmo especificado na etapa 5 da seção [Criar uma rede virtual com duas sub-redes](#create-vnet) neste artigo. As VMs e as VNets a que elas se conectam devem existir no mesmo local.|

4. Na folha **Escolha um tamanho**, clique em *DS1_V2 Standard* e em **Selecionar**. Leia o artigo [Tamanhos de VM do Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter uma lista de todos os tamanhos de VM do Windows Azure com suporte.
5. Na folha **Configurações**, digite ou selecione os valores abaixo e clique em **OK**:

    |**Configuração**|**Valor**|**Detalhes**|
    |---|---|---|
    |**Armazenamento: usar discos gerenciados**|*Sim*||
    |**Rede virtual**| Selecione *MyVNet*|Você pode selecionar qualquer rede virtual que exista no mesmo local da VM que está criando. Para saber mais sobre VNets e sub-redes, leia o artigo [Rede virtual](virtual-networks-overview.md).|
    |**Sub-rede**|Selecione *Front-end*|Você pode selecionar qualquer sub-rede existente na rede virtual.|
    |**Endereço IP público**|Aceite o padrão|Um endereço IP público permite que você se conecte à VM da Internet. Para saber mais sobre endereços IP públicos, leia o artigo [Endereços IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).|
    |**Grupo de Segurança de Rede (firewall)**|Aceite o padrão|Clique no NSG padrão **MyWebServer-nsg (novo)** criado pelo portal para exibir suas configurações. Na folha **Criar grupo de segurança de rede** que é aberta, observe que ela tenha uma regra de entrada que permite o tráfego TCP/3389 (RDP) de qualquer endereço IP de origem.|
    |**Todos os outros valores**|Aceite os padrões|Para saber mais sobre as demais configurações, leia o artigo [Sobre VMs](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|

    Os NSGs (grupos de segurança de rede) permitem que você crie regras de entrada/saída para o tipo de tráfego de rede que pode entrar e sair da VM. Por padrão, todo tráfego de entrada para a VM é negado. Você pode adicionar regras de entrada adicionais de TCP/80 (HTTP) e TCP/443 (HTTPS) para um servidor Web de produção. Não há nenhuma regra para o tráfego de saída porque, por padrão, todo tráfego de saída é permitido. Você pode adicionar/remover regras para controlar o tráfego de acordo com suas políticas. Leia o artigo [Grupos de segurança de rede](virtual-networks-nsg.md) para saber mais sobre NSGs.

6.  Na folha **Resumo**, examine as configurações e clique em **OK** para criar a VM. Um bloco de status é exibido no painel do portal enquanto a VM é criada. Os dados podem levar alguns minutos para serem criados. Você não precisa aguardar a conclusão. Você pode continuar na etapa seguinte enquanto a VM é criada.

### <a name="create-database-server-vm"></a>Criar uma VM de servidor de banco de dados

Para criar a VM do servidor de banco de dados, conclua as seguintes etapas:

1.  No painel Favoritos, clique em **Novo**, **Computação** e em **Datacenter do Windows Server 2016**.
2.  Na folha **Datacenter do Windows Server 2016**, clique em **Criar**.
3.  Na folha **Básico**, digite ou selecione os valores abaixo e clique em **OK**:

    |**Configuração**|**Valor**|**Detalhes**|
    |---|---|---|
    |**Nome**|*MyDBServer*|Essa VM serve como um servidor de banco de dados ao qual se conecta o servidor Web, mas ao qual a Internet não pode se conectar.|
    |**Tipo de disco da VM**|*SSD*||
    |**Nome de usuário**|Sua escolha||
    |**Senha e Confirmar senha**|Sua escolha||
    |**Assinatura**|<Your subscription>|A assinatura deve ser a mesma especificada na Etapa 5 da seção [Criar uma rede virtual com duas sub-redes](#create-vnet) neste artigo.|
    |**Grupo de recursos**|**Usar existente:** selecione *MyRG*|Embora estejamos estamos usando o mesmo grupo de recursos usado para a rede virtual, os recursos não têm que existir no mesmo grupo de recursos.|
    |**Localidade**|*Oeste dos EUA*|O local deve ser o mesmo especificado na etapa 5 da seção [Criar uma rede virtual com duas sub-redes](#create-vnet) neste artigo.|

4.  Na folha **Escolha um tamanho**, clique em *DS1_V2 Standard* e em **Selecionar**.
5.  Na folha **Configurações**, digite ou selecione os valores abaixo e clique em **OK**:

    |**Configuração**|**Valor**|**Detalhes**|
    |----|----|---|
    |**Armazenamento: usar discos gerenciados**|*Sim*||
    |**Rede virtual**|Selecione *MyVNet*|Você pode selecionar qualquer rede virtual que exista no mesmo local da VM que está criando.|
    |**Sub-rede**|Selecione *Back-end* clicando na caixa **Sub-rede** e selecionando **Back-end** da folha **Escolher uma sub-rede**|Você pode selecionar qualquer sub-rede existente na rede virtual.|
    |**Endereço IP público**|Nenhum – Clique no endereço padrão e em **Nenhum** na folha **Escolher endereço IP público**|Sem um endereço IP público, você só pode se conectar à VM usando outra VM conectada à mesma VNet. Você não pode se conectar a ela diretamente da Internet.|
    |**Grupo de Segurança de Rede (firewall)**|Aceite o padrão| Assim como o padrão NSG criado para a VM MyWebServer, esse NSG também tem a mesma regra de entrada padrão. Você pode adicionar uma regra de entrada adicional a TCP/1433 (MS SQL) para um servidor de banco de dados. Não há nenhuma regra para o tráfego de saída porque, por padrão, todo tráfego de saída é permitido. Você pode adicionar/remover regras para controlar o tráfego de acordo com suas políticas.|
    |**Todos os outros valores**|Aceite os padrões||

6.  Na folha **Resumo**, examine as configurações e clique em **OK** para criar a VM. Um bloco de status é exibido no painel do portal enquanto a VM é criada. Os dados podem levar alguns minutos para serem criados. Você não precisa aguardar a conclusão. Você pode continuar na etapa seguinte enquanto a VM é criada.

## <a name="review"></a>Examinar os recursos

Embora você tenha criado uma VNet e duas VMs, o portal do Azure criou vários recursos adicionais no grupo de recursos MyRG. Examine o conteúdo do grupo de recursos MyRG concluindo as seguintes etapas:

1. No painel **Favoritos**, clique em **Mais serviços**.
2. No painel **Mais serviços**, digite *Grupos de recursos* na caixa que contém a palavra *Filtro*. Clique em **Grupos de recursos** quando o vir na lista filtrada.
3. No painel **Grupos de recursos**, clique no grupo de recursos *MyRG*. Se você tiver vários grupos de recursos existentes em sua assinatura, poderá digitar *MyRG* na caixa que contém o texto *Filtrar por nome...* para acessar o grupo de recursos MyRG rapidamente.
4.  Na folha **MyRG**, você verá que o grupo de recursos contém 12 recursos, conforme mostrado na figura abaixo:

    ![Conteúdo do grupo de recursos](./media/virtual-network-get-started-vnet-subnet/resource-group-contents.png)

Para saber mais sobre VMs, discos e contas de armazenamento, leia os artigos de visão geral de [Máquina Virtual](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Disco](../virtual-machines/windows/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Conta de armazenamento](../storage/common/storage-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Você pode ver os dois NSGs padrão criados para você pelo portal. Você também pode ver que o portal criou dois recursos de NIC (interface de rede). A NIC permite que uma VM se conecte a outros recursos pela rede virtual. Leia o artigo [NIC](virtual-network-network-interface.md) para saber mais sobre NICs. O portal também criou um recurso de endereço IP público. Endereços IP públicos são uma configuração para um recurso de endereço IP público. Para saber mais sobre endereços IP públicos, leia o artigo [Endereços IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).

## <a name="connect-to-from-vms"></a>Conectar-se às VMs

Com sua VNet e duas VMs criadas, agora você pode se conectar às VMs concluindo as etapas nas seções abaixo:

### <a name="connect-from-internet"></a>Conectar-se à VM do servidor Web da Internet

Para se conectar à VM do servidor Web da Internet, conclua as seguintes etapas:

1. No portal, abra o grupo de recursos MyRG concluindo as etapas na seção [Examinar recursos](#review) deste artigo.
2. Na folha **MyRG**, clique na VM **MyWebServer**.
3. Na folha **MyWebServer**, clique em **Conectar** conforme mostrado na figura abaixo:

    ![Conectar à VM do servidor Web](./media/virtual-network-get-started-vnet-subnet/webserver.png)

4. Permite que o navegador baixe e abra o arquivo *MyWebServer.rdp*.
5. Se você vir uma caixa de diálogo informando que não é possível verificar o editor de conexão remota, clique em **Conectar**.
6. Ao inserir suas credenciais, faça logon com o nome de usuário e a senha que você especificou na etapa 3 da seção [Criar a VM do servidor Web](#create-web-server-vm) deste artigo. Se a caixa **Segurança do Windows** exibida não lista as credenciais corretas, talvez seja necessário clicar em **Mais opções** e em **Usar uma conta diferente** para poder especificar o nome correto do usuário e senha. Clique em **OK** para se conectar à VM.
7. Se você vir uma caixa **Conexão de Área de Trabalho Remota** informando que não é possível verificar a identidade do computador remoto, clique em **Sim**.
8. Agora você está conectado à VM MyWebServer da Internet. Deixe a conexão de área de trabalho remota aberta para concluir as etapas na próxima seção.

A conexão remota é para o endereço IP público atribuído ao recurso de endereço IP público criado pelo portal na etapa 5 da seção [Criar uma rede virtual com duas sub-redes](#create-vnet) neste artigo. A conexão é permitida porque a regra padrão criada no NSG **MyWebServer nsg** permite entrada TCP/3389 (RDP) para a VM de qualquer endereço IP de origem. Se você tentar se conectar à VM em qualquer outra porta, a conexão falhará se você não adicionar regras de entrada adicionais ao NSG permitindo as portas adicionais.

>[!NOTE]
>Se você adicionar mais regras de entrada para o NSG, verifique se as mesmas portas estão abertas no firewall do Windows ou a conexão falhará.
>

### <a name="connect-to-internet"></a>Conectar-se à Internet da VM do servidor Web

Para conectar saída para a Internet da VM do servidor Web, conclua as seguintes etapas:

1. Se você ainda não tem uma conexão remota para abrir o MyWebServerVM, faça uma conexão remota para a VM concluindo as etapas na seção [Conectar-se à VM do servidor Web da Internet](#connect-from-internet) deste artigo.
2. Na área de trabalho do Windows, abra o Internet Explorer. Na caixa de diálogo **Instalar o Internet Explorer 11**, clique em **Não usar as configurações recomendadas** e clique em **OK**. Recomenda-se aceitar as configurações recomendadas para um servidor de produção.
3. Na barra de endereços do Internet Explorer, digite [bing.com](http:www.bing.com). Se você vir uma caixa de diálogo do Internet Explorer, clique em **Adicionar**, em **Adicionar** na caixa de diálogo **Sites confiáveis** e clique em **Fechar**. Repita esse processo para outras caixas de diálogo do Internet Explorer.
4. Na página de pesquisa do Bing, digite *qualémeuendereçoip* e clique no botão de lupa. O Bing retorna o endereço IP público atribuído ao recurso de endereço IP público criado pelo portal na criação da VM. Se você examinar as configurações do recurso **MyWebServer-ip**, verá o mesmo endereço IP atribuído ao recurso de endereço IP público, conforme mostrado na imagem a seguir. O endereço IP atribuído à sua VM é diferente, no entanto.

    ![Conectar à VM do servidor Web](./media/virtual-network-get-started-vnet-subnet/webserver-pip.png)

5.  Deixe a conexão de área de trabalho remota aberta para concluir as etapas na próxima seção.

Você será capaz de se conectar à Internet da VM porque toda a conexão de saída da VM é permitida por padrão. Você pode limitar a conectividade de saída adicionando regras de inclusão para o NSG aplicado à NIC, para a NIC que está conectada à sub-rede, ou para ambos.

Se a VM é colocada no estado parado (desalocado) no portal, o endereço IP público pode mudar. Se você precisar que o IP público endereço jamais se altere, poderá usar o método de alocação estático para o endereço IP em vez do método de alocação dinâmica (que é o padrão). Para saber mais sobre as diferenças entre os métodos de alocação, leia o artigo [Tipos e métodos de alocação de endereço IP](virtual-network-ip-addresses-overview-arm.md).

### <a name="webserver-to-dbserver"></a>Conectar-se à VM do servidor de banco de dados da VM do servidor Web

Para se conectar à VM do servidor de banco de dados da VM do servidor Web, conclua as seguintes etapas:

1. Se você ainda não tem uma conexão remota para abrir o MyWebServerVM, faça uma conexão remota para a VM concluindo as etapas na seção [Conectar-se à VM do servidor Web da Internet](#connect-from-internet) deste artigo.
2. Clique no botão Iniciar no canto inferior esquerdo da área de trabalho do Windows e comece a digitar *área de trabalho remota*. Quando a lista do menu Iniciar exibir **Conexão de Área de Trabalho Remota**, clique nele.
3. Na caixa de diálogo **Conexão de Área de Trabalho Remota**, digite *MyDBServer* como o nome do computador e clique em **Conectar**.
4. Insira o nome de usuário e as senhas digitadas na etapa 3 da seção [Criar a VM do servidor de banco de dados](#create-database-server-vm) deste artigo e clique em **OK**.
5. Se você vir uma caixa de diálogo informando que não é possível verificar a identidade do computador remoto, clique em **Sim**.
6. Deixe a conexão de área de trabalho remota de ambos os servidores aberta para concluir as etapas na próxima seção.

Você pode fazer a conexão com a VM do servidor de banco de dados da VM do servidor Web pelos seguintes motivos:

- As conexões de entrada TCP/3389 estão habilitadas para qualquer IP de origem no padrão NSG criado na etapa 5 da seção [Criar a VM do servidor de banco de dados](#create-database-server-vm) deste artigo.
- Você iniciou a conexão da VM do servidor Web, que está conectado à mesma VNet da VM do servidor de banco de dados. Para se conectar a uma VM que não tem um endereço IP público atribuído a ela, você deve se conectar de outra VM conectada à mesma VNet, mesmo se a VM estiver conectada a uma sub-rede diferente.
- Mesmo que as VMs estejam conectadas a sub-redes diferentes, o Azure cria as rotas padrão que permitem a conectividade entre sub-redes. Você pode substituir as rotas padrão pelas suas próprias. Leia o artigo [Rotas definidas pelo usuário](virtual-networks-udr-overview.md) para saber mais sobre o roteamento no Azure.

Se você tentar iniciar uma conexão remota com a VM do servidor de banco de dados da Internet como fez na seção [Conectar-se à VM do servidor Web da Internet](#connect-from-internet) deste artigo, verá que a opção **Conectar** fica esmaecida. A opção está esmaecida porque não há nenhum endereço IP público atribuído à VM e, portanto, as conexões de entrada para ela da Internet não são possíveis.

### <a name="connect-to-the-internet-from-the-database-server-vm"></a>Conectar-se à Internet da VM do servidor de banco de dados

Conecte a saída para a Internet da VM do servidor de banco de dados concluindo as seguintes etapas:

1. Se você ainda não tem uma conexão remota com a VM MyDBServer aberta da VM MyWebServer, conclua as etapas na seção [Conectar-se à VM do servidor de banco de dados da VM do servidor Web](#webserver-to-dbserver) deste artigo.
2. Na área de trabalho do Windows na VM MyDBServer, abra o Internet Explorer e responda às caixas de diálogo como fez nas etapas 2 e 3 da seção [Conectar-se à Internet da VM do servidor Web](#connect-to-internet) deste artigo.
3. Na barra de endereços, digite [bing.com](http:www.bing.com).
4. Clique em **Adicionar** na caixa de diálogo do Internet Explorer exibida, em **Adicionar**, em **Fechar** na caixa de diálogo **Sites Confiáveis**. Conclua essas etapas em todas as caixas de diálogo adicionais que aparecerem.
5. Na página de pesquisa do Bing, digite *qualémeuendereçoip* e clique no botão de lupa. O Bing retorna o endereço IP público atribuído atualmente à VM pela infraestrutura do Azure. 6. Feche a área de trabalho remota para a VM MyDBServer da VM MyWebServer e feche a conexão remota para a VM MyWebServer.

A conexão de saída para a Internet é permitida porque todo tráfego de saída é permitido por padrão, mesmo que um recurso de endereço IP público não esteja atribuído à VM MyDBServer. Todas as VMs, por padrão, são capazes de ter conexão de saída para a Internet, com ou sem um recurso de endereço IP público atribuído à VM. No entanto, você não é capaz de se conectar ao endereço IP público da Internet como faria para a VM MyWebServer que tem um recurso de endereço IP público atribuído.

## <a name="delete-resources"></a>Excluir todos os recursos

Para excluir todos os recursos criados neste artigo, conclua as seguintes etapas:

1. Para exibir o grupo de recursos MyRG criado neste artigo, conclua as etapas 1 a 3 da seção [Examinar recursos](#review) deste artigo. Novamente, examine os recursos no grupo de recursos. Se você criou o grupo de recursos MyRG de acordo com as etapas anteriores, verá os 12 recursos mostrados na imagem da etapa 4.
2. Na folha MyRG, clique no botão **Excluir**.
3. O portal requer que você digite o nome do grupo de recursos para confirmar se deseja excluí-lo. Se você vir recursos diferentes dos mostrados na etapa 4 da seção [Examinar recursos](#review) deste artigo, clique em **Cancelar**. Se você vir somente os 12 recursos criados como parte deste artigo, digite *MyRG* como o nome do grupo de recursos e clique em **Excluir**. A exclusão de um grupo de recursos exclui todos os recursos contidos nele e, portanto, confirme sempre o conteúdo de um grupo de recursos antes de excluí-lo. O portal exclui todos os recursos contidos no grupo de recursos e o exclui em seguida. Esse processo leva vários minutos.

## <a name="next-steps"></a>Próximas etapas

Neste exercício, você criou uma VNet e duas VMs. Você especificou configurações personalizadas durante a criação da VM e aceitou várias configurações padrão. Recomendamos que você leia os artigos abaixo antes da implantação de VNets e VMs de produção para ter certeza de que entendeu todas as configurações disponíveis:

- [Redes virtuais](virtual-networks-overview.md)
- [Endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [Interfaces de rede](virtual-network-network-interface.md)
- [Grupos de segurança de rede](virtual-networks-nsg.md)
- [Máquinas virtuais](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
