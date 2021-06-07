---
title: Descoberta e Classificação de Dados
description: Descoberta e Classificação de Dados para Banco de Dados SQL do Azure, Instância Gerenciada de SQL do Azure e Azure Synapse Analytics
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
ms.date: 02/17/2021
tags: azure-synapse
ms.openlocfilehash: f47303a953dbb06b4c9efdfbbae41715cad81d2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690797"
---
# <a name="data-discovery--classification"></a>Descoberta e Classificação de Dados
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

A Descoberta e Classificação de Dados é integrada ao Banco de Dados SQL do Azure, à Instância Gerenciada de SQL do Azure e ao Azure Synapse Analytics. Fornece as funcionalidades básicas para descobrir, classificar, rotular e relatar os dados confidenciais nos seus bancos de dados.

Os dados mais confidenciais podem incluir informações comerciais, financeiras, de saúde ou pessoais. A descoberta e a classificação desses dados têm papel essencial na abordagem de proteção de informações da organização. Esse recurso pode funcionar como a infraestrutura para:

- Ajudar a atender aos padrões de privacidade de dados e requisitos de conformidade regulatória.
- Vários cenários de segurança, como o monitoramento (auditoria) de acesso anômalo a dados confidenciais.
- Controlar o acesso e fortalecer a segurança de bancos de dados que contêm dados altamente confidenciais.

> [!NOTE]
> Para obter informações sobre SQL Server local, consulte [Descoberta e Classificação de Dados do SQL](/sql/relational-databases/security/sql-data-discovery-and-classification).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>O que é a Descoberta e Classificação de Dados?

A Descoberta e Classificação de Dados lança um conjunto de serviços básicos e de novos recursos no Azure. Elas criam um paradigma de proteção de informações no Banco de Dados SQL, na Instância Gerenciada de SQL e no Azure Synapse, destinado à proteção dos dados e não apenas do banco de dados. O paradigma inclui:

- **Descoberta e recomendações:** o mecanismo de classificação examina o banco de dados e identifica colunas contendo dados potencialmente confidenciais. Em seguida, ele oferece uma forma fácil de examinar e aplicar a classificação recomendada por meio do portal do Azure.

- **Rotulagem:** você pode aplicar rótulos de classificação de sensibilidade persistentemente a colunas usando novos atributos de metadados que foram adicionados ao mecanismo de banco de dados do SQL Server. Esses metadados podem ser usados para proteção e auditoria baseada em confidencialidade.

- **Confidencialidade do conjunto de resultados da consulta:** a confidencialidade do conjunto de resultados da consulta é calculada em tempo real para fins de auditoria.

- **Visibilidade:** é possível exibir o estado da classificação do banco de dados em um dashboard detalhado no portal do Azure. Além disso, você pode baixar um relatório no formato do Excel para fins de conformidade e auditoria e outras necessidades.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Descobrir, classificar e rotular colunas confidenciais

Esta seção descreve as etapas para:

- Descobrir, classificar e rotular as colunas que contêm dados confidenciais em um banco de dado.
- Ver o estado de classificação atual do banco de dados e exportar relatórios.

A classificação inclui dois atributos de metadados:

- **Rótulos**: os atributos de classificação principais, usados para definir o nível de confidencialidade dos dados armazenados na coluna.  
- **Tipos de informações**: atributos que mostram informações mais granulares sobre o tipo de dados armazenados na coluna.

### <a name="define-and-customize-your-classification-taxonomy"></a>Definir e personalizar sua taxonomia de classificação

A Descoberta e Classificação de Dados vem com um conjunto interno de rótulos de confidencialidade e um conjunto interno de tipos de informações e lógica de descoberta. Agora é possível personalizar essa taxonomia e definir um conjunto e uma classificação de constructos de classificação especificamente para seu ambiente.

Você define e personaliza a taxonomia de classificação em um só ponto central para toda a organização do Azure. O local é a [Central de Segurança do Azure](../../security-center/security-center-introduction.md), como parte da política de segurança. Somente alguém com direitos administrativos no grupo de gerenciamento raiz da organização pode executar essa tarefa.

