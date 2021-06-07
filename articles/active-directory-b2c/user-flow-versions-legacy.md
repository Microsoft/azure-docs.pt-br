---
title: Versões de fluxo de usuário herdadas no Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre as versões herdadas dos fluxos de usuário disponíveis no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1fbe93c93b5ede2c6b031dab53a1450da473f802
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517796"
---
# <a name="legacy-user-flow-versions-in-azure-active-directory-b2c"></a>Versões de fluxo de usuário herdadas no Azure Active Directory B2C

> [!IMPORTANT]
> Este artigo descreve o método de controle de versão herdado para fluxos de usuário, que oferece versões V1 (prontas para produção) e versões V 1.1 e v2 (visualização) de fluxos de usuário. Ambientes diferentes da nuvem pública do Azure continuarão a usar esse método de controle de versão herdado. Na nuvem pública do Azure, esse método está sendo substituído por [novas versões **recomendadas** e de **Visualização**](user-flow-versions.md).
> 
O usuário flui em Azure Active Directory B2C (Azure AD B2C) ajuda a configurar políticas comuns que descrevem totalmente [as](user-flow-overview.md) experiências de identidade do cliente. Essas experiências incluem inscrição, entrada, redefinição de senha ou edição de perfil.

Na tabela a seguir, a menos que um fluxo de usuário seja identificado como **recomendado**, ele será considerado em versão *prévia*. Você deve usar somente os fluxos de usuário recomendados para seus aplicativos de produção.

## <a name="v1"></a>V1

| Fluxo de usuário | Recomendadas | Descrição |
| --------- | ----------- | ----------- |
| Redefinição de senha | Sim | Permite que um usuário escolha uma nova senha após verificar seu email. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>Configurações de compatibilidade de token</li><li>[Requisitos de complexidade de senha](password-complexity.md)</li></ul> |
| Edição de perfil | Sim | Permite que o usuário configure seus atributos de usuário. Usando este fluxo de usuário, você pode configurar: <ul><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li></ul> |
| Entrar usando ROPC | Não | Permite que um usuário com uma conta local entre diretamente em aplicativos nativos (nenhum navegador necessário). Usando este fluxo de usuário, você pode configurar: <ul><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li></ul> |
| Entrar | Não | Permite que um usuário entre em sua conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li><li>Entrar no bloco</li><li>Forçar redefinição de senha</li><li>Manter-me conectado (KMSI)</ul><br>Não é possível personalizar a interface do usuário com este fluxo de usuário. |
| Inscrição | Não | Permite que um usuário crie uma conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de senha](password-complexity.md)</li></ul> |
| Inscrever-se e entrar | Sim | Permite que um usuário crie uma conta ou entre em sua conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de senha](password-complexity.md)</li></ul>|

## <a name="v11"></a>V1.1

| Fluxo de usuário | Recomendadas | Descrição |
| --------- | ----------- | ----------- |
| Redefinição de senha v 1.1 | Não | Permite que um usuário escolha uma nova senha depois de verificar seu email (novo layout de página disponível). Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>Configurações de compatibilidade de token</li><li>[Requisitos de complexidade de senha](password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| Fluxo de usuário | Recomendadas | Descrição |
| --------- | ----------- | ----------- |
| Redefinição de senha v2 | Não | Permite que um usuário escolha uma nova senha após verificar seu email. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>Configurações de compatibilidade de token</li><li>[Restrição de idade](age-gating.md)</li><li>[requisitos de complexidade de senha](password-complexity.md)</li></ul> |
| Edição de perfil v2 | Sim | Permite que o usuário configure seus atributos de usuário. Usando este fluxo de usuário, você pode configurar: <ul><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li></ul> |
| Entrar v2 | Não | Permite que um usuário entre em sua conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li><li>[Restrição de idade](age-gating.md)</li><li>Personalização da página de entrada</li></ul> |
| Inscrever v2 | Não | Permite que um usuário crie uma conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li><li>[Restrição de idade](age-gating.md)</li><li>[Requisitos de complexidade de senha](password-complexity.md)</li></ul> |
| Inscrever-se e entrar v2 | Não | Permite que um usuário crie uma conta ou entre em sua conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Restrição de idade](age-gating.md)</li><li>[Requisitos de complexidade de senha](password-complexity.md)</li></ul> |