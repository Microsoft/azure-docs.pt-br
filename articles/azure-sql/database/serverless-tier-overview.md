---
title: Camada de computação sem servidor
description: Este artigo descreve a nova camada de computação sem servidor e a compara com a camada de computação provisionada existente para o banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 2/22/2021
ms.openlocfilehash: 4dd7bbe613b30df2611bfe6631950e121235204a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658581"
---
# <a name="azure-sql-database-serverless"></a>Banco de Dados SQL do Azure sem servidor
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Sem servidor é uma camada de computação para bancos de dados únicos no Banco de Dados SQL do Azure que dimensiona automaticamente a computação com base na demanda da carga de trabalho e cobra pela quantidade de computação usada por segundo. A camada de computação sem servidor também pausa os bancos de dados automaticamente durante períodos inativos quando apenas o armazenamento é cobrado e retoma automaticamente os bancos de dados quando a atividade retorna.

## <a name="serverless-compute-tier"></a>Camada de computação sem servidor

A camada de computação sem servidor para bancos de dados individuais no banco de dados SQL do Azure é parametrizada por um intervalo de dimensionamento automático de computação e um atraso de pausa automática. A configuração desses parâmetros forma a experiência de desempenho do banco de dados e o custo de computação.

![cobrança sem servidor](./media/serverless-tier-overview/serverless-billing.png)

### <a name="performance-configuration"></a>Configuração do desempenho

- **mínimo de vCores** e **máximo de vCores** são parâmetros configuráveis que definem o intervalo de capacidade de computação disponível para o banco de dados. Os limites de memória e E/S são proporcionais ao intervalo de vCore especificado.  
- O **atraso de pausa automática** é um parâmetro configurável que define o período de tempo que o banco de dados deve ficar inativo antes de ser pausado automaticamente. O banco de dados é retomado automaticamente quando o próximo logon ou outra atividade ocorre.  Como alternativa, a pausa automática pode ser desabilitada.

### <a name="cost"></a>Custo

- O custo de um banco de dados sem servidor é a soma do custo de computação e do custo de armazenamento.
- Quando o uso de computação está entre os limites mínimo e máximo configurados, o custo de computação baseia-se no vCore e na memória usada.
- Quando o uso de computação estiver abaixo dos limites mínimos configurados, o custo de computação baseia-se no mínimo de vCores e mínimo de memória configurada.
- Quando o banco de dados é pausado, o custo de computação é zero, e somente os custos de armazenamento são incorridos.
- O custo de armazenamento é determinado da mesma forma que na camada de computação provisionada.

