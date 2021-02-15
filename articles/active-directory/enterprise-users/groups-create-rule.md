---
title: Criar ou editar um grupo dinâmico e obter o status-Azure AD | Microsoft Docs
description: Como criar ou atualizar uma regra de associação de grupo no portal do Azure e verificar seu status de processamento.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a25caa3b59d242b8dcc8a52e1a5d684815cf7bf
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547773"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Criar ou atualizar um grupo dinâmico no Azure Active Directory

No Azure Active Directory (AD do Azure), você pode usar regras para determinar a associação de grupo com base nas propriedades do usuário ou do dispositivo. Este artigo informa como configurar uma regra para um grupo dinâmico no portal do Azure.
A associação dinâmica tem suporte para grupos de segurança ou grupos de Microsoft 365. Quando uma regra de associação de grupo é aplicada, os atributos de usuário e de dispositivo são avaliados para correspondências com a regra de associação. Quando um atributo é alterado para um usuário ou dispositivo, todas as regras de grupo dinâmico na organização são processadas para alterações de associação. Os usuários e dispositivos serão adicionados ou removidos se atenderem às condições de um grupo. Os grupos de segurança podem ser usados para dispositivos ou usuários, mas Microsoft 365 grupos só podem ser grupos de usuários. O uso de grupos dinâmicos requer a licença do Azure AD Premium P1. Consulte [regras de associação dinâmica para grupos](./groups-dynamic-membership.md) para obter mais detalhes. 

## <a name="rule-builder-in-the-azure-portal"></a>Construtor de regras no portal do Azure

O Azure AD fornece um construtor de regras para criar e atualizar suas regras importantes mais rapidamente. O construtor de regras dá suporte à construção de até cinco expressões. O construtor de regras torna mais fácil formar uma regra com algumas expressões simples, no entanto, ela não pode ser usada para reproduzir todas as regras. Se o construtor de regras não oferecer suporte à regra que você deseja criar, você poderá usar a caixa de texto.

Aqui estão alguns exemplos de regras ou sintaxe avançadas para as quais recomendamos que você construa usando a caixa de texto:

