---
title: "Azure Active Directory B2C: autoatendimento de redefinição de senha | Microsoft Docs"
description: "Um tópico que demonstra como configurar a redefinição de senha por autoatendimento para seus consumidores no Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: curtand
ms.assetid: c87ed86e-1520-42b1-8c31-46cd44ed5310
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 0508868e3b00c5771cc26038a3dd71fde6625a84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure Active Directory B2C: configurar a redefinição de senha por autoatendimento para seus consumidores
Com o recurso de redefinição de senha por autoatendimento, seus consumidores (que tenham se registrado com contas locais) poderão redefinir as respectivas senhas por conta própria. Isso reduz significativamente a sobrecarga da equipe de suporte, principalmente se milhões de consumidores usarem seu aplicativo regularmente. Atualmente, só há suporte para usar um endereço de email verificado como um método de recuperação. Vamos incluir mais métodos de recuperação (número de telefone verificado, perguntas de segurança, etc.) futuramente.

> [!NOTE]
> Este artigo se aplica ao autoatendimento de redefinição de senha usado no contexto de uma política de entrada. Se precisar de políticas de redefinição de senha totalmente personalizáveis invocadas por meio de seu aplicativo, veja [este artigo](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

Por padrão, o diretório não terá a redefinição de senha por autoatendimento ativada. Use as etapas a seguir para ativá-la:

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com/) como o Administrador da Assinatura. Essa é a mesma conta corporativa, de estudante ou da Microsoft que você usou para criar o diretório.
2. Navegue até a extensão do Active Directory na barra de navegação do lado esquerdo.
3. Localize o diretório na guia **Diretório** e clique nele.
4. Clique na guia **Configurar** .
5. Role para baixo até a seção **Política de redefinição de senha do usuário** e alterne a opção **Usuários habilitados para redefinição de senha** para **SIM**. Observe que a opção **Endereço de Email Alternativo** está marcada; deixe-a como está.
   
    ![Redefinição de senha de autoatendimento](./media/active-directory-b2c-reference-sspr/sspr.png)
6. Na parte inferior da página, clique em **Salvar** . Pronto!

Para testar, use o recurso "Executar agora" em qualquer política de entrada que tenha contas locais como um provedor de identidade. Na página de entrada da conta local (na qual você insere um endereço de email e a senha ou um nome de usuário e a senha), clique em **Não consegue acessar sua conta?** para verificar a experiência do consumidor.

> [!NOTE]
> As páginas do autoatendimento de redefinição de senha podem ser personalizadas usando o [recurso de identidade visual da empresa](../active-directory/active-directory-add-company-branding.md).
> 
> 