Como parte do gerenciamento de políticas da proteção de informações, você pode definir rótulos personalizados, classificá-los e associá-los a um conjunto selecionado de tipos de informações. Você também pode adicionar seus próprios tipos de informações personalizados e configurá-los com padrões de cadeia de caracteres. Os padrões são adicionados à lógica de descoberta para identificar esse tipo de dados nos bancos de dados.

Para mais informações, confira [Personalizar a política de proteção de informações do SQL na Central de Segurança do Azure (versão prévia)](../../security-center/security-center-info-protection-policy.md).

Após a definição da política para toda a organização, você pode continuar a classificação de bancos de dados individuais com a política personalizada.

### <a name="classify-your-database"></a>Classificar um banco de dados

> [!NOTE]
> O exemplo a seguir usa o banco de dados SQL do Azure, mas você deve selecionar o produto apropriado em que deseja configurar a Descoberta e Classificação de Dados.

1. Acesse o [portal do Azure](https://portal.azure.com).

1. Navegue para **Descoberta e Classificação de Dados** no cabeçalho **Segurança** do painel do Banco de Dados SQL do Azure. A guia Visão Geral mostra um resumo do estado de classificação atual do banco de dados. O resumo inclui a lista detalhada de todas as colunas classificadas, que você também pode filtrar para mostrar apenas determinadas partes de esquema, tipos de informação e rótulos. Se você ainda não classificou nenhuma coluna, [pule para a etapa 4](#step-4).

    ![Visão geral](./media/data-discovery-and-classification-overview/data-discovery-and-classification.png)

1. Para baixar um relatório no formato Excel, clique na opção **Exportar** no menu superior do painel.

1. <a id="step-4"></a>Para começar a classificar os dados, selecione a guia **Classificação** na página **Descoberta e Classificação de Dados**.

    O mecanismo de classificação verifica o banco de dados em busca de colunas que contenham dados possivelmente confidenciais e mostra uma lista de classificações de coluna recomendadas.

1. Veja e aplique as recomendações de classificação:

   - Para ver a lista das classificações de colunas recomendadas, selecione o painel de recomendações na parte inferior do painel.

   - Para aceitar a recomendação de uma coluna específica, marque a caixa de seleção na coluna à esquerda da linha relevante. Para marcar todas as recomendações como aceitas, marque a última caixa de seleção à esquerda no cabeçalho da tabela de recomendações.

   - Para aplicar as recomendações selecionadas, selecione **Aceitar recomendações selecionadas**.

   ![Recomendações de classificação](./media/data-discovery-and-classification-overview/recommendation.png)

1. Você também pode classificar as colunas manualmente como uma alternativa ou além da classificação de recomendação:

   1. Selecione **Adicionar classificação** no menu superior do painel.

   1. Na janela de contexto que é aberta, selecionar o esquema, a tabela e a coluna que você deseja classificar e, em seguida, o tipo de informações e o rótulo de confidencialidade.

   1. Selecione **Adicionar classificação** na parte inferior da janela de contexto.

   ![Adicionar a classificação manualmente](./media/data-discovery-and-classification-overview/manually-add-classification.png)


1. Para concluir a classificação e rotular (marcar) de maneira persistente as colunas do banco de dados com os novos metadados de classificação, selecione **Salvar** na página **Classificação**.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Auditar o acesso aos dados confidenciais

Um aspecto importante do paradigma da proteção de informações é a capacidade de monitorar o acesso a dados confidenciais. A [Auditoria do SQL do Azure](../../azure-sql/database/auditing-overview.md) foi aprimorada para incluir um novo campo no log de auditoria, chamado `data_sensitivity_information`. O campo registra as classificações de confidencialidade (rótulos) dos dados que foram retornados por uma consulta. Veja um exemplo:

![Log de auditoria](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Permissões

Estas funções internas podem ler a classificação de dados de um banco de dados:

- Proprietário
- Leitor
- Colaborador
- Gerenciador de Segurança do SQL
- Administrador de Acesso do Usuário

Estas funções internas podem modificar a classificação de dados de um banco de dados:

- Proprietário
- Colaborador
- Gerenciador de Segurança do SQL

Saiba mais sobre permissões baseadas em função no [RBAC do Azure](../../role-based-access-control/overview.md).

## <a name="manage-classifications"></a><a id="manage-classification"></a>Gerenciar classificações

Você pode usar o T-SQL, uma API REST ou o PowerShell para gerenciar as classificações.

### <a name="use-t-sql"></a>Usar T-SQL

Você pode usar o T-SQL para adicionar ou remover classificações de coluna, bem como para recuperar todas as classificações do banco de dados inteiro.

> [!NOTE]
> Ao usar o T-SQL para gerenciar rótulos, não há nenhuma validação de que os rótulos adicionados a uma coluna existem na política de proteção de informações organizacionais (o conjunto de rótulos exibido nas recomendações do portal). Portanto, cabe a você validar isso.

Para saber como usar o T-SQL para classificações, confira as seguintes referências:

- Para adicionar ou atualizar a classificação de uma ou mais colunas: [ADD SENSITIVITY CLASSIFICATION](/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Para remover a classificação de uma ou mais colunas: [DROP SENSITIVITY CLASSIFICATION](/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Para ver todas as classificações no banco de dados: [sys.sensitivity_classifications](/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>Usar cmdlets do PowerShell
Gerencie as classificações e as recomendações do Banco de Dados SQL do Azure e da Instância Gerenciada de SQL do Azure com o PowerShell.

#### <a name="powershell-cmdlets-for-azure-sql-database"></a>Cmdlets do PowerShell para o Banco de Dados SQL do Azure

- [Get-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-azure-sql-managed-instance"></a>Cmdlets do PowerShell para a Instância Gerenciada de SQL do Azure

- [Get-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>Usar a API REST

Você pode usar a API REST para gerenciar programaticamente classificações e recomendações. A API REST publicada dá suporte às seguintes operações:

- [Criar ou Atualizar](/rest/api/sql/sensitivitylabels/createorupdate): Cria ou atualiza o rótulo de confidencialidade da coluna especificada.
- [Excluir](/rest/api/sql/sensitivitylabels/delete): exclui o rótulo de confidencialidade da coluna especificada.
- [Desabilitar recomendação](/rest/api/sql/sensitivitylabels/disablerecommendation): desabilita as recomendações de confidencialidade da coluna especificada.
- [Habilitar recomendação](/rest/api/sql/sensitivitylabels/enablerecommendation): habilita as recomendações de confidencialidade da coluna especificada. As recomendações são habilitadas por padrão em todas as colunas.
- [Obter](/rest/api/sql/sensitivitylabels/get): obtém o rótulo de confidencialidade da coluna especificada.
- [Lista por Banco de Dados Atual](/rest/api/sql/sensitivitylabels/listcurrentbydatabase): obtém os rótulos de confidencialidade atuais do banco de dados especificado.
- [Lista por Banco de Dados Recomendado](/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): obtém os rótulos de confidencialidade recomendados do banco de dados especificado.


## <a name="faq---advanced-classification-capabilities"></a>Perguntas frequentes – Recursos de classificação avançada

**Pergunta**: o [Azure Purview](../../purview/overview.md) substituirá a Descoberta e Classificação de Dados SQL ou a Descoberta e Classificação de Dados SQL será desativada em breve?
**Resposta**: continuamos a oferecer suporte à Descoberta e Classificação de Dados SQL e incentivamos você a adotar o [Azure Purview](../../purview/overview.md), que tem recursos mais avançados para impulsionar os recursos de classificação avançada e a governança de dados. Se decidirmos desativar qualquer serviço, recurso, API ou SKU, você receberá uma notificação com antecedência, incluindo um caminho de migração ou de transição. Saiba mais sobre as políticas de ciclo de vida da Microsoft aqui.


## <a name="next-steps"></a><a id="next-steps"></a>Próximas etapas

- Considere configurar a [Auditoria do SQL do Azure](../../azure-sql/database/auditing-overview.md) para monitorar e auditar o acesso aos dados confidenciais classificados.
- Para ver uma apresentação que inclui a Descoberta e Classificação de Dados, confira [Descobrir, classificar, rotular e proteger dados SQL | Expondo os dados](https://www.youtube.com/watch?v=itVi9bkJUNc).
- Para classificar bancos de dados SQL do Azure e o Azure Synapse Analytics com os rótulos do Azure Purview usando comandos do T-SQL, confira [Classificar seus dados do SQL do Azure usando os rótulos do Azure Purview](../../sql-database/scripts/sql-database-import-purview-labels.md).