---
title: Controles personalizados no acesso condicional do Azure AD
description: Saiba como os controles personalizados no Azure Active Directory acesso condicional funcionam.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7585c91e42b2d3591532756c1ead9ea60b7035e
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837576"
---
# <a name="custom-controls-preview"></a>Controles personalizados (versão prévia)

Os controles personalizados são uma funcionalidade de visualização do Azure Active Directory. Ao usar controles personalizados, os usuários são redirecionados para um serviço compatível para atender aos requisitos de autenticação fora do Azure Active Directory. Para atender a esse controle, o navegador de um usuário é redirecionado para o serviço externo, executa qualquer autenticação necessária e, em seguida, é Redirecionado de volta para o Azure Active Directory. Azure Active Directory verifica a resposta e, se o usuário foi autenticado ou validado com êxito, o usuário continua no fluxo de acesso condicional.

> [!NOTE]
> Para obter mais informações sobre as alterações que estamos planejando para o recurso de controle personalizado, consulte o arquivo de fevereiro de 2020 [para](../fundamentals/whats-new-archive.md#upcoming-changes-to-custom-controls)conhecer as novidades.

## <a name="creating-custom-controls"></a>Criando controles personalizados

Os controles personalizados funcionam com um conjunto limitado de provedores de autenticação aprovados. Para criar um controle personalizado, você deve entrar em contato com o provedor que você deseja utilizar. Cada provedor que não é da Microsoft tem seu próprio processo e requisitos para inscrever-se, assinar ou se tornar uma parte do serviço e indicar que você deseja integrar com o acesso condicional. Nesse ponto, o provedor lhe fornecerá um bloco de dados no formato JSON. Esses dados permitem que o provedor e o acesso condicional funcionem juntos para seu locatário, cria o novo controle e define como o acesso condicional pode informar se os usuários executaram com êxito a verificação com o provedor.

Copie os dados JSON e, em seguida, cole-os na caixa de texto relacionada. Não faça nenhuma alteração no JSON, a menos que você entenda explicitamente a alteração que está fazendo. Fazer qualquer alteração pode interromper a conexão entre o provedor e a Microsoft e potencialmente bloquear você e seus usuários do acesso às respectivas contas.

A opção para criar um controle personalizado está na seção **gerenciar** da página de **acesso condicional** .

![Interface de controles personalizados no acesso condicional](./media/controls/custom-controls-conditional-access.png)

Clicar em **Novo controle personalizado** abrirá uma folha com uma caixa de texto para os dados JSON do seu controle.  

![Novo controle personalizado](./media/controls/new-custom-controls-conditional-access.png)

## <a name="deleting-custom-controls"></a>Excluindo controles personalizados

Para excluir um controle personalizado, primeiro você deve verificar se ele não está sendo usado em nenhuma política de acesso condicional. Depois de concluído:

1. Vá para a lista de Controles personalizados
1. Clique em...  
1. Selecione **Excluir**.

## <a name="editing-custom-controls"></a>Editando controles personalizados

Para editar um controle personalizado, você deve excluir o controle atual e criar um novo controle com as informações atualizadas.

## <a name="known-limitations"></a>Limitações conhecidas

Os controles personalizados não podem ser usados com a automação da proteção de identidade que requer autenticação multifator do Azure AD, redefinição de senha de autoatendimento do Azure AD (SSPR), atendendo aos requisitos de declaração de autenticação multifator, para elevar funções no Gerenciador de identidades privilegiadas (PIM), como parte do registro de dispositivo do Intune ou ao unir dispositivos ao Azure AD

## <a name="next-steps"></a>Próximas etapas

- [Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

- [Modo somente relatório](concept-conditional-access-report-only.md)

- [Simular comportamento de entrada usando a ferramenta What If de Acesso Condicional](troubleshoot-conditional-access-what-if.md)
