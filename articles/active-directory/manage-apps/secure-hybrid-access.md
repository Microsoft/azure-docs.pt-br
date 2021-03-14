---
title: Acesso híbrido seguro do Azure AD | Microsoft Docs
description: Este artigo descreve soluções de parceiros para integrar seus aplicativos herdados locais, na nuvem pública ou na nuvem privada ao Azure AD. Proteja seus aplicativos herdados, conectando controladores ou redes de entrega de aplicativos ao Azure AD.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 2/16/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06ed37b914c352d7162db7f8409ac09b7615da47
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174420"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Acesso híbrido seguro: proteger aplicativos herdados com o Azure Active Directory

Agora você pode proteger seus aplicativos de autenticação herdados locais e na nuvem conectando-os ao Azure Active Directory (AD) com:

- [Proxy de Aplicativo do AD do Azure](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [Seus controladores e redes de entrega de aplicativos existentes](#sha-through-networking-and-delivery-controllers)

- [Aplicativos de rede virtual privada (VPN) e de perímetro de Software-Defined (SDP)](#sha-through-vpn-and-sdp-applications)

Você pode preencher a lacuna e fortalecer sua postura de segurança em todos os aplicativos com recursos do Azure AD, como o [acesso condicional](../conditional-access/overview.md) do Azure AD e o Azure ad [Identity Protection](../identity-protection/overview-identity-protection.md).

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>Acesso híbrido seguro (SHA) por meio do Azure Proxy de Aplicativo do AD
  
Usando o [proxy de aplicativo](./what-is-application-proxy.md) , você pode fornecer [acesso remoto seguro](./application-proxy.md) aos seus aplicativos Web locais. Os usuários não precisam usar uma VPN. Os usuários se beneficiam com a conexão fácil com seus aplicativos de qualquer dispositivo após um [logon único](./add-application-portal-setup-sso.md). O proxy de aplicativo fornece acesso remoto como um serviço e permite que você [publique facilmente seus aplicativos locais](./application-proxy-add-on-premises-application.md) para usuários fora da rede corporativa. Ele ajuda a dimensionar o gerenciamento de acesso à nuvem sem exigir que você modifique seus aplicativos locais. [Planeje uma implantação de proxy de aplicativo do AD do Azure](./application-proxy-deployment-plan.md) como uma próxima etapa.

## <a name="azure-ad-partner-integrations"></a>Integrações de parceiros do Azure AD

### <a name="sha-through-networking-and-delivery-controllers"></a>SHA por meio de controladores de rede e entrega

Além do [proxy de aplicativo do AD do Azure](./what-is-application-proxy.md), para permitir que você use a [estrutura de confiança zero](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/), a Microsoft é parceira com provedores de terceiros. Você pode usar seus controladores de rede e de entrega existentes e proteger facilmente os aplicativos herdados que são essenciais para seus processos de negócios, mas que não poderiam ser protegidos antes do Azure AD. É provável que você já tenha tudo o que precisa para começar a proteger esses aplicativos.

![Imagem mostra acesso híbrido seguro com parceiros de rede e proxy de aplicativo](./media/secure-hybrid-access/secure-hybrid-access.png)

Os fornecedores de rede a seguir oferecem soluções predefinidas e diretrizes detalhadas para a integração com o Azure AD.

- [EAA (Akamai Enterprise Application Access)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [ADC (Application Delivery Controller) Citrix](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

- [Gerenciador de tráfego virtual de Pulse Secure (VTM)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial)

### <a name="sha-through-vpn-and-sdp-applications"></a>SHA por meio de aplicativos VPN e SDP

Usando soluções VPN e SDP, você pode fornecer acesso seguro à sua rede corporativa a partir de qualquer dispositivo, a qualquer momento, em qualquer local, ao mesmo tempo em que protege os dados da sua organização. Com o Azure AD como um IDP (provedor de identidade), você pode usar métodos de autenticação e autorização modernos, como o [logon único](./what-is-single-sign-on.md) do Azure AD e a [autenticação multifator](../authentication/concept-mfa-howitworks.md) para proteger seus aplicativos herdados locais.  

![Imagem mostra acesso híbrido seguro com parceiros VPN e proxy de aplicativo ](./media/secure-hybrid-access/app-proxy-vpn.png)

Os fornecedores de VPN a seguir oferecem soluções predefinidas e diretrizes detalhadas para a integração com o Azure AD.

- [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

- [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn)

- [Proteção global de redes Palo Alto](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

- [Proteção do Pulse Secure Pulse Connect (PCS)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial)

Os seguintes fornecedores de SDP oferecem soluções predefinidas e diretrizes detalhadas para a integração com o Azure AD.

- [Agente de acesso do Datawiza](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Perimeter 81](https://docs.microsoft.com/azure/active-directory/saas-apps/perimeter-81-tutorial#:~:text=For%20SSO%20to%20work,%20you%20need%20to%20establish,to%20test%20Azure%20AD%20single%20sign-on%20with%20B.Simon.)

- [Plataforma de autenticação Silverfort](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Strata](https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial)

- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)
