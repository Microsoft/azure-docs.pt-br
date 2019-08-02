---
title: Solução de VMware do Azure por CloudSimple Quickstart - criar uma nuvem privada
description: Saiba como criar e configurar uma nuvem privada com a solução de VMware do Azure por CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 85a8840ccf6f6fe6390b5eeaccd715d87169f157
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476020"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Guia de início rápido - configurar um ambiente de nuvem privada

Neste artigo, saiba como criar uma nuvem privada de CloudSimple e configurar seu ambiente de nuvem privada.

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Criar uma nuvem privada

1. Selecione **Todos os serviços**.
2. Pesquise **CloudSimple serviços**.
3. Selecione o serviço CloudSimple no qual você deseja criar sua nuvem privada.
4. Da visão geral, clique em **criar nuvem privada** para abrir uma nova guia do navegador para o portal de CloudSimple.  Se solicitado, entre com seu Azure as credenciais de logon.  

    ![Criar a nuvem privada do Azure](media/create-private-cloud-from-azure.png)

5. No portal de CloudSimple, forneça um nome para sua nuvem privada
6. Selecione o **local** da sua nuvem privada
7. Selecione o **tipo de nó** provisionada no Azure.  Você pode escolher o [opção CS28 ou CS36](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku). A última opção inclui a capacidade máxima de memória e computação.
8. Especifique o **contagem de nós**.  Nós mínimos três são necessários para criar uma nuvem privada

    ![Criar a nuvem privada - informações básicas](media/create-private-cloud-basic-info.png)

9. Clique em **Avançar: Opções avançadas**.
10. Insira o intervalo CIDR para sub-redes vSphere/vSAN. Certifique-se de que o intervalo CIDR não coincide com qualquer uma das suas instalações ou outras sub-redes do Azure.

    ![Criar a nuvem privada - opções avançadas](media/create-private-cloud-advanced-options.png)

11. Selecione **Avançar: Examinar e criar**.
12. Examine as configurações. Se você precisar alterar quaisquer configurações, clique em **anterior**.
13. Clique em **Criar**.

Nuvem privada processo de provisionamento será iniciado.  Pode levar até duas horas para a nuvem privada a ser provisionado.

## <a name="launch-cloudsimple-portal"></a>Inicie o portal de CloudSimple

