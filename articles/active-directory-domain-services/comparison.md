---
title: Como decidir se os Azure AD Domain Services são adequados ao seu caso de uso
description: Comparação dos Azure Active Directory Domain Services com controladores de domínio DIY
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 165249d5-e0e7-4ed1-aa26-91a05a87bdc9
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 347dbff6ee8fb43c7f59dc5b9bec31e4381570b7
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473892"
---
# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Como decidir se os Azure AD Domain Services são adequados ao seu caso de uso

Com o Azure AD Domain Services, você pode implantar suas cargas de trabalho nos Serviços de Infraestrutura do Azure sem precisar se preocupar em manter a infraestrutura de identidade no Azure. Esse serviço gerenciado é diferente de uma implantação típica do Windows Server Active Directory que você implanta e administra sozinho. O serviço é fácil de implantar e oferece monitoramento de integridade e correção automatizados. Estamos constantemente evoluindo o serviço a fim de adicionar suporte para cenários comuns de implantação.

Para decidir se deseja usar o Azure AD Domain Services, recomendamos que você leia o material a seguir:

* Veja a lista de [funcionalidades oferecidas pelos Azure AD Domain Services](active-directory-ds-features.md).
* Examine os [Cenários de implantação comuns dos Azure AD Domain Services](scenarios.md).
* Por fim, [compare os Azure AD Domain Services a uma opção de AD do tipo faça você mesmo](comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).

## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Compare os Azure AD Domain Services ao domínio do AD DIY no Azure

A tabela a seguir ajuda você a decidir entre usar os Azure AD Domain Services e gerenciar sua própria infraestrutura do Azure AD.

