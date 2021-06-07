---
title: Como integrar com a plataforma Microsoft Identity | Azure
titleSuffix: Microsoft identity platform
description: Conheça os benefícios de integrar seu aplicativo com a plataforma de identidade da Microsoft e obtenha recursos para recursos como entrada simplificada, gerenciamento de identidades, autenticação multifator e controle de acesso.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/01/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: b7ee283ff61753a060e49a3340cd0a795b04faf9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755937"
---
# <a name="integrating-with-the-microsoft-identity-platform"></a>Integração com a plataforma Microsoft Identity

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Neste artigo, você aprenderá sobre os benefícios de integrar seu aplicativo com a plataforma de identidade da Microsoft e obter recursos de integração. A plataforma de identidade da Microsoft e o Azure Active Directory (AD) fornecem às organizações o gerenciamento de identidades de nível empresarial para aplicativos em nuvem. A integração da plataforma de identidade da Microsoft oferece aos usuários uma experiência de entrada simplificada e ajuda seu aplicativo a estar em conformidade com a política de ti.

## <a name="how-to-integrate"></a>Como integrar

Há várias maneiras para seu aplicativo se integrar com a plataforma de identidade da Microsoft. Aproveite o maior ou menor número possível desses cenários, conforme apropriado para seu aplicativo.

### <a name="support-the-microsoft-identity-platform-as-a-way-to-sign-in-to-your-application"></a>Suporte à plataforma de identidade da Microsoft como uma maneira de entrar em seu aplicativo

**Reduza a fricção de entrada e reduza os custos de suporte.** Usando a plataforma de identidade da Microsoft para entrar em seu aplicativo, os usuários não terão mais um nome e senha para se lembrar. Como desenvolvedor, você terá uma senha a menos para armazenar e proteger. Apenas o fato de não precisar lidar com redefinições de senha, por si só, pode ser uma economia significativa. A plataforma de identidade da Microsoft liga para alguns dos aplicativos em nuvem mais populares do mundo, incluindo Microsoft 365 e Microsoft Azure. Com centenas de milhões de usuários de milhões de organizações, é provável que seu usuário já esteja conectado à plataforma de identidade da Microsoft. Saiba mais sobre como [Adicionar suporte para a conexão da plataforma Microsoft Identity](./authentication-vs-authorization.md).

**Simplifique a inscrição para o aplicativo.**  Durante a inscrição para seu aplicativo, a plataforma de identidade da Microsoft pode enviar informações essenciais sobre um usuário para que você possa preencher previamente seu formulário de inscrição ou eliminá-lo completamente. Os usuários podem inscrever-se no aplicativo usando suas respectivas contas do AD do Azure por meio de uma experiência de consentimento familiar, semelhante àquelas encontradas em mídias sociais e aplicativos móveis. Qualquer usuário pode se inscrever e entrar em um aplicativo integrado com a plataforma de identidade da Microsoft sem exigir envolvimento de ti. Saiba mais sobre como [inscrever o aplicativo para logon na conta do AD do Azure](../../app-service/configure-authentication-provider-aad.md) .

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Procurar usuários, gerenciar o provisionamento de usuários e controlar o acesso ao seu aplicativo

**Procure por usuários no diretório.**  Use a API Microsoft Graph para ajudar os usuários a Pesquisar e procurar outras pessoas em sua organização ao convidar outros ou conceder acesso, em vez de exigir que digitem endereços de email. Os usuários podem navegar usando uma interface familiar estilo livro de endereços, incluindo a exibição dos detalhes da hierarquia organizacional. Saiba mais sobre a [API de Microsoft Graph](/graph/overview).

**Reutilize grupos do Active Directory e listas de distribuição que seu cliente já está gerenciando.**   O AD do Azure contém os grupos que seu cliente já está usando para distribuição de email e gerenciamento de acesso. Usando a API Microsoft Graph, reutilize esses grupos em vez de exigir que o cliente crie e gerencie um conjunto separado de grupos em seu aplicativo. Informações de grupos também podem ser enviadas ao seu aplicativo em tokens de entrada. Saiba mais sobre a [API de Microsoft Graph](/graph/overview).

