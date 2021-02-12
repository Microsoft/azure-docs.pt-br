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
ms.openlocfilehash: 788a666e8ec509bbd29a8dbd503a60b3dddefd6b
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208345"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Dados de residência de dados e de cliente para autenticação multifator do Azure AD

Os dados do cliente são armazenados pelo Azure AD em uma localização geográfica com base no endereço fornecido pela sua organização ao assinar um serviço online da Microsoft, como o Microsoft 365 e o Azure. Para obter informações sobre onde os dados do cliente estão armazenados, você pode usar a seção [Onde seus dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) do Microsoft Trust Center.

A autenticação multifator do Azure AD baseada em nuvem e o processo do servidor de autenticação multifator do Azure AD e armazenar uma quantidade de dados pessoais e dados organizacionais. Este artigo descreve o que são os dados e onde eles são armazenados.

O serviço de autenticação multifator do Azure AD tem data centers nos EUA, Europa e Pacífico Asiático. As seguintes atividades se originam dos data centers regionais, exceto onde indicado:

* A autenticação multifator usando chamadas telefônicas se originam de data centers dos EUA e são roteadas por provedores globais.
* As solicitações de autenticação de usuário de uso geral de outras regiões, como Europa ou Austrália, são processadas no momento com base no local do usuário.
* As notificações por push usando o aplicativo Microsoft Authenticator são processadas no momento nos data centers regionais com base no local do usuário.
    * Serviços específicos do fornecedor do dispositivo, como notificações por push da Apple, podem estar fora do local do usuário.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Dados pessoais armazenados pela autenticação multifator do Azure AD

Dados pessoais são informações de nível de usuário associadas a uma pessoa específica. Os seguintes armazenamentos de dados contêm informações pessoais:

* Usuários bloqueados
* Usuários ignorados
* Solicitações de alteração de token do dispositivo Microsoft Authenticator
* Relatórios de atividade de autenticação multifator
* Ativações do Microsoft Authenticator

Essas informações são mantidas por 90 dias.

A autenticação multifator do Azure AD não registra dados pessoais, como nome de usuário, número de telefone ou endereço IP, mas há um *Userobjectid* que identifica as tentativas de autenticação multifator para os usuários. Os dados de log são armazenados por 30 dias.

### <a name="azure-ad-multifactor-authentication"></a>Autenticação multifator do Azure AD

Para nuvens públicas do Azure, excluindo a autenticação do Azure B2C, a Extensão do NPS e o Adaptador do AD FS do Windows Server 2016 ou 2019, os seguintes dados pessoais são armazenados:

| Tipo de evento                           | Tipo de armazenamento de dados |
|--------------------------------------|-----------------|
| Token OATH                           | Em logs de autenticação multifator     |
| SMS unidirecional                          | Em logs de autenticação multifator     |
| Chamada de voz                           | Em logs de autenticação multifator<br />Repositório de dados de relatório de atividade de autenticação multifator<br />Usuários bloqueados se houver fraude relatada |
| Notificação do Microsoft Authenticator | Em logs de autenticação multifator<br />Repositório de dados de relatório de atividade de autenticação multifator<br />Usuários bloqueados se houver fraude relatada<br />Solicitações de alteração quando o token do dispositivo Microsoft Authenticator é alterado |

Para o Microsoft Azure Government, o Microsoft Azure Alemanha, o Microsoft Azure operado pela 21Vianet, a autenticação do Azure B2C, a Extensão NPS e o Adaptador do AD FS do Windows Server 2016 ou 2019, os seguintes dados pessoais são armazenados:

| Tipo de evento                           | Tipo de armazenamento de dados |
|--------------------------------------|-----------------|
| Token OATH                           | Em logs de autenticação multifator<br />Repositório de dados de relatório de atividade de autenticação multifator |
| SMS unidirecional                          | Em logs de autenticação multifator<br />Repositório de dados de relatório de atividade de autenticação multifator |
| Chamada de voz                           | Em logs de autenticação multifator<br />Repositório de dados de relatório de atividade de autenticação multifator<br />Usuários bloqueados se houver fraude relatada |
| Notificação do Microsoft Authenticator | Em logs de autenticação multifator<br />Repositório de dados de relatório de atividade de autenticação multifator<br />Usuários bloqueados se houver fraude relatada<br />Solicitações de alteração quando o token do dispositivo Microsoft Authenticator é alterado |

