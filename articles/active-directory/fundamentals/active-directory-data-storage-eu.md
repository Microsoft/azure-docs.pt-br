---
title: Armazenamento de dados de identidade para clientes europeus – Azure AD
description: Saiba mais sobre onde o Azure Active Directory armazena dados relacionados à identidade para os seus clientes europeus.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a8b013723707c4a3a087a90674227c3d41c5108
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836930"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Armazenamento de dados de identidade para os clientes europeus no Azure Active Directory
Os dados de identidade são armazenados pelo Azure AD em uma localização geográfica com base no endereço fornecido pela sua organização ao assinar um serviço online da Microsoft, como o Microsoft 365 e o Azure. Para obter informações sobre onde os dados de identidade são armazenados, você pode usar a seção [onde os dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) da central de confiabilidade da Microsoft.

Para clientes que forneceram um endereço na Europa, o Azure AD mantém a maioria dos dados de identidade em datacenters europeus. Este documento fornece informações sobre todos os dados armazenados fora da Europa pelos serviços do Azure AD.

## <a name="microsoft-azure-ad-multi-factor-authentication"></a>Microsoft Azure AD a autenticação multifator

Para a autenticação multifator do Azure AD baseada em nuvem, a autenticação é concluída no datacenter mais próximo ao usuário. Os data centers da autenticação multifator do Azure AD existem no América do Norte, na Europa e no Pacífico Asiático.

* A autenticação multifator usando chamadas telefônicas se originam de data centers dos EUA e são roteadas por provedores globais.
* A autenticação multifator usando o SMS é roteada por provedores globais.
* As solicitações de autenticação multifator usando o Microsoft Authenticator notificações por push de aplicativo originadas de datacenters da UE são processadas em datacenters da UE.
    * Serviços específicos do fornecedor do dispositivo, como notificações por push da Apple, podem estar fora da Europa.
* As solicitações de autenticação multifator que usam códigos OATH originados de data centers da UE são validadas na UE.

Para obter mais informações sobre quais informações do usuário são coletadas pelo Azure Servidor de Autenticação Multifator (servidor MFA) e pela MFA do Azure AD baseada em nuvem, consulte [coleta de dados do usuário da autenticação multifator do Azure](../authentication/howto-mfa-reporting-datacollection.md).

## <a name="password-based-single-sign-on-for-enterprise-applications"></a>Sign-On único com base em senha para aplicativos empresariais
 
Se um cliente criar um novo aplicativo empresarial (seja por meio da galeria do Azure AD ou não da Galeria) e habilitar o SSO baseado em senha, a URL de entrada do aplicativo e os campos de entrada de captura personalizada serão armazenados no Estados Unidos. Para obter mais informações sobre esse recurso, consulte [Configurar logon único baseado em senha](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Os dados de configuração da política de Azure AD B2C e os contêiners de chave são armazenados nos datacenters dos EUA. Eles não contêm nenhum dado pessoal do usuário. Para obter mais informações sobre as configurações de política, consulte o artigo [Azure Active Directory B2C: Políticas internas](../../active-directory-b2c/user-flow-overview.md).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
O Azure AD B2B armazena convites com as informações de enviar link e redirecionar URL nos data centers dos EUA. Além disso, o endereço de email dos usuários que não se inscrevem no recebimento de convites B2B também são armazenados em data centers dos EUA.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

O Microsoft Azure AD DS armazena dados de usuário no mesmo que a Rede Virtual do Microsoft Azure selecionada pelo cliente. Portanto, se a rede estiver fora da Europa, os dados são replicados e armazenados fora da Europa.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Federação no Microsoft Exchange Server 2013
    
- Identificador de aplicativo (AppID) – um número exclusivo gerado pelo sistema de autenticação de Azure Active Directory para identificar organizações do Exchange.
- Lista de domínios federados aprovados para o aplicativo
- Chave pública de assinatura de token do aplicativo 

Para obter mais informações sobre Federação no Microsoft Exchange Server, consulte o artigo de ajuda sobre a [Federação: Exchange 2013](/exchange/federation-exchange-2013-help) .


## <a name="other-considerations"></a>Outras considerações

Serviços e aplicativos que se integram ao Azure AD têm acesso aos dados de identidade. Avalie cada serviço e aplicativo que você usa para determinar como os dados de identidade são processados por esse serviço e aplicativo específicos, e se eles atendem aos requisitos de armazenamento de dados da sua empresa.

Para obter mais informações sobre a residência de dados de serviços da Microsoft, consulte o [Onde seus dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) seção do Microsoft Trust Center.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre qualquer um dos recursos e funcionalidades descritos acima, consulte esses artigos:
- [O que é a Autenticação Multifator?](../authentication/concept-mfa-howitworks.md)

- [Redefinição de senha de autoatendimento do Azure AD](../authentication/concept-sspr-howitworks.md)

- [O que é o Azure Active Directory B2C?](../../active-directory-b2c/overview.md)

- [O que é a colaboração B2B do AD do Azure?](../external-identities/what-is-b2b.md)

- [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md)