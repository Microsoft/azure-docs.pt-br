---
title: Criação em massa de usuários no portal do Azure Active Directory | Microsoft Docs
description: Adicionar usuários em massa no centro de administração do Azure AD no Azure Active Directory
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
ms.openlocfilehash: c653f3e8583ef3aadff26cb2b7a3266555d313a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547807"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Criar usuários em massa no Azure Active Directory

O Azure Active Directory (Azure AD) dá suporte a operações de criação e exclusão de usuários em massa e ao download de listas de usuários. Basta preencher o modelo de valores separados por vírgulas (CSV) que você pode baixar no portal do Azure Active Directory.

## <a name="required-permissions"></a>Permissões necessárias

Para criar usuários em massa no portal de administração, você deve estar conectado como administrador global ou administrador de usuários.

## <a name="understand-the-csv-template"></a>Entender o modelo CSV

Baixe e preencha o modelo CSV de carregamento em massa para ajudá-lo a criar em massa e com êxito os usuários do Azure AD. O modelo CSV que você baixa pode ser semelhante a este exemplo:

![Planilha para upload e limites de chamada que explicam a finalidade e os valores de cada linha e coluna](./media/users-bulk-add/create-template-example.png)

> [!WARNING]
> Se você estiver adicionando apenas uma entrada usando o modelo CSV, deverá preservar a linha 3 e adicionar a nova entrada à linha 4.

### <a name="csv-template-structure"></a>Estrutura do modelo CSV

As linhas em um modelo CSV baixado são as seguintes:

- **Número de versão**: A primeira linha que contém o número de versão deve ser incluída no CSV de carregamento.
- **Cabeçalhos de coluna**: O formato dos cabeçalhos de coluna é &lt;*Nome do item*&gt; [PropertyName]&lt;*Obrigatório ou em branco*&gt;. Por exemplo, `Name [displayName] Required`. Algumas versões mais antigas do modelo podem ter pequenas variações.
- **Linha de exemplos**: incluímos no modelo uma linha de exemplos de valores aceitáveis para cada coluna. Você deve remover a linha de exemplos e substituí-la por suas entradas.

### <a name="additional-guidance"></a>Diretriz adicional

- As duas primeiras linhas do modelo de carregamento não devem ser removidas nem modificadas ou o carregamento não poderá ser processado.
- As colunas necessárias são listadas primeiro.
- Não recomendamos adicionar novas colunas ao modelo. Todas as colunas extras adicionadas são ignoradas e não são processadas.
- Recomendamos que você baixe a versão mais recente do modelo CSV sempre que possível.
- Certifique-se de verificar se não há nenhum espaço em branco indesejado antes/depois de qualquer campo. Para o **nome principal do usuário**, ter esse espaço em branco causaria falha de importação.

## <a name="to-create-users-in-bulk"></a>Para criar usuários em massa

1. [Entre em sua organização do Azure AD](https://aad.portal.azure.com) com uma conta que seja um administrador de usuários na organização.
1. No Azure AD, selecione **Usuários** > **Criar em massa**.
1. Na página **Criar usuário em massa**, selecione **Baixar** para receber um arquivo CSV (valores separados por vírgula) válido das propriedades do usuário e adicione os usuários que você deseja criar.

   ![Selecione um arquivo CSV local no qual você lista os usuários que deseja adicionar.](./media/users-bulk-add/upload-button.png)

1. Abra o arquivo CSV e adicione uma linha para cada usuário que você deseja criar. Os únicos valores necessários são **Nome**, **Nome UPN**, **Senha inicial** e **Bloquear entrada (Sim/Não)** . Em seguida, salve o arquivo.

   [![O arquivo CSV contém os nomes e as IDs dos usuários a serem criados](./media/users-bulk-add/add-csv-file.png)](./media/users-bulk-add/add-csv-file.png#lightbox)

1. Na página **Criar usuários em massa**, em Carregar seu arquivo CSV, procure o arquivo. Quando você seleciona o arquivo e clica em **Enviar**, a validação do arquivo CSV é iniciada.
1. Após a validação do conteúdo do arquivo, você verá a mensagem **Arquivo carregado com êxito**. Se houver erros, você precisará corrigi-los antes de enviar o trabalho.
1. Quando o arquivo passar na validação, selecione **Enviar** para iniciar a operação em massa do Azure que importa os novos usuários.
1. Quando a operação de importação for concluída, você verá uma notificação do status da tarefa de operação em massa.

Se houver erros, você poderá baixar e exibir o arquivo de resultados na página **Resultados da operação em massa**. O arquivo contém o motivo de cada erro. O envio de arquivo deve corresponder ao modelo fornecido e incluir os nomes de coluna exatos.

## <a name="check-status"></a>Verificar o status

É possível ver o status de todas as suas solicitações em massa pendentes na página **Resultados da operação em massa**.

   [![Verificar o status de criação na página de Resultados das Operações em Massa](./media/users-bulk-add/bulk-center.png)](./media/users-bulk-add/bulk-center.png#lightbox)

Em seguida, você pode verificar para ver se os usuários criados existem na organização do Azure AD no portal do Azure ou usando o PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Verificar usuários no portal do Azure

1. [Entre no centro de administração do portal do Azure AD](https://aad.portal.azure.com) com uma conta que seja um Administrador de usuários na organização.
1. No painel de navegação, selecione **Azure Active Directory**.
1. Em **Gerenciar**, selecione **Usuários**.
1. Em **Mostrar**, selecione **Todos os usuários** e verifique se os usuários criados estão listados.

### <a name="verify-users-with-powershell"></a>Verificar usuários com o PowerShell

Execute o comando a seguir:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Você deve ver que os usuários criados estão listados.

## <a name="bulk-import-service-limits"></a>Limites do serviço de importação em massa

Cada atividade em massa de criação de usuários pode ser executada por até uma hora. Isso permite a criação em massa de pelo menos 50 mil usuários.

## <a name="next-steps"></a>Próximas etapas

- [Exclusão em massa de usuários](users-bulk-delete.md)
- [Baixar a lista de usuários](users-bulk-download.md)
- [Restauração de usuários em massa](users-bulk-restore.md)