- Regra com mais de cinco expressões
- A regra de Subordinados diretos
- Como definir a [precedência do operador](groups-dynamic-membership.md#operator-precedence)
- [Regras com expressões complexas](groups-dynamic-membership.md#rules-with-complex-expressions); por exemplo `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> O construtor de regras pode não conseguir exibir algumas regras construídas na caixa de texto. Você poderá ver uma mensagem quando o construtor de regras não puder exibir a regra. O construtor de regras não altera a sintaxe com suporte, a validação nem o processamento de regras de grupo dinâmicas de modo algum.

![Captura de tela que mostra a página "regras de associação dinâmica" com a ação "Adicionar expressão" na guia "configurar regras" selecionada.](./media/groups-create-rule/update-dynamic-group-rule.png)

Para obter exemplos de sintaxe, propriedades com suporte, operadores e valores para uma regra de associação, consulte [regras de associação dinâmica para grupos no Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Para criar uma regra de associação de grupo

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com uma conta que esteja na função administrador global, administrador do Intune ou administrador de usuários na organização do Azure AD.
1. Procure e selecione **grupos**.
1. Selecione **Todos os grupos** e selecione **Novo grupo**.

   ![Selecione o comando para adicionar um novo grupo](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. Na página **grupo** , insira um nome e uma descrição para o novo grupo. Selecione um **tipo de associação** para usuários ou dispositivos e, em seguida, selecione **Adicionar consulta dinâmica**. O construtor de regras dá suporte a até cinco expressões. Para adicionar mais de cinco expressões, você deve usar a caixa de texto.

   ![Captura de tela que mostra a página "todos os grupos" com a ação "novo grupo" selecionada.](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Para ver as propriedades de extensão personalizadas disponíveis para sua consulta de associação:
   1. Selecionar **obter propriedades de extensão personalizadas**
   1. Insira a ID do aplicativo e, em seguida, selecione **Atualizar Propriedades**.
1. Depois de criar a regra, selecione **salvar**.
1. Selecione **criar** na página **novo grupo** para criar o grupo.

Se a regra que você inseriu não for válida, uma explicação do motivo pelo qual a regra não pôde ser processada será exibida em uma notificação do Azure no Portal. Leia com atenção para entender como corrigir a regra.

## <a name="to-update-an-existing-rule"></a>Para atualizar uma regra existente

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com uma conta que esteja na função administrador global, administrador de grupo, administrador do Intune ou administrador de usuário na organização do Azure AD.
1. Selecione **grupos**  >  **todos os grupos**.
1. Selecione um grupo para abrir seu perfil.
1. Na página perfil do grupo, selecione regras de **associação dinâmica**. O construtor de regras dá suporte a até cinco expressões. Para adicionar mais de cinco expressões, você deve usar a caixa de texto.

   ![Adicionar regra de associação a um grupo dinâmico](./media/groups-create-rule/update-dynamic-group-rule.png)

1. Para ver as propriedades de extensão personalizadas disponíveis para sua regra de associação:
   1. Selecionar **obter propriedades de extensão personalizadas**
   1. Insira a ID do aplicativo e, em seguida, selecione **Atualizar Propriedades**.
1. Depois de atualizar a regra, selecione **salvar**.

## <a name="turn-on-or-off-welcome-email"></a>Ativar ou desativar email de boas-vindas

Quando um novo grupo de Microsoft 365 é criado, uma notificação por email de boas-vindas é enviada aos usuários que são adicionados ao grupo. Posteriormente, se qualquer atributo de um usuário ou dispositivo for alterado, todas as regras dinâmicas do grupo na organização serão processadas para alterações de associação. Os usuários que são adicionados também recebem a notificação de boas-vindas. Você pode desativar esse comportamento no [Exchange PowerShell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps&preserve-view=true).

## <a name="check-processing-status-for-a-rule"></a>Verificar o status de processamento de uma regra

Veja o status do processamento de associação e a data da última atualização na página **Visão Geral** do grupo.
  
  ![exibição do status do grupo dinâmico](./media/groups-create-rule/group-status.png)

As seguintes mensagens de status podem ser mostradas para o status **Processamento de associação**:

- **Avaliando**: a alteração do grupo foi recebida e as atualizações estão sendo avaliadas.
- **Processando**: as atualizações estão sendo processadas.
- **Atualização concluída**: o processamento foi concluído e todas as atualizações aplicáveis foram feitas.
- **Erro de processamento**: não foi possível concluir o processamento devido a um erro ao avaliar a regra de associação.
- **Atualização em pausa**: as atualizações da regra de associação dinâmica foram pausadas pelo administrador. MembershipRuleProcessingState é definido como "Em pausa".

As seguintes mensagens de status podem ser mostradas para o status da **Última atualização da associação**:

- &lt;**Data e hora** &gt; : a última vez em que a associação foi atualizada.
- **Em Andamento**: as atualizações estão em andamento no momento.
- **Desconhecido**: a hora da última atualização não pode ser recuperada. O grupo pode ser novo.

Se ocorrer um erro ao processar a regra de associação de um grupo específico, um alerta será mostrado na parte superior da **página Visão Geral** do grupo. Se nenhuma atualização de associação dinâmica pendente puder ser processada para todos os grupos dentro da organização por mais de 24 horas, um alerta será mostrado na parte superior de **todos os grupos**.

![Processando alertas de mensagens de erro](./media/groups-create-rule/processing-error.png)

Esses artigos fornecem mais informações sobre grupos no Azure Active Directory.

- [Consultar grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Criar um novo grupo e adicionando membros](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Gerenciar configurações de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerenciar associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerenciar regras dinâmicas para usuários em um grupo](groups-dynamic-membership.md)