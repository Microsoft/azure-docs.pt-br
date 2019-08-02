---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 05/16/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: 6711506c1e489dcbd50aedd36241affc3bbed80b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172093"
---
### <a name="policy-for-users-in-your-directory"></a>política: Para usuários em seu diretório

Siga estas etapas se desejar que sua política para usuários e grupos no diretório que podem solicitar este pacote de acesso.

1. No **os usuários podem solicitar acesso** seção, selecione **para os usuários em seu diretório**.

1. No **selecionar usuários e grupos** seção, clique em **adicionar usuários e grupos**.

1. No painel de grupos e selecionar usuários, selecione os usuários e grupos que você deseja adicionar.

    ![Pacote de acesso - seleção de política de usuários e grupos](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. Clique em **selecionar** para adicionar os usuários e grupos.

1. Pular para a [política: Solicitar](#policy-request) seção.

### <a name="policy-for-users-not-in-your-directory"></a>política: Para que os usuários não em seu diretório

Siga estas etapas se desejar que sua política para usuários não em seu diretório que podem solicitar este pacote de acesso. Diretórios devem ser configurados para ser permitido na **restrições de colaboração de relações organizacionais** configurações.

> [!NOTE]
> Uma conta de usuário convidado será criada para um usuário ainda não existem no seu diretório cuja solicitação é aprovada ou aprovada automaticamente. O convidado será convidado, mas não receberá um email de convite. Em vez disso, eles receberão um email quando sua atribuição do pacote de acesso é entregue. Por padrão, posteriormente, quando que não seja o usuário convidado tem qualquer acesso pacote atribuições, porque sua última atribuição expirou ou foi cancelada, que conta de usuário convidado será impedida de entrar e excluída. Se você quiser manter usuários convidados em seu diretório indefinidamente, mesmo não se tiverem nenhuma atribuição de pacote de acesso, você pode alterar as configurações para a sua configuração de gerenciamento de direitos.

1. No **os usuários podem solicitar acesso** seção, selecione **para que os usuários não em seu diretório**.

1. No **Select externo do Azure AD directory** seção, clique em **adicionar diretórios**.

1. Insira um nome de domínio e pesquise externo diretório do Azure AD.

1. Verifique se que ele é o diretório correto do nome de diretório fornecido e o domínio inicial.

    > [!NOTE]
    > Todos os usuários do diretório será capazes de solicitar esse pacote de acesso. Isso inclui usuários de todos os subdomínios associados com o diretório, não apenas o domínio usado na pesquisa.

    ![Pacote de acesso - seleção de política de diretórios](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. Clique em **adicionar** para adicionar o diretório.

1. Repita essa etapa para adicionar quaisquer diretórios mais.

1. Depois de ter adicionado todos os diretórios você gostaria de incluir na política, clique em **selecionar**.

1. Pular para a [política: Solicitar](#policy-request) seção.

### <a name="policy-none-administrator-direct-assignments-only"></a>política: None (atribuições diretas administrador somente)

Siga estas etapas se desejar que sua política para ignorar as solicitações de acesso e permitem aos administradores atribuir diretamente a usuários específicos para o pacote de acesso. Os usuários não precisam solicitar o acesso de pacote. Você ainda pode definir as configurações de expiração, mas não há nenhuma configuração de solicitação.

1. No **os usuários podem solicitar acesso** seção, selecione **None (atribuições diretas administrador apenas**.

    Depois de criar o pacote de acesso, você pode atribuir usuários internos e externos específicos diretamente para o pacote de acesso. Se você especificar um usuário externo, uma conta de usuário convidado será criada em seu diretório.

1. Pular para a [política: Expiração](#policy-expiration) seção.

### <a name="policy-request"></a>política: Solicitação

Na seção Request, você especificar as configurações de aprovação quando os usuários solicitam o pacote de acesso.

1. Para solicitar aprovação para solicitações de usuários selecionados, defina as **exigir aprovação** alternar para o **Sim**. Para que as solicitações aprovadas automaticamente, defina a alternância como **não**.

1. Se você precisar de aprovação, na **Selecionar aprovadores** seção, clique em **adicione aprovadores**.

1. No painel Selecionar aprovadores, selecione um ou mais usuários e/ou grupos para ser aprovadores.

    Somente um dos aprovadores selecionados precisa aprovar uma solicitação. Não é necessária a aprovação de todos os aprovadores. A decisão de aprovação baseia-se em qualquer aprovador examina a solicitação primeiro.

    ![Pacote de acesso - seleção de política de aprovadores](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. Clique em **selecionar** para adicionar os aprovadores.

1. Clique em **Mostrar configurações de solicitação avançadas** para mostrar as configurações adicionais.

    ![Pacote de acesso - seleção de política de diretórios](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. Para exigir que os usuários forneçam uma justificativa para solicitar o acesso de pacote, defina **exigir justificativa** à **Sim**.

1. Para exigir que o aprovador fornecer uma justificativa para aprovar uma solicitação para o pacote de acesso, defina **exigir justificativa de aprovador** à **Sim**.

1. No **tempo limite de solicitação de aprovação (dias)** , especifique a quantidade de tempo que os aprovadores tem para uma solicitação de revisão. Se nenhum aprovador revisá-lo neste número de dias, a expiração da solicitação e o usuário terá que enviar outra solicitação para o pacote de acesso.

### <a name="policy-expiration"></a>política: Expiração

Na seção expiração, você especificar quando a atribuição do usuário para o pacote de acesso expira.

1. No **validade** seção, defina **pacote de acesso expira** para **na data**, **número de dias**, ou **nunca**.

    Para **data**, selecione uma data de expiração no futuro.

    Para **número de dias**, especifique um número entre 0 e 3660 dias.

    Com base em sua seleção, a atribuição do usuário para o pacote de acesso expira em uma determinada data, um determinado número de dias depois que elas são aprovadas, ou nunca.

1. Clique em **Mostrar configurações de expiração avançadas** para mostrar as configurações adicionais.

1. Para permitir que o usuário estender suas atribuições, defina **permitir que os usuários estender o acesso** à **Sim**.

    Se as extensões são permitidas na política, o usuário receberá um email 14 dias e também o 1 dia antes de sua atribuição de acesso do pacote é definida para expirar notificá-los para estender a atribuição.

    ![Pacote de acesso - configurações de política de expiração](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>política: Habilitar política

1. Se você quiser que o pacote fique imediatamente disponível para os usuários na política de acesso, clique em **Sim** para habilitar a política.

    Você sempre poderá habilitá-lo no futuro depois de concluir a criação do pacote de acesso.

    ![Pacote de acesso – habilitar política de configuração de política](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. Clique em **próxima** ou **criar**.
