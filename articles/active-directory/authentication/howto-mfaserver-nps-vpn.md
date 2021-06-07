---
title: Servidor do Azure MFA e VPNs de terceiros-Azure Active Directory
description: Guias de configuração passo a passo para o servidor Azure MFA integrar com Cisco, Citrix e Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88fe09199cb50d2a3796c3b638dca1a723016dc4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742010"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Cenários avançados com o servidor MFA do Azure e soluções de VPN de terceiros

O Azure Servidor de Autenticação Multifator (servidor Azure MFA) pode ser usado para se conectar diretamente com várias soluções VPN de terceiros. Este artigo enfoca &reg; o dispositivo VPN Cisco ASA, o dispositivo de VPN Citrix Netscaler SSL e o dispositivo de VPN Juniper Networks Secure Access/Pulse seguro Connect Secure SSL. Criamos guias de configuração para abordar esses três dispositivos comuns. O servidor MFA do Azure também pode ser integrado à maioria dos outros sistemas que usam RADIUS, LDAP, IIS ou autenticação baseada em declarações para AD FS. Você pode encontrar mais detalhes nas [configurações do servidor do Azure MFA](howto-mfaserver-deploy.md#next-steps).

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferece mais o servidor MFA para novas implantações. Novos clientes que desejam exigir a MFA (autenticação multifator) durante eventos de entrada devem usar a autenticação multifator do Azure AD baseada em nuvem.
>
> Para começar a usar a MFA baseada em nuvem, consulte [tutorial: proteger eventos de entrada de usuário com a autenticação multifator do Azure ad](tutorial-enable-azure-mfa.md).
>
> Se você usar MFA baseada em nuvem, consulte [integrar sua infraestrutura de VPN com o Azure MFA](howto-mfa-nps-extension-vpn.md).
>
> Os clientes existentes que ativaram o servidor MFA antes de 1º de julho de 2019 podem baixar a versão mais recente, atualizações futuras e gerar credenciais de ativação como de costume.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Dispositivo VPN Cisco ASA e servidor Azure MFA
O servidor MFA do Azure integra-se ao &reg; dispositivo VPN Cisco ASA para fornecer segurança adicional para &reg; logons de VPN do Cisco AnyConnect e acesso ao Portal.  Use o protocolo LDAP ou RADIUS.  Selecione um dos procedimentos a seguir para baixar os guias passo a passo de configuração detalhados.

| Guia de configuração | Descrição |
| --- | --- |
| [Cisco ASA com VPN Anyconnect e configuração do Azure MFA para LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrar o dispositivo VPN Cisco ASA ao Azure MFA usando LDAP |
| [Configuração do Cisco ASA com VPN Anyconnect e do Azure MFA para RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrar o dispositivo VPN Cisco ASA ao Azure MFA usando RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>VPN e servidor MFA do Citrix Netscaler SSL
O servidor MFA do Azure integra-se ao seu dispositivo VPN Citrix Netscaler SSL para fornecer segurança adicional para logons de VPN e acesso ao portal Citrix Netscaler SSL.  Use o protocolo LDAP ou RADIUS.  Selecione um dos procedimentos a seguir para baixar os guias passo a passo de configuração detalhados.

| Guia de configuração | Descrição |
| --- | --- |
| [Configuração da VPN do Citrix NetScaler SSL e do Azure MFA para LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrar a VPN Citrix NetScaler SSL ao dispositivo Azure MFA usando LDAP |
| [Configuração da VPN Citrix NetScaler SSL e do Azure MFA para RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrar o dispositivo VPN Citrix NetScaler SSL ao Azure MFA usando RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Dispositivo de VPN Juniper/Pulse Secure SSL e servidor Azure MFA
O servidor MFA do Azure integra-se ao seu dispositivo de VPN Juniper/Pulse Secure SSL para fornecer segurança adicional para logons de VPN Juniper/Pulse Secure SSL e acesso ao Portal.  Use o protocolo LDAP ou RADIUS.  Selecione um dos procedimentos a seguir para baixar os guias passo a passo de configuração detalhados.

| Guia de configuração | Descrição |
| --- | --- |
| [Configuração da VPN do Juniper/Pulse Secure SSL e do Azure MFA para LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integrar o dispositivo VPN Juniper/Pulse Secure SSL ao Azure MFA usando LDAP |
| [Configuração da VPN do Juniper/Pulse Secure SSL e do Azure MFA para RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrar o dispositivo VPN Juniper/Pulse Secure SSL ao Azure MFA usando RADIUS |

## <a name="next-steps"></a>Próximas etapas

- [Aumentar sua infraestrutura de autenticação atual com a extensão NPS para a Autenticação Multifator do Azure](howto-mfa-nps-extension.md)

- [Configurar a Autenticação Multifator do Azure](howto-mfa-mfasettings.md)
