---
title: Solucionar problemas de políticas personalizadas no Azure Active Directory B2C
description: Saiba mais sobre as abordagens para a solução de erros ao trabalhar com políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 87ea77540e5fcaac0c4231403473d25ebae46aac
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840246"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Solucionar problemas de políticas personalizadas do Azure AD B2C e da Estrutura de Experiência de Identidade

Se você usar as políticas personalizadas do Azure AD B2C (Azure Active Directory B2C), poderá haver desafios ao configurar a Estrutura de Experiência de Identidade em seu formato XML de linguagem de política. Aprender a escrever políticas personalizadas pode ser como aprender uma nova linguagem. Neste artigo, descrevemos algumas ferramentas e dicas que podem ajudá-lo a descobrir e resolver problemas.

Este artigo concentra-se na solução de problemas da configuração de política personalizada do Azure AD B2C. Ele não aborda o aplicativo de terceira parte confiável ou sua biblioteca de identidade.

## <a name="xml-editing"></a>Edição de XML

O erro mais comum na configuração de políticas personalizadas é XML com erro de formatação. Um bom editor de XML é praticamente essencial. Ele exibe o XML nativamente, o conteúdo de códigos de cor, preenche previamente os termos comuns, mantém os elementos XML indexados e pode validar em um esquema XML.

Dois de nossos editores favoritos são [Visual Studio Code](https://code.visualstudio.com/) e o [bloco de notas + +](https://notepad-plus-plus.org/).

A validação de esquema XML identifica os erros antes do upload do arquivo XML. Na pasta raiz do [pacote inicial](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack), obtenha o arquivo de definição de esquema XML *TrustFrameworkPolicy_0.3.0.0. xsd*. Para saber como usar o arquivo de esquema XSD para validação em seu editor, procure *ferramentas XML* e validação de *XML* ou semelhante na documentação do editor.

Talvez seja útil examinar as regras de XML. O Azure AD B2C rejeita qualquer erro de formatação de XML que detecta. Ocasionalmente, um XML formatado incorretamente pode causar mensagens de erros falsos.

## <a name="upload-policies-and-policy-validation"></a>Políticas de upload e validação de política

A validação do arquivo de política XML é executada automaticamente no carregamento. A maioria dos erros fazem com que o upload falhe. A validação inclui o arquivo de política que você está carregando. Ela também inclui a cadeia de arquivos à qual o arquivo de upload se refere (o arquivo de política de terceira parte confiável, o arquivo de extensões e o arquivo base).

Os erros comuns de validação incluem o seguinte:

> Snippet de código de erro: `...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* O valor de ClaimType pode estar incorreto ou não existir no esquema.
* Os valores de ClaimType devem ser definidos em pelo menos um dos arquivos na política.
    Por exemplo: `<ClaimType Id="issuerUserId">`
* Se ClaimType for definido no arquivo de extensões, mas também for usado em um valor de TechnicalProfile no arquivo de base, o upload do arquivo base resultará em um erro.

> Snippet de código de erro: `...makes a reference to a ClaimsTransformation with id...`

* As causas desse erro podem ser as mesmas para o erro ClaimType.

> Snippet de código de erro: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* Verifique se o valor de Tenantid nos `<TrustFrameworkPolicy\>` `<BasePolicy\>` elementos e corresponde ao seu locatário de Azure ad B2C de destino.

## <a name="troubleshoot-the-runtime"></a>Solucionar problemas de runtime

* Use **executar agora** e `https://jwt.ms` para testar suas políticas independentemente de seu aplicativo Web ou móvel. Este site funciona como um aplicativo de terceira parte confiável. Ele exibe o conteúdo do JSON Web token (JWT) que é gerado pela sua política de Azure AD B2C.

    Para criar um aplicativo de teste que possa ser redirecionado para `https://jwt.ms` para inspeção de token:

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* Para rastrear a troca de mensagens entre o navegador do cliente e o Azure AD B2C, use [Fiddler](https://www.telerik.com/fiddler). Ele pode ajudá-lo a obter uma indicação de onde sua jornada de usuário está falhando nas etapas de orquestração.

* No **Modo de desenvolvimento**, use o [Application Insights](troubleshoot-with-application-insights.md) para rastrear a atividade da sua jornada de usuário da Estrutura de Experiência de Identidade. No **modo de desenvolvimento**, você pode observar a troca de declarações entre a estrutura de experiência de identidade e os vários provedores de declarações definidos por perfis técnicos, como provedores de identidade, serviços baseados em API, o Azure ad B2C diretório de usuário e outros serviços, como a autenticação multifator do Azure AD.

## <a name="recommended-practices"></a>Práticas recomendadas

**Mantenha várias versões de seus cenários. Agrupe-os em um projeto com seu aplicativo.** A base, extensões e os arquivos de terceira parte confiável são diretamente dependentes entre si. Salve-os como um grupo. Conforme novos recursos são adicionados nas políticas, mantenha versões de trabalho separadas. Prepare as versões de trabalho em seu próprio sistema de arquivos com o código do aplicativo com o qual eles interagem. Os aplicativos podem invocar várias políticas de terceira parte confiável diferentes em um locatário. Eles podem se tornar dependentes das declarações que esperam de suas políticas do Azure AD B2C.

**Desenvolva e teste perfis técnicos com jornadas conhecidos do usuário.** Use políticas de pacote inicial testadas para configurar os perfis de técnicos. Teste-as separadamente antes de incorporá-las em suas próprias jornadas de usuário.

**Desenvolva e teste jornadas de usuário com perfis técnicos testados.** Altere as etapas de orquestração de uma jornada de usuário de forma incremental. Crie os seus cenários desejados de forma progressiva.

## <a name="next-steps"></a>Próximas etapas

Disponível no GitHub, baixe o arquivo [Active-Directory-B2C-Custom-Policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) . zip. Você também pode clonar o repositório:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
