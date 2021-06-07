---
title: Residência de dados de autenticação multifator do Azure AD
description: Saiba quais dados pessoais e organizacionais o Azure AD multifator Authentication armazena sobre você e seus usuários e quais dados permanecem dentro do país/região de origem.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7381ab62eb39c555c6b4eb34f150fc71bea1f10f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561457"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Dados de residência de dados e de cliente para autenticação multifator do Azure AD

O Azure Active Directory (Azure AD) armazena dados do cliente em uma localização geográfica com base no endereço que uma organização fornece ao assinar um serviço online da Microsoft, como o Microsoft 365 ou o Azure. Para obter informações sobre onde os dados do cliente são armazenados, consulte [onde estão localizados os dados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) na central de confiabilidade da Microsoft.

A autenticação multifator do Azure AD baseada em nuvem e o processo do servidor de autenticação multifator do Azure e armazenar dados pessoais e dados organizacionais. Este artigo descreve o que são os dados e onde eles são armazenados.

O serviço de autenticação multifator do Azure AD tem data centers no Estados Unidos, na Europa e no Pacífico Asiático. As seguintes atividades se originam dos datacenters regionais, exceto onde indicado:

* As chamadas telefônicas de autenticação multifator se originam de Estados Unidos data centers e são roteadas por provedores globais.
* As solicitações de autenticação de usuário de uso geral de outras regiões são processadas no momento com base no local do usuário.
* As notificações por push que usam o aplicativo Microsoft Authenticator são processadas no momento em data centers regionais com base no local do usuário. Serviços de dispositivo específicos do fornecedor, como Apple Push Notification Service, podem estar fora do local do usuário.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Dados pessoais armazenados pela autenticação multifator do Azure AD

Dados pessoais são informações de nível de usuário associadas a uma pessoa específica. Os seguintes armazenamentos de dados contêm informações pessoais:

* Usuários bloqueados
* Usuários ignorados
* Solicitações de alteração de token do dispositivo Microsoft Authenticator
* Relatórios de atividade de autenticação multifator
* Ativações do Microsoft Authenticator

Essas informações são mantidas por 90 dias.

A autenticação multifator do Azure AD não registra dados pessoais, como nomes de email, números de telefone ou endereços IP. No entanto, *Userobjectid* identifica as tentativas de autenticação para os usuários. Os dados de log são armazenados por 30 dias.

### <a name="data-stored-by-azure-ad-multifactor-authentication"></a>Dados armazenados pela autenticação multifator do Azure AD

Para nuvens públicas do Azure, excluindo Azure AD B2C autenticação, a extensão NPS e o adaptador do Windows Server 2016 ou 2019 Serviços de Federação do Active Directory (AD FS) (AD FS), os seguintes dados pessoais são armazenados:

| Tipo de evento                           | Tipo de armazenamento de dados |
|--------------------------------------|-----------------|
| Token OATH                           | Logs de autenticação multifator     |
| SMS unidirecional                          | Logs de autenticação multifator     |
| Chamada de voz                           | Logs de autenticação multifator<br/>Repositório de dados de relatório de atividade de autenticação multifator<br/>Usuários bloqueados (se a fraude foi relatada) |
| Notificação do Microsoft Authenticator | Logs de autenticação multifator<br/>Repositório de dados de relatório de atividade de autenticação multifator<br/>Usuários bloqueados (se a fraude foi relatada)<br/>Solicitações de alteração quando o token de dispositivo de Microsoft Authenticator é alterado |

Por Microsoft Azure Governamental, Microsoft Azure Alemanha, Microsoft Azure operado por 21Vianet, autenticação de Azure AD B2C, a extensão do NPS e o adaptador de AD FS do Windows Server 2016 ou 2019, os seguintes dados pessoais são armazenados:

| Tipo de evento                           | Tipo de armazenamento de dados |
|--------------------------------------|-----------------|
| Token OATH                           | Logs de autenticação multifator<br/>Repositório de dados de relatório de atividade de autenticação multifator |
| SMS unidirecional                          | Logs de autenticação multifator<br/>Repositório de dados de relatório de atividade de autenticação multifator |
| Chamada de voz                           | Logs de autenticação multifator<br/>Repositório de dados de relatório de atividade de autenticação multifator<br/>Usuários bloqueados (se a fraude foi relatada) |
| Notificação do Microsoft Authenticator | Logs de autenticação multifator<br/>Repositório de dados de relatório de atividade de autenticação multifator<br/>Usuários bloqueados (se a fraude foi relatada)<br/>Solicitações de alteração quando o token de dispositivo de Microsoft Authenticator é alterado |

