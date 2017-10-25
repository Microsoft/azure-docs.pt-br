---
title: Como Integrar ao Azure Active Directory | Microsoft Docs
description: "Um guia de benefícios e recursos para integração com o Active Directory do Azure."
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 3da53852eaad7a37f19d906d2ee27b72d35c513f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrating-with-azure-active-directory"></a>Integração ao Active Directory do Azure
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

O Active Directory do Azure fornece às organizações gerenciamento de identidades de nível empresarial para aplicativos em nuvem.  A integração do AD do Azure oferece aos usuários uma experiência de entrada simplificada e ajuda seu aplicativo a manter-se em conformidade com a política de TI.

## <a name="how-to-integrate"></a>Como integrar-se
Há várias maneiras de seu aplicativo integrar-se ao AD do Azure.  Aproveite o maior ou menor número possível desses cenários, conforme apropriado para seu aplicativo.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Dê suporte ao AD do Azure como um modo de entrar no aplicativo
**Reduza a fricção de entrada e reduza os custos de suporte.** Usando o AD do Azure para entrar no aplicativo, os usuários terão um nome e uma senha a menos para lembrar.  Como desenvolvedor, você terá uma senha a menos para armazenar e proteger.  Apenas o fato de não precisar lidar com redefinições de senha, por si só, pode ser uma economia significativa.  O AD do Azure aprimora a entrada para alguns dos mais populares aplicativos de nuvem do mundo, incluindo o Office 365 e Microsoft Azure.  Com centenas de milhões de usuários de milhões de organizações, provavelmente seu usuário já está conectado ao AD do Azure.  Saiba mais sobre [como adicionar suporte ao logon do AD do Azure](active-directory-authentication-scenarios.md).

**Simplifique a inscrição para o aplicativo.**  Durante a inscrição para o aplicativo, o AD do Azure pode enviar informações essenciais sobre um usuário para que você possa preencher previamente o formulário de inscrição ou eliminá-lo completamente.  Os usuários podem inscrever-se no aplicativo usando suas respectivas contas do AD do Azure por meio de uma experiência de consentimento familiar, semelhante àquelas encontradas em mídias sociais e aplicativos móveis.  Qualquer usuário pode se inscrever e entrar em um aplicativo que é integrado ao AD do Azure sem a necessidade de envolvimento de TI.  Saiba mais sobre como [inscrever o aplicativo para logon na conta do AD do Azure](../../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md) .

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Procure por usuários, gerencie o provisionamento de usuários e controle o acesso ao aplicativo
**Procure por usuários no diretório.**  Use a Graph API para ajudar os usuários, ao convidar outras pessoas ou ao conceder acesso, a pesquisar e procurar por outras pessoas em suas organizações em vez de exigir que esses usuários digitem endereços de email.  Os usuários podem navegar usando uma interface familiar estilo livro de endereços, incluindo a exibição dos detalhes da hierarquia organizacional.  Saiba mais sobre a [API do Graph](active-directory-graph-api.md).

**Reutilize grupos do Active Directory e listas de distribuição que seu cliente já está gerenciando.**  O AD do Azure contém os grupos que seu cliente já está usando para distribuição de email e gerenciamento de acesso.  Usando a Graph API, reutilize esses grupos em vez de exigir que o cliente crie e gerencie um conjunto separado de grupos em seu aplicativo.  Informações de grupos também podem ser enviadas ao seu aplicativo em tokens de entrada.  Saiba mais sobre a [API do Graph](active-directory-graph-api.md).

**Use o AD do Azure para controlar quem tem acesso ao aplicativo.**  Os administradores e os proprietários do aplicativo no AD do Azure podem atribuir acesso a aplicativos a usuários e grupos específicos.  Usando a Graph API, você pode ler essa lista e usá-la para controlar o provisionamento e desprovisionamento de recursos e acesso dentro do aplicativo.

