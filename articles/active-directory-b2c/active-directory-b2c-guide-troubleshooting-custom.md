---
title: "Azure Active Directory B2C: solucionar problemas de políticas personalizadas | Microsoft Docs"
description: "Saiba mais sobre as abordagens para a solução de erros ao trabalhar com políticas personalizadas no Azure Active Directory."
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: joroja
ms.openlocfilehash: 023390ba36a74217503ff8b3076ad17978fe3fb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Solucionar problemas de políticas personalizadas do Azure AD B2C e da Estrutura de Experiência de Identidade

Se você usar as políticas personalizadas do Azure AD B2C (Azure Active Directory B2C), poderá haver desafios ao configurar a Estrutura de Experiência de Identidade em seu formato XML de linguagem de política.  Aprender a escrever políticas personalizadas pode ser como aprender uma nova linguagem. Neste artigo, descreveremos ferramentas e dicas que podem ajudar a identificar e resolver problemas. 

> [!NOTE]
> Este artigo concentra-se na solução de problemas da configuração de política personalizada do Azure AD B2C. Ele não aborda o aplicativo de terceira parte confiável ou sua biblioteca de identidade.

## <a name="xml-editing"></a>Edição de XML

O erro mais comum na configuração de políticas personalizadas é XML com erro de formatação. Um bom editor de XML é praticamente essencial. Um bom editor de XML exibe XML nativamente, codifica o conteúdo por cor, preenche os termos comuns com antecedência, mantém os elementos XML indexados e pode validar no esquema. Aqui estão dois dos nossos editores de XML favoritos:

* [Visual Studio Code](https://code.visualstudio.com/)
* [Bloco de Notas++](https://notepad-plus-plus.org/)

A validação de esquema XML identifica os erros antes do upload do arquivo XML. Na pasta raiz do pacote inicial, obtenha a definição de esquema XML TrustFrameworkPolicy_0.3.0.0.xsd. Para obter mais informações, na documentação do seu editor de XML, procure *Ferramentas XML* e *Validação de XML*.

Talvez seja útil examinar as regras de XML. O Azure AD B2C rejeita qualquer erro de formatação de XML que detecta. Ocasionalmente, um XML formatado incorretamente pode causar mensagens de erros falsos.

## <a name="upload-policies-and-policy-validation"></a>Políticas de upload e validação de política

 A validação de upload de arquivo XML é automática. A maioria dos erros fazem com que o upload falhe. A validação inclui o arquivo de política que você está carregando. Ela também inclui a cadeia de arquivos à qual o arquivo de upload se refere (o arquivo de política de terceira parte confiável, o arquivo de extensões e o arquivo base). 
 
 Os erros de validação comuns incluem os seguintes.

Trecho de código de erro: `... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* O valor de ClaimType pode estar incorreto ou não existir no esquema.
* Os valores de ClaimType devem ser definidos em pelo menos um dos arquivos na política. 
    Por exemplo: ` <ClaimType Id="socialIdpUserId">`
* Se ClaimType for definido no arquivo de extensões, mas também for usado em um valor de TechnicalProfile no arquivo de base, o upload do arquivo base resultará em um erro.

Trecho de código de erro: `...makes a reference to a ClaimsTransformation with id...`
* As causas do erro podem ser as mesmas que as do erro ClaimType.

Trecho de código de erro: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* Verifique se o valor de TenantId nos elementos **\<TrustFrameworkPolicy\>** e **\<BasePolicy\>** correspondem a seu locatário de destino do Azure AD B2C.  

## <a name="troubleshoot-the-runtime"></a>Solucionar problemas de tempo de execução

* Use `Run Now` e `https://jwt.io` para testar suas políticas independentemente de seu aplicativo Web ou móvel. Este site funciona como um aplicativo de terceira parte confiável. Ele exibe o conteúdo do JWT (Token Web JSON) que é gerado pela sua política do Azure AD B2C. Para criar um aplicativo de teste na Estrutura de Experiência de Identidade, use os seguintes valores:
    * Nome: TestApp
    * Aplicativo Web/API Web: não
    * Cliente nativo: Não

* Para rastrear a troca de mensagens entre o navegador do cliente e o Azure AD B2C, use [Fiddler](http://www.telerik.com/fiddler). Ele pode ajudá-lo a obter uma indicação de onde sua jornada de usuário está falhando nas etapas de orquestração.

* No **Modo de desenvolvimento**, use o **Application Insights** para rastrear a atividade da sua jornada de usuário da Estrutura de Experiência de Identidade. No **Modo de desenvolvimento**, é possível observar a troca de declarações entre a Estrutura de Experiência de Identidade e os vários provedores de declarações definidos por perfis técnicos, como provedores de identidade, serviços baseados em API, o diretório de usuários do Azure AD B2C e outros serviços, como a autenticação multifator do Azure.  

## <a name="recommended-practices"></a>Práticas recomendadas

**Mantenha várias versões de seus cenários. Agrupe-os em um projeto com seu aplicativo.** A base, extensões e os arquivos de terceira parte confiável são diretamente dependentes entre si. Salve-os como um grupo. Conforme novos recursos são adicionados nas políticas, mantenha versões de trabalho separadas. Prepare as versões de trabalho em seu próprio sistema de arquivos com o código do aplicativo com o qual eles interagem.  Os aplicativos podem invocar várias políticas de terceira parte confiável diferentes em um locatário. Eles podem se tornar dependentes das declarações que esperam de suas políticas do Azure AD B2C.

**Desenvolver e testar perfis técnicos com jornadas de usuário conhecidas.** Use políticas de pacote inicial testadas para configurar os perfis de técnicos. Teste-as separadamente antes de incorporá-las em suas próprias jornadas de usuário.

**Desenvolva e teste jornadas de usuário com perfis técnicos testados.** Altere as etapas de orquestração de uma jornada de usuário de forma incremental. Crie os seus cenários desejados de forma progressiva.

## <a name="next-steps"></a>Próximas etapas

* No GitHub, baixe o arquivo .zip [active-directory-b2c-custom-policy-starterpack] (https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip).
