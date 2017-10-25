---
title: "Experiências de entrada com o Azure AD Identity Protection | Microsoft Docs"
description: "Fornece uma visão geral da experiência do usuário quando o Identity Protection tiver atenuado ou corrigido um usuário ou quando a autenticação multifator for exigida por uma política."
services: active-directory
keywords: "azure active directory identity protection, cloud app discovery, gerenciamento de aplicativos, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: de5bf637-75a7-4104-b6d8-03686372a319
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: e45936280b51fb2e54012a688fceddcc8dabe984
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Experiências de entrada com a proteção de identidade do Azure AD
Com o Azure Active Directory Identity Protection, é possível:

* exigir o registro de usuários na autenticação multifator
* lidar com entradas arriscadas e usuários comprometidos

A resposta do sistema para esses problemas tem um impacto na experiência de entrada de um usuário, pois apenas entrar diretamente fornecendo um nome de usuário e uma senha não será mais possível. São necessárias etapas adicionais para fazer com que um usuário volte ao trabalho com segurança.

Este tópico fornece uma visão geral da experiência de entrada de um usuário em relação a todos os casos que podem ocorrer.

**Autenticação multifator**

* Registro de autenticação multifator

**Entrada em risco**

* Recuperação de entrada arriscada
* Entrada arriscada bloqueada
* Registro da autenticação multifator durante a entrada arriscada

**Usuário em risco**

* Recuperação de conta comprometida
* Conta comprometida bloqueada

## <a name="multi-factor-authentication-registration"></a>Registro de autenticação multifator
A melhor experiência do usuário para ambos, o fluxo de recuperação de conta comprometida e o fluxo de entrada arriscado, é quando o usuário pode recuperar-se automaticamente. Caso os usuários estejam registrados na autenticação multifator, eles já terão um número de telefone associado às suas contas, que pode ser usado para resolver os desafios de segurança. Não é necessário qualquer envolvimento do suporte técnico ou do administrador para recuperar-se do comprometimento da conta. Por isso, é altamente recomendado registra seus usuários para autenticação multifator. 

Os administradores podem:

* definir uma política que exige que os usuários configurem suas contas para verificação de segurança adicional. 
* permitir que o registro de autenticação multifator seja ignorado por até 30 dias, caso desejem oferecer um período de carência antes de registrar-se.

**O registro na autenticação multifator tem três etapas:**

1. Na primeira etapa, o usuário recebe uma notificação sobre a necessidade de configurar a conta para a autenticação multifator. 
   
    ![Correção](./media/active-directory-identityprotection-flows/140.png "Correção")
2. Para configurar a autenticação multifator, é necessário informar ao sistema como você deseja ser contatado.
   
    ![Correção](./media/active-directory-identityprotection-flows/141.png "Correção")
3. O sistema envia um desafio para você e você precisa respondê-lo.
   
    ![Correção](./media/active-directory-identityprotection-flows/142.png "Correção")

## <a name="risky-sign-in-recovery"></a>Recuperação de entrada arriscada
Após um administrador configurar uma política para riscos de entrada, os usuários afetados são notificados ao tentarem entrar. 

**O fluxo de entrada arriscada tem duas etapas:** 

1. O usuário é informado de que algo incomum foi detectado em sua entrada, por exemplo, a entrada a partir de um novo local, dispositivo ou aplicativo. 
   
    ![Correção](./media/active-directory-identityprotection-flows/120.png "Correção")
2. O usuário precisa provar sua identidade resolvendo um desafio de segurança. Se o usuário estiver registrado para autenticação multifator, ele precisará fazer uma viagem de ida e volta com um código de segurança para seu número de telefone. Como essa é apenas uma entrada arriscada e não uma conta comprometida, o usuário não precisará alterar a senha nesse fluxo. 
   
    ![Correção](./media/active-directory-identityprotection-flows/121.png "Correção")

## <a name="risky-sign-in-blocked"></a>Entrada arriscada bloqueada
Os administradores podem optar por definir uma política de Entrada Arriscada para bloquear os usuários ao entrar dependendo do nível de risco. Para ser desbloqueado, os usuários finais devem contatar um administrador ou o suporte técnico, ou então pode tentar entrar de um dispositivo ou local familiar. Não é possível recuperar-se sozinho com uma solução de autenticação multifator neste caso.

![Correção](./media/active-directory-identityprotection-flows/200.png "Correção")

## <a name="compromised-account-recovery"></a>Recuperação de conta comprometida
Após configurar uma política de segurança de risco do usuário, os usuários que atendem o usuário nível de risco especificado na política (sendo, portanto, considerados comprometidos) devem passar pelo fluxo de recuperação de comprometimento de usuário antes de poderem entrar. 

**O fluxo de recuperação de comprometimento do usuário tem três etapas:**

1. O usuário é informado de que a segurança da conta está em risco devido à atividade suspeita ou credenciais vazadas.
   
    ![Correção](./media/active-directory-identityprotection-flows/101.png "Correção")
2. O usuário precisa provar sua identidade resolvendo um desafio de segurança. Se o usuário estiver registrado para autenticação multifator, ele pode recuperar-se automaticamente do comprometimento. Eles precisarão fazer uma viagem de ida e volta de um código de segurança para o número de telefone. 
   
   ![Correção](./media/active-directory-identityprotection-flows/110.png "Correção")
3. Por fim, o usuário será forçado a alterar a senha após outra pessoa ter tido acesso à sua conta. 
   Veja as capturas de tela desta experiência abaixo.
   
   ![Correção](./media/active-directory-identityprotection-flows/111.png "Correção")

## <a name="compromised-account-blocked"></a>Conta comprometida bloqueada
Para desbloquear um usuário que foi bloqueado por uma política de segurança de risco do usuário, este deve contatar um administrador ou o suporte técnico. Não é possível recuperar-se sozinho com uma solução de autenticação multifator neste caso.

![Correção](./media/active-directory-identityprotection-flows/104.png "Correção")

## <a name="reset-password"></a>Redefinir senha
Caso os usuários comprometidos sejam impedidos de entrar, um administrador poderá gerar uma senha temporária para eles. Os usuários terão de alterar suas senhas durante a próxima entrada.

![Correção](./media/active-directory-identityprotection-flows/160.png "Correção")

## <a name="see-also"></a>Consulte também
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md) 

