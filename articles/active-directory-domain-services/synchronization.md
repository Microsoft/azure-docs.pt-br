---
title: Como funciona a sincronização no Azure AD Domain Services | Microsoft Docs
description: Saiba como o processo de sincronização funciona para objetos e credenciais de um locatário do Azure AD ou de um ambiente de Active Directory Domain Services local para um Azure Active Directory Domain Services domínio gerenciado.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 41ba337765b4a0a93be52f08ae6656707cf7aa73
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618800"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-active-directory-domain-services-managed-domain"></a>Como os objetos e as credenciais são sincronizados em um domínio gerenciado Azure Active Directory Domain Services

Os objetos e as credenciais em um domínio gerenciado Azure Active Directory Domain Services (Azure AD DS) podem ser criados localmente no domínio ou sincronizados de um locatário do Azure Active Directory (AD do Azure). Ao implantar pela primeira vez o Azure AD DS, uma sincronização unidirecional automática é configurada e iniciada para replicar os objetos do Azure AD. Essa sincronização unidirecional continua a ser executada em segundo plano para manter o domínio gerenciado AD DS do Azure atualizado com qualquer alteração do Azure AD. Nenhuma sincronização ocorre no Azure AD DS de volta para o Azure AD.

Em um ambiente híbrido, os objetos e as credenciais de um domínio AD DS local podem ser sincronizados com o Azure AD usando Azure AD Connect. Depois que esses objetos forem sincronizados com êxito com o Azure AD, a sincronização automática em segundo plano disponibilizará esses objetos e as credenciais para os aplicativos que usam o domínio gerenciado.

Se AD DS local e o Azure AD estiverem configurados para autenticação federada usando o ADFS, não haverá nenhum hash de senha (atual/válido) disponível no Azure DS. As contas de usuário do Azure AD criadas antes da autenticação alimentada foi implementada podem ter um hash de senha antigo, mas isso provavelmente não corresponde a um hash de sua senha local. Portanto, o Azure AD DS não conseguirá validar as credenciais dos usuários.

O diagrama a seguir ilustra como funciona a sincronização entre o Azure AD DS, o Azure AD e um ambiente de AD DS local opcional:

