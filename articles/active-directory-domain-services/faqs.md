---
title: Perguntas frequentes — Serviços do domínio do Azure Active Directory | Microsoft Docs
description: Perguntas frequentes sobre os Serviços de Domínio do Active Directory do Azure
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 10131ad306a8a24cb5835e55a02f1b502b20bea4
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473382"
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: Perguntas frequentes (FAQs)
Esta página responde a perguntas frequentes sobre o Azure Active Directory Domain Services. Continue verificando as atualizações.

## <a name="troubleshooting-guide"></a>Guia de Solução de Problemas
Confira o [Guia de solução de problemas](troubleshoot.md) a fim de obter soluções para os problemas comuns encontrados ao configurar ou administrar os Azure AD Domain Services.

## <a name="configuration"></a>Configuração
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Posso criar vários domínios gerenciados para um único diretório do Azure AD?
Não. Você pode criar apenas um único domínio atendido pelo Azure AD Domain Services para um único diretório do Azure AD.  

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Posso habilitar os Serviços de Domínio do Azure AD em uma rede virtual do Azure Resource Manager?
Sim. O Azure AD Domain Services pode ser habilitado em uma rede virtual do Azure Resource Manager. As redes virtuais clássicas do Azure não são mais compatíveis com a criação de novos domínios gerenciados.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Posso migrar meu domínio gerenciado existente de uma rede virtual clássica para uma rede virtual do Resource Manager?
Não atualmente. A Microsoft fornecerá futuramente um mecanismo para migrar seu domínio gerenciado existente de uma rede virtual clássica para uma rede virtual do Resource Manager.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Posso habilitar o Azure AD Domain Services em uma assinatura do Azure CSP (Provedor de Soluções de Nuvem)?
Sim. Veja como habilitar [Azure Active Directory Domain Services nas assinaturas do Azure CSP](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>É possível habilitar o Azure AD Domain Services em um diretório federado do Azure AD? Eu não sincronizo hashes de senha para o Azure AD. É possível habilitar o Azure AD Domain Services nesse diretório?
Não. O Azure AD Domain Services precisa de acesso aos hashes de senha das contas de usuário, para autenticar os usuários por NTLM ou Kerberos. Em um diretório federado, os hashes de senha não são armazenados no diretório do Azure AD. Portanto, o Azure AD Domain Services não funciona nesses diretórios do Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Posso disponibilizar os Azure AD Domain Services em várias redes virtuais na minha assinatura?
O próprio serviço não oferece suporte diretamente para esse cenário. Seu domínio gerenciado está disponível somente em uma rede virtual por vez. No entanto, você pode configurar a conectividade entre várias redes virtuais para expor os Azure AD Domain Services a outras redes virtuais. Veja como você pode [conectar redes virtuais no Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Posso habilitar os Serviços de Domínio do AD do Azure usando o PowerShell?
Sim. Veja [como habilitar os Serviços de Domínio do AD do Azure usando o PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Posso habilitar os Azure AD Domain Services usando um modelo do Resource Manager?
Não, atualmente não é possível habilitar os Serviços de Domínio do Azure AD usando um modelo. Em vez disso, use o PowerShell, veja [como habilitar os Serviços de Domínio do Azure AD usando o PowerShell](powershell-create-instance.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Posso adicionar controladores de domínio a um domínio gerenciado dos Serviços de Domínio do AD do Azure?
Não. O domínio fornecido pelo Azure AD Domain Services é um domínio gerenciado. Você não precisa provisionar, configurar ou gerenciar de outra forma os controladores de domínio para este domínio. Essas atividades de gerenciamento são fornecidas como um serviço pela Microsoft. Portanto, você não pode adicionar outros controladores de domínio (leitura-gravação ou somente leitura) ao domínio gerenciado.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Os usuários convidados para o meu diretório podem usar os Azure AD Domain Services?
Não. Os usuários convidados para o seu diretório AD Azure usando o processo de convite [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) são sincronizados no seu domínio gerenciado dos Azure AD Domain Services. No entanto, as senhas para esses usuários não são armazenadas em seu diretório do Azure AD. Portanto, os Azure AD Domain Services não têm como sincronizar NTLM e hashes Kerberos para esses usuários em seu domínio gerenciado. Como resultado, esses usuários não podem fazer logon no domínio gerenciado ou inserir computadores no domínio gerenciado.

## <a name="administration-and-operations"></a>Administração e operações
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Posso conectar ao controlador de domínio para meu domínio gerenciado usando a Área de Trabalho Remota?
Não. Você não tem permissões para se conectar aos controladores de domínio do domínio gerenciado usando a Área de Trabalho Remota. Os membros do grupo “AAD DC Administrators” podem administrar o domínio gerenciado usando ferramentas de administração do AD como o ADAC (Active Directory Administration Center) ou o AD PowerShell. Essas ferramentas são instaladas usando o recurso “Ferramentas de Administração de Servidor Remoto” em um servidor Windows ingressado no domínio gerenciado.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Habilitei os Serviços de Domínio do AD do Azure. Qual conta de usuário eu posso usar para ingressar computadores no domínio para este domínio?
Os membros do grupo administrativo “Administradores de controladores de domínio do AAD” podem ingressar computadores em domínio. Além disso, os membros desse grupo recebem acesso de área de trabalho remota aos computadores que foram ingressados no domínio.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>É possível ter privilégios de administrador de domínio no domínio gerenciado fornecido pelo Azure AD Domain Services?
Não. Você não recebe privilégios administrativos no domínio gerenciado. Os privilégios de "Administrador do Domínio" e de "Administrador Corporativo" não estarão disponíveis no domínio. Membros do administrador de domínio ou os grupos de administradores empresariais no seu Active Directory local também não receberão privilégios de administrador do domínio/administrador empresarial para o domínio gerenciado.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Posso modificar associações de grupo usando o LDAP ou outras ferramentas administrativas do AD em domínios gerenciados?
Não. Associações de grupo não podem ser modificadas em domínios atendidos pelos Serviços de Domínio do AD do Azure. O mesmo se aplica a atributos de usuário. No entanto, é possível alterar associações de grupo ou atributos de usuário no AD do Azure ou em seu domínio local. Essas alterações serão sincronizadas automaticamente nos Azure AD Domain Services.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Quanto tempo demora para que as alterações que eu fiz ao meu diretório do Azure AD fiquem visíveis em meu domínio gerenciado?
As alterações feitas ao seu diretório do Azure AD usando a interface do usuário do Azure AD ou o PowerShell são sincronizadas com o domínio gerenciado. Esse processo de sincronização ocorre em segundo plano. Após a conclusão da sincronização inicial, geralmente demora cerca de 20 minutos para que as alterações feitas ao Azure AD sejam refletidas em seu domínio gerenciado.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>É possível estender o esquema do domínio gerenciado fornecido pelo Azure AD Domain Services?
Não. O esquema é administrado pela Microsoft para o domínio gerenciado. As extensões de esquema não têm suporte dos Serviços de Domínio do AD do Azure.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Posso modificar ou adicionar registros DNS em meu domínio gerenciado?
Sim. Os membros do grupo “Administradores de controladores de domínio do AAD” recebem privilégios de “Administrador de DNS” para modificar os registros DNS no domínio gerenciado. Eles podem usar o console do Gerenciador DNS em um computador que executa o Windows Server ingressado no domínio gerenciado para gerenciar o DNS. Para usar o console do Gerenciador DNS, instale as "Ferramentas de Servidor DNS", que fazem parte do recurso opcional de "Ferramentas de Administração de Servidor Remoto" no servidor. Mais informações sobre os [utilitários para administrar, monitorar e solucionar problemas de DNS](https://technet.microsoft.com/library/cc753579.aspx) estão disponíveis no TechNet.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Qual é a política de tempo de vida da senha em um domínio gerenciado?
O tempo de vida da senha padrão em um domínio gerenciado dos Azure AD Domain Services é de 90 dias. Esse tempo de vida de senha não está sincronizado com o tempo de vida de senha configurado no Azure AD. Portanto, você pode ter uma situação em que as senhas dos usuários expiram no seu domínio gerenciado, mas ainda são válidas no Azure AD. Em tais cenários, os usuários precisam alterar sua senha no Azure AD e a nova senha será sincronizada com seu domínio gerenciado. Além disso, os atributos 'password-does-not-expire' e 'user-must-change-password-at-next-logon' para contas de usuário não são sincronizados com seu domínio gerenciado.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>O Azure AD Domain Services fornece proteção de bloqueio de conta para o AD?
Sim. Cinco tentativas de senha inválida em dois minutos no domínio gerenciado fazem com que uma conta de usuário seja bloqueada por 30 minutos. Depois de 30 minutos, a conta do usuário será desbloqueada automaticamente. Tentativas de senha inválida no domínio gerenciado não bloqueiam a conta de usuário no Azure AD. A conta de usuário é bloqueada somente no domínio gerenciado do Azure AD Domain Services.

## <a name="billing-and-availability"></a>Disponibilidade e cobrança
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Os Azure AD Domain Services são um serviço pago?
Sim. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Há uma avaliação gratuita para o serviço?
Esse serviço está incluído na avaliação gratuita do Azure. Você pode se inscrever para uma [avaliação gratuita de um mês do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Posso pausar um domínio gerenciado do Azure AD Domain Services? 
Não. Depois que você habilitar um domínio gerenciado do Azure AD Domain Services, o serviço estará disponível em sua rede virtual selecionada até que você desabilite/exclua o domínio gerenciado. Não é possível pausar o serviço. A cobrança continuará por hora até que você exclua o domínio gerenciado.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>É possível fazer failover do Azure AD Domain Services para outra região para um evento de recuperação de desastre?
Não.  O Azure AD Domain Services atualmente não fornece um modelo de implantação com redundância geográfica. Ele é limitado a uma única rede virtual em uma região do Azure. Se quiser utilizar várias regiões do Azure, você precisará executar seus Controladores de Domínio do Active Directory em VMs IaaS do Azure.  As diretrizes de arquitetura podem ser encontradas [aqui](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Posso obter os Serviços de Domínio do AD do Azure como parte do EMS (Enterprise Mobility Suite)? Preciso do Azure AD Premium para usar os Serviços de Domínio do Azure AD?
Não. Os Serviços de Domínio do Azure AD é um serviço pré-pago do Azure e não faz parte do EMS. O Azure AD Domain Services pode ser usado em todas as edições do Azure AD (Free, Basic e Premium). Você será cobrado por hora, dependendo do uso.

### <a name="what-azure-regions-is-the-service-available-in"></a>Em quais regiões do Azure o serviço está disponível?
Consulte a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para conhecer as regiões do Azure nas quais os Serviços de Domínio do Azure AD estão disponíveis.
