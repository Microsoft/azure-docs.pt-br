---
title: Descoberta e Classificação de Dados
description: Data Discovery & classificação para o banco de dados SQL do Azure, Azure SQL Instância Gerenciada e Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
titleSuffix: Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 12/01/2020
tags: azure-synapse
ms.openlocfilehash: 41ad711de81f075727e4c177c318054677cb0ece
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185344"
---
# <a name="data-discovery--classification"></a>Descoberta e Classificação de Dados
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

A descoberta de dados & classificação é incorporada ao banco de dado SQL do Azure, SQL Instância Gerenciada do Azure e Azure Synapse Analytics. Ele fornece recursos básicos para descobrir, classificar, rotular e relatar os dados confidenciais em seus bancos de dado.

Seus dados mais confidenciais podem incluir informações comerciais, financeiras, de saúde ou pessoais. Descobrir e classificar esses dados pode desempenhar uma função dinâmica na abordagem de proteção de informações da sua organização. Isso pode servir como infraestrutura para:

- Ajudando a atender aos padrões de privacidade de dados e requisitos de conformidade regulatória.
- Vários cenários de segurança, como o monitoramento (auditoria) de acesso a dados confidenciais.
- Controlando o acesso e protegendo a segurança de bancos de dados que contêm um dado altamente confidencial.

> [!NOTE]
> Para obter informações sobre SQL Server locais, consulte [classificação de & de descoberta de dados do SQL](/sql/relational-databases/security/sql-data-discovery-and-classification).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>O que é a descoberta de dados & classificação?

A classificação de & de descoberta de dados apresenta um conjunto de serviços básicos e novos recursos no Azure. Ele forma um novo paradigma de proteção de informações para o banco de dados SQL, o SQL Instância Gerenciada e o Azure Synapse, destinado à proteção dos mesmos, e não apenas ao banco. O paradigma inclui:

- **Descoberta e recomendações:** O mecanismo de classificação verifica seu banco de dados e identifica as colunas que contenham informações potencialmente confidenciais. Em seguida, ele fornece uma maneira fácil de revisar e aplicar a classificação recomendada por meio do portal do Azure.

- **Rotulagem:** Você pode aplicar rótulos de classificação de sensibilidade persistentemente a colunas usando novos atributos de metadados que foram adicionados ao mecanismo de banco de dados SQL Server. Esses metadados podem ser usados para cenários de proteção e auditoria baseados em sensibilidade.

- **Sensibilidade do conjunto de resultados da consulta:** A sensibilidade de um conjunto de resultados de consulta é calculada em tempo real para fins de auditoria.

- **Visibilidade:** Você pode exibir o estado de classificação do banco de dados em um painel detalhado na portal do Azure. Além disso, você pode baixar um relatório no formato do Excel para usar para fins de conformidade e auditoria e outras necessidades.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Descobrir, classificar e rotular colunas confidenciais

Esta seção descreve as etapas para:

- Descobrindo, classificando e rotulando colunas que contêm dados confidenciais em seu banco de dado.
- Exibindo o estado de classificação atual do banco de dados e exportando relatórios.

A classificação inclui dois atributos de metadados:

- **Rótulos**: os principais atributos de classificação, usados para definir o nível de sensibilidade dos dados armazenados na coluna.  
- **Tipos de informações**: atributos que fornecem informações mais granulares sobre o tipo de dados armazenados na coluna.

### <a name="define-and-customize-your-classification-taxonomy"></a>Definir e personalizar sua taxonomia de classificação

A classificação de & de descoberta de dados vem com um conjunto interno de rótulos de sensibilidade e um conjunto interno de tipos de informações e lógica de descoberta. Agora é possível personalizar essa taxonomia e definir um conjunto e uma classificação de constructos de classificação especificamente para seu ambiente.

