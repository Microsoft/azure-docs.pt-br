---
title: Azure Defender para SQL
description: Saiba mais sobre a funcionalidade para gerenciar suas vulnerabilidades de banco de dados e detectar atividades anormais que podem indicar uma ameaça ao banco de dados no banco de dados SQL do Azure, Azure SQL Instância Gerenciada ou Azure Synapse.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 02/02/2021
ms.openlocfilehash: 48df96373554f6e474c3835bf81e38a9aea5450c
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508787"
---
# <a name="azure-defender-for-sql"></a>Azure Defender para SQL
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

O Azure Defender para SQL é um pacote unificado de funcionalidades avançadas de segurança do SQL. O Azure defender está disponível para o banco de dados SQL do Azure, o Azure SQL Instância Gerenciada e o Azure Synapse Analytics. Ele inclui a funcionalidade para descobrir e classificar dados confidenciais, identificando e atenuante banco de dados vulnerabilidades potenciais e detectar atividades anormais que podem indicar uma ameaça para seu banco de dados. Fornece um local único para habilitar e gerenciar esses recursos.

## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Quais são os benefícios do Azure Defender para SQL?

O Azure defender fornece um conjunto de recursos avançados de segurança do SQL, incluindo a avaliação de vulnerabilidades do SQL e a proteção avançada contra ameaças.
- A [avaliação de vulnerabilidade](sql-vulnerability-assessment.md) é um serviço fácil de configurar que pode descobrir, acompanhar e ajudá-lo a corrigir possíveis vulnerabilidades no banco de dados. Ele fornece visibilidade sobre seu estado de segurança e inclui etapas acionáveis para resolver problemas de segurança e aprimorar seu banco de dados fortifications.
- A [Proteção Avançada contra Ameaças](threat-detection-overview.md) detecta atividades anômalas, indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar seus bancos de dados. Ele monitora continuamente seu banco de dados em busca de atividades suspeitas e fornece alertas de segurança imediatos sobre possíveis vulnerabilidades, ataques de injeção de SQL do Azure e padrões de acesso de banco de dados anormais. Os alertas da Proteção Avançada contra Ameaças fornecem detalhes de atividades suspeitas e recomendam ações para investigar e atenuar a ameaça.

Habilite o Azure Defender para SQL uma vez para habilitar todos esses recursos incluídos. Com um clique, você pode habilitar o Azure defender para todos os bancos de dados em seu [servidor](logical-servers.md) no Azure ou em seu instância gerenciada do SQL. Habilitar ou gerenciar as configurações do Azure defender requer que pertença à função do [Gerenciador de segurança do SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) ou a uma das funções de administrador de banco de dados ou servidor.

Para obter mais informações sobre preços do Azure defender para SQL, consulte a [página de preços da central de segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-azure-defender"></a>Habilitar o Azure Defender

O Azure defender pode ser acessado por meio do [portal do Azure](https://portal.azure.com). Habilite o Azure defender navegando até a **central de segurança** no título de **segurança** do seu servidor ou instância gerenciada.

> [!NOTE]
> Uma conta de armazenamento é criada e configurada automaticamente para armazenar os resultados da verificação de **avaliação de vulnerabilidade** . Se você já tiver habilitado o Azure defender para outro servidor no mesmo grupo de recursos e região, a conta de armazenamento existente será usada.
>
> O custo do Azure defender está alinhado com o preço da camada Standard da central de segurança do Azure por nó, em que um nó é o servidor inteiro ou a instância gerenciada. Portanto, você está pagando apenas uma vez para proteger todos os bancos de dados no servidor ou instância gerenciada com o Azure defender. Você pode experimentar o Azure defender inicialmente com uma avaliação gratuita.

:::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Habilitar o Azure defender para SQL de dentro de bancos de dados SQL do Azure":::

## <a name="track-vulnerabilities-and-investigate-threat-alerts"></a>Acompanhar vulnerabilidades e investigar alertas de ameaça

Clique no cartão **Avaliação de Vulnerabilidade** para exibir e gerenciar relatórios e verificações de vulnerabilidade e relatórios e acompanhar sua estatura de segurança. Se alertas de segurança tiverem sido recebidos, clique no cartão **proteção avançada contra ameaças** para exibir detalhes dos alertas e para ver um relatório consolidado sobre todos os alertas em sua assinatura do Azure por meio da página alertas de segurança da central de segurança do Azure.

## <a name="manage-azure-defender-settings"></a>Gerenciar configurações do Azure defender

Para exibir e gerenciar as configurações do Azure defender:

1. Na área **segurança** do seu servidor ou instância gerenciada, selecione **central de segurança**.

    Nesta página, você verá o status do Azure defender para SQL:

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Verificando o status do Azure defender para SQL dentro de bancos de dados SQL do Azure":::

1. Se o Azure defender para SQL estiver habilitado, você verá um link **Configurar** , conforme mostrado no gráfico anterior. Para editar as configurações do Azure defender para SQL, selecione **Configurar**.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="configurações do servidor de segurança":::

1. Faça as alterações necessárias e selecione **salvar**.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [avaliação de vulnerabilidade](sql-vulnerability-assessment.md)
- Saiba mais sobre a [proteção avançada contra ameaças](threat-detection-configure.md)
- Saiba mais sobre a [Central de Segurança do Azure](../../security-center/security-center-introduction.md)