Você pode acessar o portal de CloudSimple do portal do Azure.  Portal CloudSimple será iniciado com o Azure as credenciais usando o logon único (SSO) de logon.  Acessando o portal CloudSimple exige que você autorizar a **autorização do serviço CloudSimple** aplicativo.  Para obter mais informações sobre como conceder permissões, consulte [consentir CloudSimple a autorização do serviço de aplicativo](https://docs.azure.cloudsimple.com/access-cloudsimple-portal/#consent-to-cloudsimple-service-authorization-application)

1. Selecione **Todos os serviços**.
2. Pesquise **CloudSimple serviços**.
3. Selecione o serviço CloudSimple no qual você deseja criar sua nuvem privada.
4. Da visão geral, clique em **acesse o portal CloudSimple** para abrir uma nova guia do navegador para o portal de CloudSimple.  Se solicitado, entre com seu Azure as credenciais de logon.  

    ![Inicie o Portal de CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Criar um VPN ponto a site

Uma conexão de VPN ponto a site é a maneira mais simples para se conectar à sua nuvem privada do seu computador. Use a conexão de VPN ponto a site se você estiver se conectando à nuvem privada remotamente.  Para obter acesso rápido à sua nuvem privada, siga as etapas abaixo.  O acesso ao CloudSimple região da sua rede local pode ser feito usando [VPN Site a Site](https://docs.azure.cloudsimple.com/vpn-gateway/) ou [Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/).

### <a name="create-gateway"></a>Criar gateway

1. Iniciar CloudSimple portal e selecione **rede**.
2. Selecione **Gateway de VPN**.
3. Clique em **novo Gateway de VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **configuração de Gateway**, especifique as seguintes configurações e clique em **próxima**.

    * Selecione **Point-to-Site VPN** como o tipo de gateway.
    * Insira um nome para identificar o gateway.
    * Selecione o local do Azure onde seu serviço CloudSimple é implantado.
    * Especifique a sub-rede de cliente para o gateway de point-to-site.  Endereços DHCP serão fornecidos dessa sub-rede quando você se conectar.

5. Para **Conexão/usuário**, especifique as seguintes configurações e clique em **próxima**.

    * Para permitir automaticamente a todos os usuários atuais e futuros acessar a nuvem privada por meio deste gateway point-to-site, selecione **adicionar automaticamente todos os usuários**. Quando você seleciona essa opção, todos os usuários na lista de usuários são selecionados automaticamente. Você pode substituir a opção automática, desmarcando a usuários individuais na lista.
    * Para selecionar somente a usuários individuais, clique na lista de usuários, as caixas de seleção.

6. A seção de VLANs/sub-redes permite que você especifique o gerenciamento e o usuário VLANs/sub-redes o gateway e conexões.

    * O **adicionam automaticamente** opções definir a política global para este gateway. As configurações se aplicam para o gateway atual. As configurações podem ser substituídas na **selecionar** área.
    * Selecione **adicionar o gerenciamento de VLANs/sub-redes de nuvens privadas**. 
    * Para adicionar VLANs/sub-redes tudo definido pelo usuário, clique em **adicionar VLANs/sub-redes definidas pelo usuário**. 
    * O **selecionar** configurações substituem as configurações globais sob **adicionar automaticamente**. 

7. Clique em **próxima** para examinar as configurações. Clique nos ícones de edição para fazer alterações.
8. Clique em **criar** para criar o gateway VPN.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Conectar-se ao CloudSimple usando VPN ponto a site

Cliente VPN é necessária para se conectar ao CloudSimple do seu computador.  Baixe [cliente OpenVPN](https://openvpn.net/community-downloads/) para Windows ou [Viscosidade](https://www.sparklabs.com/viscosity/download/) para macOS e nos X.

1. Iniciar CloudSimple portal e selecione **rede**.
2. Selecione **Gateway de VPN**.
3. Na lista de gateways de VPN, clique no gateway de VPN ponto a site.
4. Selecione **Usuários**.
5. Clique em **baixar minha configuração de VPN**

    ![Baixar configuração de VPN](media/download-p2s-vpn-configuration.png)

6. Importar a configuração do seu cliente VPN

    * Instruções para [Importar configuração do cliente do Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instruções para [Importar configuração no macOS ou nos X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Conectar-se ao CloudSimple

## <a name="create-a-vlan-for-your-workload-vms"></a>Criar uma VLAN de sua carga de trabalho VMs

Depois de criar uma nuvem privada, crie uma VLAN no qual você implantará sua carga de trabalho de aplicativo/VMs.

1. No Portal do CloudSimple, selecione **rede**.
2. Clique em **sub-redes/VLAN**.
3. Clique em **criar sub-rede/VLAN**

    ![Criar sub-rede/VLAN](media/create-new-vlan-subnet.png)

4. Selecione o **nuvem privada** para a nova VLAN/sub-rede.
5. Selecione uma ID de VLAN na lista.  
6. Insira um nome de sub-rede para identificar a sub-rede.
7. Especifique o intervalo de CIDR da sub-rede e a máscara.  Esse intervalo não deve sobrepor quaisquer sub-redes existentes.
8. Clique em **Enviar**.

    ![Criar detalhes de sub-rede/VLAN](media/create-new-vlan-subnet-details.png)

A sub-rede/VLAN será criada.  Agora você pode usar essa ID de VLAN para criar um grupo de portas distribuído em sua nuvem privada vCenter. 

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Conectar-se o seu ambiente para uma rede virtual do Azure

CloudSimple fornece um circuito do ExpressRoute para sua nuvem privada. Você pode conectar sua rede virtual no Azure ao circuito de ExpressRoute. Para obter detalhes completos sobre como configurar a conexão, siga as etapas em [Conexão de rede Virtual do Azure usando o ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/)

## <a name="sign-in-to-vcenter"></a>Entrar no vCenter

Agora você pode entrar vCenter para configurar máquinas virtuais e políticas.

1. Para acessar o vCenter, inicie a partir do portal CloudSimple. Na Home page, sob **tarefas comuns**, clique em **inicie o cliente vSphere**.  Selecione a nuvem privada e, em seguida, clique em **inicie o cliente vSphere** sobre a nuvem privada.

    ![Inicie o cliente vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

2. Selecione seu cliente vSphere preferencial para acessar o vCenter e entrar com seu nome de usuário e senha.  Os padrões são:
    * Nome de usuário: **CloudOwner@cloudsimple.local**
    * Senha: **CloudSimple123!**  

As telas do vCenter nos próximos procedimentos são do cliente vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Alterar sua senha do vCenter

CloudSimple recomenda que você altere sua senha a primeira vez que você entre no vCenter.  
A senha que você definir deve atender aos seguintes requisitos:

* Tempo de vida máximo: Senha deve ser alterada a cada 365 dias
* Restringir a reutilização: Os usuários não podem reutilizar qualquer uma das cinco senhas anteriores
* Tamanho: 8 - 20 caracteres
* Caractere especial: Pelo menos um caractere especial
* Caracteres alfabéticos: Pelo menos um caractere minúsculo, a – z, A-Z e pelo menos um caractere maiusculo
* Números: Pelo menos um caractere numérico, 0 a 9
* Máximo de caracteres adjacente idêntico: Três

    Exemplo: CC ou CCC é aceitável como parte da senha, mas não de CCCC.

Se você definir uma senha que não atendem aos requisitos:

* Se você usar o vSphere cliente Flash, ele relatará um erro
* Se você usar o cliente do HTML5, ele não relatar um erro. O cliente não aceita a alteração e a senha antiga continua a funcionar.

## <a name="change-nsx-administrator-password"></a>Alterar senha de administrador NSX

Gerenciador de NSX é implantado com uma senha padrão.  É recomendável que você altere a senha depois de criar sua nuvem privada.

   * Nome de usuário: **admin**
   * Senha: **CloudSimple123!**

Você pode encontrar o nome de domínio totalmente qualificado (FQDN) e o endereço IP do Gerenciador de NSX no portal de CloudSimple.

1. Iniciar CloudSimple portal e selecione **recursos**.
2. Clique na nuvem privada, o que você deseja usar.
3. Selecione **vSphere rede de gerenciamento**
4. Use o endereço IP ou FQDN de **NSX Manager** e conecte-se usando um navegador da web. 

    ![Localizar NSX Manager FQDN](media/private-cloud-nsx-manager-fqdn.png)

Para alterar a senha, siga as instruções em [gerenciar a senha do usuário](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.4/administration/GUID-DB31B304-66A5-4516-9E55-2712D12B4F27.html).

> [!WARNING]
> Por padrão, a senha do administrador NSX expirarem após 90 dias.

## <a name="create-a-port-group"></a>Criar um grupo de portas

Para criar um grupo de portas distribuído no vSphere:

1. Siga as instruções em "Adicionar um grupo de portas distribuído," a [vSphere Networking Guide](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Ao configurar o grupo de portas distribuído, forneça a ID de VLAN criado na [criar uma VLAN para suas VMs de carga de trabalho](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Próximas etapas

* [Consuma a VMs do VMware no Azure](https://docs.azure.cloudsimple.com/quickstart-create-vmware-virtual-machine)
* [Consuma a VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* [Conectar-se à rede local usando o Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Configurar VPN Site a Site no local](https://docs.azure.cloudsimple.com/vpn-gateway/)
