---
title: Carregamento em massa para adicionar ou criar membros de um grupo – Azure Active Directory | Microsoft Docs
description: Adicione membros do grupo em massa ao centro de administração do Azure Active Directory.
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
ms.openlocfilehash: 22d39a2ee66f2c63612ad2cb3cf9ae61f1660de3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547739"
---
# <a name="bulk-add-group-members-in-azure-active-directory"></a>Adicionar membros do grupo em massa no Azure Active Directory

Com o portal do Azure Active Directory (AAD), é possível adicionar um grande número de membros a um grupo usando um arquivo CSV (valores separados por vírgula) para importar membros do grupo em massa.

## <a name="understand-the-csv-template"></a>Entender o modelo CSV

Baixe e preencha o modelo CSV de carregamento em massa para ajudar a adicionar com êxito os membros do grupo do Azure AD em massa. O modelo CSV pode ser semelhante a este exemplo:

![Planilha para upload e limites de chamada que explicam a finalidade e os valores de cada linha e coluna](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>Estrutura do modelo CSV

As linhas em um modelo CSV baixado são as seguintes:

- **Número de versão**: A primeira linha que contém o número de versão deve ser incluída no CSV de carregamento.
- **Cabeçalhos de coluna**: O formato dos cabeçalhos de coluna é &lt;*Nome do item*&gt; [PropertyName]&lt;*Obrigatório ou em branco*&gt;. Por exemplo, `Member object ID or user principal name [memberObjectIdOrUpn] Required`. Algumas versões mais antigas do modelo podem ter pequenas variações. Para alterações de associação de grupo, você tem a opção de qual identificador usar: ID de objeto de membro ou nome UPN.
- **Linha de exemplos**: incluímos no modelo uma linha de exemplos de valores aceitáveis para cada coluna. Você deve remover a linha de exemplos e substituí-la por suas entradas.

### <a name="additional-guidance"></a>Diretriz adicional

- As duas primeiras linhas do modelo de carregamento não devem ser removidas nem modificadas ou o carregamento não poderá ser processado.
- As colunas necessárias são listadas primeiro.
- Não recomendamos adicionar novas colunas ao modelo. Todas as colunas extras adicionadas são ignoradas e não são processadas.
- Recomendamos que você baixe a versão mais recente do modelo CSV sempre que possível.
- Adicione pelo menos dois UPNs ou IDs de objeto dos usuários para carregar o arquivo com êxito.

## <a name="to-bulk-import-group-members"></a>Importar membros do grupo em massa

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de administrador de usuários na organização. Os proprietários do grupo também podem importar em massa membros de grupos que possuem.
1. No Azure Active Directory, escolha **Grupos** > **Todos os Grupos**.
1. Abra o grupo ao qual você está adicionando Membros e escolha **Membros**.
1. Na página **Membros**, escolha **Importar Membros**.
1. Na página **Importar membros do grupo em massa**, escolha **Baixar** para obter o modelo de arquivo CSV com as propriedades necessárias do membro do grupo.

    ![O comando Importar Membros está na página de perfil do grupo](./media/groups-bulk-import-members/import-panel.png)

1. Abra o arquivo CSV e adicione uma linha para cada membro do grupo que você quer importar para o grupo (os valores necessários são **ID de objeto de membro** ou **Nome UPN**). Em seguida, salve o arquivo.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="O arquivo CSV contém os nomes e as IDs dos membros a serem importados":::

1. Na página **Importar membros do grupo em massa**, em **Carregar o arquivo CSV**, procure o arquivo. Quando você escolhe o arquivo, a validação do arquivo CSV será iniciada.
1. Quando o conteúdo do arquivo for validado, a página para importar em massa exibirá **Arquivo carregado com êxito**. Se houver erros, você precisará corrigi-los antes de enviar o trabalho.
1. Quando o arquivo passar na validação, escolha **Enviar** para iniciar a operação em massa do Azure que importa os membros do grupo para o grupo.
1. Quando a operação de importação for concluída, você verá uma notificação indicando que a operação em massa foi bem-sucedida.

## <a name="check-import-status"></a>Verificar o status de importação

É possível ver o status de todas as suas solicitações em massa pendentes na página **Resultados da operação em massa**.

[![Verifique o status na página resultados de operações em massa.](./media/groups-bulk-import-members/bulk-center.png)](./media/groups-bulk-import-members/bulk-center.png#lightbox)

Para obter detalhes de cada item de linha na operação em massa, escolha os valores nas colunas **Nº de Êxitos**, **Nº de Falhas** ou **Total de Solicitações**. Se ocorrerem falhas, os motivos da falha serão listados.

## <a name="bulk-import-service-limits"></a>Limites do serviço de importação em massa

Cada atividade em massa para importar uma lista de membros do grupo pode ser executada por até uma hora. Isso permite a importação de uma lista de no máximo 40.000 membros.

## <a name="next-steps"></a>Próximas etapas

- [Remover membros do grupo em massa](groups-bulk-remove-members.md)
- [Baixar membros de um grupo](groups-bulk-download-members.md)
- [Baixar uma lista de todos os grupos](groups-bulk-download.md)
