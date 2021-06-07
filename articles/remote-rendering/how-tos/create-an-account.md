---
title: Criar uma conta do Azure Remote Rendering
description: Descreve as etapas para criar uma conta do Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 83bd4a7ae0082d24f7ac617719e628f4db4baeb9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98197618"
---
# <a name="create-an-azure-remote-rendering-account"></a>Criar uma conta do Azure Remote Rendering

Este capítulo orienta você pelas etapas para criar uma conta para o serviço **Azure Remote Rendering**. Uma conta válida é obrigatória para a conclusão de qualquer um dos tutoriais ou guias de início rápido.

## <a name="create-an-account"></a>Criar uma conta

As seguintes etapas são necessárias para criar uma conta para o serviço Azure Remote Rendering:

1. Vá para a [página de versão prévia da Realidade Misturada](https://aka.ms/MixedRealityPrivatePreview)
1. Clique no botão 'Criar um recurso'
1. No campo de pesquisa ("Pesquisar no Marketplace"), digite "Remote Rendering" e pressione 'Enter'.
1. Na lista de resultados, clique no bloco "Remote Rendering"
1. Na próxima tela, clique no botão "Criar". Um formulário é aberto para criar uma conta do Remote Rendering:
    1. Defina 'Nome do Recurso' como o nome da conta
    1. Atualize 'Assinatura' se necessário
    1. Defina 'Grupo de recursos' como um grupo de recursos de sua escolha
    1. Selecione uma região na lista suspensa ' local ' em que esse recurso deve ser criado. Veja comentários nas [regiões de conta](create-an-account.md#account-regions) abaixo.
1. Depois que a conta for criada, navegue até ela e:
    1. Na guia *Visão geral*, observe a 'ID da conta'
    1. Na guia *Configurações > Chaves de Acesso*, observe a "Chave primária". Essa é a chave de conta secreta da conta em questão

### <a name="account-regions"></a>Regiões da conta
O local especificado durante a criação da conta de uma conta determina a qual região o recurso de conta é atribuído. Isso não pode ser alterado após a criação. No entanto, a conta pode ser usada para se conectar a uma sessão de renderização remota em qualquer [região com suporte](./../reference/regions.md), independentemente do local da conta.

### <a name="retrieve-the-account-information"></a>Recuperar as informações da conta

Os exemplos e os tutoriais exigem que você forneça a ID da conta e uma chave. Por exemplo, no arquivo **arrconfig.json** que é usado para os scripts de exemplo do PowerShell:

```json
"accountSettings": {
    "arrAccountId": "<fill in the account ID from the Azure portal>",
    "arrAccountKey": "<fill in the account key from the Azure portal>",
    "region": "<select from available regions>"
},
```

Confira a [lista de regiões disponíveis](../reference/regions.md) para preencher a opção *região*.

Os valores para **`arrAccountId`** e **`arrAccountKey`** podem ser encontrados no portal, conforme descrito nas seguintes etapas:

* Vá para o [Portal do Azure](https://www.portal.azure.com)
* Localize sua **"conta do Remote Rendering"** : ela deve estar na lista de **"Recursos Recentes"** . Você também pode procurá-la na barra de pesquisa na parte superior. Nesse caso, verifique se a assinatura que você deseja usar está selecionada no filtro de assinatura padrão (ícone de filtro ao lado da barra de pesquisa):

![Filtro da assinatura](./media/azure-subscription-filter.png)

Clicar em sua conta o levará a esta tela, que mostra a **ID da conta** imediatamente:

![ID da conta do Azure](./media/azure-account-id.png)

Para a chave, selecione **Chaves de Acesso** no painel à esquerda. A próxima página mostra uma chave primária e outra secundária:

![Chaves de acesso do Azure](./media/azure-account-primary-key.png)

O valor para **`arrAccountKey`** pode ser uma chave primária ou secundária.

## <a name="link-storage-accounts"></a>Vincular contas de armazenamento

Este parágrafo explica como vincular contas de armazenamento à sua conta do Remote Rendering. Quando uma conta de armazenamento é vinculada, não é necessário gerar um URI de SAS toda vez que você deseja interagir com os dados em sua conta, por exemplo, ao carregar um modelo. Em vez disso, você pode usar os nomes de conta de armazenamento diretamente, conforme descrito na [seção carregar um modelo](../concepts/models.md#loading-models).

As etapas neste parágrafo devem ser executadas para cada conta de armazenamento que deve usar esse método de acesso. Se você ainda não tiver criado contas de armazenamento, poderá percorrer a etapa correspondente no [início rápido para converter um modelo para renderizar](../quickstarts/convert-model.md#storage-account-creation).

Agora, supõe-se que você tenha uma conta de armazenamento. Navegue até a conta de armazenamento no portal e vá para a guia **Controle de Acesso (IAM)** para essa conta de armazenamento:

![IAM da conta de armazenamento](./media/azure-storage-account.png)

Verifique se você tem permissões de proprietário nessa conta de armazenamento para garantir que você possa adicionar atribuições de função. Se você não tiver acesso, a opção **Adicionar atribuição de função** será desabilitada.

Clique no botão **Adicionar** no bloco "adicionar uma atribuição de função" para adicionar a função.

![Adicionar atribuição de função IAM da conta de armazenamento](./media/azure-add-role-assignment.png)

* Atribua a função de **colaborador de dados de blob de armazenamento** , conforme mostrado na captura de tela acima.
* Selecione o sistema **conta de renderização remoto**  atribuição de identidade gerenciada na lista suspensa **atribuir acesso a** .
* Selecione sua assinatura e a conta do Remote Rendering nos últimos menus suspensos.
* Clique em "salvar" para salvar as alterações.

> [!WARNING]
> Caso sua conta do Remote Rendering não esteja listada, veja esta [seção de solução de problemas](../resources/troubleshoot.md#cant-link-storage-account-to-arr-account).

> [!IMPORTANT]
> As atribuições de função do Azure são armazenadas em cache pelo armazenamento do Azure, portanto, pode haver um atraso de até 30 minutos entre quando você concede acesso à sua conta de renderização remota e quando ela pode ser usada para acessar sua conta de armazenamento. Consulte a [documentação do controle de acesso baseado em função (RBAC do Azure)](../../role-based-access-control/troubleshooting.md#role-assignment-changes-are-not-being-detected) para obter detalhes.

## <a name="next-steps"></a>Próximas etapas

* [Autenticação](authentication.md)
* [Usar as APIs de front-end do Azure para autenticação](frontend-apis.md)
* [Scripts de exemplo do PowerShell](../samples/powershell-example-scripts.md)