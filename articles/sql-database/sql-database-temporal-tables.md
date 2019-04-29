---
title: Introdução às Tabelas Temporais no Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como começar a usar as Tabelas Temporais no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 49491c5283ba16c5379c1115fae597bd7fd6ea19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614164"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Introdução às Tabelas Temporais no Banco de Dados SQL do Azure

As Tabelas Temporais são um novo recurso de programação do Banco de Dados SQL do Azure que permite controlar e analisar o histórico completo de alterações em seus dados, sem a necessidade de codificação personalizada. As Tabelas Temporais mantêm os dados relacionados ao contexto de tempo, de forma que os fatos armazenados possam ser interpretados como válidos apenas no período específico. Essa propriedade das Tabelas Temporais permite uma análise eficiente baseada em tempo e a obtenção de informações da evolução dos dados.

## <a name="temporal-scenario"></a>Cenário Temporal

Este artigo ilustra as etapas para utilizar as Tabelas Temporais em um cenário de aplicativo. Suponha que você queira acompanhar a atividade de usuário em um novo site que está sendo desenvolvido do zero ou em um site existente que você deseja estender com análises de atividade do usuário. Neste exemplo simplificado, estamos supondo que o número de páginas da Web visitadas durante um período é um indicador do que precisa ser capturado e monitorado no banco de dados do site hospedado no Banco de Dados SQL do Azure. O objetivo da análise do histórico da atividade de usuário é obter entradas para reprojetar o site e fornecer uma experiência melhor para os visitantes.

O modelo de banco de dados para esse cenário é muito simples: a métrica de atividade do usuário é representada por um único campo de inteiro, **PageVisited**, e é capturada com informações básicas no perfil do usuário. Além disso, para a análise baseada em tempo, você manteria uma série de linhas para cada usuário, em que cada linha representa o número de páginas visitadas por um usuário específico em determinado período.

![Esquema](./media/sql-database-temporal-tables/AzureTemporal1.png)

Felizmente, você não precisa fazer qualquer esforço em seu aplicativo para manter essas informações de atividade. Com as Tabelas Temporais, esse processo é automatizado - oferecendo flexibilidade total durante o design do site e mais tempo para se concentrar na análise de dados. A única coisa que você precisa fazer é garantir que a tabela **WebSiteInfo** seja configurada como [temporal versionada pelo sistema](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). As etapas exatas para utilizar as Tabelas Temporais neste cenário são descritas abaixo.

## <a name="step-1-configure-tables-as-temporal"></a>Etapa 1: Configurar tabelas como temporais
Dependendo se você estiver iniciando um novo desenvolvimento ou atualizando um aplicativo existente, você criará tabelas temporais ou modificará as existentes ao adicionar atributos temporais. Em geral, seu cenário pode ser uma combinação dessas duas opções. Execute estas ações usando o [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), o [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) ou qualquer outra ferramenta de desenvolvimento do Transact-SQL.

> [!IMPORTANT]
> Recomendamos que você sempre use a versão mais recente do Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Criar nova tabela
Use o item de menu de contexto "Nova Tabela Versionada pelo Sistema” no Pesquisador de Objetos do SSMS para abrir o editor de consulta com um script modelo de tabela temporal e então use “Especificar Valores para Parâmetros de Modelo” (Ctrl+Shift+M) para preencher o modelo:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

No SSDT, escolha o modelo “Tabela temporal (com versão do sistema)” ao adicionar novos itens ao projeto de banco de dados. Isso abrirá o designer de tabela e permitirá que você especifique o layout da tabela com facilidade:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Também é possível criar uma tabela temporal especificando diretamente as instruções Transact-SQL, conforme mostrado no exemplo a seguir. Observe que os elementos obrigatórios de todas as tabelas temporais são a definição PERIOD e a cláusula SYSTEM_VERSIONING com uma referência a outra tabela de usuário que armazenará versões de linha de histórico:

```
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
```

