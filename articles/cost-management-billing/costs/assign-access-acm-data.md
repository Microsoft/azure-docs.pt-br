---
title: Atribuir acesso a dados do Gerenciamento de Custos do Azure
description: Este artigo orienta você pela atribuição de permissão para dados do Gerenciamento de Custos do Azure para diversos escopos de acesso.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: secdec18
ms.openlocfilehash: 660b5751e3b3cbc632331e99d797af3392a8aea4
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2020
ms.locfileid: "91371959"
---
# <a name="assign-access-to-cost-management-data"></a>Atribuir acesso a dados do Gerenciamento de Custos

Para usuários com contratos Azure Enterprise, uma combinação de permissões concedidas no portal do Azure e no portal do EA (Contrato Enterprise) define o nível de acesso de um usuário aos dados do Gerenciamento de Custos do Azure. Para usuários com outros tipos de contas do Azure, definir o nível de acesso de um usuário aos dados do Gerenciamento de Custos é mais simples usando o Azure RBAC (controle de acesso baseado em função). Este artigo orienta você pela atribuição de acesso aos dados do Gerenciamento de Custos. Depois que a combinação de permissões é atribuída, os dados das exibições do usuário no Gerenciamento de Custos com base no escopo ao qual eles têm acesso e no escopo que eles selecionam no portal do Azure.

O escopo que um usuário seleciona é usado em todo o Gerenciamento de Custos para fornecer a consolidação de dados e para controlar o acesso a informações de custo. Ao usar escopos, os usuários não selecionam vários deles. Em vez disso, eles selecionam um escopo mais amplo que em que os escopos filho se acumulam, para depois filtrarem o que desejam exibir. A consolidação de dados é importante para entender por que algumas pessoas não devem ter acesso a um escopo pai acumulado por escopos filho.

