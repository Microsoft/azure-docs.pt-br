---
title: Criar ou atualizar funções personalizadas do Azure usando o portal do Azure-RBAC do Azure
description: Saiba como criar funções personalizadas do Azure usando o portal do Azure e o Azure RBAC (controle de acesso baseado em função). Isso inclui como listar, criar, atualizar e excluir funções personalizadas.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/11/2020
ms.author: rolyon
ms.openlocfilehash: ecda0edcd34999e8cbb6c7ab9039953d17c119e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97369219"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal"></a>Criar ou atualizar as funções personalizadas do Azure usando o portal do Azure

Se as [funções internas do Azure](built-in-roles.md) não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias funções personalizadas do Azure. Assim como as funções internas, você pode atribuir funções personalizadas a usuários, grupos e entidades de serviço em escopos de grupo de recursos e assinatura. As funções personalizadas são armazenadas em um diretório do Azure AD (Azure Active Directory) e podem ser compartilhadas entre assinaturas. Cada diretório pode ter até 5000 funções personalizadas. As funções personalizadas podem ser criadas usando o portal do Azure, Azure PowerShell, CLI do Azure ou a API REST. Este artigo descreve como criar funções personalizadas usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para criar funções personalizadas, você precisará:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de acesso do usuário](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>Etapa 1: determinar as permissões necessárias

O Azure tem milhares de permissões que podem ser potencialmente incluídas em sua função personalizada. Aqui estão alguns métodos que podem ajudá-lo a determinar as permissões que você deseja adicionar à sua função personalizada:

- Examine as [funções internas](built-in-roles.md)existentes.
- Lista os serviços do Azure aos quais você deseja conceder acesso.
- Determine os [provedores de recursos que são mapeados para os serviços do Azure](../azure-resource-manager/management/azure-services-resource-providers.md). Um método de pesquisa é descrito posteriormente na [etapa 4: permissões](#step-4-permissions).
- Pesquise as [permissões disponíveis](resource-provider-operations.md) para localizar as permissões que você deseja incluir. Um método de pesquisa é descrito posteriormente na [etapa 4: permissões](#step-4-permissions).

## <a name="step-2-choose-how-to-start"></a>Etapa 2: escolher como iniciar

Há três maneiras pelas quais você pode começar a criar uma função personalizada. Você pode clonar uma função existente, começar do zero ou começar com um arquivo JSON. A maneira mais fácil é encontrar uma função existente que tenha a maioria das permissões necessárias e, em seguida, clonar e modificá-la para seu cenário. 

### <a name="clone-a-role"></a>Clonar uma função

Se uma função existente não tiver as permissões necessárias, você poderá cloná-la e, em seguida, modificar as permissões. Siga estas etapas para iniciar a clonagem de uma função.

1. No portal do Azure, abra uma assinatura ou grupo de recursos em que você deseja que a função personalizada seja atribuível e, em seguida, abra o **controle de acesso (iam)**.

    A captura de tela a seguir mostra a página de controle de acesso (IAM) aberta para uma assinatura.

    ![Página de controle de acesso (IAM) para uma assinatura](./media/custom-roles-portal/access-control-subscription.png)

1. Clique na guia **Funções** para ver uma lista de todas as funções integradas e personalizadas.

1. Procure uma função que você deseja clonar, como a função de leitor de cobrança.

1. No final da linha, clique nas reticências (**...**) e, em seguida, clique em **clonar**.

    ![Clonar menu de contexto](./media/custom-roles-portal/clone-menu.png)

    Isso abre o editor de funções personalizadas com a opção **clonar uma função** selecionada.

1. Vá para a [etapa 3: Noções básicas](#step-3-basics).

### <a name="start-from-scratch"></a>Começar do zero

Se preferir, você pode seguir estas etapas para iniciar uma função personalizada do zero.

1. No portal do Azure, abra uma assinatura ou grupo de recursos em que você deseja que a função personalizada seja atribuível e, em seguida, abra o **controle de acesso (iam)**.

1. Clique em **Adicionar** e em **Adicionar função personalizada**.

    ![Adicionar menu de função personalizado](./media/custom-roles-portal/add-custom-role-menu.png)

    Isso abre o editor de funções personalizadas com a opção **Iniciar do zero** selecionada.

1. Vá para a [etapa 3: Noções básicas](#step-3-basics).

### <a name="start-from-json"></a>Iniciar do JSON

Se preferir, você pode especificar a maioria dos valores de função personalizada em um arquivo JSON. Você pode abrir o arquivo no editor de funções personalizadas, fazer alterações adicionais e, em seguida, criar a função personalizada. Siga estas etapas para começar com um arquivo JSON.

1. Crie um arquivo JSON com o seguinte formato:

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. No arquivo JSON, especifique valores para as várias propriedades. Aqui está um exemplo com alguns valores adicionados. Para obter informações sobre as diferentes propriedades, consulte [entender as definições de função do Azure](role-definitions.md).

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. No portal do Azure, abra a página **controle de acesso (iam)** .

1. Clique em **Adicionar** e em **Adicionar função personalizada**.

    ![Adicionar menu de função personalizado](./media/custom-roles-portal/add-custom-role-menu.png)

    Isso abre o editor de funções personalizadas.

1. Na guia noções básicas, em **permissões de linha de base**, selecione **Iniciar do JSON**.

1. Ao lado da caixa Selecionar um arquivo, clique no botão pasta para abrir a caixa de diálogo abrir.

1. Selecione o arquivo JSON e clique em **abrir**.

1. Vá para a [etapa 3: Noções básicas](#step-3-basics).

## <a name="step-3-basics"></a>Etapa 3: Noções básicas

Na guia **noções básicas** , você especifica o nome, a descrição e as permissões de linha de base para sua função personalizada.

1. Na caixa **nome da função personalizada** , especifique um nome para a função personalizada. O nome deve ser exclusivo para o diretório do Azure AD. O nome pode incluir letras, números, espaços e caracteres especiais.

1. Na caixa **Descrição** , especifique uma descrição opcional para a função personalizada. Isso se tornará a dica de ferramenta para a função personalizada.

    A opção **permissões de linha de base** já deve estar definida com base na etapa anterior, mas você pode alterar.

    ![Guia noções básicas com valores especificados](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>Etapa 4: permissões

Na guia **permissões** , especifique as permissões para sua função personalizada. Dependendo se você clonou uma função ou se começou com JSON, a guia permissões talvez já liste algumas permissões.

![Guia permissões de criar função personalizada](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>Adicionar ou remover permissões

Siga estas etapas para adicionar ou remover permissões para sua função personalizada.

1. Para adicionar permissões, clique em **adicionar permissões** para abrir o painel adicionar permissões.

    Esse painel lista todas as permissões disponíveis agrupadas em categorias diferentes em um formato de cartão. Cada categoria representa um *provedor de recursos*, que é um serviço que fornece recursos do Azure.

1. Na caixa **Pesquisar por uma permissão** , digite uma cadeia de caracteres para procurar permissões. Por exemplo, pesquise *fatura* para encontrar permissões relacionadas à fatura.

    Uma lista de cartões do provedor de recursos será exibida com base na sua cadeia de caracteres de pesquisa. Para obter uma lista de como os provedores de recursos são mapeados para os serviços do Azure, consulte [provedores de recursos para serviços do Azure](../azure-resource-manager/management/azure-services-resource-providers.md).

    ![Adicionar o painel de permissões com o provedor de recursos](./media/custom-roles-portal/add-permissions-provider.png)

1. Clique em um cartão de provedor de recursos que pode ter as permissões que você deseja adicionar à sua função personalizada, como **o Microsoft billing**.

    Uma lista das permissões de gerenciamento para esse provedor de recursos é exibida com base na sua cadeia de caracteres de pesquisa.

    ![Adicionar lista de permissões](./media/custom-roles-portal/add-permissions-list.png)

1. Se você estiver procurando permissões que se aplicam ao plano de dados, clique em **ações de dados**. Caso contrário, deixe as ações alternar definidas como **ações** para listar as permissões que se aplicam ao plano de gerenciamento. Para obter mais informações, sobre as diferenças entre o plano de gerenciamento e o plano de dados, consulte [Gerenciamento e operações de dados](role-definitions.md#management-and-data-operations).

1. Se necessário, atualize a cadeia de caracteres de pesquisa para refinar ainda mais sua pesquisa.

1. Depois de encontrar uma ou mais permissões que você deseja adicionar à sua função personalizada, adicione uma marca de seleção ao lado das permissões. Por exemplo, adicione uma marca de seleção ao lado de **outros: baixar fatura** para adicionar a permissão para baixar faturas.

1. Clique em **Adicionar** para adicionar a permissão à sua lista de permissões.

    A permissão é adicionada como um `Actions` ou um `DataActions` .

    ![Permissão adicionada](./media/custom-roles-portal/permissions-list-add.png)

1. Para remover permissões, clique no ícone Excluir no final da linha. Neste exemplo, como um usuário não precisará da capacidade de criar tíquetes de suporte, a `Microsoft.Support/*` permissão pode ser excluída.

### <a name="add-wildcard-permissions"></a>Adicionar permissões curinga

Dependendo de como você optou por iniciar, você pode ter permissões com curingas ( `*` ) na sua lista de permissões. Um curinga ( `*` ) estende uma permissão para tudo que corresponde à cadeia de caracteres de ação que você fornece. Por exemplo, a cadeia de caracteres curinga a seguir adiciona todas as permissões relacionadas ao gerenciamento de custos e às exportações do Azure. Isso também incluiria permissões de exportação futuras que possam ser adicionadas.

```
Microsoft.CostManagement/exports/*
```

Se você quiser adicionar uma nova permissão de caractere curinga, não poderá adicioná-la usando o painel **adicionar permissões** . Para adicionar uma permissão curinga, você precisa adicioná-la manualmente usando a guia **JSON** . Para obter mais informações, consulte [Step 6: JSON](#step-6-json).

### <a name="exclude-permissions"></a>Excluir permissões

Se sua função tiver uma permissão curinga ( `*` ) e você quiser excluir ou subtrair permissões específicas dessa permissão de curinga, você poderá excluí-las. Por exemplo, digamos que você tenha a seguinte permissão de curinga:

```
Microsoft.CostManagement/exports/*
```

Se você não quiser permitir que uma exportação seja excluída, poderá excluir a seguinte permissão de exclusão:

```
Microsoft.CostManagement/exports/delete
```

Quando você exclui uma permissão, ela é adicionada como um `NotActions` ou `NotDataActions` . As permissões efetivas de gerenciamento são computadas adicionando todos os `Actions` e subtraindo todos os `NotActions` . As permissões de dados efetivas são computadas adicionando todos os `DataActions` e subtraindo todos os `NotDataActions` .

> [!NOTE]
> A exclusão de uma permissão não é a mesma que uma negação. A exclusão de permissões é simplesmente uma maneira conveniente de subtrair permissões de uma permissão curinga.

1. Para excluir ou subtrair uma permissão de uma permissão de curinga permitido, clique em **excluir permissões** para abrir o painel de permissões de exclusão.

    Nesse painel, você especifica as permissões de gerenciamento ou de dados que são excluídas ou subtraídas.

1. Depois de encontrar uma ou mais permissões que você deseja excluir, adicione uma marca de seleção ao lado das permissões e, em seguida, clique no botão **Adicionar** .

    ![Painel de permissões de exclusão-permissão selecionada](./media/custom-roles-portal/exclude-permissions-select.png)

    A permissão é adicionada como um `NotActions` ou `NotDataActions` .

    ![Permissão excluída](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>Etapa 5: escopos atribuíveis

Na guia **escopos atribuíveis** , especifique onde sua função personalizada está disponível para atribuição, como assinatura ou grupo de recursos. Dependendo de como você optou por iniciar, essa guia pode listar o escopo onde você abriu a página controle de acesso (IAM). Não há suporte para definir o escopo atribuível para o escopo raiz ("/"). No momento, não é possível adicionar um grupo de gerenciamento como um escopo atribuível.

1. Clique em **Adicionar escopos atribuíveis** para abrir o painel Adicionar escopos atribuíveis.

    ![Guia escopos atribuíveis](./media/custom-roles-portal/assignable-scopes.png)

1. Clique em um ou mais escopos que você deseja usar, normalmente sua assinatura.

    ![Adicionar escopos atribuíveis](./media/custom-roles-portal/add-assignable-scopes.png)

1. Clique no botão **Adicionar** para adicionar seu escopo atribuível.

## <a name="step-6-json"></a>Etapa 6: JSON

Na guia **JSON** , você vê sua função personalizada formatada em JSON. Se desejar, você pode editar diretamente o JSON. Se você quiser adicionar uma permissão curinga ( `*` ), deverá usar essa guia.

1. Para editar o JSON, clique em **Editar**.

    ![Guia JSON mostrando a função personalizada](./media/custom-roles-portal/json.png)

1. Faça alterações no JSON.

    Se o JSON não estiver formatado corretamente, você verá uma linha vermelha denteada e um indicador na medianiz vertical.

1. Quando terminar de editar, clique em **salvar**.

## <a name="step-7-review--create"></a>Etapa 7: examinar + criar

Na guia **revisar + criar** , você pode examinar as configurações de função personalizadas.

1. Examine as configurações de função personalizadas.

    ![Guia Examinar + criar](./media/custom-roles-portal/review-create.png)

1. Clique em **criar** para criar sua função personalizada.

    Após alguns instantes, uma caixa de mensagem será exibida indicando que a função personalizada foi criada com êxito.

    ![Criar mensagem de função personalizada](./media/custom-roles-portal/custom-role-success.png)

    Se forem detectados erros, uma mensagem será exibida.

    ![Examinar + criar erro](./media/custom-roles-portal/review-create-error.png)

1. Exiba sua nova função personalizada na lista de **funções** . Se você não vir sua função personalizada, clique em **Atualizar**.

     Pode levar alguns minutos para que sua função personalizada apareça em todos os lugares.

## <a name="list-custom-roles"></a>Listar funções personalizadas

Siga estas etapas para exibir suas funções personalizadas.

1. Abra uma assinatura ou um grupo de recursos e, em seguida, abra o **controle de acesso (iam)**.

1. Clique na guia **Funções** para ver uma lista de todas as funções integradas e personalizadas.

1. Na lista **tipo** , selecione **CustomRole** para ver apenas suas funções personalizadas.

    Se você acabou de criar sua função personalizada e não a vir na lista, clique em **Atualizar**.

    ![Lista de funções personalizadas](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

1. Conforme descrito anteriormente neste artigo, abra a lista de funções personalizadas.

1. Clique nas reticências (**...**) para a função personalizada que você deseja atualizar e clique em **Editar**. Observe que você não pode atualizar funções internas.

    A função personalizada é aberta no editor.

    ![Menu de função personalizada](./media/custom-roles-portal/edit-menu.png)

1. Use as diferentes guias para atualizar a função personalizada.

1. Depois de concluir as alterações, clique na guia **revisar + criar** para revisar as alterações.

1. Clique no botão **Atualizar** para atualizar sua função personalizada.

## <a name="delete-a-custom-role"></a>Excluir uma função personalizada

1. Conforme descrito anteriormente neste artigo, abra a lista de funções personalizadas.

1. Remova as atribuições de função que usam a função personalizada.

1. Clique nas reticências (**...**) para a função personalizada que você deseja excluir e clique em **excluir**.

    ![Captura de tela que mostra uma lista de funções personalizadas que podem ser selecionadas para exclusão.](./media/custom-roles-portal/delete-menu.png)

    Pode levar alguns minutos para que sua função personalizada seja completamente excluída.

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Criar uma função personalizada do Azure usando o Azure PowerShell](tutorial-custom-role-powershell.md)
- [Funções personalizadas do Azure](custom-roles.md)
- [Operações do provedor de recursos do Azure](resource-provider-operations.md)
