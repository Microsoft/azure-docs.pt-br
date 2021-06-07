---
title: Noções básicas sobre as funções de administrador do EA (Contratos Enterprise) no Azure
description: Aprenda sobre as funções de administrador corporativo no Azure. Você pode atribuir cinco funções administrativas distintas.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 1ceed171b0516e293ffe58bca0225d3d3dfdb414
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094660"
---
# <a name="managing-azure-enterprise-agreement-roles"></a>Como gerenciar funções do Contrato Enterprise do Azure

Para ajudar a gerenciar o uso e os gastos da sua organização, os clientes do Azure com um Contrato Enterprise podem atribuir cinco funções administrativas distintas:

- Administrador corporativo
- Administrador corporativo (somente leitura)<sup>1</sup>
- Administrador de departamento
- Administrador de departamento (somente leitura)
- Proprietário da conta<sup>2</sup>

<sup>1</sup> O contato de cobrança do contrato EA estará sob essa função.

<sup>2</sup> O contato para cobrança não pode ser adicionado nem alterado no Portal do EA do Azure e será adicionado ao registro do EA com base no usuário que está configurado como o contato para cobrança no nível do contrato. Para alterar o contato para cobrança, uma solicitação precisa ser feita por meio de um consultor de software/parceiro para o ROC (Centro de Operações Regional).

O primeiro administrador de registro que é configurado durante o provisionamento de registro determina o tipo de autenticação da conta de contato de cobrança. Quando o contato de cobrança é adicionado ao Portal do EA como um administrador somente leitura, ele recebe a autenticação da conta Microsoft. 

Por exemplo, se o tipo de autenticação inicial for definido como Misto, o EA será adicionado como uma conta Microsoft e o contato de cobrança terá privilégios de administrador de EA somente leitura. Se o administrador de EA não aprovar a autorização da conta Microsoft para um contato de cobrança existente, o administrador de EA poderá excluir o usuário em questão e pedir ao cliente para adicionar o usuário novamente como um administrador somente leitura com uma conta corporativa ou de estudante definida apenas no nível de registro no Portal do EA.