Assista ao vídeo [Gerenciamento de Custos controlando acesso](https://www.youtube.com/watch?v=_uQzQ9puPyM) para saber como atribuir acesso para exibir custos e encargos com o Azure RBAC (controle de acesso baseado em função). Para assistir a outros vídeos, visite o [Canal do YouTube do Gerenciamento de Custos](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/_uQzQ9puPyM]

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
| Subscription | [https://portal.azure.com](https://portal.azure.com/) | Leitor do Gerenciamento de Custos (ou Leitor) | **Encargos de exibição do proprietário da conta** habilitados | Todos os recursos/grupos de recursos na assinatura |
| Resource group | [https://portal.azure.com](https://portal.azure.com/) | Leitor do Gerenciamento de Custos (ou Leitor) | **Encargos de exibição do proprietário da conta** habilitados | Todos os recursos no grupo de recursos |

<sup>1</sup> A conta de cobrança é também chamada de Registro ou Contrato Enterprise.

<sup>2</sup> A conta de registro é também chamada de proprietário da conta.


## <a name="other-azure-account-scopes"></a>Outros escopos da conta do Azure

Para exibir dados de custo de outras assinaturas do Azure, um usuário precisará ter acesso de leitura a pelo menos um ou mais dos seguintes escopos:

- Conta do Azure
- Grupo de gerenciamento
- Resource group

Vários escopos estão disponíveis após os parceiros integrarem clientes a um Contrato de Cliente da Microsoft. Clientes CSP podem, em seguida, usar os recursos de Gerenciamento de Custos quando eles são habilitados pelo parceiro CSP. Para obter mais informações, confira [Introdução ao Gerenciamento de Custos do Azure para parceiros](get-started-partners.md).

## <a name="enable-access-to-costs-in-the-azure-portal"></a>Habilitar o acesso aos custos no portal do Azure

O escopo do departamento requer que a opção **Os administradores do departamento podem ver os encargos** (AD ver encargos) esteja definida como **Ativo**. Configure a opção no portal do Azure ou no portal do EA. Todos os outros escopos requerem que a opção **Os proprietários da conta podem ver os encargos** (PC ver encargos) esteja definida como **Ativo**.

Para habilitar uma opção no portal do Azure:

1. Entrar no portal do Azure em https://portal.azure.com com uma conta de administrador corporativo.
1. Selecione o item de menu **Gerenciamento de Custos + Cobrança**.
1. Selecione **Escopos de cobrança** para exibir uma lista de escopos de cobrança e contas de cobrança disponíveis.
1. Selecione a sua **Conta de Cobrança** na lista de contas de cobrança disponíveis.
1. Em **Configurações**, selecione o item de menu **Políticas** e, em seguida, defina a configuração.  
    ![Políticas de escopo de cobrança mostrando as opções de exibição de cobranças](./media/assign-access-acm-data/azure-portal-policies-view-charges.png)

Depois que as opções de exibição de preço estiverem habilitadas, a maioria dos escopos também exigirá configuração de permissão do Azure RBAC (controle de acesso baseado em função) no portal do Microsoft Azure.

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Habilitar o acesso aos custos no portal do EA

O escopo do departamento requer as **Cobranças da visualização DA** opção **Habilitada** no portal da EA. Configure a opção no portal do Azure ou no portal do EA. Todos os outros escopos requerem a opção **Encargos de exibição do proprietário da conta** **Habilitada** no portal do EA.

Para habilitar uma opção no portal do EA:

1. Entrar no portal do EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. No painel esquerdo, selecione **Gerenciar**.
3. Para os escopos do Gerenciamento de Custos aos quais você deseja permitir acesso, habilite a opção de cobrança **Encargos de exibição do administrador de departamento** e/ou **Encargos de exibição do proprietário da conta**.  
    ![Guia Registro, mostrando as opções de encargos de exibição do administrador de departamento e do proprietário da conta](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Depois que as opções de exibição de preço estiverem habilitadas, a maioria dos escopos também exigirá configuração de permissão do Azure RBAC (controle de acesso baseado em função) no portal do Microsoft Azure.

## <a name="enterprise-administrator-role"></a>Função de administrador corporativo

Por padrão, um administrador corporativo tem acesso à conta de cobrança (Contrato Enterprise/registro) e a todos os outros escopos, que são escopos-filho. O administrador corporativo atribui acesso a escopos para outros usuários. Como prática recomendada para continuidade dos negócios, você deve sempre ter dois usuários com acesso de administrador corporativo. As seções a seguir são exemplos passo a passo do administrador corporativo atribuindo acesso a escopos para outros usuários.

## <a name="assign-billing-account-scope-access"></a>Atribuir acesso ao escopo da conta de cobrança

O acesso ao escopo da conta de cobrança requer permissão de administrador corporativo no portal do EA. O administrador corporativo pode exibir os custos em todo registro de EA ou em vários registros. Nenhuma ação é necessária no portal do Azure para o escopo da conta de cobrança.

1. Entrar no portal do EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. No painel esquerdo, selecione **Gerenciar**.
3. Na guia **registro**, selecione o registro que você deseja gerenciar.  
    ![selecione seu registro no portal do EA](./media/assign-access-acm-data/ea-portal.png)
4. Selecione **+ Adicionar Administrador**.
5. Na caixa Adicionar Administrador, selecione o tipo de autenticação e digite o endereço de email do usuário.
6. Se o usuário deve ter acesso somente leitura aos dados de uso e de custo, em **Somente leitura**, selecione **Sim**.  Caso contrário, selecione **Não**.
7. Selecione **Adicionar** para criar a conta.  
    ![informações de exemplo mostradas na caixa Adicionar administrador](./media/assign-access-acm-data/add-admin.png)

Pode levar até 30 minutos antes que o novo usuário possa acessar dados no Gerenciamento de Custos.

### <a name="assign-department-scope-access"></a>Atribuir acesso ao escopo do departamento

O acesso ao escopo de departamento requer acesso de administrador de departamento (encargos de exibição do administrador de departamento) no portal do EA. O administrador de departamento pode exibir os dados de custos e de uso associados com um departamento ou com vários departamentos. Os dados para o departamento incluem todas as assinaturas que pertencem a uma conta de registro vinculada ao departamento. Nenhuma ação é necessária no portal do Azure.

1. Entrar no portal do EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. No painel esquerdo, selecione **Gerenciar**.
3. Na guia **registro**, selecione o registro que você deseja gerenciar.
4. Selecione a guia **Departamento** e, em seguida, selecione **Adicionar Administrador**.
5. Na caixa Adicionar Administrador de Departamento, selecione o tipo de autenticação e depois digite o endereço de email do usuário.
6. Se o usuário deve ter acesso somente leitura aos dados de uso e de custo, em **Somente leitura**, selecione **Sim**.  Caso contrário, selecione **Não**.
7. Selecione os departamentos aos quais você deseja conceder permissão administrativa.
8. Selecione **Adicionar** para criar a conta.  
    ![insira as informações necessárias na caixa Adicionar administrador do departamento](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Atribuir acesso ao escopo da conta de registro

O acesso ao escopo da conta de registro requer acesso de proprietário da conta (encargos de exibição de proprietário da conta) no portal do EA. O proprietário da conta pode visualizar os custos e os dados de uso associados às assinaturas criadas a partir dessa conta de inscrição. Nenhuma ação é necessária no portal do Azure.

1. Entrar no portal do EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. No painel esquerdo, selecione **Gerenciar**.
3. Na guia **registro**, selecione o registro que você deseja gerenciar.
4. Selecione a guia **Conta** e depois **Adicionar Conta**.
5. Na caixa Adicionar conta, selecione o **departamento** ao qual associar a conta ou deixe-o como não atribuído.
6. Selecione o tipo de autenticação e digite o nome da conta.
7. Digite o endereço de email do usuário e depois, opcionalmente, digite o centro de custo.
8. Selecione **Adicionar** para criar a conta.  
    ![insira as informações necessárias na caixa Adicionar conta para uma conta de registro](./media/assign-access-acm-data/add-account.png)

Depois de concluir as etapas acima, a conta de usuário se torna uma conta de inscrição no portal da empresa e pode criar inscrições. O usuário pode acessar dados de custo e uso para assinaturas que eles criam.

## <a name="assign-management-group-scope-access"></a>Atribuir acesso ao escopo do grupo de gerenciamento

O acesso para exibir o escopo de grupo de gerenciamento requer pelo menos a permissão do Leitor de Gerenciamento de Custos (ou Leitor). Você pode configurar permissões para um grupo de gerenciamento no portal do Azure. Você deve ter pelo menos a permissão Administrador de Acesso do Usuário (ou Proprietário) para o grupo de gerenciamento para permitir o acesso de outras pessoas. E, para contas do Azure EA, você também deve habilitar a configuração **Encargos de exibição de AO** no portal do EA.

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Na barra lateral, selecione **Todos os serviços**, pesquise _Grupos de gerenciamento_ e, em seguida, selecione **Grupos de gerenciamento**.
3. Selecione o grupo de gerenciamento na hierarquia.
4. Ao lado do nome do grupo de gerenciamento, selecione **Detalhes**.
5. Selecione **Controle de Acesso (IAM)** no painel esquerdo.
6. Selecione **Adicionar**.
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
5. Selecione **Adicionar**.
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
5. Selecione **Adicionar**.
6. Em **Função**, selecione **Leitor do Gerenciamento de Custos**.
7. Em **Atribuir acesso a**, selecione **Usuário, grupo ou aplicativo do Azure AD**.
8. Para atribuir acesso, pesquise pelo usuário e selecione-o.
9. Clique em **Salvar**.

## <a name="cross-tenant-authentication-issues"></a>Problemas de autenticação entre locatários

Atualmente, o Gerenciamento de Custos do Azure tem suporte limitado para autenticação entre locatários. Em algumas circunstâncias, quando tenta autenticar entre locatários, você pode receber um erro de **Acesso negado** na análise de custo. Esse problema poderá ocorrer se você configurar o Azure RBAC (controle de acesso baseado em função) para a assinatura de outro locatário e, em seguida, tentar exibir dados de custo.

*Para contornar o problema*: depois de configurar o Azure RBAC entre locatários, aguarde uma hora. Em seguida, tente exibir os custos na análise de custos ou conceder acesso de Gerenciamento de Custos aos usuários em ambos os locatários.  


## <a name="next-steps"></a>Próximas etapas

- Se você ainda não concluiu o primeiro início rápido de Gerenciamento de Custos, leia-o em [Iniciar a análise dos custos](quick-acm-cost-analysis.md).
