---
title: Definir atributos personalizados no Azure Active Directory B2C | Microsoft Docs
description: Defina os atributos personalizados para o seu aplicativo no Azure Active Directory B2C para coletar informações sobre seus clientes.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a9ac3800d60b063c620cfc774d7a0c642f6f6821
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835388"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definir atributos personalizados no Azure Active Directory B2C

 Todo aplicativo voltado para o cliente tem requisitos exclusivos para as informações que precisam ser coletadas. O locatário B2C do seu Azure Active Directory (Azure AD) é fornecido com um conjunto interno de informações armazenadas em atributos, como Nome, Sobrenome, Cidade e CEP. Com o Azure AD B2C, você pode estender o conjunto de atributos armazenados em cada conta de cliente.

 Você pode criar atributos personalizados no [portal do Azure](https://portal.azure.com/) e usá-los em seus fluxos dos usuários de inscrição ou entrada ou de edição de perfil. Você também pode ler e gravar esses atributos usando a [API do Graph do Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md). Os atributos personalizados no Azure AD B2C usam as [Extensões de Esquema de Diretório da API do Graph do Azure AD](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

> [!NOTE]
> Suporte para mais recente [API do Microsoft Graph](https://docs.microsoft.com/graph/overview?view=graph-rest-1.0) para consultar o Azure AD B2C locatário ainda está em desenvolvimento.
>

## <a name="create-a-custom-attribute"></a>Como criar um atributo personalizado

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C alternando para ele no canto superior direito do portal do Azure. Selecione as informações da sua assinatura e depois selecione **Alternar diretório**.

    ![Alternar para seu locatário do Azure AD B2C](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    Escolha o diretório que contém seu locatário.

    ![Locatário B2C realçado no filtro de diretório e assinatura](./media/active-directory-b2c-reference-custom-attr/select-directory.PNG)

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **Atributos de usuário** e, em seguida, selecione **Adicionar**.
5. Forneça um **Nome** para o atributo personalizado (por exemplo, "ShoeSize")
6. Escolha um **Tipo de Dados**. Somente **Cadeia de Caracteres**, **Booliano** e **Int** estão disponíveis.
7. Opcionalmente, insira uma **Descrição** para fins informativos.
8. Clique em **Criar**.

O atributo personalizado agora está disponível na lista de **Atributos do usuário** e para uso nos fluxos dos usuários. Um atributo personalizado só é criado na primeira vez que é usado em qualquer fluxo de usuário, e não quando você o adiciona à lista de **Atributos de usuário**.


## <a name="use-a-custom-attribute-in-your-user-flow"></a>Usar um atributo personalizado em sua política

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
2. Selecione sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
4. Selecione **Atributos de inscrição** e, em seguida, selecione o atributo personalizado (por exemplo, "ShoeSize"). Clique em **Salvar**.
5. Selecione **Declarações de aplicativo** e selecione o atributo personalizado.
6. Clique em **Salvar**.

Depois de criar um novo usuário usando um fluxo de usuário que usa o atributo personalizado criado recentemente, o objeto pode ser consultado no [do Azure AD Graph Explorer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart). Como alternativa, você pode usar o [ **executar fluxo de usuário** ](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) recurso no fluxo de usuário para verificar a experiência do cliente. Agora você deve ver **ShoeSize** na lista de atributos coletados durante a jornada de inscrição, e vê-lo no token enviado de volta ao seu aplicativo.

