---
title: Portas e protocolos exigidos para Identidade Híbrida- Azure | Microsoft Docs
description: Essa página é uma página de referência técnica para as portas que precisam estar abertas para o Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 681d92ed162feff0510067556d7eeaa47b12d988
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520023"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Portas e Protocolos Requeridos para Identidade Híbrida
O documento a seguir é uma referência técnica sobre as portas e os protocolos para implementar uma solução de identidade híbrida. Use a ilustração a seguir e confira a tabela correspondente.

![O que é o Azure AD Connect](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabela 1 - AD do Azure Connect e AD Local
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre o servidor do Azure AD Connect e AD local.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Pesquisas de DNS na floresta de destino. |
| Kerberos |88 (TCP/UDP) |Autenticação Kerberos na floresta do AD. |
| MS-RPC |135 (TCP) |Usado durante a configuração inicial do assistente do Azure AD Connect quando ele se vincula à floresta do AD e também durante a sincronização de senha. |
| LDAP |389 (TCP/UDP) |Usado para importar dados do AD. Dados são criptografados com Sinal e Selo do Kerberos. |
| SMB | 445 (TCP) |Usado pelo SSO Contínuo para criar uma conta de computador na floresta do AD. |
| LDAP/SSL |636 (TCP/UDP) |Usado para importar dados do AD. A transferência de dados é assinada e criptografada. Usado somente se você estiver usando o TLS. |
| RPC |49152-65535 (porta RPC alta aleatória) (TCP) |Usado durante a configuração inicial do assistente do Azure AD Connect quando ele se vincula às florestas do AD e durante a sincronização de senha. Se a porta dinâmica tiver sido alterada, você precisará abrir essa porta. Confira os artigos [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017) e [KB224196](https://support.microsoft.com/kb/224196) para saber mais. |
|WinRM  | 5985 (TCP) |Usado somente se você estiver instalando AD FS com o assistente de Azure AD Connect de gMSA|
|AD DS serviços Web | 9389 (TCP) |Usado somente se você estiver instalando AD FS com o assistente de Azure AD Connect de gMSA |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabela 2 - AD do Azure Connect e Azure AD
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre o servidor do Azure AD Connect e Azure AD.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTP |80 (TCP) |Usado para baixar CRLs (listas de certificados revogados) para verificar certificados TLS/SSL. |
| HTTPS |443 (TCP) |Usado para sincronizar com o Azure AD. |

Para obter uma lista de URLs e endereços IP que você precisa abrir em seu firewall, consulte [URLs do Office 365 e intervalos de endereços IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) e [solução de problemas de conectividade Azure ad Connect](tshoot-connect-connectivity.md#troubleshoot-connectivity-issues-in-the-installation-wizard).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabela 3 – Azure AD Connect e Servidores de Federação AD FS/WAP
Essa tabela descreve as portas e protocolos que são necessários para a comunicação entre o servidor do Azure AD Connect e os servidores de Federação AD FS/WAP.  

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTP |80 (TCP) |Usado para baixar CRLs (listas de certificados revogados) para verificar certificados TLS/SSL. |
| HTTPS |443 (TCP) |Usado para sincronizar com o Azure AD. |
| WinRM |5985 |Ouvinte do WinRM |

## <a name="table-4---wap-and-federation-servers"></a>Tabela 4 - Servidores de Federação e WAP
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre os servidores de Federação e servidores WAP.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443 (TCP) |Usado para autenticação. |

## <a name="table-5---wap-and-users"></a>Tabela 5 - WAP e Usuários
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre os usuários e os servidores WAP.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443 (TCP) |Usado para autenticação de dispositivo. |
| TCP |49443 (TCP) |Usado para autenticação de certificado. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabela 6a e 6b - Autenticação de passagem com SSO (logon único) e Sincronização de hash de senha com SSO (logon único)
Esta tabela descreve as portas e protocolos necessários para a comunicação entre o Azure AD Connect e o Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabela 6a - Autenticação de passagem com SSO
|Protocolo|Número da porta|Descrição
| --- | --- | ---
|HTTP|80|Habilite o tráfego HTTP de saída para a validação de segurança como o SSL. Também é necessário para o recurso de atualização automática funcionar corretamente.
|HTTPS|443| Habilite o tráfego HTTPS de saída para operações como habilitar e desabilitar o recurso, registrar conectores, baixar atualizações do conector e manipular todas as solicitações de entrada do usuário.

Além disso, o Azure AD Connect precisa ser capaz de estabelecer conexões IP diretas com os [intervalos de IP do data center do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabela 6b - Sincronização de hash de senha com SSO

|Protocolo|Número da porta|Descrição
| --- | --- | ---
|HTTPS|443| Habilite o registro por SSO (necessário somente para o processo de registro com SSO).

Além disso, o Azure AD Connect precisa ser capaz de estabelecer conexões IP diretas com os [intervalos de IP do data center do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Novamente, isso só é necessário para o processo de registro do SSO.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 7a e 7b – Agente do Azure AD Connect Health para (AD FS/Sync) e Azure AD
As tabelas a seguir descrevem os pontos de extremidade, portas e protocolos que são necessários para a comunicação entre agentes do Azure AD Connect Health e Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 7a – Portas e protocolos para o agente do Azure AD Connect Health para o (AD FS/Sync) e Azure AD
Esta tabela descreve as portas e protocolos de saída a seguir que são necessários para a comunicação entre os agentes do Azure AD Connect Health e o Azure AD.  

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443 (TCP) |Saída |
| Barramento de Serviço do Azure |5671 (TCP) |Saída |

A porta 5671 do barramento de serviço do Azure não é mais necessária para a versão mais recente do agente. A versão mais recente do agente de Azure AD Connect Health só exigiu a porta 443.

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b – Pontos de extremidade de agente do Azure AD Connect Health para (AD FS/Sync) e Azure AD
Para obter uma lista de pontos de extremidade, veja [a seção Requisitos para o agente do Azure AD Connect Health](how-to-connect-health-agent-install.md#requirements).

