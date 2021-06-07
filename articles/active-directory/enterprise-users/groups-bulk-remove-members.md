---
title: Remover membros do grupo em massa ao carregar um arquivo CSV – Azure Active Directory | Microsoft Docs
description: Remova os membros do grupo em operações em massa no centro de administração do Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 11/15/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83b905faa892cc338b5bdf4f998ad95f7b8a484e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95488939"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>Remover membros do grupo em massa no Azure Active Directory

Com o portal do Azure AD (Azure Active Directory), é possível remover um grande número de membros de um grupo usando um arquivo CSV (valores separados por vírgula) para remover membros do grupo em massa.

## <a name="understand-the-csv-template"></a>Entender o modelo CSV

Baixe e preencha o modelo CSV de carregamento em massa para ajudar a adicionar com êxito os membros do grupo do Azure AD em massa. O modelo CSV pode ser semelhante a este exemplo:

![Planilha para upload e limites de chamada que explicam a finalidade e os valores de cada linha e coluna](./media/groups-bulk-remove-members/template-example.png)

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

## <a name="to-bulk-remove-group-members"></a>Para remover membros do grupo em massa

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de administrador de usuários na organização. Os proprietários do grupo também podem remover em massa membros de grupos que eles têm.
1. No Azure AD, escolha **Grupos** > **Todos os grupos**.
1. Abra o grupo do qual você está removendo membros e escolha **Membros**.
1. Na página **Membros**, selecione **Remover membros**.
1. Na página **Remover membros do grupo em massa**, selecione **Baixar** para obter o modelo de arquivo CSV com as propriedades necessárias do membro do grupo.

   ![O comando Remover Membros está na página de perfil do grupo](./media/groups-bulk-remove-members/remove-panel.png)

1. Abra o arquivo CSV e adicione uma linha para cada membro do grupo que você quer remover (os valores necessários são ID de objeto de membro ou Nome UPN). Em seguida, salve o arquivo.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="O arquivo CSV contém os nomes e as IDs dos membros do grupo a serem removidos":::

1. Na página **Remover membros do grupo em massa**, em **Carregar o arquivo CSV**, procure o arquivo. Quando você escolhe o arquivo, a validação do arquivo CSV será iniciada.
1. Quando o conteúdo do arquivo for validado, a página para importar em massa exibirá **Arquivo carregado com êxito**. Se houver erros, você precisará corrigi-los antes de enviar o trabalho.
1. Quando o arquivo passar na validação, selecione **Enviar** para iniciar a operação em massa do Azure que remove os membros do grupo.
1. Quando a operação de remoção for concluída, você verá uma notificação indicando que a operação em massa foi bem-sucedida.

## <a name="check-removal-status"></a>Verificar status da remoção

É possível ver o status de todas as suas solicitações em massa pendentes na página **Resultados da operação em massa**.

[![Verificar o status na página de Resultados das Operações em Massa](./media/groups-bulk-remove-members/bulk-center.png)](./media/groups-bulk-remove-members/bulk-center.png#lightbox)

Para obter detalhes de cada item de linha na operação em massa, escolha os valores nas colunas **Nº de Êxitos**, **Nº de Falhas** ou **Total de Solicitações**. Se ocorrerem falhas, os motivos da falha serão listados.

## <a name="bulk-removal-service-limits"></a>Remoção em massa de limites de serviço

Cada atividade em massa da qual remover uma lista de membros do grupo pode ser executada por até uma hora. Isso habilita a remoção de uma lista de pelo menos 40.000 membros.

## <a name="next-steps"></a>Próximas etapas

- [Importar membros do grupo em massa](groups-bulk-import-members.md)
- [Baixar membros de um grupo](groups-bulk-download-members.md)
- [Baixar uma lista de todos os grupos](groups-bulk-download.md)
