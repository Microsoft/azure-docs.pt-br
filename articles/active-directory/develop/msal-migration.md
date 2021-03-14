---
title: Migrar para a biblioteca de autenticação da Microsoft (MSAL)
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as diferenças entre a MSAL (biblioteca de autenticação da Microsoft) e a ADAL (biblioteca de autenticação do Azure AD) e como migrar para o MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/07/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 6517cdd7aafa7ae2fe351b349e62a66104469dcd
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653772"
---
# <a name="migrate-applications-to-the-microsoft-authentication-library-msal"></a>Migrar aplicativos para a biblioteca de autenticação da Microsoft (MSAL)

Muitos desenvolvedores têm aplicativos criados e implantados usando a ADAL (biblioteca de autenticação de Azure Active Directory). Agora é recomendável usar a MSAL (biblioteca de autenticação da Microsoft) para autenticação e autorização de entidades do Azure AD.

Usando MSAL em vez de ADAL:

- Você pode autenticar um conjunto mais amplo de identidades:
  - Identidades do Azure AD
  - Contas da Microsoft
  - Contas sociais e locais usando Azure AD B2C
- Os usuários terão a melhor experiência de logon único.
- Seu aplicativo pode habilitar o consentimento incremental.
- O suporte ao acesso condicional é mais fácil.
- Você se beneficia da inovação. Como todos os esforços de desenvolvimento da Microsoft agora estão concentrados no MSAL, nenhum recurso novo será implementado na ADAL.

**MSAL agora é a biblioteca de autenticação recomendada para uso com a plataforma de identidade da Microsoft**.

## <a name="migration-guidance"></a>Guia de migração

Os artigos a seguir podem ajudá-lo a migrar para o MSAL:

- [Migrar para a MSAL.Android](migrate-android-adal-msal.md)
- [Migrar para a MSAL.iOS/macOS](migrate-objc-adal-msal.md)
- [Migrar para a MSAL Java](migrate-adal-msal-java.md)
- [Migrar para o MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Migrar para o MSAL.NET](msal-net-migration.md)
- [Migrar para a MSAL Python](migrate-python-adal-msal.md)
- [Migrar aplicativos Xamarin usando agentes para o MSAL.NET](msal-net-migration-ios-broker.md)

## <a name="frequently-asked-questions-faq"></a>Perguntas frequentes

__P: a ADAL está sendo preterida?__  
A: Sim. A partir de 30 de junho de 2020, não adicionaremos mais novos recursos à ADAL. Continuaremos adicionando correções de segurança críticas à ADAL até 30 de junho de 2022. Após essa data, seus aplicativos que usam a ADAL continuarão a funcionar, mas é recomendável atualizar para o MSAL para aproveitar os recursos mais recentes e permanecer seguro.

__P: meus aplicativos ADAL existentes deixarão de funcionar?__  
R: Não. Seus aplicativos existentes continuarão funcionando sem modificação. Se você estiver planejando mantê-los além de 30 de junho de 2022, considere atualizar seus aplicativos para MSAL para mantê-los seguros, mas migrar para o MSAL não é necessário para manter a funcionalidade existente.

__P: Como fazer saber qual dos meus aplicativos está usando a ADAL?__  
R: se você tiver o código-fonte para o aplicativo, poderá fazer referência aos guias de migração acima para ajudar a determinar qual biblioteca o aplicativo usa e como migrá-lo para o MSAL. Se você tiver parcerias com um ISV, sugerimos que você entre diretamente para entender sua jornada de migração para o MSAL.

__P: por que devo investir em migrar para o MSAL?__  
R: o MSAL contém novos recursos que não estão no ADAL, incluindo consentimento incremental, logon único e gerenciamento de cache de token. Além disso, ao contrário da ADAL, o MSAL continuará a receber patches de segurança além de 30 de junho de 2022. [Saiba mais](msal-overview.md).

__P: a Microsoft atualizará seus próprios aplicativos para MSAL?__  
Sim. A Microsoft está no processo de migrar seus aplicativos para o MSAL pelo prazo final do suporte, garantindo que eles se beneficiarão da segurança contínua e dos aprimoramentos de recursos do MSAL.

__P: você liberará uma ferramenta que me ajuda a mover meus aplicativos do ADAL para o MSAL?__  
R: Não. As diferenças entre as bibliotecas exigirão recursos dedicados ao desenvolvimento e à manutenção da ferramenta que, de outra forma, seriam gastas melhorando o MSAL. No entanto, fornecemos o conjunto anterior de guias de migração para ajudá-lo a fazer as alterações necessárias em seu aplicativo.

__P: como o MSAL funciona com AD FS?__  
R: o MSAL.NET dá suporte a determinados cenários para autenticação no AD FS 2019. Se seu aplicativo precisar adquirir tokens diretamente da versão anterior do AD FS, você deverá permanecer no ADAL. [Saiba mais](msal-net-adfs-support.md).

__P: Como fazer obter ajuda para migrar meu aplicativo?__  
R: consulte a seção [diretrizes de migração](#migration-guidance) deste artigo. Se, depois de ler o guia para a plataforma do seu aplicativo, você tiver outras perguntas, poderá postar no [Microsoft Q&a](/answers/topics/azure-ad-adal-deprecation.html) com a marca `[azure-ad-adal-deprecation]` ou abrir um problema no repositório GitHub da biblioteca. Consulte a seção [linguagens e estruturas](msal-overview.md#languages-and-frameworks) do artigo Visão geral do MSAL para obter links para o repositório de cada biblioteca.

## <a name="next-steps"></a>Próximas etapas

- [Atualizar seus aplicativos para usar a biblioteca de autenticação da Microsoft e a API de Microsoft Graph](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
- [Visão geral da plataforma Microsoft Identity](v2-overview.md)
- [Examine nossos exemplos de código MSAL](sample-v2-code.md)