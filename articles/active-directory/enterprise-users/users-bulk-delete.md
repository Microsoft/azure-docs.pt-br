---
title: Excluir usuários em massa no portal de Azure Active Directory | Microsoft Docs
description: Excluir usuários em massa no centro de administração do Azure no Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: b57d675003c1ebeb29927b86338f95cf7dd68090
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574456"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Excluir usuários em massa no Azure Active Directory

Usando o portal do Azure Active Directory (AD do Azure), você pode remover um grande número de membros para um grupo usando um arquivo CSV (valores separados por vírgula) para excluir usuários em massa.

## <a name="understand-the-csv-template"></a>Entender o modelo CSV

Baixe e preencha o modelo CSV para ajudá-lo a excluir com êxito os usuários do Azure AD em massa. O modelo CSV que você baixa pode ser semelhante a este exemplo:

![Planilha para upload e limites de chamada que explicam a finalidade e os valores de cada linha e coluna](./media/users-bulk-delete/understand-template.png)

### <a name="csv-template-structure"></a>Estrutura do modelo CSV

As linhas em um modelo CSV baixado são as seguintes:

- **Número de versão**: A primeira linha que contém o número de versão deve ser incluída no CSV de carregamento.
- **Cabeçalhos de coluna**: O formato dos cabeçalhos de coluna é &lt;*Nome do item*&gt; [PropertyName]&lt;*Obrigatório ou em branco*&gt;. Por exemplo, `User name [userPrincipalName] Required`. Algumas versões mais antigas do modelo podem ter pequenas variações.
- **Linha de exemplos**: incluímos no modelo uma linha de exemplos de valores aceitáveis para cada coluna. Você deve remover a linha de exemplos e substituí-la por suas entradas.

### <a name="additional-guidance"></a>Diretriz adicional

- As duas primeiras linhas do modelo de carregamento não devem ser removidas nem modificadas ou o carregamento não poderá ser processado.
- As colunas necessárias são listadas primeiro.
- Não recomendamos adicionar novas colunas ao modelo. Todas as colunas extras adicionadas são ignoradas e não são processadas.
- Recomendamos que você baixe a versão mais recente do modelo CSV sempre que possível.

## <a name="to-bulk-delete-users"></a>Para excluir usuários em massa

1. [Entre em sua organização do Azure AD](https://aad.portal.azure.com) com uma conta que seja um administrador de usuários na organização.
1. No Azure AD, selecione **usuários**  >  **excluir em massa**.
1. Na página **usuário de exclusão em massa** , selecione **baixar** para receber um arquivo CSV válido de propriedades do usuário.

   ![Selecione um arquivo CSV local no qual você lista os usuários que deseja excluir](./media/users-bulk-delete/bulk-delete.png)

1. Abra o arquivo CSV e adicione uma linha para cada usuário que você deseja excluir. O único valor necessário é **nome principal do usuário**. Em seguida, salve o arquivo.

   ![O arquivo CSV contém nomes e IDs dos usuários a serem excluídos](./media/users-bulk-delete/delete-csv-file.png)

1. Na página **usuário de exclusão em massa** , em **carregar o arquivo CSV**, navegue até o arquivo. Quando você seleciona o arquivo e clica em enviar, a validação do arquivo CSV é iniciada.
1. Quando o conteúdo do arquivo for validado, você verá a mensagem **Arquivo carregado com êxito**. Se houver erros, você precisará corrigi-los antes de enviar o trabalho.
1. Quando o arquivo passar na validação, selecione **Enviar** para iniciar a operação em massa do Azure que exclui os usuários.
1. Quando a operação de exclusão for concluída, você verá uma notificação de que a operação em massa foi bem-sucedida.

Se houver erros, você poderá baixar e exibir o arquivo de resultados na página **Resultados da operação em massa**. O arquivo contém o motivo de cada erro.

## <a name="check-status"></a>Verificar o status

É possível ver o status de todas as suas solicitações em massa pendentes na página **Resultados da operação em massa**.

   [![Verifique o status de exclusão na página resultados de operações em massa.](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

Em seguida, você pode verificar para ver se os usuários que você excluiu existem na organização do Azure AD na portal do Azure ou usando o PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Verificar usuários excluídos no portal do Azure

1. Entre no portal do Azure com uma conta que seja um Administrador de usuários na organização.
1. No painel de navegação, selecione **Azure Active Directory**.
1. Em **Gerenciar**, selecione **Usuários**.
1. Em **Mostrar**, selecione somente **todos os usuários** e verifique se os usuários que você excluiu não estão mais listados.

### <a name="verify-deleted-users-with-powershell"></a>Verificar usuários excluídos com o PowerShell

Execute o comando a seguir:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Verifique se os usuários que você excluiu não estão mais listados.

## <a name="next-steps"></a>Próximas etapas

- [Adição de Usuários em Massa](users-bulk-add.md)
- [Baixar a lista de usuários](users-bulk-download.md)
- [Restauração de usuários em massa](users-bulk-restore.md)