Essas funções são específicas para gerenciar os Contratos Enterprise do Azure e são complementares às funções internas que o Azure controla para acessar os recursos. Para obter mais informações, veja [Funções internas do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="azure-enterprise-portal-hierarchy"></a>Hierarquia do Azure Enterprise Portal

A hierarquia do Azure Enterprise Portal consiste em:

- **Azure Enterprise Portal** – um portal de gerenciamento online que ajuda você a gerenciar os custos dos serviços de EA do Azure. Você pode:

  - Criar uma hierarquia de EA do Azure com departamentos, contas e assinaturas.
  - Reconciliar os custos de seus serviços consumidos, baixar relatórios de uso e exibir listas de preços.
  - Criar chaves de API para seu registro.

- Os **departamentos** ajudam a segmentar os custos em agrupamentos lógicos. Os departamentos permitem que você defina um orçamento ou uma cota no nível do departamento.

- As **contas** são unidades organizacionais no Azure Enterprise Portal. Você pode usar as contas para gerenciar assinaturas e acessar relatórios.

- As **assinaturas** são a menor unidade no Azure Enterprise Portal. Elas são contêineres para os serviços do Azure gerenciados pelo administrador de serviços.

O diagrama a seguir ilustra as hierarquias simples do EA do Azure.

![Diagrama de hierarquias simples do EA do Azure](./media/understand-ea-roles/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Funções de usuário corporativo

As seguintes funções de usuário administrativas fazem parte do seu Registro Enterprise:

- Administrador corporativo
- Administrador de departamento
- Proprietário da conta
- Administrador de serviço
- Contato de notificação

As funções funcionam em dois portais diferentes para concluir as tarefas. Você usa o [Azure Enterprise Portal](https://ea.azure.com) para gerenciar a cobrança e os custos e o [portal do Azure](https://portal.azure.com) para gerenciar serviços do Azure.

As funções de usuário são associadas a uma conta de usuário. Para validar a autenticidade do usuário, cada usuário deve ter uma conta Microsoft, corporativa ou de estudante válida. Verifique se cada conta está associada a um endereço de email monitorado ativamente. As notificações de conta são enviadas para o endereço de email.

Ao configurar usuários, você pode atribuir várias contas à função Administrador corporativo. No entanto, apenas uma conta pode conter a função de proprietário da conta. Além disso, você pode atribuir as funções administrador corporativo e proprietário da conta a uma conta.

### <a name="enterprise-administrator"></a>Administrador corporativo

Os usuários com essa função têm o nível mais alto de acesso. Eles podem:

- Gerenciar contas e proprietários da conta.
- Gerenciar outros administradores corporativos.
- Gerenciar administradores de departamento.
- Gerenciar contatos de notificação.
- Exibir o uso em todas as contas.
- Exibir encargos não cobrados em todas as contas.
- Ver e gerenciar todos os pedidos de reserva e as reservas que se aplicam ao Contrato Enterprise.
  - O administrador corporativo (somente leitura) pode ver os pedidos de reserva e as reservas. Eles não podem gerenciá-los.

Você pode ter vários administradores corporativos em um Registro Enterprise. Você pode permitir acesso somente leitura a administradores corporativos. Todos eles herdam a função de administrador do departamento.

### <a name="department-administrator"></a>Administrador de departamento

Os usuários com essa função podem:

- Criar e gerenciar departamentos.
- Criar proprietários da conta.
- Exibir detalhes de uso dos departamentos que eles gerenciam.
- Exibir custos, se tiverem as permissões necessárias.

Você pode ter vários administradores de departamento para cada Registro Enterprise.

Você pode conceder acesso somente leitura aos administradores de departamento ao editar ou criar um administrador de departamento. Defina a opção somente leitura como **Sim**.

### <a name="account-owner"></a>Proprietário da conta

Os usuários com essa função podem:

- Criar e gerenciar assinaturas.
- Gerenciar administradores de serviços.
- Exibir o uso de assinaturas.

Cada conta requer uma conta Microsoft, corporativa ou de estudante, exclusiva. Para obter mais informações sobre as funções administrativas do Azure Enterprise Portal, confira [Entendendo as funções administrativas do Contrato Enterprise do Azure](understand-ea-roles.md).

### <a name="service-administrator"></a>Administrador de serviço

A função administrador de serviços tem permissões para gerenciar serviços no portal do Azure e atribuir usuários à função de coadministrador.

### <a name="notification-contact"></a>Contato de notificação

O contato de notificação pode receber notificações de uso relacionadas ao registro.

As seções a seguir descrevem as limitações e os recursos de cada função.

## <a name="user-limit-for-admin-roles"></a>Limite de usuários para funções de administrador

|Função| Limite de usuários|
|---|---|
|Administrador corporativo|Ilimitado|
|Administrador da empresa (somente leitura)|Ilimitado|
|Administrador de departamento|Ilimitado|
|Administrador de departamento (somente leitura)|Ilimitado|
|Proprietário da conta|Um por conta<sup>3</sup>|

<sup>3</sup> Cada conta exige uma conta Microsoft exclusiva ou uma conta corporativa ou de estudante.

## <a name="organization-structure-and-permissions-by-role"></a>Estrutura de organização e permissões por função

|Tarefas| Administrador corporativo|Administrador da empresa (somente leitura)|Administrador de departamento|Administrador de departamento (somente leitura)|Proprietário da conta| Partner (parceiro)|
|---|---|---|---|---|---|---|
|Exibir administradores de empresa|✔|✔|✘|✘|✘|✔|
|Administradores da Empresa|✔|✘|✘|✘|✘|✘|
|Ver contatos para notificação<sup>4</sup> |✔|✔|✘|✘|✘|✔|
|Adicionar ou remover contatos para notificação<sup>4</sup> |✔|✘|✘|✘|✘|✘|
|Criar e gerenciar os departamentos |✔|✘|✘|✘|✘|✘|
|Administradores de departamento do modo de exibição|✔|✔|✔|✔|✘|✔|
|Adicionar ou remover administradores de departamento|✔|✘|✔|✘|✘|✘|
|Exibir contas na inscrição |✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|Adicione contas para o registro e alterar o proprietário da conta|✔|✘|✔<sup>5</sup>|✘|✘|✘|
|Criar e gerenciar assinaturas e permissões de assinatura|✘|✘|✘|✘|✔|✘|

- <sup>4</sup> Os contatos para notificação recebem comunicações por email sobre o Contrato Enterprise do Azure.
- <sup>5</sup> A tarefa é limitada às contas do seu departamento.

## <a name="add-a-new-enterprise-administrator"></a>Adicionar um novo administrador corporativo

Os administradores corporativos têm mais privilégios ao gerenciar um registro de EA do Azure. O administrador inicial do EA do Azure foi criado quando o contrato de EA foi configurado. No entanto, você pode adicionar ou remover novos administradores a qualquer momento. Novos administradores são adicionados somente por administradores existentes. Para obter mais informações sobre como adicionar outros admins corporativos, confira [Criar outro admin corporativo](ea-portal-administration.md#create-another-enterprise-administrator). Para saber mais sobre funções e tarefas do perfil de cobrança, confira [Funções e tarefas do perfil de cobrança](understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-account-owner-state-from-pending-to-active"></a>Atualizar estado do proprietário da conta de pendente para ativo

Quando novos AOs (proprietários de conta) são adicionados a um registro de EA do Azure pela primeira vez, o status deles aparece como _pendente_. Quando um novo proprietário da conta receber o email de ativação de boas-vindas, ele poderá entrar para ativar a própria conta. Assim que ele ativa a própria conta, o status da conta é atualizado de _pendente_ para _ativo_. O proprietário da conta precisa ler a mensagem de 'Aviso' e selecionar **Continuar**. Pode ser solicitado de novos usuários que insiram o nome e sobrenome deles para criar uma conta de comércio. Nesse caso, eles precisam adicionar as informações necessárias para continuar e, em seguida, a conta é ativada.

## <a name="add-a-department-admin"></a>Adicionar um administrador de departamento

Depois que um administrador de EA cria um departamento, o administrador corporativo do Azure pode adicionar administradores de departamento e associar cada um deles a um departamento. Um administrador de departamento pode criar novas contas. Novas contas são necessárias para que as assinaturas de EA do Azure sejam criadas.

Para obter mais informações sobre como adicionar um administrador de departamento, confira [Criar um administrador de departamento do Azure EA](ea-portal-administration.md#add-a-department-administrator).

## <a name="usage-and-costs-access-by-role"></a>Acesso de uso e os custos por função

|Tarefas| Administrador corporativo|Administrador da empresa (somente leitura)|Administrador de departamento|Administrador de departamento (somente leitura) |Proprietário da conta| Partner (parceiro)|
|---|---|---|---|---|---|---|
|Ver o saldo de crédito, incluindo o pagamento antecipado do Azure|✔|✔|✘|✘|✘|✔|
|Cotas de gastos do departamento de exibição|✔|✔|✘|✘|✘|✔|
|Definir cotas de gastos do departamento|✔|✘|✘|✘|✘|✘|
|Exibir a folha de preços EA da organização|✔|✔|✘|✘|✘|✔|
|Exibir detalhes de uso e custo|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|Gerenciar recursos no portal do Azure|✘|✘|✘|✘|✔|✘|

- <sup>6</sup> Exige que o administrador corporativo habilite a política de **preços de exibição de DA** no Enterprise Portal. O Administrador do Departamento pode, então, ver os detalhes do custo para o departamento.
- <sup>7</sup> Exige que o administrador corporativo habilite a política de **preços de exibição de AO** no Enterprise Portal. O proprietário da conta pode ver detalhes do custo da conta.

## <a name="see-pricing-for-different-user-roles"></a>Consulte os preços das diferentes funções de usuário

Você pode ver preços diferentes no portal do Azure, dependendo de sua função administrativa e de como as políticas de cobranças de visualização são definidas pelo administrador corporativo. As duas políticas no portal da empresa que afetam o preço que você vê no portal do Azure são:

- Encargos de visualização DA
- Encargos de visualização AO

Para saber como definir essas políticas, consulte [Gerenciar acesso às informações de faturamento do Azure](manage-billing-access.md).

A tabela a seguir mostra o relacionamento entre as funções de administração do Contrato Enterprise, a política de cobranças de exibição, a função do Azure no portal do Azure e o preço que você vê no portal do Azure. O administrador corporativo sempre vê detalhes de uso com base no preço do EA da organização. No entanto, o Administrador do Departamento e o Proprietário da Conta veem exibições de preços diferentes com base na política de cobranças de exibição e na função do Azure deles. A função Administrador de departamento listada na tabela a seguir refere-se às funções Admin de departamento e Admin de departamento (somente leitura).

|Função de administrador do Contrato Enterprise|Política de encargos de exibição para função|Função do Azure|Exibição de preço|
|---|---|---|---|
|Proprietário da conta ou administrador de departamento|✔ Habilitado|Proprietário|Preços de EA da organização|
|Proprietário da conta ou administrador de departamento|✘ Desabilitado|Proprietário|Preço de varejo|
|Proprietário da conta ou administrador de departamento|✔ Habilitado |none|Não há preços|
|Proprietário da conta ou administrador de departamento|✘ Desabilitado |none|Não há preços|
|Nenhum|Não aplicável |Proprietário|Preço de varejo|

Você define a função de administrador da empresa e exibe as políticas de cobranças no portal da empresa. A função do Azure pode ser atualizada no portal do Azure. Para obter mais informações, confira [Atribuir funções do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md).



## <a name="next-steps"></a>Próximas etapas

- [Gerenciar o acesso a informações de faturamento do Azure](manage-billing-access.md)
- [Atribuir funções do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md)
- [Funções internas do Azure](../../role-based-access-control/built-in-roles.md)
