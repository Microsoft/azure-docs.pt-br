---
title: Sobre conectores de API no Azure AD B2C
description: Use os conectores de API do Azure Active Directory (AD do Azure) para personalizar e estender seus fluxos de usuário de inscrição usando APIs da Web.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: 94d6b0192b014396f8751e58f5620aec5c132203
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92503871"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows"></a>Usar conectores de API para personalizar e estender fluxos de usuário de inscrição

> [!IMPORTANT]
> Os conectores de API para inscrição são um recurso de visualização pública do Azure AD B2C. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Visão geral 
Como desenvolvedor ou administrador de ti, você pode usar conectores de API para integrar seus fluxos de usuário de inscrição com APIs Web para personalizar a experiência de inscrição. Por exemplo, com conectores de API, você pode:

- **Validar os dados de entrada do usuário**. Validar contra dados de usuário malformados ou inválidos. Por exemplo, você pode validar dados fornecidos pelo usuário em relação a dados existentes em um repositório de dados externo ou lista de valores permitidos. Se for inválido, você poderá solicitar que um usuário forneça dados válidos ou impedir que o usuário continue o fluxo de inscrição.
- **Integre com um fluxo de trabalho de aprovação personalizado**. Conecte-se a um sistema de aprovação personalizado para gerenciar e limitar a criação da conta.
- **Substituir atributos de usuário**. Reformate ou atribua um valor a um atributo coletado do usuário. Por exemplo, se um usuário inserir o nome com todas as letras maiúsculas ou minúsculas, você poderá formatar o nome usando somente a primeira letra maiúscula. 
- **Execute a verificação de identidade**. Use um serviço de verificação de identidade para adicionar um nível extra de segurança às decisões de criação de conta.
- **Executar a lógica de negócios personalizada**. Você pode disparar eventos downstream em seus sistemas de nuvem para enviar notificações por push, atualizar bancos de dados corporativos, gerenciar permissões, auditar bancos de dados e executar outras ações personalizadas.

Um conector de API fornece Azure Active Directory com as informações necessárias para chamar uma API, incluindo uma URL de ponto de extremidade e autenticação. Depois de configurar um conector de API, você pode habilitá-lo para uma etapa específica em um fluxo de usuário. Quando um usuário atinge essa etapa no fluxo de inscrição, o conector de API é invocado e materializa como uma solicitação HTTP POST para sua API, enviando informações do usuário ("declarações") como pares de chave-valor em um corpo JSON. A resposta da API pode afetar a execução do fluxo do usuário. Por exemplo, a resposta da API pode impedir que um usuário se inscreva, pedir que o usuário insira novamente as informações ou substitua e acrescente atributos de usuário.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Onde você pode habilitar um conector de API em um fluxo de usuário

Há dois locais em um fluxo de usuário onde você pode habilitar um conector de API:

- Depois de entrar com um provedor de identidade
- Antes de criar o usuário

> [!IMPORTANT]
> Em ambos os casos, os conectores de API são invocados durante **a inscrição**do usuário, não entrem.

### <a name="after-signing-in-with-an-identity-provider"></a>Depois de entrar com um provedor de identidade

Um conector de API nesta etapa no processo de inscrição é invocado imediatamente depois que o usuário é autenticado com um provedor de identidade (como o Google, Facebook, & Azure AD). Esta etapa precede a **_página coleção de atributos_**, que é o formulário apresentado ao usuário para coletar atributos de usuário. Esta etapa não será invocada se um usuário estiver se registrando com uma conta local. Veja a seguir exemplos de cenários de conector de API que você pode habilitar nesta etapa:

- Use o email ou a identidade federada que o usuário forneceu para pesquisar declarações em um sistema existente. Retorne essas declarações do sistema existente, preencha previamente a página de coleção de atributos e torne-as disponíveis para retornar no token.
- Implemente uma lista de permissões ou de bloqueio com base na identidade social.

### <a name="before-creating-the-user"></a>Antes de criar o usuário

Um conector de API nesta etapa no processo de inscrição é invocado após a página de coleção de atributos, se um for incluído. Essa etapa é sempre invocada antes de uma conta de usuário ser criada. Veja a seguir exemplos de cenários que você pode habilitar neste ponto durante a inscrição:

- Valide os dados de entrada do usuário e peça a um usuário para reenviar os dados.
- Bloquear uma inscrição de usuário com base nos dados inseridos pelo usuário.
- Execute a verificação de identidade.
- Consultar sistemas externos para obter dados existentes sobre o usuário para retorná-los no token do aplicativo ou armazená-los no Azure AD.


## <a name="next-steps"></a>Próximas etapas
- Saiba como [Adicionar um conector de API a um fluxo de usuário](add-api-connector.md)
- Comece com nossos [exemplos](code-samples.md#api-connectors).
<!-- - Learn how to [add a custom approval system to self-service sign-up](add-approvals.md) -->