---
title: Solução do Azure VMware por CloudSimple-use o site de nuvem privada para hospedar uma infraestrutura de área de trabalho virtual usando o VMware horizonte
description: Descreve como você pode usar seu site de nuvem privada do CloudSimple para hospedar uma infraestrutura de área de trabalho virtual usando o VMware horizonte
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 513af1ba2e354412278fb88af4ee6527c236fae3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895590"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Usar o site de nuvem privada do CloudSimple para hospedar uma infraestrutura de área de trabalho virtual usando o VMware horizonte

Você pode usar seu site de nuvem privada do CloudSimple para hospedar uma VDI (Virtual Desktop Infrastructure) usando o VMware horizonte 7. x. A figura a seguir mostra a arquitetura da solução lógica para o VDI.

![Implantação de horizonte](media/horizon-deployment.png)

Com essa solução, você tem controle total sobre o Gerenciador de exibição de horizonte e o volume do aplicativo. A interface do usuário familiar, API e interfaces CLI permitem o uso de seus scripts e ferramentas existentes.

A solução CloudSimple exige que você faça o seguinte:

* Instale, configure e gerencie o VMware horizonte 7. x em sua nuvem privada.
* Forneça suas próprias licenças de horizonte.

## <a name="deploy-the-solution"></a>Implantar a solução

As seções a seguir descrevem como implantar uma solução de VDI usando o horizonte em sua nuvem privada.

