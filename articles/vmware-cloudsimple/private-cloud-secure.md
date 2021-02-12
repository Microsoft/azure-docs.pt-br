---
title: Soluções do Azure VMware por CloudSimple – nuvem privada segura
description: Descreve como proteger as soluções do Azure VMware pela nuvem privada do CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5e70745cd6e2f6a2a13581052f65e014bd0d0481
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899160"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Como proteger seu ambiente de nuvem privada

Defina o RBAC (controle de acesso baseado em função) para o serviço CloudSimple, o portal do CloudSimple e a nuvem privada do Azure.  Usuários, grupos e funções para acessar o vCenter da nuvem privada são especificados usando o SSO do VMware.  

## <a name="azure-rbac-for-cloudsimple-service"></a>RBAC do Azure para serviço CloudSimple

A criação do serviço CloudSimple requer a função de **proprietário** ou **colaborador** na assinatura do Azure.  Por padrão, todos os proprietários e colaboradores podem criar um serviço CloudSimple e acessar o portal CloudSimple para criar e gerenciar nuvens privadas.  Somente um serviço CloudSimple pode ser criado por região.  Para restringir o acesso a administradores específicos, siga o procedimento abaixo.

1. Criar um serviço CloudSimple em um novo **grupo de recursos** no portal do Azure
2. Especifique o RBAC do Azure para o grupo de recursos.
3. Comprar nós e usar o mesmo grupo de recursos que o serviço CloudSimple

Somente os usuários que têm privilégios de **proprietário** ou **colaborador** no grupo de recursos verão o serviço CloudSimple e iniciarão o portal CloudSimple.

Para obter mais informações, consulte [o que é o Azure RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md).

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC para vCenter de nuvem privada

Um usuário padrão `CloudOwner@cloudsimple.local` é criado no domínio de SSO do vCenter quando uma nuvem privada é criada.  O usuário CloudOwner tem privilégios para gerenciar o vCenter. As fontes de identidade adicionais são adicionadas ao SSO do vCenter para conceder acesso a diferentes usuários.  Funções e grupos predefinidos são configurados no vCenter que pode ser usado para adicionar usuários adicionais.

### <a name="add-new-users-to-vcenter"></a>Adicionar novos usuários ao vCenter

1. [Escalonar privilégios](escalate-private-cloud-privileges.md) para o usuário **CloudOwner \@ Cloudsimple. local** na nuvem privada.
2. Entre no vCenter usando **CloudOwner \@ cloudsimple. local**
3. [Adicionar usuários de Sign-On único do vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Adicione usuários a [grupos de logon único do vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Para obter mais informações sobre funções e grupos predefinidos, consulte o artigo [modelo de permissão de nuvem privada CloudSimple do VMware vCenter](learn-private-cloud-permissions.md) .

### <a name="add-new-identity-sources"></a>Adicionar novas fontes de identidade

Você pode adicionar outros provedores de identidade para o domínio de SSO do vCenter de sua nuvem privada.  Os provedores de identidade fornecem autenticação e grupos de SSO do vCenter fornecem autorização para usuários.

* [Use Active Directory como um provedor de identidade](set-vcenter-identity.md) no vCenter de nuvem privada.
* [Usar o Azure ad como um provedor de identidade](azure-ad.md) no vCenter de nuvem privada

1. [Escalonar privilégios](escalate-private-cloud-privileges.md) para o usuário **CloudOwner \@ Cloudsimple. local** na nuvem privada.
2. Entre no vCenter usando **CloudOwner \@ cloudsimple. local**
3. Adicione usuários do provedor de identidade para [grupos de logon único do vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-private-cloud-environment"></a>Proteger a rede em seu ambiente de nuvem privada

A segurança de rede do ambiente de nuvem privada é controlada pela proteção do acesso à rede e controle do tráfego de rede entre os recursos.

### <a name="access-to-private-cloud-resources"></a>Acesso a recursos de nuvem privada

O acesso de recursos e vCenter na nuvem privada é por uma conexão de rede segura:

* **[Conexão do ExpressRoute](on-premises-connection.md)**. O ExpressRoute fornece uma conexão segura, de alta largura de banda e baixa latência do seu ambiente local.  O uso da conexão permite que seus serviços, redes e usuários locais acessem seu vCenter de nuvem privada.
* **[Gateway de VPN site a site](vpn-gateway.md)**. A VPN site a site fornece acesso aos recursos de nuvem privada local por meio de um túnel seguro.  Você especifica quais redes locais podem enviar e receber o tráfego de rede para sua nuvem privada.
* **[Gateway de VPN ponto a site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**. Use a conexão VPN ponto a site para acesso remoto rápido ao seu vCenter de nuvem privada.

### <a name="control-network-traffic-in-private-cloud"></a>Controlar o tráfego de rede na nuvem privada

As tabelas e as regras de firewall controlam o tráfego de rede na nuvem privada.  A tabela de firewall permite controlar o tráfego de rede entre uma rede de origem ou endereço IP e uma rede de destino ou endereço IP com base na combinação de regras definidas na tabela.

1. Crie uma [tabela de firewall](firewall.md#add-a-new-firewall-table).
2. [Adicione regras](firewall.md#create-a-firewall-rule) à tabela de firewall.
3. [Anexe uma tabela de firewall a uma VLAN/sub-rede](firewall.md#attach-vlans-subnet).