Quando você cria a tabela temporal versionada pelo sistema, a tabela de histórico que acompanha este artigo com a configuração padrão é criada automaticamente. A tabela de histórico padrão contém um índice de árvore B clusterizado nas colunas de período (fim, início) com a compactação de página habilitada. Essa configuração é ideal para a maioria dos cenários em que as tabelas temporais são usadas, especialmente para a [auditoria de dados](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

Neste caso em particular, temos o objetivo de realizar análises de tendências baseadas em tempo ao longo de um histórico de dados maior e com conjuntos de dados maiores, de forma que a opção de armazenamento para a tabela de histórico seja um índice columnstore clusterizado. Um columnstore clusterizado oferece compactação e desempenho ótimos para consultas analíticas. As Tabelas Temporais oferecem a flexibilidade de configurar os índices nas tabelas atuais e temporais de forma totalmente independente. 

> [!NOTE]
> Índices ColumnStore estão disponíveis na camada Premium e na camada Standard, S3 e posteriores.
>

O script a seguir mostra como o índice padrão na tabela de histórico pode ser alterado para o columnstore clusterizado:

```
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

As Tabelas Temporais são representadas no Pesquisador de Objetos com o ícone específico para facilitar sua identificação, enquanto sua tabela de histórico é exibida como um nó filho.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Alterar uma tabela existente para temporal
Vamos abordar o cenário alternativo no qual a tabela WebsiteUserInfo já existe, mas que não foi projetada para manter um histórico de alterações. Nesse caso, você pode simplesmente estender a tabela existente para se tornar temporal, conforme mostrado no exemplo a seguir:

```
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

## <a name="step-2-run-your-workload-regularly"></a>Etapa 2: Executar a carga de trabalho regularmente
A principal vantagem das Tabelas Temporais é que você não precisa alterar ou ajustar seu site de alguma maneira para executar o controle de alterações. Depois de criadas, as Tabelas Temporais fazem as versões de linha persistirem de forma transparente sempre que você executa modificações em seus dados. 

Para aproveitar o controle de alterações automático deste cenário em particular, iremos atualizar apenas a coluna **PagesVisited** sempre que um usuário finalizar sua sessão no site:

```
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
```

É importante observar que a consulta de atualizações não precisa saber a hora exata em que a operação real ocorreu nem como os dados históricos serão preservados para análise futura. Ambos os aspectos são tratados automaticamente pelo Banco de Dados SQL do Azure. O diagrama a seguir ilustra como os dados históricos estão sendo gerados em cada atualização.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Etapa 3: Executar a análise de dados históricos
Agora, quando o versionamento pelo sistema temporal estiver habilitado, a análise dos dados históricos estará a apenas uma consulta de distância de você. Neste artigo, mostraremos alguns exemplos que tratam dos cenários comuns da análise. Para obter todos os detalhes, explore as diversas opções introduzidas com a cláusula [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3).

Para ver os 10 principais usuários ordenados pelo número de páginas da Web visitadas na última hora, execute esta consulta:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

Você pode modificar facilmente essa consulta para analisar as visitas ao site no dia anterior, no mês anterior ou em qualquer ponto no passado desejado.

Para executar a análise estatística básica do dia anterior, use o exemplo a seguir:

```
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

Para procurar atividades de um usuário específico, em um período de tempo, use a cláusula CONTAINED IN:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

A visualização gráfica é especialmente conveniente para consultas temporais porque você pode mostrar com muita facilidade tendências e padrões de uso de uma maneira intuitiva:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Aprimoramento do esquema de tabela
Normalmente, você precisará alterar o esquema de tabela temporal enquanto estiver fazendo o desenvolvimento de aplicativos. Para isso, bastará executar as instruções ALTER TABLE normais e o Banco de Dados SQL do Azure propagará adequadamente as alterações na tabela de histórico. O script a seguir mostra como você pode adicionar outros atributos ao controle:

```
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Da mesma forma, você pode alterar a definição de coluna enquanto sua carga de trabalho está ativa:

```
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Por fim, você pode remover uma coluna que já não precisa.

```
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
```

Como alternativa, use o [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) mais recente para alterar o esquema de tabela temporal enquanto estiver conectado ao banco de dados (modo online) ou como parte do projeto do banco de dados (modo offline).

## <a name="controlling-retention-of-historical-data"></a>Controle de retenção de dados históricos
Com as tabelas temporais versionadas pelo sistema, a tabela de histórico pode aumentar o tamanho do banco de dados mais do que as tabelas normais. Uma tabela de histórico grande e sempre crescente pode se tornar um problema devido aos custos puros de armazenamento, além de impor um imposto de desempenho sobre as consultas temporais. Portanto, o desenvolvimento de uma política de retenção de dados para o gerenciamento de dados na tabela de histórico é um aspecto importante do planejamento e do gerenciamento do ciclo de vida de cada tabela temporal. Com o Banco de Dados SQL do Azure, você tem as seguintes abordagens para o gerenciamento dos dados históricos na tabela temporal:

* [Particionamento de tabela](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Script de Limpeza Personalizado](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Próximas etapas
Para obter informações detalhadas sobre as Tabelas Temporais, confira a [documentação do MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
Visite o Channel 9 para ouvir uma [história de sucesso real de implementação temporal do cliente](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) e assista a uma [demonstração temporal dinâmica](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

