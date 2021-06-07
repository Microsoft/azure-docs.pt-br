---
title: Conectar-se ao compartilhamento de dados do Azure
description: Este artigo descreve como conectar uma conta do compartilhamento de dados do Azure com o Azure alcance para pesquisar ativos e acompanhar a linhagem de dados.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: edea881d67d5a677339c6ff357c1ac45f5dfd420
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96551850"
---
# <a name="how-to-connect-azure-data-share-and-azure-purview"></a>Como conectar o compartilhamento de dados do Azure e o Azure alcance

Este artigo discute como conectar sua conta de [compartilhamento de dados do Azure](../data-share/overview.md) com o Azure alcance e controlar os conjuntos de dados compartilhados (enviados e recebidos) em seu espaço de dado. Várias pessoas de governança de dados podem descobrir e controlar a linhagem de dados entre limites, como organizações, departamentos e até mesmo data centers.

## <a name="common-scenarios"></a>Cenários comuns

A linhagem de compartilhamento de dados destina-se a fornecer informações detalhadas para análise de causa raiz e análise de impacto.

### <a name="scenario-1-360-view-of-datasets-shared-inout-for-a-partner-organization-or-internal-department"></a>Cenário 1:360 exibição de conjuntos de valores compartilhados de entrada/saída para uma organização parceira ou departamento interno

Os data diriges podem ver uma lista de todos os conjuntos de dados que são compartilhados bidirecionalmente com suas organizações parceiras. Eles podem pesquisar e descobrir os conjuntos de valores por nome da organização e ver uma exibição completa de todos os compartilhamentos de entrada e de saída.

### <a name="scenario-2-root-cause-analysis---upstream-dependency-on-datasets-coming-into-organization-consumer-view-of-incoming-shares"></a>Cenário 2: análise da causa raiz – dependência de upstream em conjuntos de valores que entram na organização (exibição do consumidor de compartilhamentos de entrada)

Um relatório tem informações incorretas devido a problemas de dados upstream de uma conta de compartilhamento de dados externos. Os engenheiros de dados podem entender as falhas de upstream, ser informado sobre os motivos e entrar em contato com o proprietário do compartilhamento para corrigir os problemas que causam a discrepância dos dados.

### <a name="scenario-3-impact-analysis-on-datasets-going-outside-organization-provider-view-of-outgoing-shares"></a>Cenário 3: análise de impacto em conjuntos de valores indo fora da organização (exibição de provedor de compartilhamentos de saída)

Os produtores de dados desejam saber quem será afetado na realização de uma alteração em relação ao conjunto. Usando a linhagem, um produtor de dados pode entender facilmente o impacto dos parceiros internos ou externos downstream que estão consumindo dados usando o compartilhamento de dados do Azure.

## <a name="azure-data-share-and-purview-connected-experience"></a>Compartilhamento de dados do Azure e experiência conectada do alcance

Para conectar o compartilhamento de dados do Azure e a conta do Azure alcance, faça o seguinte:

1. Crie uma conta do alcance. Todas as informações de linhagem do compartilhamento de dados serão coletadas por uma conta do alcance. Você pode usar um existente ou criar uma nova conta do alcance.

1. Conecte seu compartilhamento de dados do Azure à sua conta do alcance.

    1. No portal do alcance, você pode ir para o **centro de gerenciamento** e conectar o compartilhamento de dados do Azure na seção **conexões externas** .
    1. Selecione **+ novo** na barra superior, localize o compartilhamento de dados do Azure na barra lateral pop-up e adicione a conta de compartilhamento de dados. Execute um trabalho de instantâneo depois de conectar seu compartilhamento de dados à conta do alcance, para que os ativos de compartilhamento de dados e as informações de linhagem sejam visíveis no alcance.

       :::image type="content" source="media/how-to-link-azure-data-share/connect-to-data-share.png" alt-text="Centro de gerenciamento para vincular o compartilhamento de dados do Azure":::

1. Execute o instantâneo no compartilhamento de dados do Azure.

    - Depois que a conexão do compartilhamento de dados do Azure for estabelecida com o Azure alcance, você poderá executar um instantâneo para seus compartilhamentos existentes. 
    - Se você não tiver compartilhamentos existentes, vá para o portal de compartilhamento de dados do Azure para [compartilhar seus dados](../data-share/share-your-data.md) [e assinar um compartilhamento de dados](../data-share/subscribe-to-data-share.md).
    - Depois que o instantâneo de compartilhamento for concluído, você poderá exibir os ativos de compartilhamento de dados associados e a linhagem em alcance.

1. Descubra contas de compartilhamento de dados e compartilhe informações em sua conta do alcance.

    - Na home page da conta alcance, selecione **procurar por tipo de ativo** e selecione o bloco **compartilhamento de dados do Azure** . Você pode procurar um nome de conta, nome de compartilhamento, instantâneo de compartilhamento ou organização parceira. Caso contrário, aplique filtros na página de resultado da pesquisa para nome da conta, tipo de compartilhamento (compartilhamentos enviados vs recebidos).

       :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-search-result-page.png" alt-text="Compartilhamento de dados do Azure na página de resultados da pesquisa":::

    >[!Important]
    >Para ativos de compartilhamento de dados a serem mostrados no alcance, um trabalho de instantâneo deve ser executado depois que você conectar seu compartilhamento de dados ao alcance.

1. Rastreie a linhagem de conjuntos de dados compartilhados com o compartilhamento de data do Azure.

    - Na página resultado da pesquisa do alcance, escolha o instantâneo de compartilhamento de dados (recebido/enviado) e selecione a guia **linhagem** para ver um gráfico de linhagem com dependências upstream e downstream.

    :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-lineage.png" alt-text="Linhagem de conjuntos de dados compartilhados usando o compartilhamento de data do Azure":::

## <a name="next-steps"></a>Próximas etapas

- [Guia do usuário de linhagem de catálogo](catalog-lineage-user-guide.md)
- [Link para Azure Data Factory para linhagem](how-to-link-azure-data-factory.md)