![Visão geral de sincronização para um Azure AD Domain Services domínio gerenciado](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Sincronização do Azure AD para o Azure AD DS

As contas de usuário, as associações de grupo e os hashes de credenciais são sincronizados de uma maneira do Azure AD para o Azure AD DS. Esse processo de sincronização é automático. Você não precisa configurar, monitorar ou gerenciar esse processo de sincronização. A sincronização inicial pode levar algumas horas a alguns dias, dependendo do número de objetos no diretório do AD do Azure. Após a conclusão da sincronização inicial, as alterações feitas no Azure AD, como as alterações de senha ou atributo, são sincronizadas automaticamente com o Azure AD DS.

Quando um usuário é criado no Azure AD, eles não são sincronizados com o Azure AD DS até que eles alterem sua senha no Azure AD. Esse processo de alteração de senhas faz com que as hashes de senha para a autenticação Kerberos e NTLM sejam geradas e armazenadas no Azure AD. Os hashes de senha são necessários para autenticar com êxito um usuário no Azure AD DS.

O processo de sincronização é de uma forma/unidirecional por design. Não há sincronização reversa de alterações do Azure AD DS de volta para o Azure AD. Um domínio gerenciado é amplamente somente leitura, exceto para UOs personalizadas que você pode criar. Você não pode fazer alterações em atributos de usuário, senhas de usuário ou associações de grupo em um domínio gerenciado.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Sincronização de atributos e mapeamento para o Azure AD DS

A tabela a seguir lista alguns atributos comuns e como eles são sincronizados com o Azure AD DS.

| Atributo no Azure AD DS | Fonte | Observações |
|:--- |:--- |:--- |
| UPN | Atributo *UPN* do usuário no locatário do Azure AD | O atributo UPN do locatário do Azure AD é sincronizado no estado em que se encontra para o Azure AD DS. A maneira mais confiável de entrar em um domínio gerenciado é usando o UPN. |
| SAMAccountName | Atributo *mailNickname* do usuário no locatário do Azure ad ou gerado automaticamente | O atributo *sAMAccountName* é originado do atributo *mailNickname* no locatário do Azure AD. Se várias contas de usuário tiverem o mesmo atributo *mailNickname* , o *sAMAccountName* será gerado automaticamente. Se o prefixo de *mailNickname* ou *UPN* do usuário tiver mais de 20 caracteres, o *sAMAccountName* será gerado automaticamente para atender ao limite de 20 caracteres em atributos *sAMAccountName* . |
| Senhas | Senha do usuário do locatário do Azure AD | Os hashes de senha herdados necessários para a autenticação NTLM ou Kerberos são sincronizados do locatário do Azure AD. Se o locatário do Azure AD estiver configurado para sincronização híbrida usando Azure AD Connect, esses hashes de senha serão originados do ambiente de AD DS local. |
| SID do usuário/grupo primário | Gerado automaticamente | O SID principal para contas de usuário/grupo é gerado automaticamente no Azure AD DS. Esse atributo não corresponde ao SID do grupo/usuário primário do objeto em um ambiente de AD DS local. Essa incompatibilidade ocorre porque o domínio gerenciado tem um namespace SID diferente do domínio AD DS local. |
| Histórico de SID para usuários e grupos | SID de usuário e grupo primário local | O atributo *SIDHistory* para usuários e grupos no Azure AD DS é definido para corresponder ao SID de grupo ou usuário primário correspondente em um ambiente de AD DS local. Esse recurso ajuda a fazer com que você tire e Shift dos aplicativos locais para o Azure AD DS mais fácil, pois não é necessário recriar a ACL de recursos. |

> [!TIP]
> **Entre no domínio gerenciado usando o formato UPN** O atributo *sAMAccountName* , como `AADDSCONTOSO\driley` , pode ser gerado automaticamente para algumas contas de usuário em um domínio gerenciado. O *sAMAccountName* gerado automaticamente pelos usuários pode ser diferente do prefixo UPN, portanto nem sempre é uma maneira confiável de se conectar.
>
> Por exemplo, se vários usuários tiverem o mesmo atributo *mailNickname* ou os usuários tiverem prefixos UPN muito longos, o *sAMAccountName* para esses usuários poderá ser gerado automaticamente. Use o formato UPN, como `driley@aaddscontoso.com` , para entrar de forma confiável em um domínio gerenciado.

### <a name="attribute-mapping-for-user-accounts"></a>Mapeamento de atributos para contas de usuário

A tabela a seguir ilustra como os atributos específicos para objetos de usuário no Azure AD são sincronizados com os atributos correspondentes no Azure AD DS.

| Atributo de usuário no Azure AD | Atributo de usuário no Azure AD DS |
|:--- |:--- |
| accountEnabled |userAccountControl (define ou limpa o bit de ACCOUNT_DISABLED) |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| employeedId |employeeId |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |título |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (às vezes pode ser gerado automaticamente) |
| manager |manager |
| Serviço Móvel |Serviço Móvel |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (define ou limpa o bit de DONT_EXPIRE_PASSWORD) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| proxyAddresses | proxyAddresses |
| state |st |
| streetAddress |streetAddress |
| sobrenome |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Mapeamento de atributo para grupos

A tabela a seguir ilustra como os atributos específicos para objetos de grupo no Azure AD são sincronizados com os atributos correspondentes no Azure AD DS.

| Atributo de grupo no Azure AD | Atributo de grupo no Azure AD DS |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (às vezes pode ser gerado automaticamente) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| proxyAddresses | proxyAddresses |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>Sincronização do AD DS local para o Azure AD e o Azure AD DS

Azure AD Connect é usado para sincronizar contas de usuário, associações de grupo e hashes de credenciais de um ambiente de AD DS local para o Azure AD. Os atributos de contas de usuário, como o UPN e o SID (identificador de segurança) local, são sincronizados. Para entrar usando o Azure AD DS, os hashes de senha herdados necessários para a autenticação NTLM e Kerberos também são sincronizados com o Azure AD.

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes do AD DS locais. A instalação do Azure AD Connect em um domínio gerenciado a fim de sincronizar objetos com o Azure AD não é um procedimento compatível.

Se você configurar o Write-back, as alterações do Azure AD serão sincronizadas de volta para o ambiente de AD DS local. Por exemplo, se um usuário alterar sua senha usando o gerenciamento de senhas de autoatendimento do Azure AD, a senha será atualizada de volta no ambiente de AD DS local.

> [!NOTE]
> Sempre use a versão mais recente do Azure AD Connect para garantir que você tenha correções para todos os bugs conhecidos.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Sincronização de um ambiente de várias florestas locais

Muitas organizações têm um ambiente de AD DS local bastante complexo que inclui várias florestas. O Azure AD Connect dá suporte à sincronização de usuários, grupos e hashes de credenciais de ambientes de várias florestas para o Azure AD.

O Azure AD tem um namespace muito mais simples e uniforme. Para permitir aos usuários acessar aplicativos protegidos pelo Azure AD de forma confiável, resolva os conflitos de UPN entre contas de usuário em diferentes florestas. Os domínios gerenciados usam uma estrutura de UO simples, semelhante ao Azure AD. Todas as contas de usuário e grupos são armazenados no contêiner *AADDC Users* , apesar de serem sincronizados de diferentes domínios ou florestas locais, mesmo que você tenha configurado uma estrutura de UO hierárquica local. O domínio gerenciado mescla todas as estruturas de UO hierárquicas.

Conforme detalhado anteriormente, não há nenhuma sincronização do Azure AD DS de volta para o Azure AD. Você pode [criar uma UO (unidade organizacional) personalizada](create-ou.md) no Azure AD DS e, em seguida, usuários, grupos ou contas de serviço nessas UOs personalizadas. Nenhum dos objetos criados em UOs personalizadas é sincronizado de volta para o Azure AD. Esses objetos estão disponíveis somente dentro do domínio gerenciado e não são visíveis usando cmdlets do PowerShell do Azure AD, Microsoft Graph API ou usando a interface do usuário de gerenciamento do Azure AD.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>O que não está sincronizado com o Azure AD DS

Os seguintes objetos ou atributos não são sincronizados de um ambiente de AD DS local para o Azure AD ou o Azure AD DS:

* **Atributos excluídos:** Você pode optar por excluir determinados atributos da sincronização para o Azure AD de um ambiente de AD DS local usando Azure AD Connect. Esses atributos excluídos não estão disponíveis no Azure AD DS.
* **Políticas de Grupo:** As políticas de grupo configuradas em um ambiente de AD DS local não são sincronizadas com o AD DS do Azure.
* **Pasta SYSVOL:** O conteúdo da pasta *SYSVOL* em um ambiente de AD DS local não é sincronizado com o AD DS do Azure.
* **Objetos de computador:** Objetos de computador para computadores ingressados em um ambiente de AD DS local não são sincronizados com o AD DS do Azure. Esses computadores não têm uma relação de confiança com o domínio gerenciado e só pertencem ao ambiente de AD DS local. No Azure AD DS, somente os objetos de computador para computadores que ingressaram explicitamente no domínio com o domínio gerenciado são mostrados.
* **Atributos de SIDHistory para usuários e grupos:** Os SIDs de usuário primário e grupo primário de um ambiente de AD DS local são sincronizados com o AD DS do Azure. No entanto, os atributos *SIDHistory* existentes para usuários e grupos não são sincronizados do ambiente de AD DS local para o Azure AD DS.
* **Estruturas de UO (unidades organizacionais):** As unidades organizacionais definidas em um ambiente de AD DS local não são sincronizadas com o AD DS do Azure. Há duas UOs internas no Azure AD DS-uma para usuários e outra para computadores. O domínio gerenciado tem uma estrutura de UO simples. Você pode optar por [criar uma UO personalizada em seu domínio gerenciado](create-ou.md).

## <a name="password-hash-synchronization-and-security-considerations"></a>Considerações de segurança e sincronização de hash de senha

Quando você habilita o Azure AD DS, os hashes de senha herdados para autenticação NTLM + Kerberos são necessários. O Azure AD não armazena senhas de texto não criptografado, portanto, esses hashes não podem ser gerados automaticamente para contas de usuário existentes. Depois de gerados e armazenados, os hashes de senha compatíveis com Kerberos e NTLM são sempre armazenados de forma criptografada no Azure AD.

As chaves de criptografia são exclusivas para cada locatário do Azure AD. Esses hashes são criptografados de modo que somente o Azure AD DS tenha acesso às chaves de descriptografia. Nenhum outro serviço ou componente no Azure AD tem acesso às chaves de descriptografia.

Os hashes de senha herdados são então sincronizados do Azure AD para os controladores de domínio para um domínio gerenciado. Os discos para esses controladores de domínio gerenciados no Azure AD DS são criptografados em repouso. Esses hashes de senha são armazenados e protegidos nesses controladores de domínio semelhantes a como as senhas são armazenadas e protegidas em um ambiente de AD DS local.

Para ambientes do Azure AD somente na nuvem, [os usuários devem redefinir/alterar sua senha](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para que os hashes de senha necessários sejam gerados e armazenados no Azure AD. Para qualquer conta de usuário de nuvem criada no Azure AD depois de habilitar o Azure AD Domain Services, os hashes de senha são gerados e armazenados nos formatos compatíveis com NTLM e Kerberos. Todas as contas de usuário de nuvem devem alterar sua senha antes que elas sejam sincronizadas com o Azure AD DS.

Para contas de usuário híbridos sincronizadas do ambiente de AD DS local usando Azure AD Connect, você deve [configurar Azure ad Connect para sincronizar hashes de senha nos formatos compatíveis com NTLM e Kerberos](tutorial-configure-password-hash-sync.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as especificidades de sincronização de senha, consulte [como funciona a sincronização de hash de senha com Azure ad Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context).

Para começar a usar o Azure AD DS, [crie um domínio gerenciado](tutorial-create-instance.md).