### <a name="data-stored-by-azure-multifactor-authentication-server"></a>Dados armazenados pelo servidor de autenticação multifator do Azure

Se você usar o servidor de autenticação multifator do Azure, os dados pessoais a seguir serão armazenados.

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferece mais o servidor de autenticação multifator para novas implantações. Novos clientes que desejam exigir a autenticação multifator de seus usuários devem usar a autenticação multifator do Azure AD baseada em nuvem. Os clientes existentes que ativaram o servidor de autenticação multifator antes de 1º de julho de 2019 podem baixar a versão e as atualizações mais recentes e gerar credenciais de ativação como de costume.

| Tipo de evento                           | Tipo de armazenamento de dados |
|--------------------------------------|-----------------|
| Token OATH                           | Logs de autenticação multifator<br />Repositório de dados de relatório de atividade de autenticação multifator |
| SMS unidirecional                          | Logs de autenticação multifator<br />Repositório de dados de relatório de atividade de autenticação multifator |
| Chamada de voz                           | Logs de autenticação multifator<br />Repositório de dados de relatório de atividade de autenticação multifator<br />Usuários bloqueados (se a fraude foi relatada) |
| Notificação do Microsoft Authenticator | Logs de autenticação multifator<br />Repositório de dados de relatório de atividade de autenticação multifator<br />Usuários bloqueados (se a fraude foi relatada)<br />Solicitações de alteração quando o token do dispositivo Microsoft Authenticator é alterado |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Dados organizacionais armazenados pela autenticação multifator do Azure AD

Os dados organizacionais são informações de nível de locatário que podem expor configuração ou instalação de ambiente. As configurações de locatário das seguintes portal do Azure páginas de autenticação multifator podem armazenar dados organizacionais, como limites de bloqueio ou informações de ID do chamador para solicitações de autenticação de telefone de entrada:

* Bloqueio de conta
* Alerta de fraude
* Notificações
* Configurações de chamada telefônica

Para o servidor de autenticação multifator do Azure, as seguintes portal do Azure páginas podem conter dados organizacionais:

* Configurações do servidor
* Desvio único
* Regras de cache
* Status do servidor de autenticação multifator

## <a name="multifactor-authentication-logs-location"></a>Local dos logs de autenticação multifator

A tabela a seguir mostra o local dos logs de serviço para nuvens públicas.

| Nuvem pública| Logs de entrada | Relatório de atividade de autenticação multifator        | Logs do serviço de autenticação multifator       |
|-------------|--------------|----------------------------------------|------------------------|
| Estados Unidos          | Estados Unidos           | Estados Unidos                                     | Estados Unidos                     |
| Europa      | Europa       | Estados Unidos                                     | Europa <sup>2</sup>    |
| Austrália   | Austrália    | Estados Unidos<sup>1</sup>                         | Austrália <sup>2</sup> |

<sup>1</sup> Os logs de código OATH são armazenados na Austrália.

<sup>2</sup> Chamadas de voz os logs do serviço de autenticação multifator são armazenados no Estados Unidos.

A tabela a seguir mostra o local dos logs de serviço para nuvens soberanas.

| Nuvem soberana                      | Logs de entrada                         | Relatório de atividade de autenticação multifator (inclui dados pessoais)| Logs do serviço de autenticação multifator |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Alemanha              | Alemanha                              | Estados Unidos                            | Estados Unidos               |
| Azure China 21Vianet                 | China                                | Estados Unidos                            | Estados Unidos               |
| Nuvem do Microsoft governamental           | Estados Unidos                                   | Estados Unidos                            | Estados Unidos               |

Os relatórios de atividade de autenticação multifator contêm dados pessoais, como nome principal do usuário (UPN) e número de telefone completo.

Os logs do serviço de autenticação multifator são usados para operar o serviço.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre quais informações de usuário são coletadas pela autenticação multifator do Azure AD baseada em nuvem e pelo servidor de autenticação multifator do Azure, consulte [coleta de dados de usuário de autenticação multifator do Azure ad](howto-mfa-reporting-datacollection.md).
