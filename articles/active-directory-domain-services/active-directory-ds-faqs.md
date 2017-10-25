---
title: "Perguntas frequentes — Serviços do domínio do Azure Active Directory | Microsoft Docs"
description: "Perguntas frequentes sobre os Serviços de Domínio do Active Directory do Azure"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: maheshu
ms.openlocfilehash: ad25663f97b83f11f14eeb8bb07dac46f6c080d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: perguntas frequentes
Esta página responde a perguntas frequentes sobre o Azure Active Directory Domain Services. Continue verificando as atualizações.

### <a name="troubleshooting-guide"></a>Guia de Solução de Problemas
Consulte nosso [Guia de solução de problemas](active-directory-ds-troubleshooting.md) a fim de obter soluções para os problemas comuns encontrados ao configurar ou administrar os Serviços de Domínio do AD do Azure.

### <a name="configuration"></a>Configuração
#### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Posso criar vários domínios gerenciados para um único diretório do Azure AD?
Não. Você pode criar apenas um único domínio atendido pelo Azure AD Domain Services para um único diretório do Azure AD.  

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Posso habilitar os Serviços de Domínio do Azure AD em uma rede virtual do Azure Resource Manager?
Sim. O Azure AD Domain Services pode ser habilitado em uma rede virtual do Azure Resource Manager. Esta funcionalidade está em preview.

#### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Posso migrar meu domínio gerenciado existente de uma rede virtual clássica para uma rede virtual do Resource Manager?
Não atualmente. Forneceremos futuramente um mecanismo para migrar seu domínio gerenciado existente de uma rede virtual clássica para uma rede virtual do Resource Manager.

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Posso habilitar o Azure AD Domain Services em uma assinatura do Azure CSP (Provedor de Soluções de Nuvem)?
Não. Estamos trabalhando para adicionar suporte para assinaturas do CSP.

#### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-use-adfs-to-authenticate-users-for-access-to-office-365-and-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>É possível habilitar o Azure AD Domain Services em um diretório federado do Azure AD? Uso o ADFS para autenticar usuários para acesso ao Office 365 e não sincronizar hashes de senha com o Azure AD. É possível habilitar o Azure AD Domain Services nesse diretório?
Não. O Azure AD Domain Services precisa de acesso aos hashes de senha das contas de usuário, para autenticar os usuários por NTLM ou Kerberos. Em um diretório federado, os hashes de senha não são armazenados no diretório do Azure AD. Portanto, o Azure AD Domain Services não funciona nesses diretórios do Azure AD.

#### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Posso disponibilizar os Azure AD Domain Services em várias redes virtuais na minha assinatura?
O próprio serviço não oferece suporte diretamente para esse cenário. Seu domínio gerenciado está disponível somente em uma rede virtual por vez. No entanto, você pode configurar a conectividade entre várias redes virtuais para expor os Azure AD Domain Services a outras redes virtuais. Veja como você pode [conectar redes virtuais no Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

#### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Posso habilitar os Serviços de Domínio do AD do Azure usando o PowerShell?
A implantação do PowerShell/automatizada dos Serviços de Domínio do AD do Azure não está disponível no momento.

#### <a name="is-azure-ad-domain-services-available-in-the-new-azure-portal"></a>Os Serviços de Domínio do AD do Azure estão disponíveis no novo portal do Azure?
Sim. O Azure AD Domain Services pode ser configurado no [Portal do Azure](https://portal.azure.com). Esperamos parar o suporte ao [Portal Clássico do Azure](https://manage.windowsazure.com) no futuro.

#### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Posso habilitar os Azure AD Domain Services usando um modelo do Resource Manager?
Não. Algumas tarefas precisam ser executadas como parte da habilitação do Azure AD Domain Services. Essas tarefas não são possíveis por meio de um modelo do Resource Manager. Use o novo portal do Azure para habilitar o Azure AD Domain Services para seu diretório.

#### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Posso adicionar controladores de domínio a um domínio gerenciado dos Serviços de Domínio do AD do Azure?
Não. O domínio fornecido pelo Azure AD Domain Services é um domínio gerenciado. Você não precisa provisionar, configurar ou gerenciar de outra forma os controladores de domínio para este domínio. Essas atividades de gerenciamento são fornecidas como um serviço pela Microsoft. Portanto, você não pode adicionar outros controladores de domínio (leitura-gravação ou somente leitura) ao domínio gerenciado.

### <a name="administration-and-operations"></a>Administração e operações
#### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Posso conectar ao controlador de domínio para meu domínio gerenciado usando a Área de Trabalho Remota?
Não. Você não tem permissões para se conectar aos controladores de domínio do domínio gerenciado usando a Área de Trabalho Remota. Os membros do grupo “AAD DC Administrators” podem administrar o domínio gerenciado usando ferramentas de administração do AD como o ADAC (Active Directory Administration Center) ou o AD PowerShell. Essas ferramentas são instaladas usando o recurso “Ferramentas de Administração de Servidor Remoto” em um servidor Windows ingressado no domínio gerenciado.

#### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Habilitei os Serviços de Domínio do AD do Azure. Qual conta de usuário eu posso usar para ingressar computadores no domínio para este domínio?
Os membros do grupo administrativo “Administradores de controladores de domínio do AAD” podem ingressar computadores em domínio. Além disso, os membros desse grupo recebem acesso de área de trabalho remota aos computadores que foram ingressados no domínio.

#### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>É possível ter privilégios de administrador de domínio no domínio gerenciado fornecido pelo Azure AD Domain Services?
Não. Você não recebe privilégios administrativos no domínio gerenciado. Os privilégios de "Administrador do Domínio" e de "Administrador Corporativo" não estarão disponíveis no domínio. O administrador de domínio existente ou os grupos de administradores corporativos no seu diretório do Azure AD também não receberão privilégios de administrador do domínio/administrador corporativo para o domínio.

#### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Posso modificar associações de grupo usando o LDAP ou outras ferramentas administrativas do AD em domínios gerenciados?
Não. Associações de grupo não podem ser modificadas em domínios atendidos pelos Serviços de Domínio do AD do Azure. O mesmo se aplica a atributos de usuário. No entanto, é possível alterar associações de grupo ou atributos de usuário no AD do Azure ou em seu domínio local. Essas alterações serão sincronizadas automaticamente nos Azure AD Domain Services.

#### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Quanto tempo demora para que as alterações que eu fiz ao meu diretório do Azure AD fiquem visíveis em meu domínio gerenciado?
As alterações feitas ao seu diretório do Azure AD usando a interface do usuário do Azure AD ou o PowerShell são sincronizadas com o domínio gerenciado. Esse processo de sincronização ocorre em segundo plano. Após a conclusão da sincronização inicial do seu diretório, que só ocorre uma vez, geralmente demora cerca de 20 minutos para que as alterações feitas ao Azure AD sejam refletidas em seu domínio gerenciado.

#### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>É possível estender o esquema do domínio gerenciado fornecido pelo Azure AD Domain Services?
Não. O esquema é administrado pela Microsoft para o domínio gerenciado. As extensões de esquema não têm suporte dos Serviços de Domínio do AD do Azure.

#### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Posso modificar ou adicionar registros DNS em meu domínio gerenciado?
Sim. Os membros do grupo “Administradores de controladores de domínio do AAD” recebem privilégios de “Administrador de DNS” para modificar os registros DNS no domínio gerenciado. Eles podem usar o console do Gerenciador DNS em um computador que executa o Windows Server ingressado no domínio gerenciado para gerenciar o DNS. Para usar o console do Gerenciador DNS, instale as "Ferramentas de Servidor DNS", que fazem parte do recurso opcional de "Ferramentas de Administração de Servidor Remoto" no servidor. Mais informações sobre os [utilitários para administrar, monitorar e solucionar problemas de DNS](https://technet.microsoft.com/library/cc753579.aspx) estão disponíveis no TechNet.

### <a name="billing-and-availability"></a>Disponibilidade e cobrança
#### <a name="is-azure-ad-domain-services-a-paid-service"></a>Os Azure AD Domain Services são um serviço pago?
Sim. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/active-directory-ds/).

#### <a name="is-there-a-free-trial-for-the-service"></a>Há uma avaliação gratuita para o serviço?
Esse serviço está incluído na avaliação gratuita do Azure. Você pode se inscrever para uma [avaliação gratuita de um mês do Azure](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Posso pausar um domínio gerenciado do Azure AD Domain Services? 
Não. Depois que você habilitar um domínio gerenciado do Azure AD Domain Services, o serviço estará disponível em sua rede virtual selecionada até que você desabilite/exclua o domínio gerenciado. Não é possível pausar o serviço. A cobrança continuará por hora até que você exclua o domínio gerenciado.

#### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Posso obter os Serviços de Domínio do AD do Azure como parte do EMS (Enterprise Mobility Suite)? Preciso do Azure AD Premium para usar os Serviços de Domínio do Azure AD?
Não. Os Serviços de Domínio do Azure AD é um serviço pré-pago do Azure e não faz parte do EMS. O Azure AD Domain Services pode ser usado em todas as edições do Azure AD (Free, Basic e Premium). Você será cobrado por hora, dependendo do uso.

#### <a name="what-azure-regions-is-the-service-available-in"></a>Em quais regiões do Azure o serviço está disponível?
Consulte a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para conhecer as regiões do Azure nas quais os Serviços de Domínio do Azure AD estão disponíveis.
