---
title: Resolver o erro de logon das ferramentas de desenvolvimento do Azure para ensinar
description: Isso descreve as ações que um aluno deve tomar se receber uma mensagem de erro ao fazer logon nas ferramentas de desenvolvimento do Azure para ensinar.
author: rymend
ms.author: rymend
ms.topic: troubleshooting
ms.service: azure-education
ms.subservice: education-hub
ms.date: 06/30/2020
ms.openlocfilehash: 276ab2cc1d79235c00ce1bb50ff9b648bb1de6ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095624"
---
# <a name="troubleshooting-student-login-issues"></a>Solucionando problemas de logon de aluno
Acessar as ferramentas de desenvolvimento do Azure para ensinar requer que um usuário tenha uma conta da Microsoft (MSA). Os alunos serão direcionados automaticamente para criar uma MSA se sua conta ainda não for uma MSA ou vinculada a um MSA. Se seu domínio estiver associado a Active Directory, todas as contas nesse domínio já serão consideradas uma MSA.

Se um aluno tentar fazer logon e receber a seguinte mensagem de erro, use uma das soluções descritas abaixo.

:::image type="content" source="media/troubleshoot-login/error-login-message.png" alt-text="Mensagem de erro de entrada." border="false":::

Há duas soluções: criar um novo conta Microsoft ou usar um conta Microsoft existente.

## <a name="create-a-new-microsoft-account"></a>Criar um novo conta Microsoft
### <a name="use-a-university-email-address"></a>Usar um endereço de email da Universidade
Se estiver fazendo logon com um endereço de email da Universidade (por exemplo, `John.Smith@university.edu` ), você precisará criar um conta Microsoft com esse endereço de email. A criação de uma conta é gratuita e leva apenas alguns minutos. Ter um conta Microsoft permitirá que você faça logon automaticamente em todos os seus produtos da Microsoft com um único nome de usuário e senha.

### <a name="use-a-personal-email-address"></a>Usar um endereço de email pessoal
Se você estiver fazendo logon com um endereço de email pessoal (por exemplo, `John.Smith@email.com` ), mas também tiver um endereço de email da Universidade, tente usar o endereço de email da Universidade. Se você fez logon anteriormente na webstore de sua instituição com um endereço de email pessoal ou não tem um endereço de email da Universidade, você precisará criar ou vincular um conta Microsoft com seu endereço de email pessoal.

## <a name="use-an-existing-microsoft-account"></a>Usar um conta Microsoft existente
Se um aluno tiver um conta Microsoft existente (por exemplo, Xbox), ele poderá conectar essa conta a uma conta das ferramentas de desenvolvimento do Azure.

1. Acesse https://account.microsoft.com.
1. Faça logon com suas credenciais de conta Microsoft.
1. Selecione **suas informações** no menu superior da faixa de opções.

1. Clique em **gerenciar como você entra na Microsoft**. Você será solicitado a verificar sua identidade. Um código de segurança será enviado por email.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in.png" alt-text="Mensagem de erro de entrada." border="false":::

1. Insira o código de segurança por email.

    :::image type="content" source="media/troubleshoot-accounts/sign-in-enter-code.png" alt-text="Mensagem de erro de entrada." border="false":::

1. Clique em **Adicionar email** à sua conta e insira seu endereço de email da Universidade.
Na próxima vez que você entrar, poderá usar seu endereço de email da Universidade para acessar suas ferramentas de desenvolvimento do Azure para ensinar.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in-add-email.png" alt-text="Mensagem de erro de entrada." border="false":::

## <a name="next-steps"></a>Próximas etapas
- [perguntas frequentes](program-faq.md)

- [Opções de suporte](program-support.md)
