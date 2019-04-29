---
title: Atribuir acesso a dados do Gerenciamento de Custos do Azure | Microsoft Docs
description: Este artigo orienta você pela atribuição de permissão para dados do Gerenciamento de Custos do Azure para diversos escopos de acesso.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: 24a77561d08cc3db5356dd0e931f62bf2d16406d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60577000"
---
# <a name="assign-access-to-cost-management-data"></a>Atribuir acesso a dados do Gerenciamento de Custos

Para usuários com contratos Azure Enterprise, uma combinação de permissões concedidas no portal do Azure e no portal do EA (Contrato Enterprise) define o nível de acesso de um usuário aos dados do Gerenciamento de Custos do Azure. Para os usuários com outros tipos de contas do Azure, o nível de acesso de um usuário aos dados do Gerenciamento de Custos é mais simples. Este artigo orienta você pela atribuição de acesso aos dados do Gerenciamento de Custos. Depois que a combinação de permissões é atribuída, os dados das exibições do usuário no Gerenciamento de Custos com base no escopo ao qual eles têm acesso e no escopo que eles selecionam no portal do Azure.

O escopo que um usuário seleciona é usado em todo o Gerenciamento de Custos para fornecer a consolidação de dados e para controlar o acesso a informações de custo. Ao usar escopos, os usuários não selecionam vários deles. Em vez disso, eles selecionam um escopo mais amplo que em que os escopos filho se acumulam, para depois filtrarem o que desejam exibir. A consolidação de dados é importante para entender porque algumas pessoas não devem ter acesso a um escopo pai acumulado por escopos filho.

## <a name="cost-management-scopes"></a>Escopos do Gerenciamento de Custos

O Gerenciamento de Custos dá suporte a vários tipos de contas do Azure. Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](understand-cost-mgt-data.md). O tipo de conta determina os escopos disponíveis.

### <a name="azure-ea-subscription-scopes"></a>Escopos de assinatura do Azure EA

Para exibir dados de custo de assinaturas do Azure EA, um usuário precisará ter acesso de leitura a pelo menos um ou mais dos escopos a seguir.

