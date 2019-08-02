---
title: Banco de dados SQL do Azure sem servidor (versão prévia) | Microsoft Docs
description: Este artigo descreve a nova camada de computação sem servidor e a compara com a camada existente de computação provisionada
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 07/05/2019
ms.openlocfilehash: 5a1a5ea39c9c0ed8973e1ecfa46977d2d06f83e7
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603618"
---
# <a name="azure-sql-database-serverless-preview"></a>Azure SQL Database sem servidor (visualização)

Banco de dados SQL do Azure sem servidor (versão prévia) é uma camada de computação para bancos de dados individuais que dimensiona automaticamente com base na demanda de carga de trabalho e listas para a quantidade de computação de computação usada por segundo. A camada de computação sem servidor também faz uma pausa automaticamente bancos de dados durante períodos de inatividade quando apenas armazenamento é cobrado e bancos de dados será retomada automaticamente quando a atividade retorna.

## <a name="serverless-compute-tier"></a>Camada de computação sem servidor

A camada de computação sem servidor para um banco de dados é parametrizada por um intervalo de dimensionamento automático de computação e um atraso de autopause.  A configuração desses parâmetros moldar a experiência de desempenho do banco de dados e o custo de computação.

![cobrança sem servidor](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Configuração de desempenho

- O **vCores mínimo** e **vCores máximo** são parâmetros configuráveis que definem o intervalo de capacidade de computação disponível para o banco de dados. Os limites de memória e E/S são proporcionais ao intervalo de vCore especificado.  
- O **autopause atraso** é um parâmetro configurável que define o período de tempo que o banco de dados deverá ficar inativo antes que ele está em pausa automaticamente. O banco de dados é retomado automaticamente quando ocorre o próximo logon ou outra atividade.  Como alternativa, autopausing pode ser desabilitado.

### <a name="cost"></a>Custo

- O custo para um banco de dados sem servidor é a soma do custo de computação e do custo de armazenamento.
- Quando o uso de computação estiver entre min e max limites configurados, o custo de computação baseia-se na memória usada e vCore.
- Quando o uso de computação está abaixo dos limites de min configurados, o custo de computação se baseia o mínimo de vCores e memória mínima configurado.
- Quando o banco de dados está em pausa, o custo de computação é zero e incorrem apenas os custos de armazenamento.
- O custo de armazenamento é determinado da mesma forma como a camada de computação provisionada.

Para obter mais detalhes de custo, consulte [cobrança](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Cenários

A camada sem servidor é otimizada para preço/desempenho para bancos de dados individuais com padrões de uso intermitentes e imprevisíveis que podem gerar atraso no aquecimento de computação após períodos ociosos. Por outro lado, a camada de computação provisionada é otimizado para bancos de dados únicos ou vários bancos de dados em pools Elásticos com o uso médio de mais alto que não podem manter qualquer atraso na computação aquecimento preço-desempenho.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Cenários adequados para a computação sem servidor

- Bancos de dados individuais com padrões de uso de intermitente, imprevisíveis Intercalado com períodos de inatividade e menor utilização de computação média ao longo do tempo.
- Bancos de dados individuais na camada de computação provisionada são redimensionados com frequência e clientes que preferem para delegar o redimensionamento do serviço de computação.
- Novos bancos de dados individuais sem histórico de uso em que o dimensionamento de computação é difícil ou não é possível estimar antes da implantação no banco de dados SQL.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Cenários adequados para computação provisionada

- Bancos de dados individuais com padrões de uso mais regular e previsível e a maior média de utilização ao longo do tempo de computação.
- Bancos de dados que não podem tolerar compensações de desempenho resultantes do corte mais frequente de memória ou do atraso de retomada automática após uma pausa.
- Vários bancos de dados com padrões de uso de intermitente, imprevisíveis que podem ser consolidados em pools Elásticos para melhor otimizar o desempenho de preço.

## <a name="comparison-with-provisioned-compute-tier"></a>Comparação com a camada de computação provisionada

A tabela a seguir resume as diferenças entre a camada de computação sem servidor e a camada de computação provisionada:

| | **Computação sem servidor** | **Computação provisionada** |
|:---|:---|:---|
|**Padrão de uso do banco de dados**| Uso de intermitente, imprevisível com menor utilização de computação média ao longo do tempo. |  Padrões de uso mais regulares com a maior média de utilização ao longo do tempo, ou vários bancos de dados usando pools Elásticos de computação.|
| **Esforço de gerenciamento de desempenho** |Inferior|Superior|
|**Dimensionamento de computação**|Automático|Manual|
|**Capacidade de resposta de computação**|Inferior após períodos de inatividade|Imediata|
|**Granularidade de cobrança**|Por segundo|Por hora|

## <a name="purchasing-model-and-service-tier"></a>Camada de serviço e modelo de compra

Atualmente, o Banco de dados SQL sem servidor é compatível apenas com a camada de uso geral da Geração 5 de hardware no modelo de compra de vCore.

## <a name="autoscaling"></a>Dimensionamento automático

### <a name="scaling-responsiveness"></a>Capacidade de resposta de dimensionamento

Em geral, os bancos de dados sem servidor são executados em uma máquina com capacidade suficiente para satisfazer as demandas de recursos sem interrupção para qualquer quantidade de computação solicitada dentro dos limites definidos pelo valor máximo de vCores. Ocasionalmente, o balanceamento de carga ocorrerá automaticamente se o computador não puder atender à demanda de recursos dentro de alguns minutos. Por exemplo, se a demanda de recursos é 4 vCores, mas somente 2 vCores estão disponíveis, em seguida, essa operação pode demorar alguns minutos para balancear a carga antes de 4 vCores são fornecidos. O banco de dados permanece online durante o balanceamento de carga, exceto por um breve período ao final da operação, quando as conexões são perdidas.

### <a name="memory-management"></a>Gerenciamento de memória

Memória para bancos de dados sem servidor é recuperada mais frequentemente que em bancos de dados de computação provisionada. Esse comportamento é importante para controlar os custos em sem servidor e pode afetar o desempenho.

#### <a name="cache-reclamation"></a>Recuperação de cache

Ao contrário de bancos de dados provisionado de computação, memória do cache do SQL é recuperada de um banco de dados sem servidor quando a utilização de CPU ou de cache é baixa.

- A utilização do cache é considerada baixo quando o tamanho total dos mais usados recentemente cache entradas cai abaixo do limite por um período de tempo.
- Quando a recuperação de cache é disparada, o tamanho do cache de destino é reduzido de forma incremental para uma fração do tamanho anterior e recuperando continua apenas se o uso permanece baixo.
- Quando ocorre a reclamação do cache, a política para a seleção de entradas de cache para remover é a mesma política de seleção para bancos de dados de computação provisionada quando a pressão de memória é alta.
- O tamanho do cache nunca é reduzido abaixo do limite de memória mínima, conforme definido pelo mínimo de vCores que pode ser configurado.

Sem servidor e provisionado computação bancos de dados, cache entradas podem ser removidas se toda a memória disponível é usada.

#### <a name="cache-hydration"></a>Hidratação de cache

O cache do SQL aumenta à medida que dados são buscados de disco, da mesma maneira e com a mesma velocidade de bancos de dados provisionados. Quando o banco de dados estiver ocupado, o cache tem permissão para crescer irrestrito até o limite máximo de memória.

## <a name="autopausing-and-autoresuming"></a>Autopausing e autoresuming

### <a name="autopausing"></a>Autopausing

Autopausing será disparada se todas as seguintes condições forem verdadeiras para a duração do atraso autopause:

- Número de sessões = 0
- CPU = 0 para carga de trabalho do usuário em execução no pool de usuário

Uma opção é fornecida para desabilitar autopausing se desejado.

Os recursos a seguir não têm suporte autopausing.  Ou seja, se qualquer um dos seguintes recursos são usados, o banco de dados permanece online, independentemente da duração da inatividade do banco de dados:

- Replicação geográfica (grupos ativos de failover automático e replicação geográfica).
- Retenção de longo prazo (LTR).
- O banco de dados de sincronização usado na sincronização de dados SQL.

Autopausing é impedido temporariamente durante a implantação de algumas atualizações de serviço que exigem que o banco de dados estar online.  Nesses casos, autopausing torna-se permitido novamente após a conclusão da atualização do serviço.

### <a name="autoresuming"></a>Autoresuming

Autoresuming será disparada se qualquer uma das seguintes condições forem verdadeiras a qualquer momento:

|Recurso|Gatilho de retomada automática|
|---|---|
|Autenticação e autorização|Logon|
|Detecção de ameaças|Habilitar/desabilitar as configurações de detecção de ameaças no nível do banco de dados ou servidor.<br>Modificando as configurações de detecção de ameaças no nível do banco de dados ou servidor.|
|Descoberta e classificação de dados|Adicionar, modificar, excluir ou exibir os rótulos de confidencialidade|
|Auditoria|Exibir os registros de auditoria.<br>Atualizando ou exibindo a política de auditoria.|
|Mascaramento de dados|Adicionar, modificar, excluir ou exibir as regras de mascaramento de dados|
|Transparent Data Encryption|Exibir o estado ou status de Transparent Data Encryption|
|Consultar o armazenamento de dados (desempenho)|Modificar ou exibir configurações do repositório de consultas; ajuste automático|
|Ajuste automático|Aplicação e verificação de recomendações de ajuste automático, como indexação automática|
|Cópia de banco de dados|Crie banco de dados como cópia.<br>Exportar para um arquivo BACPAC.|
|Sincronização de dados SQL|Sincronização entre bancos de dados membro e hub que são executados em um cronograma configurável ou são executados manualmente|
|Modificar alguns metadados do banco de dados|Adicionando novas marcas de banco de dados.<br>Alterar vCores máximo, mínimo de vCores ou autopause atraso.|
|SQL Server Management Studio (SSMS)|Usar o SSMS versão 18 e abrir uma nova janela de consulta para qualquer banco de dados no servidor retomará qualquer banco de dados em pausa automática no mesmo servidor. Esse comportamento não ocorre ao usar o SSMS versão 17.9.1 com o IntelliSense desativado.|

Autoresuming também é acionado durante a implantação de algumas atualizações de serviço que exigem que o banco de dados estar online.

### <a name="connectivity"></a>Conectividade

Se um banco de dados sem servidor está em pausa, o primeiro logon retomar o banco de dados e retornar um erro informando que o banco de dados está disponível com o código de erro 40613. Depois que o banco de dados for retomado, o logon deve ser repetido para estabelecer a conectividade. Os clientes do banco de dados com lógica de repetição de conexão não devem ser modificados.

### <a name="latency"></a>Latency

A latência autoresume e autopause um banco de dados sem servidor geralmente é a ordem de 1 minuto para autoresume e 1 a 10 minutos para autopause.

## <a name="onboarding-into-serverless-compute-tier"></a>Integração na camada de computação sem servidor

Criando um novo banco de dados ou mover que um banco de dados existente para uma camada de computação sem servidor segue o mesmo padrão como criar um novo banco de dados em provisionado de camada de computação e envolve duas etapas a seguir.

1. Especificar o nome do objetivo de serviço. O objetivo de serviço prescreve a camada de serviço, geração de hardware e vCores max. A tabela a seguir mostra as opções de objetivo de serviço:

   |Nome do objetivo de serviço|Camada de serviço|Geração de hardware|Máx. vCores|
   |---|---|---|---|
   |GP_S_Gen5_1|Uso geral|Gen5|1|
   |GP_S_Gen5_2|Uso geral|Gen5|2|
   |GP_S_Gen5_4|Uso geral|Gen5|4|

2. Opcionalmente, especifique o atraso mínimo de vCores e autopause para alterar seus valores padrão. A tabela a seguir mostra os valores disponíveis para esses parâmetros.

   |Parâmetro|Opções de valor|Valor padrão|
   |---|---|---|---|
   |Mínimo de vCores|{0,5, 1, 2, 4} sem exceder o máximo de vCores|vCores de 0,5|
   |Atraso de pausa automática|Mínimo: 60 minutos (1 hora)<br>Máximo: 10.080 minutos (7 dias)<br>Incrementos: 60 minutos<br>Desabilitar pausa automática: -1|60 minutos|

> [!NOTE]
> Não há suporte para usar o T-SQL para mover um banco de dados existente para uma camada sem servidor ou alterar seu tamanho da computação, mas isso pode ser feito por meio do portal do Azure ou do PowerShell.

### <a name="create-new-database-in-serverless-compute-tier"></a>Criar novo banco de dados na camada de computação sem servidor 

#### <a name="use-azure-portal"></a>Usar o portal do Azure

Confira [Início Rápido: Criar um banco de dados individual no Banco de Dados SQL do Azure usando o portal do Azure](sql-database-single-database-get-started.md).

#### <a name="use-powershell"></a>Usar o PowerShell

O exemplo a seguir cria um novo banco de dados na camada de computação sem servidor.  Este exemplo especifica explicitamente o mínimo de vCores, o máximo de vCores e o atraso de pausa automática.

```powershell
New-AzSqlDatabase `
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -ComputeModel Serverless `
  -Edition GeneralPurpose `
  -ComputeGeneration Gen5 `
  -MinVcore 0.5 `
  -MaxVcore 2 `
  -AutoPauseDelayInMinutes 720
```

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Mover o banco de dados da camada de computação provisionada em camada de computação sem servidor

#### <a name="use-powershell"></a>Usar o PowerShell

O exemplo a seguir move um banco de dados da camada de computação provisionada para a camada de computação sem servidor. Este exemplo especifica explicitamente o mínimo de vCores, o máximo de vCores e o atraso de pausa automática.

```powershell
Set-AzSqlDatabase
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -Edition GeneralPurpose `
  -ComputeModel Serverless `
  -ComputeGeneration Gen5 `
  -MinVcore 1 `
  -MaxVcore 4 `
  -AutoPauseDelayInMinutes 1440
```

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Mover o banco de dados da camada de computação sem servidor na camada de computação provisionada

Um banco de dados sem servidor pode ser movido para uma camada de computação provisionada da mesma forma utilizada para mover um banco de dados de computação provisionada para uma camada de computação sem servidor.

## <a name="modifying-serverless-configuration"></a>Modificando a configuração sem servidor

### <a name="maximum-vcores"></a>Máximo de vCores

#### <a name="use-powershell"></a>Usar o PowerShell

Modificar os vCores max é executada usando o [AzSqlDatabase conjunto](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) comando no PowerShell usando o `MaxVcore` argumento.

### <a name="minimum-vcores"></a>Mínimo de vCores

#### <a name="use-powershell"></a>Usar o PowerShell

Modificar o mínimo de vCores é executada usando o [AzSqlDatabase conjunto](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) comando no PowerShell usando o `MinVcore` argumento.

### <a name="autopause-delay"></a>Atraso de pausa automática

#### <a name="use-powershell"></a>Usar o PowerShell

Modificar o atraso autopause é executada usando o [AzSqlDatabase conjunto](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) comando no PowerShell usando o `AutoPauseDelayInMinutes` argumento.

## <a name="monitoring"></a>Monitoramento

### <a name="resources-used-and-billed"></a>Recursos usados e cobrados

Os recursos de um banco de dados sem servidor são encapsulados por pacote do aplicativo, a instância SQL e entidades de pool de recursos do usuário.

#### <a name="app-package"></a>Pacote do Aplicativo

O pacote do aplicativo é o limite externo de gerenciamento de recursos de um banco de dados, independentemente de se o banco de dados está em uma camada de computação sem servidor ou provisionada. O pacote do aplicativo contém a instância do SQL e os serviços externos que, juntos, englobam todos os recursos de usuário e do sistema usados por um banco de dados no Banco de Dados SQL. R e pesquisa de texto completo são exemplos de serviços externos. Em geral, a instância do SQL domina a utilização geral de recursos no pacote do aplicativo.

#### <a name="user-resource-pool"></a>Pool de recursos do usuário

O pool de recursos do usuário é o limite interno de gerenciamento de recursos de um banco de dados, independentemente de se o banco de dados está em uma camada de computação sem servidor ou provisionada. Os usuário pool escopos de recursos da CPU e e/s para carga de trabalho do usuário gerada por consultas DDL, como consultas CREATE e ALTER e DML, como selecionar, inserir, atualizar e excluir. Essas consultas geralmente representam a proporção mais substancial de utilização dentro do pacote do aplicativo.

### <a name="metrics"></a>metrics

As métricas para monitorar o uso de recursos do pool de usuário e o pacote de aplicativos de banco de dados sem servidor estão listadas na tabela a seguir:

|Entidade|Métrica|DESCRIÇÃO|Unidades|
|---|---|---|---|
|Pacote do Aplicativo|app_cpu_percent|Percentual de vCores usados pelo aplicativo em relação ao máximo de vCores permitido para o aplicativo.|Percentual|
|Pacote do Aplicativo|app_cpu_billed|A quantidade de computação cobrada para o aplicativo durante o período do relatório. O valor pago durante esse período é o produto dessa métrica e o preço unitário de vCore. <br><br>Os valores dessa métrica são determinados pela agregação ao longo do tempo do máximo de CPU usado e a memória usada por segundo. Se o valor usado for menor que a quantidade mínima provisionada conforme definido pelo mínimo de vCores e de memória, a quantidade mínima provisionada será cobrada. Para comparar CPU com memória para fins de cobrança, a memória é normalizada em unidades de vCores ao redimensionar a quantidade de memória em GB por 3 GB por vCore.|Segundos de vCore|
|Pacote do Aplicativo|app_memory_percent|Percentual de memória usada pelo aplicativo em relação ao máximo de memória permitida para o aplicativo.|Percentual|
|Pool de usuários|cpu_percent|Percentual de vCores usados pela carga de trabalho do usuário em relação ao máximo de vCores permitido para a carga de trabalho do usuário.|Percentual|
|Pool de usuários|data_IO_percent|Percentual de IOPS de dados usados pela carga de trabalho do usuário em relação ao máximo de IOPS de dados permitido para a carga de trabalho do usuário.|Percentual|
|Pool de usuários|log_IO_percent|Percentual de MB/s de log usados pela carga de trabalho do usuário em relação ao máximo de MB/s de log permitido para a carga de trabalho do usuário.|Percentual|
|Pool de usuários|workers_percent|Percentual de funções de trabalho usadas pela carga de trabalho do usuário em relação ao máximo de funções de trabalho permitidas para a carga de trabalho do usuário.|Percentual|
|Pool de usuários|sessions_percent|Percentual de sessões usadas pela carga de trabalho do usuário em relação ao máximo de sessões permitidas para a carga de trabalho do usuário.|Percentual|

### <a name="pause-and-resume-status"></a>Pausar e retomar status

No portal do Azure, o status do banco de dados é exibido no painel de visão geral do servidor que lista os bancos de dados que ele contém. O status do banco de dados também é exibido no painel de visão geral do banco de dados.

Uso do seguinte comando do PowerShell para consultar o status de pausa ou retomar de um banco de dados:

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Limites de recursos

Para limites de recursos, consulte [Camada de computação sem servidor](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)

## <a name="billing"></a>Cobrança

A quantidade de computação cobrada é a quantidade máxima de uso de CPU e memória usada por segundo. Se a quantidade de CPU e memória usada for menor que a quantidade mínima provisionada para cada uma delas, a quantidade provisionada será cobrada. Para comparar CPU com memória para fins de cobrança, a memória é normalizada em unidades de vCores ao redimensionar a quantidade de memória em GB por 3 GB por vCore.

- **Recurso cobrado**: CPU e memória
- **Valor cobrado**: preço unitário de vCore * max (mínimo de vCores, vCores usado, a memória mínima do GB * memória de 1/3 GB usado * 1/3) 
- **Frequência de cobrança**: Por segundo

O preço unitário de vCore no custo por vCore por segundo. Consulte a [página de preços do Banco de Dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/single/) para obter os preços unitários específicos em uma determinada região.

A quantidade de computação cobrada é exposta pela métrica a seguir:

- **Métrica**: app_cpu_billed (segundos de vCore)
- **Definição**: máx. (mínimo de vCores, vCores usados, GB de memória mín. * 1/3, GB de memória usados * 1/3)
- **Frequência de relatórios**: Por minuto

Essa quantidade é calculada a cada segundo e agregada a cada 1 minuto.

Considere um banco de dados sem servidor configurado com 1 min vCore e 4 vCores max.  Isso corresponde a cerca de 3 GB de memória de mínimo e máximo de 12 GB de memória.  Suponha que o atraso de pausa automática é definido como 6 horas e a carga de trabalho de banco de dados estiver ativo durante as primeiras horas 2 de um período de 24 horas e caso contrário, inativa.    

Nesse caso, o banco de dados é cobrado para computação e armazenamento durante as primeiras 8 horas.  Mesmo que o banco de dados está iniciando inativo após a segunda hora, ela ainda será cobrada para a computação nas 6 horas subsequentes com base na computação mínima provisionada enquanto o banco de dados está online.  Apenas o armazenamento é cobrado durante o restante do período de 24 horas, enquanto o banco de dados está em pausa.

Mais precisamente, a fatura de computação neste exemplo é calculada da seguinte maneira:

|Intervalo de tempo|vCores usados por segundo|GB usado por segundo|Computação cobrada de dimensão|segundos de vCore cobrados durante o intervalo de tempo|
|---|---|---|---|---|
|0:00-1:00|4|9|vCores usado|4 vCores * 3600 segundos = 14400 segundos de vCore|
|1:00-2:00|1|12|Memória usada|12 GB * 1/3 * 3.600 segundos = 14400 segundos de vCore|
|2:00-8:00|0|0|Memória mínima do provisionado|3 GB * 1/3 * 21600 segundos = 21600 segundos de vCore|
|8:00-24:00|0|0|Nenhuma computação cobrada enquanto está em pausa|vCore 0 segundos|
|Segundos de vCore total cobrados mais de 24 horas||||vCore 50400 segundos|

Suponha que o preço de unidade de computação seja US$ 0,000073/vCore/segundo.  Em seguida, cobrada por esse período de 24 horas para a computação é o produto dos computação unit price e vCore segundos cobrado: $0.000073/vCore/second * 50400 segundos de vCore = US $3,68

## <a name="available-regions"></a>Regiões disponíveis

A camada de computação sem servidor está disponível em todo o mundo, exceto regiões a seguir: Austrália Central, China Oriental, Norte da China, Sul da França, Alemanha Central, Alemanha nordeste, Índia Ocidental, Sul da Coreia, Oeste da África do Sul, Norte do Reino Unido, Sul do Reino Unido, oeste do Reino Unido e Oeste dos EUA.

## <a name="next-steps"></a>Próximas etapas

- Para começar, confira [Início rápido: Criar um banco de dados individual no Banco de Dados SQL do Azure usando o portal do Azure](sql-database-single-database-get-started.md).
- Para limites de recursos, consulte [Limites de recursos de camada de computação sem servidor](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).
