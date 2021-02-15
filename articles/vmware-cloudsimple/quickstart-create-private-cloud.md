---
title: 'Início rápido: criar uma nuvem privada'
titleSuffix: Azure VMware Solutions by CloudSimple
description: Saiba como criar e configurar uma nuvem privada com soluções do Azure VMware por CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 487308aca3231650aee3fac5ae127006649e19b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073902"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Início rápido-configurar um ambiente de nuvem privada

Neste artigo, saiba como criar uma nuvem privada do CloudSimple e configurar seu ambiente de nuvem privada.

## <a name="before-you-begin"></a>Antes de começar

Examine os [pré-requisitos de rede](cloudsimple-network-checklist.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Criar uma nuvem privada

Uma nuvem privada é uma pilha VMware isolada que dá suporte a hosts ESXi, vCenter, vSAN e NSX.

As nuvens privadas são gerenciadas por meio do portal do CloudSimple. Eles têm seu próprio servidor vCenter em seu próprio domínio de gerenciamento. A pilha é executada em nós dedicados e nós de hardware bare-metal isolados.

1. Selecione **Todos os serviços**.
2. Procure **Serviços CloudSimples**.
3. Selecione o serviço CloudSimple no qual você deseja criar sua nuvem privada.
4. Em **visão geral**, clique em **criar nuvem privada** para abrir uma nova guia do navegador para o portal do CloudSimple.  Se solicitado, entre com suas credenciais de entrada do Azure.  

    ![Criar nuvem privada do Azure](media/create-private-cloud-from-azure.png)

5. No portal do CloudSimple, forneça um nome para sua nuvem privada.
6. Selecione o **local** da sua nuvem privada.
7. Selecione o **tipo de nó**, consistente com o que você provisionou no Azure.
8. Especifique a **contagem de nós**.  Pelo menos três nós são necessários para criar uma nuvem privada.

    ![Criar nuvem privada-informações básicas](media/create-private-cloud-basic-info.png)

9. Clique em **Avançar: opções avançadas**.
10. Insira o intervalo CIDR para sub-redes vSphere/vSAN. Certifique-se de que o intervalo CIDR não se sobreponha a nenhuma das suas sub-redes do Azure locais ou outras (redes virtuais) ou com a sub-rede de gateway.

    **Opções de intervalo CIDR:** /24,/23,/22 ou/21. Um intervalo CIDR/24 dá suporte a até 26 nós, um intervalo CIDR/23 dá suporte a até 58 nós, e um intervalo CIDR/22 e/21 dá suporte a nós 64 (o número máximo de nós em uma nuvem privada).  Para saber mais e VLANs e sub-redes, consulte [visão geral de VLANs e sub-redes](cloudsimple-vlans-subnets.md).

      > [!IMPORTANT]
      > Os endereços IP no intervalo CIDR vSphere/vSAN são reservados para uso pela infraestrutura de nuvem privada.  Não use o endereço IP nesse intervalo em qualquer máquina virtual.

11. Clique em **Avançar: examinar e criar**.
12. Examine as configurações. Se você precisar alterar as configurações, clique em **anterior**.
13. Clique em **Criar**.

O processo de provisionamento de nuvem particular é iniciado.  Pode levar até duas horas para que a nuvem privada seja provisionada.

## <a name="launch-cloudsimple-portal"></a>Iniciar o portal do CloudSimple

Você pode acessar o portal do CloudSimple do portal do Azure.  O portal do CloudSimple será iniciado com suas credenciais de logon do Azure usando o SSO (Sign-On único).  O acesso ao portal do CloudSimple exige que você autorize o aplicativo de **autorização do serviço CloudSimple** .  Para obter mais informações sobre como conceder permissões, consulte [consentimento ao aplicativo de autorização do serviço CloudSimple](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application).

1. Selecione **Todos os serviços**.
2. Procure **Serviços CloudSimples**.
3. Selecione o serviço CloudSimple no qual você deseja criar sua nuvem privada.
4. Em visão geral, clique em **ir para o portal do CloudSimple** para abrir uma nova guia do navegador para o portal do CloudSimple.  Se solicitado, entre com suas credenciais de entrada do Azure.  

    ![Iniciar o portal do CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Criar VPN ponto a site

Uma conexão VPN ponto a site é a maneira mais simples de se conectar à sua nuvem privada do seu computador. Use a conexão VPN ponto a site se você estiver se conectando à nuvem privada remotamente.  Para ter acesso rápido à sua nuvem privada, siga as etapas abaixo.  O acesso à região CloudSimple da sua rede local pode ser feito usando a [VPN site a site](vpn-gateway.md) ou o [Azure ExpressRoute](on-premises-connection.md).

### <a name="create-gateway"></a>Criar gateway

1. Inicie o portal do CloudSimple e selecione **rede**.
2. Selecione **Gateway de VPN**.
3. Clique em **novo gateway de VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **configuração de gateway**, especifique as seguintes configurações e clique em **Avançar**.

    * Selecione **VPN ponto a site** como o tipo de gateway.
    * Insira um nome para identificar o gateway.
    * Selecione o local do Azure onde o serviço CloudSimple está implantado.
    * Especifique a sub-rede do cliente para o gateway ponto a site.  Os endereços DHCP serão fornecidos dessa sub-rede quando você se conectar.

5. Para **conexão/usuário**, especifique as seguintes configurações e clique em **Avançar**.

    * Para permitir que todos os usuários atuais e futuros acessem automaticamente a nuvem privada por meio desse gateway de ponto a site, selecione **adicionar todos os usuários automaticamente**. Quando você seleciona essa opção, todos os usuários na lista de usuários são selecionados automaticamente. Você pode substituir a opção automática desmarcando usuários individuais na lista.
    * Para selecionar somente usuários individuais, clique nas caixas de seleção na lista de usuários.

6. A seção VLANs/sub-redes permite especificar VLANs de gerenciamento e de usuário/sub-redes para o gateway e as conexões.

    * As opções **adicionar automaticamente** definem a política global para esse gateway. As configurações se aplicam ao gateway atual. As configurações podem ser substituídas na área de **seleção** .
    * Selecione **Adicionar VLANs de gerenciamento/sub-redes de nuvens privadas**.
    * Para adicionar todas as VLANs/sub-redes definidas pelo usuário, clique em  **Adicionar VLANs/sub-redes definidas pelo usuário**.
    * As configurações de **selecionar** substituem as configurações globais em **adicionar automaticamente**.

7. Clique em **Avançar** para revisar as configurações. Clique nos ícones de edição para fazer alterações.
8. Clique em **criar** para criar o gateway de VPN.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Conectar-se ao CloudSimple usando a VPN ponto a site

O cliente VPN é necessário para se conectar ao CloudSimple do seu computador.  Baixe o [openvpn client](https://openvpn.net/community-downloads/) para Windows ou [viscosity](https://www.sparklabs.com/viscosity/download/) para MacOS e os X.

1. Inicie o portal do CloudSimple e selecione **rede**.
2. Selecione **Gateway de VPN**.
3. Na lista de gateways de VPN, clique no gateway de VPN ponto a site.
4. Selecione **Usuários**.
5. Clique em **baixar minha configuração de VPN**.

    ![Baixar configuração de VPN](media/download-p2s-vpn-configuration.png)

6. Importe a configuração em seu cliente VPN.

    * Instruções para [importar a configuração no cliente Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instruções para [importar a configuração no MacOS ou no os X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Conecte-se ao CloudSimple.

## <a name="create-a-vlan-for-your-workload-vms"></a>Criar uma VLAN para suas VMs de carga de trabalho

Depois de criar uma nuvem privada, crie uma VLAN na qual você implantará suas VMs de carga de trabalho/aplicativo.

1. No portal do CloudSimple, selecione **rede**.
2. Clique em **VLAN/sub-redes**.
3. Clique em **criar VLAN/sub-rede**.

    ![Criar VLAN/sub-rede](media/create-new-vlan-subnet.png)

4. Selecione a **nuvem privada** para a nova VLAN/sub-rede.
5. Selecione uma ID de VLAN na lista.  
6. Insira um nome de sub-rede para identificar a sub-rede.
7. Especifique o intervalo e a máscara de CIDR da sub-rede.  Esse intervalo não deve se sobrepor a nenhuma sub-rede existente.
8. Clique em **Enviar**.

    ![Criar detalhes de VLAN/sub-rede](media/create-new-vlan-subnet-details.png)

A VLAN/sub-rede será criada.  Agora você pode usar essa ID de VLAN para criar um grupo de portas distribuídas em sua nuvem privada do vCenter.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Conectar seu ambiente a uma rede virtual do Azure

O CloudSimple fornece um circuito de ExpressRoute para sua nuvem privada. Você pode conectar sua rede virtual no Azure ao circuito do ExpressRoute. Para obter detalhes completos sobre como configurar a conexão, siga as etapas em [conexão de rede virtual do Azure usando o ExpressRoute](./cloudsimple-azure-network-connection.md).

## <a name="sign-in-to-vcenter"></a>Entrar no vCenter

Agora você pode entrar no vCenter para configurar máquinas virtuais e políticas.

1. Para acessar o vCenter, inicie no portal do CloudSimple. Na Home Page, em **tarefas comuns**, clique em **Iniciar vSphere cliente**.  Selecione a nuvem privada e clique em **Iniciar vSphere cliente** na nuvem privada.

    ![Iniciar cliente do vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

2. Selecione seu cliente vSphere preferencial para acessar o vCenter e entrar com seu nome de usuário e senha.  Os padrões são:
    * Nome de usuário: `CloudOwner@cloudsimple.local`
    * Senha: `CloudSimple123!`

As telas do vCenter nos próximos procedimentos são do cliente vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Alterar sua senha do vCenter

O CloudSimple recomenda que você altere sua senha na primeira vez que entrar no vCenter.  
A senha que você definir deve atender aos seguintes requisitos:

* Tempo de vida máximo: a senha deve ser alterada a cada 365 dias
* Restringir reutilização: os usuários não podem reutilizar nenhuma das cinco senhas anteriores
* Comprimento: 8-20 caracteres
* Caractere especial: pelo menos um caractere especial
* Caracteres alfabéticos: pelo menos um caractere maiúsculo, A-Z e, pelo menos, um caractere minúsculo, a-z
* Números: pelo menos um caractere numérico, 0-9
* Máximo de caracteres adjacentes idênticos: três

    Exemplo: CC ou CCC é aceitável como parte da senha, mas CCCC não é.

Se você definir uma senha que não atenda aos requisitos:

* Se você usar o cliente vSphere flash, ele relatará um erro
* Se você usar o cliente HTML5, ele não relatará um erro. O cliente não aceita a alteração e a senha antiga continua a funcionar.

## <a name="access-nsx-manager"></a>Acessar o Gerenciador do NSX

O NSX Manager é implantado com uma senha padrão. 

* Nome de usuário: **administrador**
* Senha: **CloudSimple123!**

Você pode encontrar o FQDN (nome de domínio totalmente qualificado) e o endereço IP do NSX Manager no portal do CloudSimple.

1. Inicie o portal do CloudSimple e selecione **recursos**.
2. Clique na nuvem privada que você deseja usar.
3. Selecionar **rede de gerenciamento do vSphere**
4. Use o FQDN ou o endereço IP do **NSX Manager** e conecte-se usando um navegador da Web.

    ![Localizar FQDN do NSX Manager](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Criar um grupo de portas

Para criar um grupo de portas distribuídas no vSphere:

1. Siga as instruções em "adicionar um grupo de portas distribuídas" no [Guia de rede do vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Ao configurar o grupo de portas distribuídas, forneça a ID de VLAN criada em [criar uma VLAN para suas VMs de carga de trabalho](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Próximas etapas

* [Consumir VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* [Conectar-se à rede local usando o Azure ExpressRoute](on-premises-connection.md)
* [Configurar VPN site a site a partir do local](vpn-gateway.md)
