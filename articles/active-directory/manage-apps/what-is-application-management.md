---
title: Gerenciando aplicativos com o Azure Active Directory | Microsoft Docs
description: Este artigo descreve os benefícios da integração do Active Directory do Azure com seus aplicativos locais, de nuvem e SaaS.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 06/05/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d1ebb4b094ab5c03343486e7af6624273288453
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68421211"
---
# <a name="application-management-with-azure-active-directory"></a>Gerenciamento de aplicativo com o Azure Active Directory

O Azure AD (Azure Active Directory) simplifica a maneira de gerenciar seus aplicativos, fornecendo um sistema de identidade única para seus aplicativos de nuvem e locais. Você pode adicionar seus aplicativos SaaS (software como serviço), aplicativos locais e aplicativos LOB (linha de negócios) ao Azure AD. Os usuários entram uma vez para acessar com segurança esses aplicativos, juntamente com o Office 365 e outros aplicativos de negócios da Microsoft. Você pode reduzir os custos administrativos [automatizando o provisionamento de usuários](user-provisioning.md). Também é possível usar a autenticação multifator e as políticas de Acesso Condicional para oferecer acesso seguro aos aplicativos.

![Diagrama que mostra aplicativos federados por meio do Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Por que gerenciar aplicativos com uma solução de nuvem?

Geralmente, as organizações têm centenas de aplicativos que os usuários precisam para trabalhar. Os usuários acessam esses aplicativos em vários dispositivos e locais. Novos aplicativos são adicionados, desenvolvidos e publicados todos os dias. Com tantos aplicativos e pontos de acesso, é mais importante do que nunca usar uma solução baseada em nuvem para gerenciar o acesso dos usuários a todos os aplicativos.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Quais tipos de aplicativos posso integrar ao Azure AD?

Há quatro tipos principais de aplicativos que você pode adicionar aos seus **Aplicativos empresariais** e gerenciar com o Azure AD:

- **Os aplicativos da Galeria do Azure AD** – o Azure AD tem uma galeria que contém milhares de aplicativos que foram pré-integrados para logon único com o Azure AD. Alguns dos aplicativos que sua organização usa provavelmente estão na galeria. [Saiba mais sobre como planejar a integração do aplicativo](plan-an-application-integration.md) ou obter etapas detalhadas de integração para aplicativos individuais nos [tutoriais de aplicativos SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/).

- **Aplicativos locais com Proxy de Aplicativo** – com o Proxy de Aplicativo do Azure AD, você pode integrar seus aplicativos Web locais com o Azure AD para dar suporte ao logon único. Os usuários finais podem acessar seus aplicativos Web locais da mesma forma que acessam o Office 365 e outros aplicativos SaaS. [Saiba como usar o Proxy de Aplicativo e como ele funciona](what-is-application-proxy.md).

- **Aplicativos desenvolvidos de forma personalizada** – ao criar seus próprios aplicativos de linha de negócios, você pode integrá-los ao Azure AD para dar suporte ao logon único. Ao registrar seu aplicativo com o Azure AD, você tem controle sobre a política de autenticação para o aplicativo. Para saber mais, confira [Orientação para desenvolvedores](developer-guidance-for-integrating-applications.md).

- **Aplicativos inexistentes na Galeria** – traga seus aplicativos! Suporte a logon único para outros aplicativos adicionando-os ao Azure AD. Você pode integrar qualquer link da Web desejado, ou qualquer aplicativo que renderiza um campo de nome de usuário e senha, dá suporte aos protocolos SAML ou OpenID Connect ou dá suporte ao SCIM. Para saber mais, confira [Configurar o logon único para aplicativos inexistente na galeria](configure-single-sign-on-non-gallery-applications.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Gerenciar os riscos com as políticas de Acesso Condicional

Combinar o SSO (logon único) do Azure AD com [Acesso Condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) aumenta a segurança no acesso aos aplicativos. As funcionalidades de segurança incluem a proteção de identidade em escala de nuvem, o controle de acesso baseado em risco, a autenticação multifator nativa e as políticas de Acesso Condicional. Essas funcionalidades permitem ter um controle granular das políticas baseadas em aplicativos ou em grupos que precisam de mais segurança.

## <a name="improve-productivity-with-single-sign-on"></a>Aumentar a produtividade com o logon único

Habilitar o SSO (logon único) em todos os aplicativos e no Office 365 aprimora a experiência de logon para os usuários atuais ao reduzir ou eliminar as solicitações de entrada. O ambiente do usuário parece mais coeso e há menos distração sem as várias solicitações ou a necessidade de gerenciar diversas senhas. O grupo de negócios pode gerenciar e aprovar o acesso por meio de uma associação dinâmica de autoatendimento. Permitir que as pessoas certas na empresa gerenciem o acesso a um aplicativo aumenta a segurança do sistema de identidade.

O SSO melhora a segurança. *Sem o logon único*, os administradores precisam criar e atualizar as contas de usuário para cada aplicativo individual, o que leva tempo. Além disso, os usuários precisam controlar várias credenciais para acessar seus aplicativos. Dessa maneira, os usuários tendem a anotar suas senhas ou usar outras soluções de gerenciamento de senhas que apresentam riscos de segurança de dados. [Leia mais sobre o logon único](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Abordar a governança e a conformidade

Com o Azure AD, é possível monitorar os acessos ao aplicativo por meio de relatórios que usam as ferramentas de SIEM (gerenciamento de eventos e informações de segurança). É possível acessar os relatórios no portal ou nas APIs. Programaticamente, é possível auditar quem tem acesso aos aplicativos e remover o acesso de usuários inativos nas revisões de acesso.

## <a name="manage-costs"></a>Gerenciar os custos

Ao migrar para o Azure AD, você economiza custos e elimina a dificuldade de gerenciar uma infraestrutura local. O Azure AD também oferece acesso de autoatendimento para aplicativos, o que economiza tempo para administradores e usuários. O logon único elimina as senhas específicas de aplicativo. Essa capacidade de iniciar sessão uma vez economiza custos relacionados com a redefinição de senha para aplicativos e acaba com a perda de produtividade ao recuperar senhas.

## <a name="next-steps"></a>Próximas etapas

- [O que é o Proxy de Aplicativo?](what-is-application-proxy.md)
- [Início Rápido: Adicionar um aplicativo de galeria ao locatário do Azure AD](add-application-portal.md)