**Use a plataforma de identidade da Microsoft para controlar quem tem acesso ao seu aplicativo.**   Os administradores e os proprietários do aplicativo no AD do Azure podem atribuir acesso a aplicativos a usuários e grupos específicos. Usando a API de Microsoft Graph, você pode ler essa lista e usá-la para controlar o provisionamento e desprovisionamento de recursos e o acesso em seu aplicativo.

**Use a plataforma de identidade da Microsoft para o controle de acesso baseado em funções.**  Os administradores e os proprietários de aplicativos podem atribuir usuários e grupos a funções que você define ao registrar seu aplicativo na plataforma de identidade da Microsoft. As informações de função são enviadas para seu aplicativo em tokens de entrada e também podem ser lidas usando a API Microsoft Graph. Saiba mais sobre como [usar a plataforma de identidade da Microsoft para autorização](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Obter acesso ao perfil dos usuários, calendário, email, contatos, arquivos e muito mais

**A plataforma Microsoft Identity é o servidor de autorização para Microsoft 365 e outros serviços comerciais da Microsoft.**  Se você oferecer suporte à plataforma de identidade da Microsoft para entrar no seu aplicativo ou oferecer suporte à vinculação de suas contas de usuário atuais às contas de usuário do Azure AD usando o OAuth 2,0, você poderá solicitar acesso de leitura e gravação para o perfil, calendário, email, contatos, arquivos e outras informações de um usuário. Você pode gravar eventos sem nenhum contratempo ao calendário dos usuários e ler ou gravar arquivos ao OneDrive desses mesmos usuários. Saiba mais sobre [as APIs de Microsoft 365](/graph/overview).

### <a name="promote-your-application-in-the-azure-and-microsoft-365-marketplaces"></a>Promova seu aplicativo no Azure e Microsoft 365 Marketplaces

**Promova o aplicativo para milhões de organizações que já estão usando o AD do Azure.**   Usuários que pesquisam e procuram por itens nesses Marketplaces já estão usando um ou mais serviços de nuvem, tornando-os clientes qualificados do serviço de nuvem. Saiba mais sobre como promover seu aplicativo no [Marketplace do Azure](https://azure.microsoft.com/marketplace/partner-program/).

**Quando os usuários se inscrevem em seu aplicativo, eles aparecerão no painel de acesso do Azure AD e no iniciador do aplicativo Microsoft 365.**   Os usuários poderão retornar rapidamente e facilmente ao seu aplicativo mais tarde, melhorando o envolvimento do usuário. Saiba mais sobre o [Painel de acesso do AD do Azure](../user-help/my-apps-portal-end-user-access.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Comunicação segura de dispositivo para serviço e serviço a serviço

**O uso da plataforma Microsoft Identity para gerenciamento de identidade de serviços e dispositivos reduz o código que você precisa escrever e permite que ele gerencie o acesso.**  Os serviços e dispositivos podem obter tokens da plataforma Microsoft Identity usando o OAuth e usar esses tokens para acessar APIs da Web. Usando a plataforma de identidade da Microsoft, você pode evitar escrever código de autenticação complexo. Como as identidades dos serviços e dispositivos são armazenadas no AD do Azure, o TI pode gerenciar chaves e a revogação em um único lugar, em vez de fazer isso separadamente no aplicativo.

## <a name="benefits-of-integration"></a>Vantagens da integração

A integração com a plataforma Microsoft Identity vem com benefícios que não exigem que você escreva código adicional.

### <a name="integration-with-enterprise-identity-management"></a>Integração com o Enterprise Identity Management

**Ajude seu aplicativo a manter-se em conformidade com as políticas de TI.**  As organizações integram seus sistemas de gerenciamento de identidades empresariais à plataforma de identidade da Microsoft. portanto, quando uma pessoa deixa uma organização, ela perde automaticamente o acesso ao seu aplicativo sem precisar executar etapas adicionais. O TI pode gerenciar quem pode acessar o aplicativo e determinar quais políticas de acesso são necessárias - por exemplo, Multi-Factor Authentication - reduzindo a necessidade de escrever código para estar em conformidade com políticas corporativas complexas. AD do Azure fornece aos administradores um log de auditoria detalhado de quem está conectado ao seu aplicativo, de modo que o TI pode controlar o uso.

**O AD do Azure estende o Active Directory para a nuvem para que o aplicativo possa integrar-se ao AD.**   Muitas organizações em todo o mundo usam o Active Directory como seu sistema principal de entrada e de gerenciamento de identidade, além de exigirem que os seus aplicativos trabalhem com o AD. A integração com o AD do Azure integra seu aplicativo ao Active Directory.

### <a name="advanced-security-features"></a>Recursos de segurança avançados

**Autenticação multifator.**  A plataforma de identidade da Microsoft fornece autenticação multifator nativa. Os administradores de TI podem exigir Multi-Factor Authentication para acessar o aplicativo, para que você não precise codificar esse suporte por conta própria. Saiba mais sobre [a autenticação multifator](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Detecção de entrada anormal.**  A plataforma de identidade da Microsoft processa mais de um bilhão de entradas por dia, enquanto usa algoritmos de aprendizado de máquina para detectar atividades suspeitas e notificar os administradores de ti de possíveis problemas. Ao dar suporte à entrada da plataforma de identidade da Microsoft, seu aplicativo obtém o benefício dessa proteção. Saiba mais sobre a [visualização do relatório de acesso do Azure Active Directory](../reports-monitoring/overview-reports.md).

**Acesso condicional.**   Além da Multi-Factor Authentication, os administradores podem exigir que condições específicas sejam atendidas antes que os usuários possam entrar no aplicativo. As condições que podem ser definidas incluem o intervalo de endereços IP de dispositivos cliente, a participação em grupos especificados e o estado do dispositivo que está sendo usado para o acesso. Saiba mais sobre [Azure Active Directory acesso condicional](../conditional-access/overview.md).

### <a name="easy-development"></a>Desenvolvimento fácil

**Protocolos padrão da indústria.**   A Microsoft está comprometida em oferecer suporte aos padrões do setor da indústria. A plataforma de identidade da Microsoft dá suporte aos protocolos OAuth 2,0 e OpenID Connect 1,0 padrão do setor. Saiba mais sobre os [protocolos de autenticação da plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

**Bibliotecas de software livre.**   A Microsoft fornece bibliotecas software livre para as quais há suporte total, voltadas a plataformas e linguagens populares para acelerar o desenvolvimento. O código-fonte está licenciado sob Apache 2.0, e você é livre para buscar alternativas e fornecer feedback, contribuindo para os projetos. Saiba mais sobre a [MSAL (biblioteca de autenticação da Microsoft)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Presença mundial e alta disponibilidade

**O AD do Azure é implantado em data centers em todo o mundo e é gerenciado e monitorado ininterruptamente.**  O Azure AD é o sistema de gerenciamento de identidades para Microsoft Azure e Microsoft 365 e é implantado em 28 data centers em todo o mundo. É garantido que dados de diretório serão replicados para pelo menos três datacenters. Balanceadores de carga global garantem o acesso de usuários à cópia mais próxima do AD do Azure que contém seus dados e rotearão automaticamente solicitações para outros datacenters, se algum problema for detectado.

## <a name="next-steps"></a>Próximas etapas

[Introdução à escrita de código](v2-overview.md#getting-started).

[Conectar usuários usando a plataforma de identidade da Microsoft](./authentication-vs-authorization.md)