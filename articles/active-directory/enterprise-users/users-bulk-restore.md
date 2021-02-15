---
title: Restauração em massa de usuários excluídos no portal do Azure Active Directory | Microsoft Docs
description: Restaurar usuários excluídos em massa no centro de administração do Azure AD no Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: be3947e3de18f8ccaf47382c4035e187521ac710
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571497"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>Restaurar em massa usuários excluídos no Azure Active Directory

O Azure AD (Azure Active Directory) dá suporte a operações de restauração de usuário em massa e para o download de listas de usuários, grupos e membros do grupo.

## <a name="understand-the-csv-template"></a>Entender o modelo CSV

Baixe e preencha o modelo CSV para ajudar você a obter sucesso na restauração em massa de usuários do Azure AD. O modelo CSV que você baixa pode ser semelhante a este exemplo:

![Planilha para upload e limites de chamada que explicam a finalidade e os valores de cada linha e coluna](./media/users-bulk-restore/understand-template.png)

### <a name="csv-template-structure"></a>Estrutura do modelo CSV

As linhas em um modelo CSV baixado são as seguintes:

- **Número de versão**: A primeira linha que contém o número de versão deve ser incluída no CSV de carregamento.
- **Cabeçalhos de coluna**: O formato dos cabeçalhos de coluna é &lt;*Nome do item*&gt; [PropertyName] &lt;*Obrigatório ou em branco*&gt;. Por exemplo, `Object ID [objectId] Required`. Algumas versões mais antigas do modelo podem ter pequenas variações.
- **Linha de exemplos**: incluímos no modelo uma linha de exemplos de valores aceitáveis para cada coluna. Você deve remover a linha de exemplos e substituí-la por suas entradas.

### <a name="additional-guidance"></a>Diretriz adicional

- As duas primeiras linhas do modelo de carregamento não devem ser removidas nem modificadas ou o carregamento não poderá ser processado.
- As colunas necessárias são listadas primeiro.
- Não recomendamos adicionar novas colunas ao modelo. Todas as colunas extras adicionadas são ignoradas e não são processadas.
- Recomendamos que você baixe a versão mais recente do modelo CSV sempre que possível.

## <a name="to-bulk-restore-users"></a>Para restaurar usuários em massa

1. [Entre em sua organização do Azure AD](https://aad.portal.azure.com) com uma conta que seja um Administrador de usuário na organização do Azure AD.
1. No Azure AD, selecione **Usuários** > **Excluídos**.
1. Na página **Usuários excluídos**, selecione **Restauração em massa** para carregar um arquivo CSV válido de propriedades dos usuários a serem restaurados.

    ![Selecione o comando restauração em massa na página Usuários excluídos](./media/users-bulk-restore/bulk-restore.png)

1. Abra o modelo CSV e adicione uma linha para cada usuário que você deseja restaurar. O único valor necessário é **ObjectID**. Em seguida, salve o arquivo.

    :::image type="content" source="./media/users-bulk-restore/upload-button.png" alt-text="Selecione um arquivo CSV local no qual você lista os usuários que deseja adicionar":::

1. Na página **Restauração em massa**, em **Carregar seu arquivo csv**, procure o arquivo. Quando você seleciona o arquivo e clica em **Enviar**, a validação do arquivo CSV é iniciada.
1. Quando o conteúdo do arquivo for validado, você verá a mensagem **Arquivo carregado com êxito**. Se houver erros, você precisará corrigi-los antes de enviar o trabalho.
1. Quando o arquivo passar na validação, selecione **Enviar** para iniciar a operação em massa do Azure que restaura os usuários.
1. Quando a operação de restauração é concluída, você verá uma notificação indicando que a operação em massa foi bem-sucedida.

Se houver erros, você poderá baixar e exibir o arquivo de resultados na página **Resultados da operação em massa**. O arquivo contém o motivo de cada erro.

## <a name="check-status"></a>Verificar o status

Você pode ver o status de todas as suas solicitações em massa pendentes na página **Resultados da operação em massa**.

[![Verifique o status na página resultados de operações em massa.](./media/users-bulk-restore/bulk-center.png)](./media/users-bulk-restore/bulk-center.png#lightbox)

Em seguida, você pode verificar para ver se os usuários restaurados existem na organização do Azure AD no portal do Azure ou usando o PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Exibir usuários restaurados no portal do Azure

1. [Entre no centro de administração do portal do Azure AD](https://aad.portal.azure.com) com uma conta que seja um Administrador de usuários na organização.
1. No painel de navegação, selecione **Azure Active Directory**.
1. Em **Gerenciar**, selecione **Usuários**.
1. Em **Mostrar**, selecione **Todos os usuários** e verifique se os usuários restaurados estão listados.

### <a name="view-users-with-powershell"></a>Exibir usuários com o PowerShell

Execute o comando a seguir:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Você deve ver que os usuários restaurados estão listados.

## <a name="next-steps"></a>Próximas etapas

- [Importação em massa de usuários](users-bulk-add.md)
- [Exclusão em massa de usuários](users-bulk-delete.md)
- [Baixar a lista de usuários](users-bulk-download.md)