| **Recurso** | **Azure AD Domain Services** | **AD "faça você mesmo" em VMs do Azure** |
| --- |:---:|:---:|
| [**Serviço gerenciado**](comparison.md#managed-service) |**&#x2713;** |**&#x2715;** |
| [**Implantações seguras**](comparison.md#secure-deployments) |**&#x2713;** |O administrador precisa proteger a implantação. |
| [**Servidor DNS**](comparison.md#dns-server) |**&#x2713;** (serviço gerenciado) |**&#x2713;** |
| [**Domain or Enterprise administrator privileges**](comparison.md#domain-or-enterprise-administrator-privileges) |**&#x2715;** |**&#x2713;** |
| [**Ingresso no domínio**](comparison.md#domain-join) |**&#x2713;** |**&#x2713;** |
| [**Autenticação de domínio usando Kerberos e NTLM**](comparison.md#domain-authentication-using-ntlm-and-kerberos) |**&#x2713;** |**&#x2713;** |
| [**Delegação restrita de Kerberos**](comparison.md#kerberos-constrained-delegation)|baseado em recursos|baseado em recursos e em conta|
| [**Estrutura de UO personalizada**](comparison.md#custom-ou-structure) |**&#x2713;** |**&#x2713;** |
| [**Extensões de esquema**](comparison.md#schema-extensions) |**&#x2715;** |**&#x2713;** |
| [**Relações de confiança de floresta/domínio do AD**](comparison.md#ad-domain-or-forest-trusts) |**&#x2715;** |**&#x2713;** |
| [**LDAP read**](comparison.md#ldap-read) |**&#x2713;** |**&#x2713;** |
| [**LDAP Seguro (LDAPS)** ](comparison.md#secure-ldap) |**&#x2713;** |**&#x2713;** |
| [**LDAP write**](comparison.md#ldap-write) |**&#x2715;** |**&#x2713;** |
| [**Group Policy**](comparison.md#group-policy) |**&#x2713;** |**&#x2713;** |
| [**Implantações com distribuição geográfica**](comparison.md#geo-dispersed-deployments) |**&#x2715;** |**&#x2713;** |

#### <a name="managed-service"></a>Serviço gerenciado

Os domínios dos Azure AD Domain Services são gerenciados pela Microsoft. Você não precisa se preocupar com a aplicação de patches, atualizações, monitoramento, backups e em garantir a disponibilidade de seu domínio. Essas tarefas de gerenciamento são oferecidas como um serviço pelo Microsoft Azure para seus domínios gerenciados.

#### <a name="secure-deployments"></a>Implantações seguras

O domínio gerenciado é bloqueado com segurança, de acordo com as recomendações de segurança da Microsoft para implantações do AD. Essas recomendações derivam de décadas de experiência da equipe de produto do AD com engenharia e suporte a implantações do AD. Para implantações do tipo faça você mesmo, é necessário executar etapas de implantação específicas para bloquear/proteger sua implantação.

#### <a name="dns-server"></a>Servidor DNS

Um domínio gerenciado dos Azure AD Domain Services inclui serviços de DNS gerenciados. Os membros do grupo "Administradores de controlador de domínio AAD" podem gerenciar o DNS no domínio gerenciado. Os membros desse grupo recebem privilégios totais de Administração do DNS no domínio gerenciado. O gerenciamento de DNS pode ser executado usando o "Console de administração do DNS" incluído no pacote RSAT (Ferramentas de administração de servidor remoto).
[Mais informações](manage-dns.md)

#### <a name="domain-or-enterprise-administrator-privileges"></a>Privilégios de administrador corporativo ou de domínio

Esses privilégios elevados não são oferecidos em um domínio gerenciado de DS AAD. Aplicativos que exigem esses privilégios elevados não podem ser implantados em domínios gerenciados do AAD DS. Um subconjunto menor de privilégios administrativos está disponível para os membros do grupo de administração delegada chamado "Administradores de DC AAD". Esses privilégios incluem privilégios de configuração do DNS, configuração de política de grupo, obtenção de privilégios de administrador em computadores que ingressaram em domínio etc.

#### <a name="domain-join"></a>Ingresso no domínio

Você pode ingressar máquinas virtuais no domínio gerenciado de uma maneira parecida com o ingresso de computadores a um domínio do AD.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Autenticação de domínio usando Kerberos e NTLM

Com os Azure AD Domain Services, você pode usar suas credenciais corporativas para autenticar com o domínio gerenciado. As credenciais são mantidas em sincronia com seu locatário do Azure AD. Para locatários sincronizados, o Azure AD Connect garante que as alterações feitas nas credenciais localmente sejam sincronizadas com o Azure AD. Com uma configuração de domínio do tipo faça você mesmo, talvez seja necessário configurar uma relação de confiança de domínio do AD com seu AD local para os usuários se autenticarem usando suas credenciais corporativas. Como alternativa, talvez seja necessário configurar a replicação do AD a fim de garantir que as senhas de usuário sejam sincronizadas com suas máquinas de virtuais do controlador de domínio do Azure.

#### <a name="kerberos-constrained-delegation"></a>Delegação restrita de Kerberos

Você não tem privilégios de "Administrador do Domínio" em um domínio gerenciado pelo Active Directory Domain Services. Portanto, você não pode configurar a delegação restrita do Kerberos baseada em conta (tradicional). No entanto, é possível configurar a delegação restrita baseada em recursos mais segura.
[Mais informações](deploy-kcd.md)

#### <a name="custom-ou-structure"></a>Estrutura de UO personalizada

Os membros do grupo "Administradores de controlador de domínio do AAD" podem criar UOs personalizadas dentro do domínio gerenciado. Usuários que criam unidades organizacionais personalizadas recebem privilégios administrativos totais sobre a unidade organizacional.
[Mais informações](create-ou.md)

#### <a name="schema-extensions"></a>Extensões de esquema

Você não pode estender o esquema de base de um domínio gerenciado dos Azure AD Domain Services. Portanto, os aplicativos que dependem de extensões ao esquema do AD (por exemplo, novos atributos no objeto do usuário) não podem ser deslocados para domínios do AAD-DS.

#### <a name="ad-domain-or-forest-trusts"></a>Relações de confiança de floresta ou domínio do AD

Os domínios gerenciados não podem ser configurados para estabelecer relações de confiança com outros domínios (entrada/saída). Portanto, cenários de implantação de florestas de recursos não podem usar o Azure AD Domain Services. De forma semelhante, implantações em que você preferir não sincronizar senhas para o Azure AD não poderão usar o Azure AD Domain Services.

#### <a name="ldap-read"></a>Leitura LDAP

O domínio gerenciado oferece suporte a cargas de trabalho de leitura de LDAP. Portanto, você pode implantar aplicativos que realizam operações de leitura LDAP no domínio gerenciado.

#### <a name="secure-ldap"></a>LDAP seguro

Você pode configurar os Azure AD Domain Services para fornecer acesso seguro do LDAP ao seu domínio gerenciado, incluindo pela internet.
[Mais informações](configure-ldaps.md)

#### <a name="ldap-write"></a>Gravação LDAP

O domínio gerenciado é somente leitura para objetos de usuário. Portanto, os aplicativos que executam operações de gravação LDAP em atributos do objeto do usuário não funcionam em um domínio gerenciado. Além disso, as senhas de usuário não podem ser alteradas dentro do domínio gerenciado. Outro exemplo seria a modificação das associações de grupo ou atributos de grupo dentro do domínio gerenciado, o que não é permitido. No entanto, quaisquer alterações em atributos de usuário ou senhas feitas no Azure AD (por meio do Portal do Azure/PowerShell) ou no AD local são sincronizadas com o domínio gerenciado do AAD-DS.

#### <a name="group-policy"></a>Política de grupo

Há um GPO interno para os contêineres "Computadores AADDC" e "Usuários AADDC". É possível personalizar esses GPOs internos para configurar a política de grupo. Membros do grupo "Administradores do DC do AAD" também podem criar GPOs personalizados e vinculá-los a unidades organizacionais existentes (incluindo unidades organizacionais personalizadas).
[Mais informações](manage-group-policy.md)

#### <a name="geo-dispersed-deployments"></a>Implantações geograficamente dispersas

Os domínios gerenciados dos Azure AD Domain Services estão disponíveis em uma única rede virtual no Azure. Para os cenários que exigem a disponibilidade de controladores de domínio em várias regiões do Azure no mundo todo, configurar controladores de domínio em VMs IaaS do Azure pode ser a melhor alternativa.

## <a name="do-it-yourself-diy-ad-deployment-options"></a>Opções de implantação de AD do tipo DIY (faça você mesmo)

Você pode ter casos de uso de implantação nos quais precisa de alguns dos recursos oferecidos por uma instalação de AD do Windows Server. Nesses casos, considere uma das seguintes opções de DIY (faça você mesmo):

* **Domínio de nuvem autônomo:** Você pode configurar um “domínio de nuvem” autônomo usando máquinas virtuais do Azure que foram configuradas como controladores de domínio. Essa infraestrutura não se integra ao seu ambiente local do AD. Essa opção exigiria um conjunto diferente de "credenciais de nuvem" para fazer logon/administrar VMs na nuvem.
* **Implantação de floresta de recursos:** Você pode configurar um domínio na topologia de floresta de recursos, usando máquinas virtuais do Azure configuradas como controladores de domínio. Em seguida, você pode configurar uma relação de confiança do AD com seu ambiente local do AD. Você pode ingressar computadores em domínio (VMs do Azure) nessa floresta de recursos na nuvem. A autenticação do usuário acontece em um uma conexão de VPN/ExpressRoute para seu diretório local.
* **Estender seu domínio local para o Azure:** Você pode conectar uma rede virtual do Azure à sua rede local usando uma conexão VPN/ExpressRoute. Essa configuração permite que VMs do Azure sejam ingressadas em seu AD local. Outra alternativa é promover uma réplica dos controladores de domínio de seu domínio local no Azure como uma VM. Você pode configurá-lo para replicar em uma conexão de VPN/ExpressRoute para seu diretório local. Esse modo de implantação estende efetivamente seu domínio local para o Azure.

> [!NOTE]
> Você pode determinar se uma opção de DIY é mais adequada para seus casos de uso de implantação. Considere [compartilhar comentários](contact-us.md) para nos ajudar a entender quais recursos ajudariam você a escolher os Azure AD Domain Services no futuro. Esses comentários nos ajudam a desenvolver o serviço a fim de atender melhor às suas necessidades de implantação e casos de uso.
>
>

Publicamos [Diretrizes para implantação do Windows Server Active Directory em máquinas virtuais do Azure](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) para facilitar as instalações DIY.

## <a name="related-content"></a>Conteúdo relacionado

* [Recursos – Azure AD Domain Services](active-directory-ds-features.md)
* [Cenários de implantação – Azure AD Domain Services](scenarios.md)
* [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100)
