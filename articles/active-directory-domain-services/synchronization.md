---
title: 'Azure Active Directory Domain Services: Sincronização em domínios gerenciados | Microsoft Docs'
description: Compreender a sincronização em um domínio gerenciado do Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 475817985885cdd6023e72f20ecf35a3ca582924
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472440"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Sincronização em um domínio gerenciado dos Serviços de Domínio do Azure AD
O diagrama a seguir ilustra o funcionamento da sincronização nos domínios gerenciados dos Serviços de Domínio do Azure AD.

![Sincronização no Azure AD Domain Services](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Sincronização do diretório local para seu locatário do Azure AD
A sincronização do Azure AD Connect é usada para sincronizar contas de usuário, associações de grupo e hashes de credencial para seu locatário do Azure AD. Os atributos de contas de usuário, como o UPN e o SID (identificador de segurança) local são sincronizados. Se você usar os Serviços de Domínio do Azure AD, os hashes de credencial herdados necessários para a autenticação NTLM e Kerberos também serão sincronizados com seu locatário do Azure AD.

Se você configurar o write-back, as alterações que ocorrem em seu diretório do Azure AD serão sincronizadas com o Active Directory local. Por exemplo, se você alterar sua senha usando o gerenciamento de senha de autoatendimento do Azure AD, a senha alterada será atualizada em seu domínio do AD local.

> [!NOTE]
> Sempre use a versão mais recente do Azure AD Connect para garantir que você tenha correções para todos os bugs conhecidos.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Sincronização do seu locatário do Azure AD para seu domínio gerenciado
As contas de usuário, as associações de grupo e os hashes de credenciais são sincronizados do seu locatário do Azure AD para seu domínio gerenciado dos Serviços de Domínio do Azure AD. Esse processo de sincronização é automático. Você não precisa configurar, monitorar ou gerenciar esse processo de sincronização. A sincronização inicial pode levar de algumas horas a alguns dias, dependendo do número de objetos em seu diretório do Azure AD. Após a conclusão da sincronização inicial, leva cerca de 20 a 30 minutos para que as alterações feitas ao Azure AD sejam atualizadas em seu domínio gerenciado. Esse intervalo de sincronização aplica-se a alterações de senha ou alterações em atributos feitos no Azure AD.

O processo de sincronização também é unidirecional por natureza. O domínio gerenciado é basicamente somente leitura, exceto qualquer OUs personalizadas que você criar. Portanto, você não pode fazer alterações em atributos de usuário, senhas de usuário ou associações de grupo do domínio gerenciado. Como resultado, não há nenhuma sincronização reversa de alterações do seu domínio gerenciado de volta para seu locatário do Azure AD.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Sincronização de um ambiente de várias florestas locais
Muitas organizações têm uma infraestrutura de identidade local bastante complexa que consiste em várias florestas de contas. O Azure AD Connect oferece suporte à sincronização de usuários, de grupos e de hashes de credencial de ambientes de várias florestas para seu locatário do Azure AD.

Por outro lado, o seu locatário do Azure AD é um namespace muito mais simples. Para permitir aos usuários acessar aplicativos protegidos pelo Azure AD de forma confiável, resolva os conflitos de UPN entre contas de usuário em diferentes florestas. Seu domínio gerenciado dos Serviços de Domínio do Azure AD é ligeiramente parecido com o seu locatário do Azure AD. Você verá uma estrutura de UO simples em seu domínio gerenciado. Todas as contas de usuário e grupos são armazenados no contêiner 'Usuários AADDC', apesar de estarem sendo sincronizadas de florestas ou domínios locais diferentes. Talvez você tenha configurado uma estrutura de UO hierárquica local. O domínio gerenciado ainda tem uma estrutura de UO simples.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Exclusões - o que não é sincronizado com o domínio gerenciado
Os seguintes atributos ou objetos não são sincronizados para seu locatário do Azure AD ou para seu domínio gerenciado:

* **Atributos excluídos:** você pode optar por excluir determinados atributos da sincronização do domínio local com seu locatário do Azure AD usando o Azure AD Connect. Esses atributos excluídos não estão disponíveis no seu domínio gerenciado.
* **Políticas de grupo**: as políticas de grupo configuradas em seu domínio local não são sincronizadas com o domínio gerenciado.
* **Compartilhamento Sysvol**: da mesma forma, o conteúdo do compartilhamento Sysvol no domínio local não é sincronizado com o domínio gerenciado.
* **Objetos de computador:** objetos de computador dos computadores ingressados no domínio local não são sincronizados com o domínio gerenciado. Esses computadores não têm uma relação de confiança com o domínio gerenciado e pertencem ao domínio local apenas. Em seu domínio gerenciado, você encontrará os objetos de computador somente para computadores que você ingressou explicitamente no domínio gerenciado.
* **Atributos SidHistory para usuários e grupos:** o usuário primário e os SIDs de grupo primário de seu domínio local são sincronizados com seu domínio gerenciado. No entanto, os atributos SidHistory existentes para usuários e grupos não são sincronizados do seu domínio local para seu domínio gerenciado.
* **Estruturas de UOs (unidades organizacionais):** as unidades organizacionais definidas em seu domínio local não serão sincronizadas com o domínio gerenciado. Há duas UOs internas em seu domínio gerenciado. Por padrão, o domínio gerenciado tem uma estrutura de UO simples. No entanto, você pode optar por [criar uma UO personalizada em seu domínio gerenciado](create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Como os atributos específicos são sincronizados com o domínio gerenciado
A tabela a seguir lista alguns atributos comuns e descreve como eles serão sincronizados com o domínio gerenciado.

| O atributo em seu domínio gerenciado | source | Observações |
|:--- |:--- |:--- |
| UPN |Atributo UPN do usuário no seu locatário do Azure AD |O atributo UPN do seu locatário do Azure AD é sincronizado como ele está para o domínio gerenciado. Portanto, a maneira mais confiável para entrar no seu domínio gerenciado é usando seu UPN. |
| SAMAccountName |O atributo mailNickname do usuário no seu locatário do Azure AD ou gerado automaticamente |O atributo SAMAccountName é originado do atributo mailNickname no seu locatário do Azure AD. Se várias contas de usuário tiverem o mesmo atributo mailNickname, SAMAccountName será gerado automaticamente. Se o mailNickname ou o prefixo UPN do usuário tiver mais de 20 caracteres, o SAMAccountName será gerado automaticamente para satisfazer o limite de 20 caracteres em atributos SAMAccountName. |
| Senhas |Senha do usuário do seu locatário do Azure AD |Os hashes de credenciais necessários para a autenticação NTLM ou Kerberos (também chamados de credenciais suplementares) são sincronizados do seu locatário do Azure AD. Se seu locatário do Azure AD for um locatário sincronizado, essas credenciais serão obtidas do seu domínio local. |
| SID do usuário/grupo primário |Gerado automaticamente |O SID primário para contas de usuário/grupo é gerado automaticamente no seu domínio gerenciado. Esse atributo não coincide com o SID do usuário/grupo primário do objeto em seu local de domínio do AD. Essa incompatibilidade ocorre porque o domínio gerenciado tem um namespace de SID diferente do seu domínio local. |
| Histórico de SID para usuários e grupos |SID de usuário e grupo primário local |O atributo SidHistory para usuários e grupos no domínio gerenciado é definido para corresponder ao SID do grupo ou usuário primário correspondente em seu domínio local. Esse recurso ajuda a facilitar o arrastar e deslocar de aplicativos no local para o domínio gerenciado, já que você não precisa reaplicar a ACL nos recursos. |

> [!NOTE]
> **Entrar no domínio gerenciado usando o formato UPN:** o atributo SAMAccountName pode ser gerado automaticamente para algumas contas de usuário em seu domínio gerenciado. Se vários usuários tiverem o mesmo atributo mailNickname ou os usuários tiverem prefixos UPN excessivamente longos, o SAMAccountName para esses usuários poderá ser gerado automaticamente. Portanto, o formato de SAMAccountName (por exemplo, ' CONTOSO100\pedrousuario') nem sempre é uma maneira confiável de entrar no domínio. O SAMAccountName gerado automaticamente dos usuários pode diferir do seu prefixo UPN. Use o formato UPN (por exemplo, 'joeuser@contoso100.com') para entrar no domínio gerenciado de forma confiável.

### <a name="attribute-mapping-for-user-accounts"></a>Mapeamento de atributos para contas de usuário
A tabela a seguir ilustra como os atributos específicos de objetos de usuário em seu locatário do Azure AD são sincronizados com os atributos correspondentes em seu domínio gerenciado.

| Atributo de usuário no seu locatário do Azure AD | Atributo de usuário em seu domínio gerenciado |
|:--- |:--- |
| accountEnabled |userAccountControl (define ou limpa o bit de ACCOUNT_DISABLED) |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |título |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (às vezes pode ser gerado automaticamente) |
| Serviço Móvel |Serviço Móvel |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (define ou limpa o bit de DONT_EXPIRE_PASSWORD) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |st |
| streetAddress |streetAddress |
| sobrenome |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Mapeamento de atributo para grupos
A tabela a seguir ilustra como os atributos específicos de objetos de grupo em seu locatário do Azure AD são sincronizados com os atributos correspondentes em seu domínio gerenciado.

| Atributo de grupo em seu locatário do Azure AD | Atributo de grupo em seu domínio gerenciado |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (às vezes pode ser gerado automaticamente) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>Considerações de segurança e sincronização de hash de senha
Quando você habilita o Azure AD Domain Services, seu diretório do Azure AD gera e armazena hashes de senha em formatos compatíveis com NTLM e Kerberos. 

Para contas de usuário de nuvem existentes, como o Azure AD nunca armazena suas senhas de texto não criptografado, esses hashes não podem ser gerados automaticamente. Portanto, a Microsoft exige que os [usuários de nuvem redefinam/alterem as senhas](active-directory-ds-getting-started-password-sync.md) para que seus hashes de senha possam ser gerados e armazenados no Azure AD. Para qualquer conta de usuário de nuvem criada no Azure AD depois de habilitar o Azure AD Domain Services, os hashes de senha são gerados e armazenados nos formatos compatíveis com NTLM e Kerberos. 

Para contas de usuário sincronizadas do AD local usando o Sincronização do Azure AD Connect, é necessário [configurar o Azure AD Connect para sincronizar os hashes de senha nos formatos compatíveis com Kerberos e NTLM](active-directory-ds-getting-started-password-sync-synced-tenant.md).

Os hashes de senha compatíveis com Kerberos e NTLM são sempre armazenados de forma criptografada no Azure AD. Esses hashes são criptografados, de modo que somente o Azure AD Domain Services tem acesso às chaves de descriptografia. Nenhum outro serviço ou componente no Azure AD tem acesso às chaves de descriptografia. As chaves de criptografia são exclusivas por locatário do Azure AD. O Azure AD Domain Services sincroniza os hashes de senha nos controladores de domínio para seu domínio gerenciado. Esses hashes de senha são armazenados e protegidos nesses controladores de domínio semelhantes a como as senhas são armazenadas e protegidas em controladores de domínio do AD do Windows Server. Os discos para esses controladores de domínio gerenciado são criptografados em repouso.

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objetos que não são sincronizados com seu locatário do Azure AD do seu domínio gerenciado
Conforme descrito em uma seção anterior deste artigo, há uma sincronização de seu domínio gerenciado para seu locatário do Azure AD. Você pode optar por [criar uma UO (unidade organizacional) personalizada](create-ou.md) em seu domínio gerenciado. Além disso, você pode criar outras OUs, usuários, grupos ou contas de serviço nessas OUs personalizadas. Nenhum dos objetos criados em OUs personalizadas é sincronizado de volta para seu locatário do Azure AD. Esses objetos estão disponíveis para uso somente dentro de seu domínio gerenciado. Portanto, esses objetos não são visíveis usando cmdlets do PowerShell do Azure AD, a API do Graph do Azure AD ou usando o interface do usuário de gerenciamento do Azure AD.

## <a name="related-content"></a>Conteúdo relacionado
* [Recursos – Azure AD Domain Services](active-directory-ds-features.md)
* [Cenários de implantação – Azure AD Domain Services](scenarios.md)
* [Considerações de rede para Serviços de Domínio do Azure AD](network-considerations.md)
* [Introdução aos Serviços de Domínio do Azure AD](create-instance.md)
