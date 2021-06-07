---
title: Azure Defender para SQL
description: Saiba mais sobre a funcionalidade para gerenciar suas vulnerabilidades de banco de dados e detectar atividades anormais que podem indicar uma ameaça ao banco de dados no banco de dados SQL do Azure, Azure SQL Instância Gerenciada ou Azure Synapse.
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 03/08/2021
ms.openlocfilehash: 27f17b3d1060e8b693c2a34cdb4643840f1bfd13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452298"
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
Há várias maneiras de habilitar os planos do Azure defender. Você pode habilitá-lo no nível da assinatura (**recomendado**) de:

- [Central de Segurança do Azure](#enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center)
- [Programaticamente com a API REST, CLI do Azure, PowerShell ou Azure Policy](#enable-azure-defender-plans-programatically)

Como alternativa, você pode habilitá-lo no nível de recurso conforme descrito em [habilitar o Azure defender para o banco de dados SQL do Azure no nível de recurso](#enable-azure-defender-for-azure-sql-database-at-the-resource-level)

### <a name="enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center"></a>Habilitar o Azure defender para o banco de dados SQL do Azure no nível de assinatura da central de segurança do Azure
Para habilitar o Azure defender para o banco de dados SQL do Azure no nível da assinatura de dentro da central de segurança do Azure:

1. No [portal do Azure](https://portal.azure.com), abra a **Central de Segurança**.
1. No menu da central de segurança, selecione **preços e configurações**.
1. Selecione a assinatura relevante.
1. Altere a configuração de plano para **ativado**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender-sql-subscription-level.png" alt-text="Habilitando o Azure defender para o banco de dados SQL do Azure no nível da assinatura.":::

1. Clique em **Salvar**.


### <a name="enable-azure-defender-plans-programatically"></a>Habilitar planos do Azure defender programaticamente 

A flexibilidade do Azure permite vários métodos programáticos para habilitar os planos do Azure defender. 

Use qualquer uma das ferramentas a seguir para habilitar o Azure defender para sua assinatura: 

| Método       | Instruções                                                                                                                                       |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| API REST     | [API de Preços](/rest/api/securitycenter/pricings)                                                                                                  |
| CLI do Azure    | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell   | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy | [Preços de pacotes](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|              |                                                                                                                                                    |

### <a name="enable-azure-defender-for-azure-sql-database-at-the-resource-level"></a>Habilitar o Azure defender para o banco de dados SQL do Azure no nível de recurso

É recomendável habilitar os planos do Azure defender no nível da assinatura e isso pode ajudar na criação de recursos desprotegidos. No entanto, se você tiver um motivo organizacional para habilitar o Azure defender no nível do servidor, use as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com), abra o servidor ou a instância gerenciada.
1. No título **segurança** , selecione **central de segurança**.
1. Selecione **habilitar Azure defender para SQL**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Habilite o Azure defender para SQL de dentro de bancos de dados SQL do Azure.":::

> [!NOTE]
> Uma conta de armazenamento é criada e configurada automaticamente para armazenar os resultados da verificação de **avaliação de vulnerabilidade** . Se você já tiver habilitado o Azure defender para outro servidor no mesmo grupo de recursos e região, a conta de armazenamento existente será usada.
>
> O custo do Azure defender está alinhado com o preço da camada Standard da central de segurança do Azure por nó, em que um nó é o servidor inteiro ou a instância gerenciada. Portanto, você está pagando apenas uma vez para proteger todos os bancos de dados no servidor ou instância gerenciada com o Azure defender. Você pode experimentar o Azure defender inicialmente com uma avaliação gratuita.


## <a name="manage-azure-defender-settings"></a>Gerenciar configurações do Azure defender

Para exibir e gerenciar as configurações do Azure defender:

1. Na área **segurança** do seu servidor ou instância gerenciada, selecione **central de segurança**.

    Nesta página, você verá o status do Azure defender para SQL:

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Verificando o status do Azure defender para SQL dentro de bancos de dados SQL do Azure.":::

1. Se o Azure defender para SQL estiver habilitado, você verá um link **Configurar** , conforme mostrado no gráfico anterior. Para editar as configurações do Azure defender para SQL, selecione **Configurar**.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="Configurações do Azure defender para SQL.":::

1. Faça as alterações necessárias e selecione **salvar**.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [avaliação de vulnerabilidade](sql-vulnerability-assessment.md)
- Saiba mais sobre a [proteção avançada contra ameaças](threat-detection-configure.md)
- Saiba mais sobre a [Central de Segurança do Azure](../../security-center/security-center-introduction.md)