**Use o AD do Azure para funções com base em controle de acesso.**  Os administradores e os proprietários do aplicativo podem atribuir usuários e grupos a funções que você define quando registra seu aplicativo no AD do Azure.  Informações de função são enviadas ao aplicativo em tokens de entrada e também podem ser lidas usando a Graph API.  Saiba mais sobre [uso do AD do Azure para autorização](http://blogs.technet.com/b/ad/archive/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles.aspx).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Obtenha acesso ao perfil do usuário, calendário, email, contatos, arquivos e muito mais
**O AD do Azure é o servidor de autorização para o Office 365 e outros serviços de negócios da Microsoft.**  Se você der suporte ao AD do Azure para entrada em seu aplicativo ou suporte à vinculação de suas contas de usuário atuais para contas de usuário do AD do Azure usando OAuth 2.0, você poderá solicitar acesso de leitura e de gravação para um perfil de usuário, calendário, email, contatos, arquivos e outras informações.  Você pode gravar eventos sem nenhum contratempo ao calendário dos usuários e ler ou gravar arquivos ao OneDrive desses mesmos usuários.  Saiba mais sobre como [acessar as APIs do Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Promova o aplicativo nos Marketplaces do Azure e do Office 365
**Promova o aplicativo para milhões de organizações que já estão usando o AD do Azure.**  Usuários que pesquisam e procuram por itens nesses Marketplaces já estão usando um ou mais serviços de nuvem, tornando-os clientes qualificados do serviço de nuvem.  Saiba mais sobre como promover seu aplicativo no [Marketplace do Azure](https://azure.microsoft.com/marketplace/partner-program/).

**Quando os usuários se inscreverem no aplicativo, este será exibido no painel de acesso do AD do Azure e no lançador de aplicativo do Office 365 desses usuários.**  Os usuários poderão retornar rapidamente e facilmente ao seu aplicativo mais tarde, melhorando o envolvimento do usuário.  Saiba mais sobre o [Painel de acesso do AD do Azure](../active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Comunicação segura de dispositivo a serviço e entre serviços
**Usar o AD do Azure para gerenciamento de identidade de dispositivos e serviços reduz o volume de código que você precisa escrever e permite que a TI gerencie o acesso.**  Serviços e dispositivos podem obter tokens do AD do Azure usando OAuth e usar esses tokens para acessar APIs da Web.  Usando o AD do Azure, você pode evitar escrever código de autenticação complexo.  Como as identidades dos serviços e dispositivos são armazenadas no AD do Azure, o TI pode gerenciar chaves e a revogação em um único lugar, em vez de fazer isso separadamente no aplicativo.

## <a name="benefits-of-integration"></a>Vantagens da integração
A integração ao AD do Azure traz benefícios que não exigem que você escreva código adicional.

### <a name="integration-with-enterprise-identity-management"></a>Integração com o gerenciamento de identidades corporativas
**Ajude seu aplicativo a manter-se em conformidade com as políticas de TI.**  As organizações integram seus sistemas de gerenciamento de identidade corporativa com o AD do Azure, para que quando uma pessoa sair de uma organização, essa pessoa perca automaticamente o acesso ao seu aplicativo sem precisar que o TI realize mais etapas.  O TI pode gerenciar quem pode acessar o aplicativo e determinar quais políticas de acesso são necessárias - por exemplo, Multi-Factor Authentication - reduzindo a necessidade de escrever código para estar em conformidade com políticas corporativas complexas.  AD do Azure fornece aos administradores um log de auditoria detalhado de quem está conectado ao seu aplicativo, de modo que o TI pode controlar o uso.

**O AD do Azure estende o Active Directory para a nuvem para que o aplicativo possa integrar-se ao AD.**  Muitas organizações em todo o mundo usam o Active Directory como seu sistema principal de entrada e de gerenciamento de identidade, além de exigirem que os seus aplicativos trabalhem com o AD.  A integração com o AD do Azure integra seu aplicativo ao Active Directory.

### <a name="advanced-security-features"></a>Recursos avançados de segurança
**Multi-Factor Authentication.**  O AD do Azure fornece Multi-Factor Authentication nativa.  Os administradores de TI podem exigir Multi-Factor Authentication para acessar o aplicativo, para que você não precise codificar esse suporte por conta própria.  Saiba mais sobre [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Detecção de entrada anormal.**  O AD do Azure processa mais de um bilhão de entradas por dia, enquanto usa algoritmos de aprendizado de máquina para detectar atividades suspeitas e notifica os administradores de TI sobre possíveis problemas.  Ao oferecer suporte a entrada no AD do Azure, o aplicativo obtém o benefício dessa proteção. Saiba mais sobre a [visualização do relatório de acesso do Azure Active Directory](../active-directory-view-access-usage-reports.md).

**Acesso condicional.**  Além da Multi-Factor Authentication, os administradores podem exigir que condições específicas sejam atendidas antes que os usuários possam entrar no aplicativo.  As condições que podem ser definidas incluem o intervalo de endereços IP de dispositivos cliente, a participação em grupos especificados e o estado do dispositivo que está sendo usado para o acesso.  Saiba mais sobre o [acesso condicional ao Azure Active Directory](../active-directory-conditional-access.md).

### <a name="easy-development"></a>Desenvolvimento fácil
**Protocolos padrão da indústria.**  A Microsoft está comprometida em oferecer suporte aos padrões do setor da indústria.  O AD do Azure dá suporte aos protocolos de autenticação SAML 2.0, OpenID Connect 1.0, OAuth 2.0 e WS-Federation 1.2.  A Graph API é compatível com o OData 4.0.  Se seu aplicativo já dá suporte aos protocolos SAML 2.0 ou OpenID Connect 1.0 para entrada federada, adicionar suporte para o AD do Azure pode ser simples.  Saiba mais sobre [protocolos de autenticação com suporte no AD do Azure](active-directory-authentication-protocols.md).

**Bibliotecas de software livre.**  A Microsoft fornece bibliotecas software livre para as quais há suporte total, voltadas a plataformas e linguagens populares para acelerar o desenvolvimento.  O código-fonte está licenciado sob Apache 2.0, e você é livre para buscar alternativas e fornecer feedback, contribuindo para os projetos.  Saiba mais sobre as [bibliotecas de autenticação do AD do Azure](active-directory-authentication-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Presença mundial e alta disponibilidade
**O AD do Azure é implantado em data centers em todo o mundo e é gerenciado e monitorado ininterruptamente.**  O AD do Azure é o sistema de gerenciamento de identidade para ambos o Microsoft Azure e o Office 365, e é implantado em 28 datacenters em todo o mundo.  É garantido que dados de diretório serão replicados para pelo menos três datacenters.  Balanceadores de carga global garantem o acesso de usuários à cópia mais próxima do AD do Azure que contém seus dados e rotearão automaticamente solicitações para outros datacenters, se algum problema for detectado.

## <a name="next-steps"></a>Próximas etapas
[Introdução à escrita de código](active-directory-developers-guide.md#get-started).

[Conectar usuários usando o AD do Azure](active-directory-authentication-scenarios.md)

