---
title: Perguntas frequentes sobre implantação de chave de segurança FIDO2 híbrida-Azure Active Directory
description: Saiba mais sobre algumas perguntas frequentes sobre a entrada de chave de segurança FIDO2 híbrida sem senha usando Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca4943293f9474d4089267d05460d6d8766b79e6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646377"
---
# <a name="deployment-frequently-asked-questions-faqs-for-hybrid-fido2-security-keys-in-azure-ad"></a>Perguntas frequentes sobre a implantação de chaves de segurança FIDO2 híbridas no Azure AD 

Este artigo aborda as perguntas frequentes sobre a implantação (FAQs) para dispositivos ingressados no Azure AD híbrido e entrada sem senha em recursos locais. Com esse recurso sem senha, você pode habilitar a autenticação do Azure AD em dispositivos Windows 10 para dispositivos ingressados no Azure AD híbrido usando chaves de segurança FIDO2. Os usuários podem entrar no Windows em seus dispositivos com credenciais modernas, como chaves FIDO2, e acessar recursos baseados em AD DS (Active Directory Domain Services tradicional) com uma experiência de SSO (logon único) contínuo para seus recursos locais.

Há suporte para os seguintes cenários para usuários em um ambiente híbrido:

* Entre em dispositivos ingressados no Azure AD híbrido usando chaves de segurança FIDO2 e obtenha acesso SSO a recursos locais.
* Entre em dispositivos ingressados no Azure AD usando chaves de segurança FIDO2 e obtenha acesso SSO a recursos locais.

Para começar a usar as chaves de segurança do FIDO2 e o acesso híbrido a recursos locais, consulte os seguintes artigos:

* [Chaves de segurança FIDO2 sem senha](howto-authentication-passwordless-security-key.md)
* [Windows 10 sem senha](howto-authentication-passwordless-security-key-windows.md)
* [Locais sem senha](howto-authentication-passwordless-security-key-on-premises.md)

## <a name="security-keys"></a>Chaves de segurança

