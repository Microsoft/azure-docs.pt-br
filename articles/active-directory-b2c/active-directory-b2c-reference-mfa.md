---
title: "Azure Active Directory B2C: autenticação multifator | Microsoft Docs"
description: Como habilitar o Multi-Factor Authentication em aplicativos voltados para o consumidor protegidos pelo Active Directory B2C do Azure
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 62ec48ab067cf02bc8409aca6da704a5418ec270
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure Active Directory B2C: habilitar a Multi-Factor Authentication nos seus aplicativos voltados para o consumidor
O Azure AD (Azure Active Directory) B2C integra-se diretamente ao [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) para facilitar a adição de uma segunda camada de segurança para as experiências de inscrição e entrada nos seus aplicativos voltados para o consumidor. E você pode fazer isso sem escrever uma única linha de código. No momentos damos suporte à verificação por ligação telefônica e mensagem de texto. Se você já tiver criado as políticas de credenciais e de entrada, ainda poderá habilitar a Multi-Factor Authentication.

> [!NOTE]
> A Multi-Factor Authentication também pode ser habilitada durante a criação de políticas de credenciais e de entrada, não apenas na edição de políticas existentes.
> 
> 

Esse recurso ajuda os aplicativos a lidarem com cenários como os seguintes:

* Você não exige Multi-Factor Authentication para acessar um aplicativo, mas para acessar outro sim. Por exemplo, o consumidor pode entrar em um aplicativo de seguro automobilístico com uma conta local ou social, porém precisa verificar o número de telefone para poder acessar o aplicativo de seguro residencial registrado no mesmo diretório.
* Você não exige a Multi-Factor Authentication para acessar um aplicativo em geral, mas sim para acessar partes confidenciais dele. Por exemplo, o consumidor pode entrar em um aplicativo bancário com uma conta local ou social e consultar o saldo da conta, porém precisa verificar o número de telefone para poder tentar realizar uma transferência bancária.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Modificar a política de inscrição para habilitar a Multi-Factor Authentication
1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. Clique em **Políticas de inscrição**.
3. Clique na sua política de inscrição (por exemplo, "B2C_1_SiUp") para abri-la.
4. Clique em **Autenticação multifator** e ative o **Estado** colocando em **ON**. Clique em **OK**.
5. Clique em **Salvar** na parte superior da folha.

Você pode usar o recurso "Executar agora" da política para verificar a experiência do consumidor. Confirme o seguinte:

Uma conta de consumidor é criada no diretório antes da etapa de Multi-Factor Authentication. Durante a etapa, o consumidor é solicitado a fornecer seu número de telefone e a verificá-lo. Se a verificação for bem-sucedida, o número de telefone será anexado à conta de consumidor para uso posterior. Mesmo que o consumidor cancele a ação ou saia, pode ser solicitado que ele confirme um número de telefone novamente durante a próxima conexão (com a Multi-Factor Authentication habilitada).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Modificar a política de entrada para habilitar a Multi-Factor Authentication
1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. Clique em **Políticas de entrada**.
3. Clique na sua política de entrada (por exemplo, "B2C_1_SiIn") para abri-la. Clique em **Editar** na parte superior da folha.
4. Clique em **Autenticação multifator** e ative o **Estado** colocando em **ON**. Clique em **OK**.
5. Clique em **Salvar** na parte superior da folha.

Você pode usar o recurso "Executar agora" da política para verificar a experiência do consumidor. Confirme o seguinte:

Quando o consumidor entra (usando uma conta local ou social), se um número de telefone verificado está anexado à conta de consumidor, ele deve verificá-lo. Se nenhum número de telefone estiver anexado, o consumidor é solicitado a fornecer seu número de telefone e a confirmá-lo. Se a verificação for bem-sucedida, o número de telefone será anexado à conta do consumidor para uso posterior.

## <a name="multi-factor-authentication-on-other-policies"></a>Multi-Factor Authentication em outras políticas
Conforme descrito para as políticas de inscrição e entrada acima, também é possível habilitar a autenticação multifator nas políticas de inscrição ou entrada e nas políticas de redefinição de senha. Ela estará disponível em breve nas políticas de edição de perfil.

