---
title: Atributos sincronizados pelo Azure AD Connect | Microsoft Docs
description: Lista os atributos sincronizados com o Active Directory do Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb7010ec16592fea2f530329916e00056ca03a70
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096107"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Sincronização do Azure AD Connect: Atributos sincronizados com o Active Directory do Azure
Este tópico lista os atributos que são sincronizados pela sincronização do Azure AD Connect.  
Os atributos são agrupados pelos aplicativos relacionados do AD do Azure.

## <a name="attributes-to-synchronize"></a>Atributos para sincronizar
Uma pergunta comum é *qual é a lista de atributos mínimos para sincronizar*. A abordagem padrão e recomendada é manter os atributos padrão para que uma GAL (Lista de Endereços Global) completa possa ser construída na nuvem e obter todos os recursos nas cargas de trabalho do Office 365. Em alguns casos, há alguns atributos que sua organização não deseja sincronizar com a nuvem, já que eles contêm dados confidenciais ou PII (Informações de identificação pessoal), como neste exemplo:   
![atributos incorretos](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

Nesse caso, inicie com a lista de atributos neste tópico e identifique aqueles que poderiam conter dados confidenciais ou PII e que não podem ser sincronizados. Então, desmarque-os durante a instalação usando o [aplicativo Azure AD e a filtragem de atributos](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> Ao cancelar a seleção de atributos, seja cuidadoso e desmarque somente aqueles que não podem ser sincronizados em absoluto. Desmarcar outros atributos poderá causar um impacto negativo sobre os recursos.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Nome do atributo | Usuário | Comentário |
| --- |:---:| --- |
| accountEnabled |X |Define se uma conta está habilitada. |
| cn |X | |
| displayName |X | |
| objectSID |X |propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o AD do Azure e o AD. |
| pwdLastSet |X |propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de hash de senha, autenticação de passagem e federação. |
|samAccountName|X| |
| sourceAnchor |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o AD do Azure. |
| usageLocation |X |propriedade mecânica. O país/região do usuário. Usado para atribuição de licença. |
| userPrincipalName |X |UPN é a ID de logon do usuário. Geralmente, o mesmo valor de [mail]. |

## <a name="exchange-online"></a>Exchange Online
| Nome do atributo | Usuário | Contato | Agrupar | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está habilitada. |
| assistente |X |X | | |
| altRecipient |X | | |Exige o Azure AD Connect, build 1.1.552.0 ou posterior. |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| homePhone |X |X | | |
| informações |X |X |X |Atualmente, este atributo não é consumido para grupos. |
| Initials |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| Serviço Móvel |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Disponível no Azure AD Connect versão 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Atualmente, este atributo não é consumido pelo Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Atualmente, este atributo não é consumido pelo Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Atualmente, este atributo não é consumido pelo Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Atualmente, este atributo não é consumido pelo Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Atualmente, este atributo não é consumido pelo Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg-IsOrganizational | | |X | |
| objectSID |X | |X |propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o AD do Azure e o AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de senha e pela federação. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |Derivado de groupType |
| sn |X |X | | |
| sourceAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o AD do Azure. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| título |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |propriedade mecânica. O país/região do usuário. Usado para atribuição de licença. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |UPN é a ID de logon do usuário. Geralmente, o mesmo valor de [mail]. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Nome do atributo | Usuário | Contato | Agrupar | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está habilitada. |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |X | |
| homePhone |X |X | | |
| informações |X |X |X | |
| Initials |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| middleName |X |X | | |
| Serviço Móvel |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSID |X | |X |propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o AD do Azure e o AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| postOfficeBox |X |X | |Atualmente, este atributo não é consumido pelo SharePoint Online. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de hash de senha, autenticação de passagem e federação. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |Derivado de groupType |
| sn |X |X | | |
| sourceAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o AD do Azure. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| título |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |propriedade mecânica. O país/região do usuário. Usado para atribuição de licença. |
| userPrincipalName |X | | |UPN é a ID de logon do usuário. Geralmente, o mesmo valor de [mail]. |
| wWWHomePage |X |X | | |

## <a name="lync-online-subsequently-known-as-skype-for-business"></a>Lync Online (posteriormente conhecido como Skype for Business)
| Nome do atributo | Usuário | Contato | Agrupar | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está habilitada. |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| homePhone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| Serviço Móvel |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| objectSID |X | |X |propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o AD do Azure e o AD. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de hash de senha, autenticação de passagem e federação. |
| securityEnabled | | |X |Derivado de groupType |
| sn |X |X | | |
| sourceAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o AD do Azure. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| título |X |X | | |
| usageLocation |X | | |propriedade mecânica. O país/região do usuário. Usado para atribuição de licença. |
| userPrincipalName |X | | |UPN é a ID de logon do usuário. Geralmente, o mesmo valor de [mail]. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Nome do atributo | Usuário | Contato | Agrupar | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está habilitada. |
| cn |X | |X |Alias ou nome comum. Geralmente, o prefixo do valor [mail]. |
| displayName |X |X |X |Uma cadeia de caracteres que representa o nome geralmente é mostrada como um nome amigável (nome sobrenome). |
| mail |X |X |X |endereço de email completo. |
| member | | |X | |
| objectSID |X | |X |propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o AD do Azure e o AD. |
| proxyAddresses |X |X |X |propriedade mecânica. Usado pelo AD do Azure. Contém todos os endereços de email secundários para o usuário. |
| pwdLastSet |X | | |propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. |
| securityEnabled | | |X |Derivado de groupType. |
| sourceAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o AD do Azure. |
| usageLocation |X | | |propriedade mecânica. O país/região do usuário. Usado para atribuição de licença. |
| userPrincipalName |X | | |Este UPN é a ID de logon do usuário. Geralmente, o mesmo valor de [mail]. |

## <a name="intune"></a>Intune
| Nome do atributo | Usuário | Contato | Agrupar | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está habilitada. |
| c |X |X | | |
| cn |X | |X | |
| description |X |X |X | |
| displayName |X |X |X | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| member | | |X | |
| objectSID |X | |X |propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o AD do Azure e o AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de hash de senha, autenticação de passagem e federação. |
| securityEnabled | | |X |Derivado de groupType |
| sourceAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o AD do Azure. |
| usageLocation |X | | |propriedade mecânica. O país/região do usuário. Usado para atribuição de licença. |
| userPrincipalName |X | | |UPN é a ID de logon do usuário. Geralmente, o mesmo valor de [mail]. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Nome do atributo | Usuário | Contato | Agrupar | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está habilitada. |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| Serviço Móvel |X |X | | |
| objectSID |X | |X |propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o AD do Azure e o AD. |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de hash de senha, autenticação de passagem e federação. |
| securityEnabled | | |X |Derivado de groupType |
| sn |X |X | | |
| sourceAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o AD do Azure. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| título |X |X | | |
| usageLocation |X | | |propriedade mecânica. O país/região do usuário. Usado para atribuição de licença. |
| userPrincipalName |X | | |UPN é a ID de logon do usuário. Geralmente, o mesmo valor de [mail]. |

## <a name="3rd-party-applications"></a>aplicativos de terceira parte
Esse grupo é um conjunto de atributos usados como os atributos mínimos necessários para uma carga de trabalho ou um aplicativo genérico. Ele pode ser usado para uma carga de trabalho não listada em outra seção ou para um aplicativo não Microsoft. Explicitamente, ele é usado para o seguinte:

* Yammer (somente o Usuário é consumido)
* [Cenários de colaboração híbrida entre organizações B2B (entre empresas) oferecidos por recursos como o SharePoint](https://go.microsoft.com/fwlink/?LinkId=747036)

Esse é um conjunto de atributos que poderão ser usados se o diretório do Azure AD não for usado para dar suporte ao Office 365, Dynamics ou Intune. Ele tem um pequeno conjunto de atributos principais.

| Nome do atributo | Usuário | Contato | Agrupar | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está habilitada. |
| cn |X | |X | |
| displayName |X |X |X | |
| givenName |X |X | | |
| mail |X | |X | |
| managedBy | | |X | |
| mailNickname |X |X |X | |
| member | | |X | |
| objectSID |X | | |propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o AD do Azure e o AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de hash de senha, autenticação de passagem e federação. |
| sn |X |X | | |
| sourceAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o AD do Azure. |
| usageLocation |X | | |propriedade mecânica. O país/região do usuário. Usado para atribuição de licença. |
| userPrincipalName |X | | |UPN é a ID de logon do usuário. Geralmente, o mesmo valor de [mail]. |

## <a name="windows-10"></a>Windows 10
Um computador (dispositivo) ingressado no domínio do Windows 10 sincroniza alguns atributos com o Azure AD. Para saber mais sobre os cenários, veja [Conectar dispositivos ingressados no domínio ao Azure AD para experiências com o Windows 10](../active-directory-azureadjoin-devices-group-policy.md). Esses atributos sempre são sincronizados e o Windows 10 não aparece como um aplicativo que pode ser desmarcado. Um computador do Windows 10 ingressado no domínio é identificado por ter o atributo userCertificate populado.

| Nome do atributo | Dispositivo | Comentário |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Valor fixo no código para computadores ingressados do domínio. |
| displayName |X | |
| ms-DS-CreatorSID |X |Também chamado de registeredOwnerReference. |
| objectGUID |X |Também chamado de deviceID. |
| objectSID |X |Também chamado de onPremisesSecurityIdentifier. |
| operatingSystem |X |Também chamado de deviceOSType. |
| operatingSystemVersion |X |Também chamado de deviceOSVersion. |
| userCertificate |X | |

Esses atributos para o **usuário** são adicionais aos outros aplicativos que você selecionou.  

| Nome do atributo | Usuário | Comentário |
| --- |:---:| --- |
| domainFQDN |X |Também chamado de dnsDomainName. Por exemplo, contoso.com. |
| domainNetBios |X |Também chamado de netBiosName. Por exemplo, CONTOSO. |
| msDS-KeyCredentialLink |X |Depois que o usuário estiver registrado no Windows Hello para Empresas. | 

## <a name="exchange-hybrid-writeback"></a>Write-back híbrido do Exchange
É feito write-back desses atributos do Azure AD para o Active Directory local quando você opta por habilitar **Híbrido do Exchange**. Dependendo da sua versão do Exchange, menos atributos poderão ser sincronizados.

| Nome do atributo (interface do usuário do Connect) |Nome do atributo (AD local) | Usuário | Contato | Agrupar | Comentário |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Derivado de cloudAnchor no AD do Azure. Esse atributo é novo no AD do Windows Server 2016 e Exchange 2016. |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |X | | |Arquivo Morto Online: permite que os clientes arquivem emails. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |Filtragem: faz write-back de dados de remetentes bloqueados e seguros online e filtragem local nos clientes. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |Filtragem: faz write-back de dados de remetentes bloqueados e seguros online e filtragem local nos clientes. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |Filtragem: faz write-back de dados de remetentes bloqueados e seguros online e filtragem local nos clientes. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |Habilitar UM (Unificação de Mensagens) – caixa postal online: usado pela integração do Microsoft Lync Server para indicar ao Lync Server local que o usuário tem caixa postal nos serviços online. |
| msExchUserHoldPolicies| ms-Exc-hUserHoldPolicies |X | | |Retenção de Litígio: permite que os serviços de nuvem determinem quais usuários estão em Retenção de Litígio. |
| proxyAddresses| proxyAddresses |X |X |X |Somente o endereço x500 do Exchange Online é inserido. |
| publicDelegates| ms-Exch-Public-Delegates  |X | | |Permite que uma caixa de correio do Exchange Online obtenha direitos de SendOnBehalfTo para usuários com caixa de correio do Exchange local. Exige o Azure AD Connect, build 1.1.552.0 ou posterior. |

## <a name="exchange-mail-public-folder"></a>Pasta pública do Exchange Mail
Esses atributos são sincronizados do Active Directory local para o Azure AD quando você opta por habilitar a **Pasta pública do Exchange Mail**.

| Nome do atributo | PublicFolder | Comentário |
| --- | :---:| --- |
| displayName | X |  |
| mail | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Write-back de dispositivo
Os objetos do dispositivo são criados no Active Directory. Esses objetos podem ser dispositivos ingressados no domínio do Azure AD ou computadores ingressados no domínio do Windows 10.

| Nome do atributo | Dispositivo | Comentário |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| dn |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Somente com o esquema do AD do Windows Server 2016 |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Observações
* Ao usar uma ID Alternativa, o atributo local userPrincipalName é sincronizado com o atributo do Azure AD onPremisesUserPrincipalName. O atributo de ID Alternativa, por exemplo, email, é sincronizado com o atributo do Azure AD userPrincipalName.
* Nas listas acima, o tipo de objeto **User** também se aplica ao tipo de objeto **iNetOrgPerson**.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](how-to-connect-sync-whatis.md) .

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