1. [Verificar se as versões do produto VMware são compatíveis](#verify-that-vmware-product-versions-are-compatible)
2. [Estimar o tamanho do seu ambiente de desktop](#estimate-the-size-of-your-desktop-environment)
3. [Criar uma nuvem privada para seu ambiente](#create-a-private-cloud-for-your-environment)
4. [Instale o VMware horizonte em sua nuvem privada](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Verificar se as versões do produto VMware são compatíveis

* Verifique se suas versões atuais e planejadas do horizonte, dos volumes de aplicativos, do Unified Access Gateway e do Gerenciador de ambiente de usuário são compatíveis entre si e com o vCenter e o PSC na nuvem privada. Para obter informações de compatibilidade, consulte [matriz de compatibilidade do VMware para o horizonte 7,5](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=).
* Para descobrir as versões atuais do vCenter e do PSC em sua nuvem privada, acesse **recursos** no [portal do CloudSimple](access-cloudsimple-portal.md), selecione sua nuvem privada e clique na guia **rede de gerenciamento do vSphere** .

![versões do vCenter e do PSC](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Estimar o tamanho do seu ambiente de desktop

* Verifique se sua configuração identificada está dentro dos limites operacionais do VMware.
* Estime os recursos necessários para todas as suas áreas de trabalho e seus componentes de gerenciamento de horizonte.

### <a name="create-a-private-cloud-for-your-environment"></a>Criar uma nuvem privada para seu ambiente

1. Crie uma nuvem privada no portal do CloudSimple seguindo as instruções em [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md).  CloudSimple cria um usuário vCenter padrão chamado ' cloudowner ' em cada nuvem privada recém-criada. Para obter detalhes sobre o usuário de nuvem privada padrão e o modelo de permissão, consulte [saiba mais sobre o modelo de permissões de nuvem privada](learn-private-cloud-permissions.md).
2. Crie uma VLAN em sua nuvem privada para o plano de gerenciamento de horizonte e atribua a ela um CIDR de sub-rede. Para obter instruções, consulte [criar e gerenciar VLANs/sub-redes](create-vlan-subnet.md). Essa é a rede onde todos os componentes da solução (gateway de acesso unificado, servidor de conexão, servidor de volume de aplicativo e servidores do Gerenciador de ambiente de usuário) serão instalados.
3. Decida se deseja usar um provedor de identidade externo com seu vCenter de nuvem privada. Em caso afirmativo, escolha uma destas opções:
    * Use seu Active Directory local como o provedor de identidade externa. Para obter instruções, consulte [fontes de identidade do vCenter](set-vcenter-identity.md).
    * Configure um servidor de Active Directory na nuvem privada na VLAN do plano de gerenciamento de horizonte para usar como seu provedor de identidade externo. Para obter instruções, consulte [fontes de identidade do vCenter](set-vcenter-identity.md).
    * Configure um servidor DHCP e DNS na VLAN de plano de gerenciamento de horizonte na nuvem privada. Para obter instruções, confira [Configurar o DNS e os aplicativos DHCP e as cargas de trabalho em sua nuvem privada do CloudSimple](dns-dhcp-setup.md).
4. Configure o encaminhamento de DNS no servidor DNS instalado na nuvem privada. Para obter instruções, consulte [criar um encaminhador condicional](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vmware-horizon-in-your-private-cloud"></a>Instale o VMware horizonte em sua nuvem privada

O diagrama de implantação a seguir ilustra uma solução de horizonte implantada em uma nuvem privada. O Unified Access Gateway, o AD/DC, o modo de exibição e o servidor de volume de aplicativo são instalados em uma VLAN 234 criada pelo usuário. O Unified Access Gateway tem um endereço IP público que pode ser acessado pela Internet. As VMs do pool de área de trabalho do horizonte são implantadas no VLAN 235 para fornecer isolamento e segurança adicionais.

![Implantação de horizonte na nuvem privada](media/horizon-private-cloud.png)

As seções a seguir descrevem as instruções para configurar uma implantação semelhante à descrita na figura. Antes de começar, verifique se você tem o seguinte:

* Uma nuvem privada criada usando o portal do CloudSimple com capacidade suficiente para executar seus pools de área de trabalho.
* Largura de banda suficiente entre o ambiente local e o ambiente de nuvem privada para dar suporte ao tráfego de rede para suas áreas de trabalho.
* Um túnel VPN site a site configurado entre seu datacenter local e a nuvem privada.
* Acessibilidade de IP de sub-redes de usuário final em seu ambiente local para as sub-redes de nuvem privada do CloudSimple.
* AD/DHCP/DNS instalados para sua nuvem privada.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>Portal do CloudSimple: criar uma VLAN/sub-rede dedicada para pools de área de trabalho

Crie uma VLAN para os pools da área de trabalho do horizonte e atribua a ele um CIDR de sub-rede. Para obter instruções, consulte [criar e gerenciar VLANs/sub-redes](create-vlan-subnet.md). Essa é a rede onde todas as máquinas virtuais de desktop serão executadas.

Siga as práticas recomendadas de segurança padrão para proteger sua implantação de horizonte:

* Permita somente tráfego de SSH/tráfego de RDP para suas VMs de área de trabalho.
* Permitir somente o tráfego de gerenciamento entre a VLAN do plano de gerenciamento de horizonte e a VLAN do pool de desktops
* Permitir somente o tráfego de gerenciamento da rede local.

Você pode impor essas práticas recomendadas Configurando [regras de firewall](firewall.md) no portal do CloudSimple.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>Portal do CloudSimple: configurar regras de firewall para proteger o plano de gerenciamento de horizonte

Configure as regras a seguir no portal do CloudSimple. Para obter instruções, consulte [configurar regras e tabelas de firewall](firewall.md).

1. Configure as regras de firewall no firewall CloudSimple N-S para permitir a comunicação entre sub-redes locais e a VLAN de gerenciamento de horizonte para que somente as portas de rede listadas na [lista de porta do horizonte](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) de documento do VMware sejam permitidas.

2. Crie regras de firewall e-W entre a VLAN de gerenciamento de horizonte e a VLAN de pool de área de trabalho na nuvem privada.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>Portal do CloudSimple: criar um endereço IP público para o gateway de acesso unificado

Crie um endereço IP público para o dispositivo de gateway de acesso unificado para habilitar conexões de cliente de desktop da Internet. Para obter instruções, consulte [alocar endereços IP públicos](public-ips.md).

Quando a instalação for concluída, o endereço IP público será atribuído e listado na página IPs públicos.

#### <a name="cloudsimple-portal-escalate-privileges"></a>Portal do CloudSimple: escalonar privilégios

O usuário ' cloudowner ' padrão não tem privilégios suficientes no vCenter da nuvem privada para instalar o horizonte, portanto, os privilégios de vCenter do usuário devem ser escalados. Para obter mais informações, consulte [escalonar privilégios](escalate-private-cloud-privileges.md).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>interface do usuário do vCenter: criar um usuário na nuvem privada para instalação do horizonte

1. Entre no vCenter usando as credenciais de usuário ' cloudowner '.
2. Crie um novo usuário, ' horizonte-soln-admin ', no vCenter e adicione o usuário ao grupo de administradores no vCenter.
3. Saia do vCenter como o usuário ' cloudowner ' e entre como o usuário ' horizonte-soln-admin '.

#### <a name="vcenter-ui-install-vmware-horizon"></a>interface do usuário do vCenter: instalar o VMware horizonte

Conforme mencionado na seção arquitetura lógica anterior, a solução de horizonte tem os seguintes componentes:

* Exibição de horizonte do VMware
* Gateway de acesso unificado VMware
* Gerenciador de volumes de aplicativos VMware
* Gerenciador de ambiente de usuário do VMware

Instale os componentes da seguinte maneira:

1. Instale e configure o gateway de acesso unificado seguindo as instruções fornecidas no documento VMware [Implantando e Configurando o VMware Unified Access Gateway](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html).

2. Instale o modo de exibição de horizonte na nuvem privada seguindo as instruções em [Exibir guia de instalação](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html).

3. Instale o Gerenciador de volume de aplicativo seguindo as instruções em [instalar e configurar volumes de aplicativos VMware](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html).

4. Instale e configure o Gerenciador de ambiente de usuário seguindo as instruções em [sobre como instalar e configurar o Gerenciador de ambiente de usuário do VMware](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html).

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Arquivar uma solicitação de suporte para carregar volumes de aplicativos predefinidos do VMware horizonte

Como parte do processo de instalação, o Gerenciador de volume de aplicativo usa volumes predefinidos para provisionar pilhas de aplicativos e volumes graváveis. Esses volumes servem como modelos para pilhas de aplicativos e volumes graváveis.

Carregar os volumes no repositório de armazenamento de nuvem privada requer a senha raiz ESXi. Para obter assistência, envie uma [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Anexe o pacote do instalador do AppVolumes para que a equipe de suporte do CloudSimple possa carregar os modelos em seu ambiente de nuvem privada.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>Portal do CloudSimple: desescalonamento de privilégios

Agora você pode [redimensionar os privilégios](escalate-private-cloud-privileges.md#de-escalate-privileges) do usuário ' cloudowner '.

## <a name="ongoing-management-of-your-horizon-solution"></a>Gerenciamento contínuo da sua solução de horizonte

Você tem controle total sobre o horizonte e o software do Gerenciador de volume de aplicativos em seu ambiente de nuvem privada e deve executar o gerenciamento de ciclo de vida de software necessário. Verifique se todas as novas versões do software são compatíveis com o vCenter e o PSC da nuvem privada antes de atualizar ou atualizar o horizonte ou o volume do aplicativo.
