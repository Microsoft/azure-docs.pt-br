---
title: 'Azure Active Directory B2C: atributos personalizados | Microsoft Docs'
description: "Como usar atributos personalizados no Active Directory B2C do Azure para coletar informações sobre seus consumidores"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 055ffb0a-197b-4716-8dad-1fd8a01e174f
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 356aaeff3a78fc7b682d621e8e0de9312582b2fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Azure Active Directory B2C: usar atributos personalizados para coletar informações sobre seus consumidores
O diretório do Azure AD (Azure Active Directory) B2C é fornecido com um conjunto interno de informações (atributos): Nome, Sobrenome, Cidade e CEP, entre outros atributos. No entanto, todos os aplicativos voltados para o consumidor têm requisitos exclusivos sobre quais atributos devem ser coletados dos consumidores. Com o Azure AD B2C, você pode estender o conjunto de atributos armazenados em cada conta de consumidor. Você pode criar atributos personalizados no [Portal do Azure](https://portal.azure.com/) e usá-los em suas políticas de inscrição, conforme mostrado abaixo. Você também pode ler e gravar esses atributos usando a [API do Graph do Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [!NOTE]
> Os atributos personalizados usam as [Extensões de Esquema de Diretório da API do Graph do Azure AD](https://msdn.microsoft.com/library/azure/dn720459.aspx).
> 
> 

## <a name="create-a-custom-attribute"></a>Como criar um atributo personalizado
1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. Clique em **Atributos de usuário**.
3. Clique em **+Adicionar** , na parte superior da folha.
4. Forneça um **Nome** para o atributo personalizado (por exemplo, "ShoeSize") e, opcionalmente, uma **Descrição**. Clique em **Criar**.
   
   > [!NOTE]
   > Apenas o **Tipo de Dados** "String" está disponível no momento.
   > 
   > 

O atributo personalizado agora está disponível na lista de **Atributos do usuário**e para uso em suas políticas de inscrição.

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>Usar um atributo personalizado na sua política de inscrição
1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. Clique em **Políticas de inscrição**.
3. Clique na sua política de inscrição (por exemplo, "B2C_1_SiUp") para abri-la. Clique em **Editar** na parte superior da folha.
4. Clique em **Atributos de inscrição** e selecione o atributo personalizado (por exemplo, "ShoeSize"). Clique em **OK**.
5. Clique em **Declarações de aplicativo** e selecione o atributo personalizado. Clique em **OK**.
6. Clique em **Salvar** na parte superior da folha.

Você pode usar o recurso "Executar agora" da política para verificar a experiência do consumidor. Agora você deve ver "ShoeSize" na lista de atributos que são coletados durante a inscrição do consumidor e vê-lo no token enviado de volta ao seu aplicativo.

## <a name="notes"></a>Observações
* Juntamente com as políticas de inscrição, os atributos personalizados também podem ser usados nas políticas de inscrição ou de entrada e também nas políticas de edição de perfil.
* Há uma limitação conhecida de atributos personalizados. Esse tipo de atributo só é criado na primeira vez que é usado em qualquer política, e não quando você o adiciona à lista de **Atributos de usuário**.

