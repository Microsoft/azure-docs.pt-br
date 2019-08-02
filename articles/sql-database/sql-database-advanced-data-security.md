---
title: Segurança de Dados Avançada – Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba sobre a funcionalidade para descobrir e classificar dados sensíveis, gerenciando suas vulnerabilidades do banco de dados e detectando atividades anômalas que podem indicar uma ameaça ao banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: v-mohabe
ms.reviewer: vanto
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: ce02fee31041222c48d62ef8410b97fedf74dfdb
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297670"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Segurança de Dados Avançada para Banco de Dados SQL do Azure

A Segurança de Dados Avançada do SQL é um pacote unificado de funcionalidades avançadas de segurança do SQL. Ele inclui a funcionalidade para descobrir e classificar dados confidenciais, identificando e atenuante banco de dados vulnerabilidades potenciais e detectar atividades anormais que podem indicar uma ameaça para seu banco de dados. Fornece um local único para habilitar e gerenciar esses recursos.

## <a name="overview"></a>Visão geral

O ADS (Advanced Data Security) fornece um conjunto de recursos avançados de segurança do SQL, incluindo a descoberta de dados & classificação, avaliação de vulnerabilidade e proteção avançada contra ameaças.

- A [Descoberta de dados e classificação](sql-database-data-discovery-and-classification.md) (atualmente na versão prévia) fornece recursos internos avançados no Banco de Dados SQL do Azure para descobrir, classificar, rotular e proteger os dados sensíveis em seus bancos de dados. Pode ser usada para fornecer visibilidade em seu estado de classificação do banco de dados e para controlar o acesso a dados confidenciais no banco de dados e, além de suas bordas.
- A [Avaliação de vulnerabilidade ](sql-vulnerability-assessment.md) é um serviço fácil de ser configurado que pode descobrir, acompanhar e ajudar a corrigir possíveis vulnerabilidades do banco de dados. Fornece visibilidade sobre o estado de segurança e inclui etapas acionáveis para resolver problemas de segurança e aperfeiçoar as fprtificações do banco de dados.
- A [proteção avançada contra ameaças](sql-database-threat-detection-overview.md) detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar seu banco de dados. Monitora continuamente o banco de dados com relação a atividades suspeitas e fornece alertas de segurança imediata sobre vulnerabilidades potenciais, ataques de injeção de SQL e padrões de acesso anormal do banco de dados. Alertas de proteção avançada contra ameaças fornecem detalhes da atividade suspeita e recomendam a ação de como investigar e mitigar a ameaça.

Habilite a ADS do SQL uma vez para habilitar todos esses recursos incluídos. Com um clique, você pode habilitar a ADS para todos os bancos de dados em seu servidor do Banco de Dados SQL ou instância gerenciada. A habilitação ou o gerenciamento das configurações da ADS requer a função de [Gerenciador de Segurança de SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager), a função de administrador do banco de dados SQL ou a função de administrador do servidor SQL. 

O preço da ADS está alinhado à camada padrão da Central de Segurança do Azure, na qual cada instância gerenciada ou servidor do Banco de Dados SQL protegido é contado como um nó. Recursos protegidos recentemente se qualificam para uma avaliação gratuita da camada Standard da Central de Segurança. Para obter mais informações, confira a [página de preços](https://azure.microsoft.com/pricing/details/security-center/) da Central de Segurança do Azure.

## <a name="getting-started-with-ads"></a>Introdução à ADS

As etapas a seguir são para você começar a usar a ADS.

## <a name="1-enable-ads"></a>1. Habilitar a ADS

Habilite a ADS navegando até **Segurança de Dados Avançada** sob o título **Segurança** do servidor do Banco de Dados SQL ou da instância gerenciada. Para habilitar a ADS para todos os bancos de dados no servidor de banco de dados ou instância gerenciada, clique em **Habilitar segurança de dados avançada no servidor**.

> [!NOTE]
> Uma conta de armazenamento é criada e configurada automaticamente para armazenar os resultados da verificação de **avaliação de vulnerabilidade** . Se você já tiver habilitado o ADS para outro servidor no mesmo grupo de recursos e região, a conta de armazenamento existente será usada.

![Habilitar a ADS](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> O custo da ADS está alinhado aos preços da camada Standard da Central de Segurança do Azure por nó, em que um nó é toda a instância gerenciada ou servidor do Banco de Dados SQL. Portanto, você está pagando apenas uma vez para proteger todos os bancos de dados na instância gerenciada ou servidor de banco de dados com a ADS. Você pode experimentar a ADS inicialmente com uma avaliação gratuita.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Iniciar dados de classificação, vulnerabilidades de rastreamento e investigação de alertas de ameaça

Clique no cartão **Descoberta de Dados e Classificação** para ver colunas confidenciais a serem classificadas e classificar seus dados com rótulos de confidencialidade persistente. Clique no cartão **Avaliação de Vulnerabilidade** para exibir e gerenciar relatórios e verificações de vulnerabilidade e relatórios e acompanhar sua estatura de segurança. Se alertas de segurança tiverem sido recebidos, clique no cartão **proteção avançada contra ameaças** para exibir detalhes dos alertas e para ver um relatório consolidado sobre todos os alertas em sua assinatura do Azure por meio da página alertas de segurança da central de segurança do Azure.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. Gerenciar configurações da ADS em seu servidor do Banco de Dados SQL ou instância gerenciada

Para exibir e gerenciar as configurações da ADS, navegue até **Segurança de Dados Avançada** sob o título **Segurança** para seu servidor do Banco de Dados SQL ou instância gerenciada. Nessa página, você pode habilitar ou desabilitar anúncios e modificar as configurações de avaliação de vulnerabilidade e proteção avançada contra ameaças para todo o servidor do banco de dados SQL ou instância gerenciada.

![Configurações do servidor](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. Gerenciar configurações da ADS para um Banco de Dados SQL

Para substituir as configurações da ADS para determinado banco de dados, marque a caixa de seleção **Habilitar Segurança de Dados Avançada no nível do banco de dados**. Use esta opção somente se você tiver um requisito específico para receber alertas separados de proteção avançada contra ameaças ou resultados de avaliação de vulnerabilidade para o banco de dados individual, no lugar do ou além dos alertas e resultados recebidos para todos os bancos de dado no servidor de banco de dados ou instância gerenciada.

Depois que a caixa de seleção for selecionada, você poderá definir as configurações relevantes para esse banco de dados.
 
![Configurações de proteção avançada contra ameaças e banco de dados](./media/sql-advanced-protection/database_threat_detection_settings.png) 

As configurações de Segurança de Dados Avançada para seu servidor de banco de dados ou instância gerenciada também podem ser acessadas do painel de banco de dados da ADS. Clique em **Configurações** no painel principal da ADS e, em seguida, clique em **Exibir configurações do servidor da Segurança de Dados Avançada**. 

![Configurações de banco de dados](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Próximas etapas 

- Saiba mais sobre [descoberta e classificação de dados](sql-database-data-discovery-and-classification.md) 
- Saiba mais sobre [avaliação de vulnerabilidade](sql-vulnerability-assessment.md) 
- Saiba mais sobre a [proteção avançada contra ameaças](sql-database-threat-detection.md)
- Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