| **Escopo** | **Definido em** | **Acesso necessário para exibir dados** | **Configuração de pré-requisito de EA** | **Consolida os dados para** |
| --- | --- | --- | --- | --- |
| Conta de cobrança<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Administrador Corporativo | Nenhum | Todas as assinaturas do contrato empresarial |
| department | [https://ea.azure.com](https://ea.azure.com/) | Administrador de departamento | **Encargos de exibição do administrador de departamento** habilitados | Todas as assinaturas que pertencem a uma conta de registro que esteja vinculada ao departamento |
| Conta de registro<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Proprietário da conta | **Encargos de exibição do proprietário da conta** habilitados | Todas as assinaturas da conta de registro |
| Grupo de gerenciamento | [https://portal.azure.com](https://portal.azure.com/) | Leitor do Gerenciamento de Custos (ou Leitor) | **Encargos de exibição do proprietário da conta** habilitados | Todas as assinaturas abaixo do grupo de gerenciamento |
| Assinatura | [https://portal.azure.com](https://portal.azure.com/) | Leitor do Gerenciamento de Custos (ou Leitor) | **Encargos de exibição do proprietário da conta** habilitados | Todos os recursos/grupos de recursos na assinatura |
| Grupo de recursos | [https://portal.azure.com](https://portal.azure.com/) | Leitor do Gerenciamento de Custos (ou Leitor) | **Encargos de exibição do proprietário da conta** habilitados | Todos os recursos no grupo de recursos |

<sup>1</sup> A conta de cobrança é também chamada de Registro ou Contrato Enterprise.

<sup>2</sup> A conta de registro é também chamada de proprietário da conta.

O diagrama a seguir ilustra o relacionamento entre escopos de Gerenciamento de Custos com as funções e configurações do portal do EA.

![Diagrama mostrando a relação entre escopos de Gerenciamento de Custos com as funções e configurações do portal do EA](./media/assign-access-acm-data/scope-access-relationship-diagram.png)

Quando os **encargos de exibição de departamentos e contas** estiverem desabilitados no portal do EA, você verá uma mensagem dizendo que *os custos estão desabilitados para sua organização* ao tentar exibir os custos para departamentos e contas.

Da mesma forma, quando os **encargos de exibição de AO** estiverem desabilitados no portal do EA, você verá uma mensagem dizendo que *os custos estão desabilitados para sua organização* ao tentar exibir os custos para contas de registro, grupos de gerenciamento, assinaturas e grupos de recursos.

## <a name="other-azure-account-scopes"></a>Outros escopos da conta do Azure

Para exibir dados de custo de outras assinaturas do Azure, um usuário precisará ter acesso de leitura a pelo menos um ou mais dos seguintes escopos:

- Conta do Azure
- Grupo de gerenciamento
- Grupo de recursos

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Habilitar o acesso aos custos no portal do EA

O escopo do departamento requer as **Cobranças da visualização DA** opção **Habilitada** no portal da EA. Todos os outros escopos requerem a opção **Encargos de exibição do proprietário da conta** **Habilitada** no portal do EA.

Para habilitar uma opção:

1. Entrar no portal do EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. No painel esquerdo, selecione **Gerenciar**.
3. Para os escopos do Gerenciamento de Custos aos quais você deseja permitir acesso, habilite a opção de cobrança **Encargos de exibição do administrador de departamento** e/ou **Encargos de exibição do proprietário da conta**.  
    ![Guia Registro, mostrando as opções de encargos de exibição do administrador de departamento e do proprietário da conta](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Depois que as opções de cobrança de exibição estiverem ativadas, a maioria dos escopos também exigirá configuração de permissão de controle de acesso baseado em função (RBAC) no portal do Microsoft Azure.

## <a name="enterprise-administrator-role"></a>Função de administrador corporativo

Por padrão, um administrador corporativo tem acesso à conta de cobrança (Contrato Enterprise/registro) e a todos os outros escopos, que são escopos-filho. O administrador corporativo atribui acesso a escopos para outros usuários. Como prática recomendada para continuidade dos negócios, você deve sempre ter dois usuários com acesso de administrador corporativo. As seções a seguir são exemplos passo a passo do administrador corporativo atribuindo acesso a escopos para outros usuários.

## <a name="assign-billing-account-scope-access"></a>Atribuir acesso ao escopo da conta de cobrança

O acesso ao escopo da conta de cobrança requer permissão de administrador corporativo no portal do EA. O administrador corporativo tem acesso para exibir os custos em todo registro de EA ou em vários registros. Nenhuma ação é necessária no portal do Azure para o escopo da conta de cobrança.

1. Entrar no portal do EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. No painel esquerdo, selecione **Gerenciar**.
3. Na guia **registro**, selecione o registro que você deseja gerenciar.  
    ![selecione seu registro no portal do EA](./media/assign-access-acm-data/ea-portal.png)
4. Clique em **+ Adicionar Administrador**.
5. Na caixa Adicionar Administrador, selecione o tipo de autenticação e digite o endereço de email do usuário.
6. Se o usuário deve ter acesso somente leitura aos dados de uso e de custo, em **Somente leitura**, selecione **Sim**.  Caso contrário, selecione **Não**.
7. Clique em **Adicionar** para criar a conta.  
    ![informações de exemplo mostradas na caixa Adicionar administrador](./media/assign-access-acm-data/add-admin.png)

Pode levar até 30 minutos antes que o novo usuário possa acessar dados no Gerenciamento de Custos.

### <a name="assign-department-scope-access"></a>Atribuir acesso ao escopo do departamento

O acesso ao escopo de departamento requer acesso de administrador de departamento (encargos de exibição do administrador de departamento) no portal do EA. O administrador de departamento tem acesso para exibir os dados de custos e de uso associados com um departamento ou com vários departamentos. Os dados para o departamento incluem todas as assinaturas que pertencem a uma conta de registro vinculada ao departamento. Nenhuma ação é necessária no portal do Azure.

1. Entrar no portal do EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. No painel esquerdo, selecione **Gerenciar**.
3. Na guia **registro**, selecione o registro que você deseja gerenciar.
4. Clique na guia **departamento** e, em seguida, clique em **Adicionar administrador**.
5. Na caixa Adicionar Administrador de Departamento, selecione o tipo de autenticação e depois digite o endereço de email do usuário.
6. Se o usuário deve ter acesso somente leitura aos dados de uso e de custo, em **Somente leitura**, selecione **Sim**.  Caso contrário, selecione **Não**.
7. Selecione os departamentos aos quais você deseja conceder permissão administrativa.
8. Clique em **Adicionar** para criar a conta.  
    ![insira as informações necessárias na caixa Adicionar administrador do departamento](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Atribuir acesso ao escopo da conta de registro

O acesso ao escopo da conta de registro requer acesso de proprietário da conta (encargos de exibição de proprietário da conta) no portal do EA. O proprietário da conta pode visualizar os custos e os dados de uso associados às assinaturas criadas a partir dessa conta de inscrição. Nenhuma ação é necessária no portal do Azure.

1. Entrar no portal do EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. No painel esquerdo, selecione **Gerenciar**.
3. Na guia **registro**, selecione o registro que você deseja gerenciar.
4. Clique na guia **Conta** e depois clique em **Adicionar conta**.
5. Na caixa Adicionar conta, selecione o **departamento** ao qual associar a conta ou deixe-o como não atribuído.
6. Selecione o tipo de autenticação e digite o nome da conta.
7. Digite o endereço de email do usuário e depois, opcionalmente, digite o centro de custo.
8. Clique em **Adicionar** para criar a conta.  
    ![insira as informações necessárias na caixa Adicionar conta para uma conta de registro](./media/assign-access-acm-data/add-account.png)

Depois de concluir as etapas acima, a conta de usuário se torna uma conta de inscrição no portal da empresa e pode criar inscrições. O usuário pode acessar dados de custo e uso para assinaturas que eles criam.

## <a name="assign-management-group-scope-access"></a>Atribuir acesso ao escopo do grupo de gerenciamento

O acesso a um escopo de grupo de gerenciamento requer pelo menos a permissão do Leitor de Gerenciamento de Custos (ou Leitor). Você pode configurar permissões para um grupo de gerenciamento no portal do Azure. Você deve ter pelo menos a permissão Administrador de Acesso do Usuário (ou Proprietário) para o grupo de gerenciamento para permitir o acesso de outras pessoas. E, para contas do Azure EA, você também deve habilitar a configuração **Encargos de exibição de AO** no portal do EA.

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Na barra lateral, selecione **Todos os serviços**, pesquise _Grupos de gerenciamento_ e, em seguida, selecione **Grupos de gerenciamento**.
3. Selecione o grupo de gerenciamento na hierarquia.
4. Ao lado do nome do grupo de gerenciamento, clique em **Detalhes**.
5. Selecione **Controle de Acesso (IAM)** no painel esquerdo.
6. Clique em **Adicionar**.
7. Em **Função**, selecione **Leitor do Gerenciamento de Custos**.
8. Em **Atribuir acesso a**, selecione **Usuário, grupo ou aplicativo do Azure AD**.
9. Para atribuir acesso, pesquise pelo usuário e selecione-o.
10. Clique em **Salvar**.  
    ![informações de exemplo na caixa Adicionar permissões para um grupo de gerenciamento](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Atribuir acesso ao escopo de assinatura

O acesso a uma assinatura requer pelo menos a permissão do Leitor de Gerenciamento de Custos (ou Leitor). Você pode configurar permissões para uma inscrição no portal do Azure. Você deve ter pelo menos a permissão de Administrador de Acesso do Usuário (ou Proprietário) para a assinatura para permitir o acesso de outras pessoas. E, para contas do Azure EA, você também deve habilitar a configuração **Encargos de exibição de AO** no portal do EA.

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Na barra lateral, selecione **Todos os serviços**, pesquise _assinaturas_ e, em seguida, selecione **Assinaturas**.
3. Selecione sua assinatura.
4. Selecione **Controle de Acesso (IAM)** no painel esquerdo.
5. Clique em **Adicionar**.
6. Em **Função**, selecione **Leitor do Gerenciamento de Custos**.
7. Em **Atribuir acesso a**, selecione **Usuário, grupo ou aplicativo do Azure AD**.
8. Para atribuir acesso, pesquise pelo usuário e selecione-o.
9. Clique em **Salvar**.

## <a name="assign-resource-group-scope-access"></a>Atribuir acesso ao escopo do grupo de recursos

O acesso a um grupo de recursos requer pelo menos a permissão do Leitor de Gerenciamento de Custos (ou Leitor). Você pode configurar permissões para um grupo de recursos no portal do Azure. Você deve ter pelo menos a permissão Administrador de Acesso do Usuário (ou Proprietário) para o grupo de recursos para permitir o acesso de outras pessoas. E, para contas do Azure EA, você também deve habilitar a configuração **Encargos de exibição de AO** no portal do EA.

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Na barra lateral, selecione **Todos os serviços**, pesquise _Grupos de recursos_ e, em seguida, selecione **Grupos de recursos**.
3. Selecione o grupo de recursos.
4. Selecione **Controle de Acesso (IAM)** no painel esquerdo.
5. Clique em **Adicionar**.
6. Em **Função**, selecione **Leitor do Gerenciamento de Custos**.
7. Em **Atribuir acesso a**, selecione **Usuário, grupo ou aplicativo do Azure AD**.
8. Para atribuir acesso, pesquise pelo usuário e selecione-o.
9. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não concluiu o primeiro início rápido de Gerenciamento de Custos, leia-o em [Iniciar a análise dos custos](quick-acm-cost-analysis.md).