Para obter mais detalhes, consulte [Cobrança](serverless-tier-overview.md#billing).

## <a name="scenarios"></a>Cenários

A camada sem servidor é otimizada para preço/desempenho para bancos de dados individuais com padrões de uso intermitentes e imprevisíveis que podem gerar atraso no aquecimento de computação após períodos ociosos. Por outro lado, a camada de computação provisionada é otimizada para preço/desempenho para bancos de dados individuais ou múltiplos bancos de dados em pools elásticos com maior uso médio que não podem apresentar atrasos no aquecimento de computação.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Cenários bem adequados para computação sem servidor

- Bancos de dados individuais com padrões de uso intermitentes e imprevisíveis intercalados com períodos de inatividade e menor utilização média de computação ao longo do tempo.
- Bancos de dados individuais na camada de computação provisionada que são frequentemente reescalonados e clientes que preferem delegar o reescalonamento de computação para o serviço.
- Novos bancos de dados individuais sem histórico de uso em que o escalonamento de computação é difícil ou impossível de estimar antes da implantação no Banco de Dados SQL.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Cenários bem adequados para computação provisionada

- Bancos de dados individuais com padrões de uso mais regulares, previsíveis e maior utilização média de computação ao longo do tempo.
- Bancos de dados que não podem tolerar compensações de desempenho resultantes de recorte de memória mais frequente ou atrasos na retomada de um estado em pausa.
- Vários bancos de dados com padrões de uso intermitentes e imprevisíveis que podem ser consolidados em pools elásticos para melhor otimização de preços e desempenho.

## <a name="comparison-with-provisioned-compute-tier"></a>Comparação com a camada de computação provisionada

A tabela a seguir resume as diferenças entre a camada de computação sem servidor e a camada de computação provisionada:

| | **Computação sem servidor** | **Computação provisionada** |
|:---|:---|:---|
|**Padrão de uso do banco de dados**| Uso intermitente e imprevisível com menor utilização média de computação ao longo do tempo. | Padrões de uso mais regulares com maior utilização média de computação ao longo do tempo ou vários bancos de dados usando pools elásticos.|
| **Esforço de gerenciamento de desempenho** |Inferior|Superior|
|**Dimensionamento de computação**|Automático|Manual|
|**Capacidade de resposta de computação**|Inferior após períodos de inatividade|Imediata|
|**Granularidade de cobrança**|Por segundo|Por hora|

## <a name="purchasing-model-and-service-tier"></a>Camada de serviço e modelo de compra

Atualmente, o Banco de dados SQL sem servidor é compatível apenas com a camada de uso geral da Geração 5 de hardware no modelo de compra de vCore.

## <a name="autoscaling"></a>Dimensionamento automático

### <a name="scaling-responsiveness"></a>Capacidade de resposta de dimensionamento

Em geral, os bancos de dados sem servidor são executados em um computador com capacidade suficiente para satisfazer as demandas de recursos sem interrupção para qualquer quantidade de computação solicitada dentro dos limites definidos pelo valor de máximo de vCores. Ocasionalmente, o balanceamento de carga ocorrerá automaticamente se o computador não puder atender à demanda de recursos dentro de alguns minutos. Por exemplo, se a demanda de recursos for 4 vCores, mas só 2 vCores estiverem disponíveis, poderá levar alguns minutos para balancear a carga antes do fornecimento de 4 vCores. O banco de dados permanece online durante o balanceamento de carga, exceto por um breve período ao final da operação, quando as conexões são perdidas.

### <a name="memory-management"></a>Gerenciamento de memória

A memória em bancos de dados sem servidor é recuperada mais frequentemente que em bancos de dados de computação provisionados. Esse comportamento é importante para controlar os custos na camada sem servidor e pode afetar o desempenho.

#### <a name="cache-reclamation"></a>Recuperação de cache

Ao contrário dos bancos de dados de computação provisionados, a memória do cache SQL é recuperada de um banco de dados sem servidor quando a utilização da CPU ou do cache ativo é baixa.

- A utilização do cache ativo é considerada baixa quando o tamanho total das entradas de cache usadas mais recentemente fica abaixo de um limite por um período de tempo.
- Quando a recuperação do cache é disparada, o tamanho do cache de destino é reduzido incrementalmente para uma fração do tamanho anterior, e a recuperação só continua se o uso permanece baixo.
- Quando ocorre a recuperação do cache, a política para selecionar entradas de cache a serem removidas é a mesma política de seleção que para bancos de dados de computação provisionados quando a pressão de memória é alta.
- O tamanho do cache nunca é reduzido abaixo do limite mínimo de memória definido por min vCores, que pode ser configurado.

Em bancos de dados de computação sem servidor e provisionados, as entradas de cache poderão ser removidas se toda a memória disponível for usada.

Observe que quando a utilização da CPU é baixa, a utilização do cache ativo pode permanecer alta dependendo do padrão de uso e evitar a reclamação da memória.  Além disso, pode haver atrasos adicionais depois que a atividade do usuário for interrompida antes que a recuperação de memória ocorra devido a processos de plano de fundo periódicos respondendo à atividade anterior do usuário.  Por exemplo, operações de exclusão e tarefas de limpeza QDS geram registros fantasmas que são marcados para exclusão, mas não são fisicamente excluídos até que o processo de limpeza de fantasma seja executado, o que pode envolver a leitura de páginas de dados no cache.

#### <a name="cache-hydration"></a>Hidratação de cache

O cache do SQL cresce à medida que os dados são obtidos do disco da mesma forma e com a mesma velocidade que para bancos de dados provisionados. Quando o banco de dados está ocupado, o cache tem permissão para aumentar sem restrição até o limite máximo de memória.

## <a name="auto-pausing-and-auto-resuming"></a>Pausa automática e retomada automática

### <a name="auto-pausing"></a>Pausa automática

A pausa automática será disparada se todas as condições a seguir forem verdadeiras durante o atraso de pausa automática:

- Número de sessões = 0
- CPU = 0 para a carga de trabalho do usuário em execução no pool de usuários

Uma opção é fornecida para desabilitar a pausa automática, se desejado.

Os recursos a seguir não oferecem suporte à pausa automática, mas oferecem suporte ao dimensionamento automático.  Se qualquer um dos recursos a seguir for usado, a pausa automática deverá ser desabilitada e o banco de dados permanecerá online, independentemente da duração da inatividade do banco de dados:

- Replicação geográfica (replicação geográfica ativa e grupos de failover automático).
- LTR (retenção de backup de longo prazo).
- O banco de dados de sincronização usado no SQL Data Sync.  Ao contrário dos bancos de dados de sincronização, os bancos de dados de Hub e de membros dão suporte à pausa automática.
- Alias de DNS
- O banco de dados de trabalho usado em trabalhos elásticos (visualização).

A pausa automática é temporariamente impedida durante a implantação de algumas atualizações de serviço que exigem que o banco de dados esteja online.  Nesses casos, a pausa automática é permitida novamente após a conclusão da atualização do serviço.

### <a name="auto-resuming"></a>Retomada automática

A retomada automática será disparada se qualquer uma das seguintes condições for verdadeira a qualquer momento:

|Recurso|Gatilho de retomada automática|
|---|---|
|Autenticação e autorização|Logon|
|Detecção de ameaças|Habilitar/desabilitar as configurações de detecção de ameaças no nível do banco de dados ou do servidor.<br>Alterar as configurações de detecção de ameaças no nível do banco de dados ou do servidor.|
|Descoberta e classificação de dados|Adicionar, modificar, excluir ou exibir os rótulos de confidencialidade|
|Auditoria|Exibir os registros de auditoria.<br>Atualizar ou exibir a política de auditoria.|
|Mascaramento de dados|Adicionar, modificar, excluir ou exibir as regras de mascaramento de dados|
|Transparent Data Encryption|Estado de exibição ou status da Transparent Data Encryption|
|Avaliação de vulnerabilidade|Verificações ad hoc e verificações periódicas, se habilitadas|
|Consultar o armazenamento de dados (desempenho)|Modificar ou exibir configurações do repositório de consultas|
|Recomendações de desempenho|Exibindo ou aplicando recomendações de desempenho|
|Ajuste automático|Aplicativo e verificação de recomendações de ajuste automático, como indexação automática|
|Cópia de banco de dados|Criar banco de dados como uma cópia.<br>Exportar para um arquivo BACPAC.|
|Sincronização de dados SQL|Sincronização entre bancos de dados membro e hub que são executados em um cronograma configurável ou são executados manualmente|
|Modificar alguns metadados do banco de dados|Adicionar novas marcas de banco de dados.<br>Alterar o máximo de vCores, mínimo de vCores ou atraso de pausa automática.|
|SQL Server Management Studio (SSMS)|Usar versões do SSMS anteriores à 18.1 e abrir uma nova janela de consulta para qualquer banco de dados no servidor retomará qualquer banco de dados em pausa automática no mesmo servidor. Esse comportamento não ocorre ao usar a versão 18.1 do SSMS ou posteriores.|

O monitoramento, o gerenciamento ou outras soluções que executam qualquer uma das operações listadas acima vão disparar a retomada automática.

A retomada automática também é disparada durante a implantação de algumas atualizações de serviço que exigem que o banco de dados esteja online.

### <a name="connectivity"></a>Conectividade

Se um banco de dados sem servidor estiver em pausa, o primeiro logon retomará o banco de dados e gerará um erro informando que o banco de dados está indisponível com o código de erro 40613. Depois que o banco de dados for retomado, o logon deve ser repetido para estabelecer a conectividade. Os clientes do banco de dados com lógica de repetição de conexão não devem ser modificados.

### <a name="latency"></a>Latency

A latência para retomar automaticamente e pausar automaticamente um banco de dados sem servidor geralmente é uma ordem de 1 minuto para retomar automaticamente e 1-10 minutos para pausar automaticamente.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Transparent Data Encryption (BYOK) gerenciada pelo cliente

Se estiver usando a [Transparent Data Encryption (BYOK) gerenciada pelo cliente](transparent-data-encryption-byok-overview.md), e o banco de dados sem servidor for pausado automaticamente quando ocorrer a exclusão ou a revogação da chave, o banco de dados permanecerá no estado de pausa automática.  Nesse caso, depois que o banco de dados for retomado, o banco de dados se tornará inacessível dentro de aproximadamente 10 minutos.  Quando o banco de dados se tornar inacessível, o processo de recuperação será o mesmo para bancos de dados de computação provisionados.  Se o banco de dados sem servidor estiver online quando ocorrer a exclusão ou a revogação de chave, o banco de dados também se tornará inacessível dentro de aproximadamente 10 minutos da mesma forma que com os bancos de dados de computação provisionados.

## <a name="onboarding-into-serverless-compute-tier"></a>Integração à camada de computação sem servidor

Criar um novo banco de dados ou mover um banco de dados existente para uma camada de computação sem servidor segue o mesmo padrão que criar um novo banco de dados em uma camada de computação provisionada e envolve as duas etapas a seguir.

1. Especificar o objetivo de serviço. O objetivo de serviço prescreve a camada de serviço, a geração de hardware e o máximo de vCores. Para opções de objetivo de serviço, consulte [limites de recursos sem servidor](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)


2. Opcionalmente, especifique o atraso de pausa automática e o mínimo de vCores para alterar seus valores padrão. A tabela a seguir mostra os valores disponíveis para esses parâmetros.

   |Parâmetro|Opções de valor|Valor padrão|
   |---|---|---|---|
   |Mínimo de vCores|Depende do máximo de vCores configurado. Consulte [limites de recursos](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).|vCores de 0,5|
   |Atraso de pausa automática|Mínimo: 60 minutos (1 hora)<br>Máximo: 10.080 minutos (7 dias)<br>Incrementos: 10 minutos<br>Desabilitar pausa automática: -1|60 minutos|


### <a name="create-a-new-database-in-the-serverless-compute-tier"></a>Criar um novo banco de dados na camada de computação sem servidor

Os exemplos seguintes criam um novo banco de dados na camada de computação sem servidor.

#### <a name="use-the-azure-portal"></a>Use o Portal do Azure

Confira [Início Rápido: Criar um banco de dados individual no Banco de Dados SQL do Azure usando o portal do Azure](single-database-create-quickstart.md).


#### <a name="use-powershell"></a>Usar o PowerShell

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-the-azure-cli"></a>Usar a CLI do Azure

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 --min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Usar o Transact-SQL (T-SQL)

Ao usar o T-SQL, os valores padrão são aplicados para o mínimo de vCores e o atraso de pausa automática.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Para obter detalhes, consulte [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true).  

### <a name="move-a-database-from-the-provisioned-compute-tier-into-the-serverless-compute-tier"></a>Mover um banco de dados da camada de computação provisionada para a camada de computação sem servidor

Os exemplos a seguir movem um banco de dados da camada de computação provisionada para a camada de computação sem servidor.

#### <a name="use-powershell"></a>Usar o PowerShell


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-the-azure-cli"></a>Usar a CLI do Azure

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Usar o Transact-SQL (T-SQL)

Ao usar o T-SQL, os valores padrão são aplicados para o mínimo de vcores e o atraso de pausa automática.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Para obter detalhes, consulte [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).

### <a name="move-a-database-from-the-serverless-compute-tier-into-the-provisioned-compute-tier"></a>Mover um banco de dados da camada de computação sem servidor para a camada de computação provisionada

Um banco de dados sem servidor pode ser movido para uma camada de computação provisionada da mesma forma utilizada para mover um banco de dados de computação provisionada para uma camada de computação sem servidor.

## <a name="modifying-serverless-configuration"></a>Modificar a configuração sem servidor

### <a name="use-powershell"></a>Usar o PowerShell

A modificação do máximo ou do mínimo de vCores, e o atraso de pausa automática, é executada usando o comando [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) no PowerShell usando os argumentos `MaxVcore`, `MinVcore` e `AutoPauseDelayInMinutes`.

### <a name="use-the-azure-cli"></a>Usar a CLI do Azure

A modificação do máximo ou o mínimo de vCores, e o atraso de pausa automática, é executada usando o comando [az sql db update](/cli/azure/sql/db#az-sql-db-update) no CLI do Azure usando os argumentos `capacity`, `min-capacity` e `auto-pause-delay`.


## <a name="monitoring"></a>Monitoramento

### <a name="resources-used-and-billed"></a>Recursos usados e cobrados

Os recursos de um banco de dados sem servidor são encapsulados pelo pacote do aplicativo, instância do SQL e entidades do pool de recursos do usuário.

#### <a name="app-package"></a>Pacote do Aplicativo

O pacote do aplicativo é o limite externo de gerenciamento de recursos de um banco de dados, independentemente de se o banco de dados está em uma camada de computação sem servidor ou provisionada. O pacote do aplicativo contém a instância do SQL e os serviços externos que, juntos, englobam todos os recursos de usuário e do sistema usados por um banco de dados no Banco de Dados SQL. R e pesquisa de texto completo são exemplos de serviços externos. Em geral, a instância do SQL domina a utilização geral de recursos no pacote do aplicativo.

#### <a name="user-resource-pool"></a>Pool de recursos do usuário

O pool de recursos do usuário é o limite interno de gerenciamento de recursos de um banco de dados, independentemente de se o banco de dados está em uma camada de computação sem servidor ou provisionada. O pool de recursos do usuário engloba CPU e E/S para cargas de trabalho do usuário geradas por consultas DDL (por exemplo, CREATE e ALTER ) e consultas DML (por exemplo, SELECT, INSERT, UPDATE e DELETE). Essas consultas geralmente representam a proporção mais substancial de utilização dentro do pacote do aplicativo.

### <a name="metrics"></a>Métricas

As métricas para monitorar o uso de recursos do pacote do aplicativo e o pool de usuários de um banco de dados sem servidor são listadas na tabela a seguir:

|Entidade|Métrica|Descrição|Unidades|
|---|---|---|---|
|Pacote do Aplicativo|app_cpu_percent|Percentual de vCores usados pelo aplicativo em relação ao máximo de vCores permitido para o aplicativo.|Porcentagem|
|Pacote do Aplicativo|app_cpu_billed|A quantidade de computação cobrada para o aplicativo durante o período do relatório. O valor pago durante esse período é o produto dessa métrica e o preço unitário de vCore. <br><br>Os valores dessa métrica são determinados pela agregação ao longo do tempo do máximo de CPU usado e a memória usada por segundo. Se o valor usado for menor que a quantidade mínima provisionada conforme definido pelo mínimo de vCores e de memória, a quantidade mínima provisionada será cobrada. Para comparar CPU com memória para fins de cobrança, a memória é normalizada em unidades de vCores ao redimensionar a quantidade de memória em GB por 3 GB por vCore.|Segundos de vCore|
|Pacote do Aplicativo|app_memory_percent|Percentual de memória usada pelo aplicativo em relação ao máximo de memória permitida para o aplicativo.|Porcentagem|
|Pool de usuários|cpu_percent|Percentual de vCores usados pela carga de trabalho do usuário em relação ao máximo de vCores permitido para a carga de trabalho do usuário.|Porcentagem|
|Pool de usuários|data_IO_percent|Percentual de IOPS de dados usados pela carga de trabalho do usuário em relação ao máximo de IOPS de dados permitido para a carga de trabalho do usuário.|Porcentagem|
|Pool de usuários|log_IO_percent|Percentual de MB/s de log usados pela carga de trabalho do usuário em relação ao máximo de MB/s de log permitido para a carga de trabalho do usuário.|Porcentagem|
|Pool de usuários|workers_percent|Percentual de funções de trabalho usadas pela carga de trabalho do usuário em relação ao máximo de funções de trabalho permitidas para a carga de trabalho do usuário.|Porcentagem|
|Pool de usuários|sessions_percent|Percentual de sessões usadas pela carga de trabalho do usuário em relação ao máximo de sessões permitidas para a carga de trabalho do usuário.|Porcentagem|

### <a name="pause-and-resume-status"></a>Pausar e retomar status

No portal do Azure, o status do banco de dados é exibido no painel de visão geral do servidor que lista os bancos de dados que ele contém. O status do banco de dados também é exibido no painel de visão geral do banco de dados.

Uso dos seguintes comandos para consultar o status de pausa ou retomada de um banco de dados:

#### <a name="use-powershell"></a>Usar o PowerShell

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-the-azure-cli"></a>Usar a CLI do Azure

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Limites de recursos

Para limites de recursos, consulte [camada de computação sem servidor](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).

## <a name="billing"></a>Cobrança

A quantidade de computação cobrada é a quantidade máxima de uso de CPU e memória usada por segundo. Se a quantidade de CPU e memória usada for menor que a quantidade mínima provisionada para cada uma delas, a quantidade provisionada será cobrada. Para comparar CPU com memória para fins de cobrança, a memória é normalizada em unidades de vCores ao redimensionar a quantidade de memória em GB por 3 GB por vCore.

- **Recurso cobrado**: CPU e memória
- **Valor cobrado**: preço unitário de vCore * máx (mínimo de vCores, vCores usados, GB de memória mín * 1/3, GB de memória usados * 1/3) 
- **Frequência de cobrança**: Por segundo

O preço unitário de vCore é o custo por vCore por segundo. Consulte a [página de preços do Banco de Dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/single/) para obter os preços unitários específicos em uma determinada região.

A quantidade de computação cobrada é exposta pela métrica a seguir:

- **Métrica**: app_cpu_billed (segundos de vCore)
- **Definição**: máx. (mínimo de vCores, vCores usados, GB de memória mín. * 1/3, GB de memória usados * 1/3)
- **Frequência de relatórios**: Por minuto

Essa quantidade é calculada a cada segundo e agregada a cada 1 minuto.

### <a name="minimum-compute-bill"></a>Fatura de computação mínima

Se um banco de dados sem servidor for pausado, a fatura de computação será zero.  Se um banco de dados sem servidor não estiver em pausa, a conta de computação mínima não será menor do que a quantidade de vCores com base no Max (min vCores, min memory GB * 1/3).

Exemplos:

- Suponha que um banco de dados sem servidor não seja pausado e configurado com 8 vCores Max e um mínimo de vCore correspondente a 3,0 GB de memória mín.  Em seguida, a fatura de computação mínima é baseada no máximo (1 vCore, 3,0 GB * 1 vCore/3 GB) = 1 vCore.
- Suponha que um banco de dados sem servidor não seja pausado e configurado com quatro vCores Max e 0,5 min vCores correspondentes à memória mínima de 2,1 GB.  Em seguida, a fatura de computação mínima é baseada em Max (0,5 vCores, 2,1 GB * 1 vCore/3 GB) = 0,7 vCores.

A [calculadora de preços do banco de dados SQL do Azure](https://azure.microsoft.com/pricing/calculator/?service=sql-database) para servidor pode ser usada para determinar a memória mínima configurável com base no número de vCores Max e min configurado.  Como regra, se a quantidade mínima de vCores configurada for maior que 0,5 vCores, a conta de computação mínima será independente da memória mínima configurada e com base apenas no número de vCores mínimas configurados.

### <a name="example-scenario"></a>Cenário de exemplo

Considere um banco de dados sem servidor configurado com um vCore mínimo de 1 e 4 vCores máximos.  Isso corresponde a cerca de 3 GB de memória e 12 GB de memória máxima.  Suponha que o atraso de pausa automática seja definido como 6 horas, e que a carga de trabalho do banco de dados esteja ativa durante as 2 primeiras horas de um período de 24 horas e, de outra forma, inativa.    

Nesse caso, o banco de dados é cobrado por computação e armazenamento durante as primeiras 8 horas.  Apesar de o banco de dados estar inativo e iniciar após a segunda hora, ele ainda é cobrado pela computação nas próximas 6 horas com base na computação mínima provisionada enquanto o banco de dados está online.  Somente o armazenamento é cobrado durante o restante do período de 24 horas enquanto o banco de dados está pausado.

Mais precisamente, a fatura de computação neste exemplo é calculada da seguinte maneira:

|Intervalo de tempo|vCores usado por segundo|GB usados por segundo|Dimensão de computação cobrada|Segundos de vCore cobrados no intervalo de tempo|
|---|---|---|---|---|
|0:00-1:00|4|9|vCores usados|4 vCores * 3600 segundos = 14400 segundos de vCore|
|1:00-2:00|1|12|Memória usada|12 GB * 1/3 * 3600 segundos = 14400 segundos de vCore|
|2:00-8:00|0|0|Mínimo de memória provisionada|3 GB * 1/3 * 21600 segundos = 21600 segundos de vCore|
|8:00-24:00|0|0|Nenhuma computação cobrada durante a pausa|0 segundo de vCore|
|Total de segundos de vCore cobrados em 24 horas||||50400 segundos de vCore|

Suponha que o preço de unidade de computação seja US$ 0,000145/vCore/segundo.  Em seguida, a computação cobrada para esse período de 24 horas é o produto do preço de unidade de computação e os segundos de vCore cobrados: US$ 0,000145/vCore/segundo * 50400 segundos de vCore ~ US$ 7,31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Benefício Híbrido do Azure e capacidade reserva

O Benefício Híbrido do Azure (AHB) e os descontos de capacidade reservada não se aplicam à camada de computação sem servidor.

## <a name="available-regions"></a>Regiões disponíveis

A camada de computação sem servidor está disponível em todo o mundo, exceto as seguintes regiões: Leste da China, Norte da China, Alemanha central, Alemanha nordeste e US Gov central (Iowa).

## <a name="next-steps"></a>Próximas etapas

- Para começar, confira [Início rápido: Criar um banco de dados individual no Banco de Dados SQL do Azure usando o portal do Azure](single-database-create-quickstart.md).
- Para limites de recursos, consulte [Limites de recursos de camada de computação sem servidor](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).