* [Minha organização requer autenticação de dois fatores para acessar recursos. O que posso fazer para dar suporte a esse requisito?](#my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement)
* [Onde posso encontrar chaves de segurança FIDO2 em conformidade?](#where-can-i-find-compliant-fido2-security-keys)
* [O que devo fazer se perder minha chave de segurança?](#what-if-i-lose-my-security-key)
* [Como os dados são protegidos na chave de segurança do FIDO2?](#how-is-the-data-protected-on-the-fido2-security-key)
* [Como funciona o registro das chaves de segurança do FIDO2?](#how-does-the-registering-of-fido2-security-keys-work)
* [Há uma maneira para os administradores provisionarem as chaves para os usuários diretamente?](#is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly)

### <a name="my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Minha organização requer autenticação multifator para acessar recursos. O que posso fazer para dar suporte a esse requisito?

As chaves de segurança FIDO2 vêm em uma variedade de fatores forma. Entre em contato com o fabricante do dispositivo de interesse para discutir como seus dispositivos podem ser habilitados com um PIN ou biométrica como um segundo fator. Para obter uma lista de provedores com suporte, consulte [FIDO2 Security Keys Providers](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="where-can-i-find-compliant-fido2-security-keys"></a>Onde posso encontrar chaves de segurança FIDO2 em conformidade?

Para obter uma lista de provedores com suporte, consulte [FIDO2 Security Keys Providers](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="what-if-i-lose-my-security-key"></a>E se eu perder minha chave de segurança?

Você pode remover chaves no portal do Azure navegando até a página **informações de segurança** e removendo a chave de segurança FIDO2.

### <a name="how-is-the-data-protected-on-the-fido2-security-key"></a>Como os dados são protegidos na chave de segurança do FIDO2?

As chaves de segurança FIDO2 têm enclaves seguro que protegem as chaves privadas armazenadas nelas. Uma chave de segurança FIDO2 também tem propriedades antifalsificadas incorporadas, como no Windows Hello, em que você não pode extrair a chave privada.

### <a name="how-does-the-registering-of-fido2-security-keys-work"></a>Como funciona o registro das chaves de segurança do FIDO2?

Para obter mais informações sobre como registrar e usar as chaves de segurança do FIDO2, consulte [Habilitar entrada de chave de segurança sem senha](howto-authentication-passwordless-security-key.md).

### <a name="is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly"></a>Há uma maneira para os administradores provisionarem as chaves para os usuários diretamente?

Não, não neste momento.

## <a name="prerequisites"></a>Pré-requisitos

* [Esse recurso funcionará se não houver conectividade com a Internet?](#does-this-feature-work-if-theres-no-internet-connectivity)
* [Quais são os pontos de extremidade específicos que devem ser abertos no Azure AD?](#what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad)
* [Como fazer identificar o tipo de ingresso no domínio (ingressado no Azure AD ou no Azure AD híbrido) para meu dispositivo Windows 10?](#how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device)
* [Qual é a recomendação sobre o número de DCs que devem ser corrigidos?](#whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched)
* [Posso implantar o provedor de credenciais do FIDO2 em um dispositivo somente local?](#can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device)
* [A entrada da chave de segurança do FIDO2 não está funcionando para meu administrador de domínio ou outras contas de alto privilégio. Por?](#fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why)

### <a name="does-this-feature-work-if-theres-no-internet-connectivity"></a>Esse recurso funcionará se não houver conectividade com a Internet?

A conectividade com a Internet é um pré-requisito para habilitar esse recurso. Na primeira vez que um usuário faz logon usando chaves de segurança FIDO2, eles devem ter conectividade com a Internet. Para eventos de entrada subsequentes, a entrada armazenada em cache deve funcionar e permitir que o usuário se autentique sem conectividade com a Internet.

Para uma experiência consistente, verifique se os dispositivos têm acesso à Internet e a linha de visão para os DCs.

### <a name="what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad"></a>Quais são os pontos de extremidade específicos que devem ser abertos no Azure AD?

Os pontos de extremidade a seguir são necessários para o registro e a autenticação:

* **. microsoftonline.com*
* **. microsoftonline-p.com*
* **. msauth.net*
* **. msauthimages.net*
* **. msecnd.net*
* **. msftauth.net*
* **. msftauthimages.net*
* **. phonefactor.net*
* *enterpriseregistration.windows.net*
* *management.azure.com*
* *policykeyservice.dc.ad.msft.net*
* *Secure.aadcdn.microsoftonline p.com*

Para obter uma lista completa de pontos de extremidade necessários para usar os produtos online da Microsoft, consulte [URLs e intervalos de endereços IP do Office 365](/microsoft-365/enterprise/urls-and-ip-address-ranges).

### <a name="how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device"></a>Como fazer identificar o tipo de ingresso no domínio (ingressado no Azure AD ou no Azure AD híbrido) para meu dispositivo Windows 10?

Para verificar se o dispositivo cliente do Windows 10 tem o tipo de junção de domínio correto, use o seguinte comando:

```console
Dsregcmd/status
```

A saída de exemplo a seguir mostra que o dispositivo é associado ao Azure AD, pois *AzureADJoined* está definido como *Sim*:

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: NO
```

A saída de exemplo a seguir mostra que o dispositivo é ingressado no Azure AD híbrido, pois *DomainedJoined* também é definido como *Sim*. O *DomainName* também é mostrado:

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: YES
DomainName: CONTOSO
```

Em um controlador de domínio do Windows Server 2016 ou 2019, verifique se os patches a seguir foram aplicados. Se necessário, execute Windows Update para instalá-los:

* Windows Server 2016- [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019- [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

Em um dispositivo cliente, execute o seguinte comando para verificar a conectividade com um controlador de domínio apropriado com os patches instalados:

```console
nltest /dsgetdc:<domain> /keylist /kdc
```

### <a name="whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched"></a>Qual é a recomendação sobre o número de DCs que devem ser corrigidos?

Recomendamos aplicar patches à maioria dos seus controladores de domínio do Windows Server 2016 ou 2019 com o patch para garantir que eles possam lidar com a carga de solicitação de autenticação de sua organização.

Em um controlador de domínio do Windows Server 2016 ou 2019, verifique se os patches a seguir foram aplicados. Se necessário, execute Windows Update para instalá-los:

* Windows Server 2016- [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019- [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device"></a>Posso implantar o provedor de credenciais do FIDO2 em um dispositivo somente local?

Não, esse recurso não tem suporte para o dispositivo somente no local. O provedor de credenciais FIDO2 não aparecerá.

### <a name="fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why"></a>A entrada da chave de segurança do FIDO2 não está funcionando para meu administrador de domínio ou outras contas de alto privilégio. Por quê?

A política de segurança padrão não concede permissão ao Azure AD para assinar contas com privilégios altos em recursos locais.

Para desbloquear as contas, use **Active Directory usuários e computadores** para modificar a propriedade *msDS-NeverRevealGroup* do *objeto de computador Kerberos do Azure AD (CN = AzureADKerberos, ou = controladores de domínio, \<domain-DN> )*.

## <a name="under-the-hood"></a>Nos bastidores

* [Como o Kerberos do Azure AD está vinculado ao meu ambiente de Active Directory Domain Services local?](#how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment)
* [Onde posso exibir esses objetos de servidor Kerberos que são criados no AD e publicados no Azure AD?](#where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad)
* [Por que não podemos ter a chave pública registrada no local AD DS para que não haja nenhuma dependência na Internet?](#why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet)
* [Como as chaves são giradas no objeto do servidor Kerberos?](#how-are-the-keys-rotated-on-the-kerberos-server-object)
* [Por que precisamos de Azure AD Connect? Ele grava todas as informações para AD DS do Azure AD?](#why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad)
* [Qual é a aparência da solicitação/resposta HTTP ao solicitar PRT + TGT parcial?](#what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt)

### <a name="how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment"></a>Como o Kerberos do Azure AD está vinculado ao meu ambiente de Active Directory Domain Services local?

Há duas partes: o ambiente de AD DS local e o locatário do Azure AD.

**Active Directory Domain Services (AD DS)**

O servidor Kerberos do Azure AD é representado em um ambiente de AD DS local como um objeto de controlador de domínio (DC). Este objeto DC é composto por vários objetos:

* *CN = AzureADKerberos, OU = Controladores de domínio,\<domain-DN>*
    
    Um objeto de *computador* que representa um RODC (controlador de domínio Read-Only) no AD DS. Não há nenhum computador associado a este objeto. Em vez disso, é uma representação lógica de um controlador de domínio.

* *CN = krbtgt_AzureAD, CN = usuários,\<domain-DN>*

    Um objeto de *usuário* que representa uma chave de criptografia TGT (tíquete de concessão de tíquete) Kerberos do RODC.

* *CN = 900274c4-b7d2-43c8-90ee-00a9f650e335, CN = AzureAD, CN = System,\<domain-DN>*

    Um objeto *serviceConnectionPoint* que armazena metadados sobre os objetos de servidor Kerberos do Azure AD. As ferramentas administrativas usam esse objeto para identificar e localizar os objetos de servidor Kerberos do Azure AD.

**Azure Active Directory**

O servidor Kerberos do Azure AD é representado no Azure AD como um objeto *KerberosDomain* . Cada ambiente de AD DS local é representado como um único objeto *KerberosDomain* no locatário do Azure AD.

Por exemplo, você pode ter uma floresta AD DS com dois domínios, como *contoso.com* e *fabrikam.com*. Se você permitir que o Azure AD emita tíquetes de concessão de tíquete Kerberos (TGTs) para toda a floresta, haverá dois objetos *KerberosDomain* no Azure ad – um objeto para *contoso.com* e outro para *fabrikam.com*.

Se você tiver várias florestas AD DS, terá um objeto *KerberosDomain* para cada domínio em cada floresta.

### <a name="where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad"></a>Onde posso exibir esses objetos de servidor Kerberos criados no AD DS e publicados no Azure AD?

Para exibir todos os objetos, use os cmdlets do PowerShell do servidor Kerberos do Azure AD incluídos na versão mais recente do Azure AD Connect.

Para obter mais informações, incluindo instruções sobre como exibir os objetos, consulte [criar objetos de servidor Kerberos](howto-authentication-passwordless-security-key-on-premises.md#create-kerberos-server-object).

### <a name="why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet"></a>Por que não podemos ter a chave pública registrada no local AD DS para que não haja nenhuma dependência na Internet?

Recebemos comentários sobre a complexidade do modelo de implantação do Windows Hello para empresas, portanto, desejava simplificar o modelo de implantação sem precisar usar certificados e PKI (FIDO2 não usa certificados).

### <a name="how-are-the-keys-rotated-on-the-kerberos-server-object"></a>Como as chaves são giradas no objeto do servidor Kerberos?

Como qualquer outro controlador de domínio, as chaves *krbtgt* de criptografia de servidor Kerberos do Azure ad devem ser giradas regularmente. É recomendável seguir o mesmo agendamento que você usa para girar todas as outras AD DS chaves *krbtgt* .

> [!NOTE]
> Embora existam outras ferramentas para girar as chaves *krbtgt* , você deve [usar os cmdlets do PowerShell para girar as chaves *krbtgt*](howto-authentication-passwordless-security-key-on-premises.md#rotating-the-azure-ad-kerberos-server-key) do seu servidor Kerberos do Azure AD. Esse método garante que as chaves sejam atualizadas tanto no ambiente de AD DS local quanto no Azure AD.

### <a name="why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad"></a>Por que precisamos de Azure AD Connect? Ele grava todas as informações para AD DS do Azure AD?

Azure AD Connect não grava informações de volta do Azure AD para AD DS. O utilitário inclui o módulo do PowerShell para criar o objeto do servidor Kerberos no AD DS e publicá-lo no Azure AD.

### <a name="what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt"></a>Qual é a aparência da solicitação/resposta HTTP ao solicitar PRT + TGT parcial?

A solicitação HTTP é uma solicitação de PRT (token de atualização primária) padrão. Essa solicitação de PRT inclui uma declaração que indica que um TGT (tíquete de concessão de tíquete) Kerberos é necessário.

| Declaração | Valor | Descrição                             |
|-------|-------|-----------------------------------------|
| TGT   | true  | Declaração indica que o cliente precisa de um TGT. |

O Azure AD combina a chave do cliente criptografada e o buffer de mensagens na resposta de PRT como propriedades adicionais. A carga é criptografada usando a chave de sessão de dispositivo do Azure AD.

| Campo              | Tipo   | Descrição  |
|--------------------|--------|--------------|
| tgt_client_key     | string | Chave de cliente codificada em Base64 (segredo). Essa chave é o segredo do cliente usado para proteger o TGT. Nesse cenário sem senha, o segredo do cliente é gerado pelo servidor como parte de cada solicitação de TGT e, em seguida, retornado ao cliente na resposta. |
| tgt_key_type       | INT    | O tipo de chave AD DS local usado para a chave do cliente e a chave de sessão do Kerberos incluídos no KERB_MESSAGE_BUFFER. |
| tgt_message_buffer | string | KERB_MESSAGE_BUFFER codificado na base64. |

## <a name="next-steps"></a>Próximas etapas

Para começar a usar as chaves de segurança do FIDO2 e o acesso híbrido a recursos locais, consulte os seguintes artigos:

* [Chaves de segurança FIDO2 sem senha](howto-authentication-passwordless-security-key.md)
* [Windows 10 sem senha](howto-authentication-passwordless-security-key-windows.md)
* [Locais sem senha](howto-authentication-passwordless-security-key-on-premises.md)
