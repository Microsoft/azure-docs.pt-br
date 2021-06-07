---
title: Exemplos de código do conector de API para fluxos dos usuários – Azure AD
description: Exemplos de código para conectores de API em fluxos de inscrição por autoatendimento para Identidades Externas do Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3995395cfd6256689bedc7a4a3c83effc65c0b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87906788"
---
# <a name="samples-for-external-identities-self-service-sign-up"></a>Exemplos para inscrição por autoatendimento de Identidades Externas

As tabelas a seguir fornecem links para exemplos de código para aproveitar as APIs Web em seus fluxos dos usuários de inscrição por autoatendimento usando [conectores de API](api-connectors-overview.md).

## <a name="api-connector-azure-function-quickstarts"></a>Guias de início rápido da Função do Azure do conector de API

| Amostra                                                                                                                          | Descrição                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Este exemplo de Função do Azure do .NET Core demonstra como limitar os inscrições a domínios de locatário específicos e validar as informações fornecidas pelo usuário. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Este exemplo de Função do Azure do Node.js demonstra como limitar os inscrições a domínios de locatário específicos e validar as informações fornecidas pelo usuário.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Este exemplo de Função do Azure do Python demonstra como limitar os inscrições a domínios de locatário específicos e validar as informações fornecidas pelo usuário.    |

<!-- \| [Java](../../azure-docs-pr/articles/active-directory/b2b/invite-internal-users.md#use-the-invitation-api-to-send-a-b2b-invitation) |  The sample below illustrates how to call the invitation API to invite an internal user as a B2B user. | -->

## <a name="custom-approval-workflows"></a>Fluxos de trabalho de aprovação personalizados

| Amostra | Descrição |
|--------| ----------- |
| [Fluxo de trabalho de aprovação manual](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connectors-approvals) | Este exemplo demonstra um fluxo de trabalho de aprovação de ponta a ponta para gerenciar a criação de conta de usuário convidado na inscrição por autoatendimento |

## <a name="identity-verification"></a>Verificação de identidade

| Amostra                                                                                                            | Descrição                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | Este exemplo mostra como verificar uma identidade de usuário como parte de sua inscrição por autoatendimento usando um conector de API para integrar ao IDology. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | Este exemplo mostra como verificar uma identidade de usuário como parte de sua inscrição por autoatendimento usando um conector de API para integrar ao Experian. |