### <a name="multifactor-authentication-server"></a>Servidor de autenticação multifator

Se você implantar e executar o servidor de autenticação multifator do Azure AD, os seguintes dados pessoais serão armazenados:

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o servidor de autenticação multifator para novas implantações. Novos clientes que desejarem exigir a autenticação multifator de seus usuários devem usar a autenticação multifator do Azure AD baseada em nuvem. Os clientes existentes que tiverem ativado o servidor de autenticação multifator antes de 1º de julho poderão baixar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

| Tipo de evento                           | Tipo de armazenamento de dados |
|--------------------------------------|-----------------|
| Token OATH                           | Em logs de autenticação multifator<br />Repositório de dados de relatório de atividade de autenticação multifator |
| SMS unidirecional                          | Em logs de autenticação multifator<br />Repositório de dados de relatório de atividade de autenticação multifator |
| Chamada de voz                           | Em logs de autenticação multifator<br />Repositório de dados de relatório de atividade de autenticação multifator<br />Usuários bloqueados se houver fraude relatada |
| Notificação do Microsoft Authenticator | Em logs de autenticação multifator<br />Repositório de dados de relatório de atividade de autenticação multifator<br />Usuários bloqueados se houver fraude relatada<br />Solicitações de alteração quando o token do dispositivo Microsoft Authenticator é alterado |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Dados organizacionais armazenados pela autenticação multifator do Azure AD

Os dados organizacionais são informações de nível de locatário que podiam expor a instalação da configuração ou do ambiente. As configurações de locatário das seguintes portal do Azure páginas de autenticação multifator podem armazenar dados organizacionais, como limites de bloqueio ou informações de ID do chamador para solicitações de autenticação de telefone de entrada:

* Bloqueio de conta
* Alerta de fraude
* Notificações
* Configurações de chamada telefônica

E para o servidor de autenticação multifator do Azure AD, as seguintes portal do Azure páginas podem conter dados organizacionais:

* Configurações do servidor
* Desvio único
* Regras de cache
* Status do servidor de autenticação multifator

## <a name="multifactor-authentication-logs-location"></a>Local dos logs de autenticação multifator

A tabela a seguir mostra o local dos logs de serviço para nuvens públicas.

| Nuvem pública| Logs de entrada | Relatório de atividade de autenticação multifator        | Logs do serviço de autenticação multifator       |
|-------------|--------------|----------------------------------------|------------------------|
| EUA          | EUA           | EUA                                     | EUA                     |
| Europa      | Ocidental       | EUA                                     | Europa <sup>2</sup>    |
| Austrália   | Austrália    | <sup>1</sup>                         | Austrália <sup>2</sup> |

<sup>1</sup> Os logs de código OATH são armazenados na Austrália

<sup>2</sup> Os logs do serviço de autenticação multifator de chamadas de voz são armazenados nos EUA

A tabela a seguir mostra o local dos logs de serviço para nuvens soberanas.

| Nuvem soberana                      | Logs de entrada                         | Relatório de atividade de autenticação multifator (inclui dados pessoais)| Logs do serviço de autenticação multifator |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Alemanha              | Alemanha                              | EUA                            | EUA               |
| Microsoft Azure operado pela 21Vianet | China                                | EUA                            | EUA               |
| Nuvem do Microsoft governamental           | EUA                                   | EUA                            | EUA               |

Os dados do relatório de atividade de autenticação multifator contêm dados pessoais, como nome principal do usuário (UPN) e número de telefone completo.

Os logs do serviço de autenticação multifator são usados para operar o serviço.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre quais informações de usuário são coletadas pela autenticação multifator do Azure AD baseada em nuvem e pelo servidor de autenticação multifator do Azure AD, consulte [coleta de dados de usuário de autenticação multifator](howto-mfa-reporting-datacollection.md)do Azure AD.