Você define e personaliza sua taxonomia de classificação em um local central para toda a organização do Azure. Esse local está na [central de segurança do Azure](../../security-center/security-center-introduction.md), como parte de sua política de segurança. Somente alguém com direitos administrativos no grupo de gerenciamento raiz da organização pode realizar essa tarefa.

Como parte do gerenciamento de políticas para proteção de informações, você pode definir rótulos personalizados, classificá-los e associá-los a um conjunto selecionado de tipos de informações. Você também pode adicionar seus próprios tipos de informações personalizados e configurá-los com padrões de cadeia de caracteres. Os padrões são adicionados à lógica de descoberta para identificar esse tipo de dados em seus bancos.

Para obter mais informações, consulte [Personalizar a política de proteção de informações do SQL na central de segurança do Azure (versão prévia)](../../security-center/security-center-info-protection-policy.md).

Depois que a política de toda a organização tiver sido definida, você poderá continuar classificando bancos de dados individuais usando sua política personalizada.

### <a name="classify-your-database"></a>Classificar seu banco de dados

> [!NOTE]
> O exemplo a seguir usa o banco de dados SQL do Azure, mas você deve selecionar o produto apropriado que deseja configurar a descoberta & data Discovery.

1. Vá para o [Portal do Azure](https://portal.azure.com).

1. Vá para **descoberta de dados & classificação** no título **segurança** em seu painel de banco de dados SQL do Azure. A guia Visão geral inclui um resumo do estado de classificação atual do banco de dados. O resumo inclui uma lista detalhada de todas as colunas classificadas, que você também pode filtrar para mostrar apenas partes de esquema, tipos de informações e rótulos específicos. Se você ainda não classificou nenhuma coluna, [pule para a etapa 4](#step-4).

    ![Visão geral](./media/data-discovery-and-classification-overview/data-discovery-and-classification.png)

1. Para baixar um relatório no formato do Excel, selecione **Exportar** no menu superior do painel.

1. <a id="step-4"></a>Para começar a classificar seus dados, selecione a guia **classificação** na página **classificação de & de dados data Discovery** .

    O mecanismo de classificação verifica o banco de dados em busca de colunas que contenham dado potencialmente confidenciais e fornece uma lista de classificações de coluna recomendadas.

1. Exibir e aplicar recomendações de classificação:

   - Para exibir a lista de classificações de coluna recomendadas, selecione o painel recomendações na parte inferior do painel.

   - Para aceitar uma recomendação para uma coluna específica, marque a caixa de seleção na coluna à esquerda da linha relevante. Para marcar todas as recomendações como aceitas, marque a caixa de seleção mais à esquerda no cabeçalho da tabela de recomendações.

   - Para aplicar as recomendações selecionadas, selecione **aceitar recomendações selecionadas**.

   ![Recomendações para classificação](./media/data-discovery-and-classification-overview/recommendation.png)

1. Você também pode classificar colunas manualmente, como uma alternativa ou além da classificação baseada em recomendação:

   1. Selecione **Adicionar classificação** no menu superior do painel.

   1. Na janela de contexto que é aberta, selecione o esquema, a tabela e a coluna que você deseja classificar e o tipo de informação e o rótulo de sensibilidade.

   1. Selecione **Adicionar classificação** na parte inferior da janela de contexto.

   ![Adicionar a classificação manualmente](./media/data-discovery-and-classification-overview/manually-add-classification.png)


1. Para concluir sua classificação e rotular de forma persistente (marca) as colunas do banco de dados com os novos metadados de classificação, selecione **salvar** na página **classificação** .

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Auditoria de acesso a dados confidenciais

Um aspecto importante do paradigma da proteção de informações é a capacidade de monitorar o acesso a dados confidenciais. A [auditoria do SQL do Azure](../../azure-sql/database/auditing-overview.md) foi aprimorada para incluir um novo campo no log de auditoria chamado `data_sensitivity_information` . Esse campo registra as classificações de sensibilidade (rótulos) dos dados que foram retornados por uma consulta. Veja um exemplo:

![Log de auditoria](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Permissões

Essas funções internas podem ler a classificação de dados de um banco de dado:

- Proprietário
- Leitor
- Colaborador
- Gerenciador de Segurança do SQL
- Administrador de Acesso do Usuário

Essas funções internas podem modificar a classificação de dados de um banco de dado:

- Proprietário
- Colaborador
- Gerenciador de Segurança do SQL

Saiba mais sobre permissões baseadas em função no [RBAC do Azure](../../role-based-access-control/overview.md).

## <a name="manage-classifications"></a><a id="manage-classification"></a>Gerenciar classificações

Você pode usar o T-SQL, uma API REST ou o PowerShell para gerenciar as classificações.

### <a name="use-t-sql"></a>Usar T-SQL

Você pode usar o T-SQL para adicionar ou remover classificações de coluna e para recuperar todas as classificações de todo o banco de dados.

> [!NOTE]
> Quando você usa o T-SQL para gerenciar rótulos, não há nenhuma validação que os rótulos que você adiciona a uma coluna existam na política de proteção de informações da organização (o conjunto de rótulos que aparecem nas recomendações do Portal). Então, cabe a você validar isso.

Para obter informações sobre como usar o T-SQL para classificações, consulte as seguintes referências:

- Para adicionar ou atualizar a classificação de uma ou mais colunas: [Adicionar classificação de sensibilidade](/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Para remover a classificação de uma ou mais colunas: [remover classificação de sensibilidade](/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Para exibir todas as classificações no banco de dados: [Sys.sensitivity_classifications](/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>Usar cmdlets do PowerShell
Gerencie classificações e recomendações para o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada usando o PowerShell.

#### <a name="powershell-cmdlets-for-azure-sql-database"></a>Cmdlets do PowerShell para o banco de dados SQL do Azure

- [Get-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Habilitar-AzSqlDatabaSesensitivityRecommendation](/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Desabilitar-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-azure-sql-managed-instance"></a>Cmdlets do PowerShell para Azure SQL Instância Gerenciada

- [Get-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Habilitar-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Desabilitar-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>Usar a API REST

Você pode usar a API REST para gerenciar programaticamente as classificações e recomendações. A API REST publicada dá suporte às seguintes operações:

- [Criar ou Atualizar](/rest/api/sql/sensitivitylabels/createorupdate): Cria ou atualiza o rótulo de confidencialidade da coluna especificada.
- [Excluir](/rest/api/sql/sensitivitylabels/delete): exclui o rótulo de sensibilidade da coluna especificada.
- [Desabilitar recomendação](/rest/api/sql/sensitivitylabels/disablerecommendation): desabilita as recomendações de sensibilidade na coluna especificada.
- [Habilitar recomendação](/rest/api/sql/sensitivitylabels/enablerecommendation): habilita as recomendações de sensibilidade na coluna especificada. (As recomendações são habilitadas por padrão em todas as colunas.)
- [Get](/rest/api/sql/sensitivitylabels/get): Obtém o rótulo de sensibilidade da coluna especificada.
- [Listar atual pelo banco de dados](/rest/api/sql/sensitivitylabels/listcurrentbydatabase): Obtém os rótulos de sensibilidade atuais do banco de dados especificado.
- [Lista recomendada pelo banco de dados](/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): Obtém os rótulos de sensibilidade recomendados do banco de dados especificado.

## <a name="next-steps"></a><a id="next-steps"></a>Próximas etapas

- Considere configurar a [auditoria do SQL Azure](../../azure-sql/database/auditing-overview.md) para monitorar e auditar o acesso aos seus dados confidenciais classificados.
- Para uma apresentação que inclui a descoberta de dados & classificação, consulte [descobrindo, classificando, rotulando & protegendo dados SQL | Dados expostos](https://www.youtube.com/watch?v=itVi9bkJUNc).
