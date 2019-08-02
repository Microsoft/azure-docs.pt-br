---
title: Visão geral de Hiperescala do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo descreve a camada de serviço da Hiperescala no modelo de compra baseado em vCore no Banco de Dados SQL do Azure e explica como ele é diferente do que as camadas de serviço de Uso Geral e Comercialmente Crítico.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: 707f7f47b050d22d0bd225cf412181902ae84f50
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67974458"
---
# <a name="hyperscale-service-tier-for-up-to-100-tb"></a>Camada de serviço de hiperescala para até 100 TB

Banco de dados SQL do Azure baseia-se na arquitetura de mecanismo de banco de dados do SQL Server que é ajustada para o ambiente de nuvem para garantir a disponibilidade de 99,99%, até mesmo no caso de falhas de infraestrutura. Há três modelos de arquitetura que são usados no Banco de Dados SQL do Azure:
- Uso Geral/Padrão 
-  Hiperescala
-  Comercialmente Crítico/Premium

A camada de serviço em hiperescala no banco de dados SQL do Azure é a camada de serviço mais recente em que o modelo de compra baseado em vCore. Essa camada de serviço é um armazenamento altamente escalonável e o nível de desempenho de computação que aproveita a arquitetura do Azure para escalar horizontalmente o armazenamento e recursos de computação para um banco de dados SQL do Azure substancialmente além dos limites disponíveis para uso geral e negócios Comercialmente Crítico.

> 
> [!NOTE]
> Para obter detalhes sobre as camadas de serviço de Uso Geral e Comercialmente Crítica no modelo de compra baseado em vCore, confira [Camadas de serviço de Uso Geral](sql-database-service-tier-general-purpose.md) e [Comercialmente Crítico](sql-database-service-tier-business-critical.md). Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [Modelos e recursos de compra do Banco de Dados SQL do Azure](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>Quais são as funcionalidades de Hiperescala

A camada de serviço da Hiperescala no Banco de Dados SQL do Azure fornece os seguintes recursos adicionais:

- Suporte para até 100 TB de tamanho de banco de dados
- Backups de banco de dados quase instantâneos (com base em instantâneos de arquivo armazenados no armazenamento de BLOBs do Azure), independentemente do tamanho, sem nenhum impacto de e/s  
- Rápidas restaurações de banco de dados (com base em instantâneos de arquivo) em minutos, em vez de horas ou dias (não um tamanho de operação de dados)
- Maior desempenho geral devido à maior taxa de transferência de log e tempos mais rápidos de confirmação de transação, independentemente dos volumes de dados
- Rápida expansão - você pode provisionar uma ou mais somente leitura nós para o descarregamento de sua carga de trabalho de leitura e para uso como reserva quente
- Rápida expansão - você pode, em tempo constante, escalar verticalmente seus recursos de computação para acomodar cargas de trabalho pesadas conforme a necessidade e, em seguida, dimensionar os recursos de computação novamente quando não é necessário.

A camada de serviço da Hiperescala elimina muitos dos limites práticos vistos tradicionalmente em bancos de dados de nuvem. Onde mais outros bancos de dados são limitados pelos recursos disponíveis em um único nó, bancos de dados na camada de serviço da Hiperescala não têm esses limites. Com sua arquitetura de armazenamento flexível, o armazenamento aumenta conforme necessário. Na verdade, os bancos de dados da Hiperescala não são criados com um tamanho máximo definido. Um banco de dados da Hiperescala aumenta conforme necessário – e você será cobrado apenas pela capacidade de que usar. Para cargas de trabalho de leitura intensa, a camada de serviço da Hiperescala fornece rápida expansão por meio do provisionamento de leitura de réplicas adicionais conforme necessário para o descarregamento de cargas de trabalho leitura.

Além disso, o tempo necessário para criar backups de banco de dados ou para aumentar ou diminuir a escala não está mais vinculado ao volume de dados no banco de dados. Bancos de dados de Hiperescala podem ser armazenados virtualmente instantaneamente. Você também pode dimensionar um banco de dados em dezenas de terabytes para cima ou para baixo em minutos. Esse recurso libera você das preocupações sobre ser encaixotado pelas opções iniciais de configuração.

Para obter mais informações sobre os tamanhos da computação para a camada de serviço em Hiperescala, confira [Características da camada de serviço](sql-database-service-tiers-vcore.md#service-tier-characteristics).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Quem deve considerar a camada de serviço da Hiperescala

A camada de serviço é usado principalmente para clientes que têm bancos de dados grandes tanto no local e Hiperescala modernizar seus aplicativos, movendo para a nuvem ou para clientes que já estão na nuvem e são limitados pelo tamanho máximo do banco de dados restrições (1 a 4 TB). Ele também é destinado a clientes que busca de alto desempenho e alta escalabilidade para armazenamento e computação.

A camada de serviço da Hiperescala dá suporte a todas as cargas de trabalho do SQL Server, mas basicamente é otimizado para OLTP. A camada de serviço da Hiperescala também dá suporte a híbrida analítica e cargas de trabalho (armazém de dados).

> [!IMPORTANT]
> Pools Elásticos não dão suporte a camada de serviço da Hiperescala.

## <a name="hyperscale-pricing-model"></a>Modelo de preços de Hiperescala

A camada de serviço em Hiperescala só está disponível no [modelo vCore](sql-database-service-tiers-vcore.md). Para alinhar-se com a nova arquitetura, o modelo de preços é ligeiramente diferente de camadas de serviço de Uso Geral ou Comercialmente Críticas:

- **Computação**:

  O preço de unidade de computação em Hiperescala é por réplica. O preço do [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) é aplicado para réplicas em escala de leitura automaticamente. Criamos uma réplica primária e uma réplica somente leitura por banco de dados de hiperescala por padrão.  Os usuários podem ajustar o número total de réplicas, incluindo a primária de 1-5.

- **Armazenamento**:

  Você não precisa especificar o tamanho máximo de dados ao configurar um banco de dados da Hiperescala. Na camada de hiperescala, você é cobrado pelo armazenamento para seu banco de dados de acordo com o uso real. O armazenamento é alocado automaticamente entre 10 GB e 100 TB, em incrementos que são dinamicamente ajustados entre 10 GB e 40 GB.  

Para obter mais informações sobre os preços da Hiperescala, confira [Preços do Banco de Dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Arquitetura de funções distribuídas

Ao contrário de mecanismos de banco de dados tradicional que centralizou a todas as funções de gerenciamento de dados em um local/processo (até mesmo então chamados bancos de dados distribuídos em produção hoje têm várias cópias de um mecanismo de dados monolítico), um banco de dados da Hiperescala separa o mecanismo de processamento de consulta, em que a semântica de diversos mecanismos de dados divergem dos componentes que fornecem armazenamento de longo prazo e a durabilidade dos dados. Dessa forma, a capacidade de armazenamento pode ser perfeitamente expandida até onde conforme necessário (o destino inicial é 100 TB). Réplicas somente leitura compartilham os mesmos componentes de armazenamento, portanto, nenhuma cópia de dados é necessária para criar uma nova réplica legível. 

O diagrama a seguir ilustra os diferentes tipos de nós em um banco de dados de Hiperescala:

![Arquitetura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Um banco de dados da Hiperescala contém os seguintes tipos diferentes de nós:

### <a name="compute-node"></a>Nó de computação

O nó de computação é onde reside o mecanismo relacional, portanto, os elementos de linguagem, processamento de consulta e assim por diante, ocorrerem. Todas as interações do usuário com um banco de dados da Hiperescala ocorrem por meio de nós de computação. Nós têm caches baseado em SSD (rotulado como RBPEX - extensão do Pool de buffers resiliente no diagrama anterior) para minimizar o número da rede de computação viagens de ida e necessárias para buscar uma página de dados. Há um nó de computação principal em que todas as transações e cargas de trabalho de leitura / gravação são processadas. Há um ou mais nós de computação secundária que atuam como nós em espera ativos para fins de failover, bem como para atuam como nós de computação de somente leitura para o descarregamento de cargas de trabalho de leitura (se essa funcionalidade é desejada).

### <a name="page-server-node"></a>Nó do servidor de páginas

Servidores de página são sistemas que representam um mecanismo de armazenamento dimensionado.  Cada servidor de páginas é responsável por um subconjunto das páginas no banco de dados.  No mínimo, cada servidor de página controla entre 128 GB e 1 TB de dados. Nenhum dado é compartilhado em mais de um servidor de página (fora de réplicas que são mantidos por redundância e disponibilidade). O trabalho de um servidor de páginas é servir páginas de banco de dados para os nós de computação sob demanda e manter as páginas atualizadas à medida que as transações atualizam dados. Servidores de página são mantidas atualizadas ao reproduzir os registros de log do serviço de log. Servidores de página também mantenham caches baseado em SSD para melhorar o desempenho. Armazenamento de longo prazo de páginas de dados é mantido no armazenamento do Azure para confiabilidade adicional.

### <a name="log-service-node"></a>Nó de serviço de log

O nó do serviço de log aceita registros de log do nó de computação principal, persisti-los em um cache durável e encaminha os registros de log para o restante de nós de computação (de modo que eles possam atualizar seus caches), bem como os servidores de página relevante, para que os dados possam ser atualizado t aqui. Dessa forma, todas as alterações de dados do nó de computação principal são propagadas por meio do serviço de log para todos os nós de computação secundária e os servidores da página. Por fim, os registros de log são enviados para o armazenamento de longo prazo no armazenamento do Azure, que é um repositório de armazenamento infinita. Esse mecanismo remove a necessidade de truncamento de log com frequência. O serviço de log também tem um cache local para acelerar o acesso.

### <a name="azure-storage-node"></a>Nó de armazenamento do Azure

O nó de armazenamento do Azure é o destino final dos dados de servidores de página. Esse armazenamento é usado para fins de backup, bem como para a replicação entre regiões do Azure. Os backups consistem em instantâneos de arquivos de dados. Restaurar operação são rápidas a partir desses instantâneos e dados podem ser restaurados para qualquer ponto no tempo.

## <a name="backup-and-restore"></a>Backup e restauração

Os backups são a base de dados de instantâneo de arquivo e, portanto, eles são quase instantâneos. Separação de computação e armazenamento permitem realizar a operação de backup/restauração para a camada de armazenamento reduzir a carga de processamento no nó de computação principal. Como resultado, o backup de um banco de dados grande não afeta o desempenho do nó de computação principal. Da mesma forma, as restaurações são feitas pelo copiando o instantâneo de arquivo e como tal, não têm um tamanho de operação de dados. Para obter restaurações dentro da mesma conta de armazenamento, a operação de restauração é rápida.

## <a name="scale-and-performance-advantages"></a>Vantagens de desempenho e escala

Com a capacidade de criar rapidamente nós de computação adicionais de somente leitura para cima/para baixo, a arquitetura permite significativa ler recursos de escala e Hiperescala também pode liberar o nó de computação principal para atender às solicitações de gravação mais. Além disso, os nós de computação podem ser dimensionados para cima/para baixo rapidamente devido à arquitetura de armazenamento compartilhado da arquitetura da Hiperescala.

## <a name="create-a-hyperscale-database"></a>Criar um banco de dados em Hiperescala

Um banco de dados em Hiperescala pode ser criado usando o [portal do Azure](https://portal.azure.com), o [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), o [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) ou a [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). Bancos de dados em Hiperescala estão disponíveis somente usando o [Modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

O comando T-SQL a seguir cria um banco de dados em Hiperescala. Você deve especificar tanto o objetivo do serviço quanto a edição na instrução `CREATE DATABASE`. Consulte os [limites de recurso](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale-service-tier) para obter uma lista de objetivos de serviço válidos.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Isso criará um banco de dados de hiperescala no hardware Gen5 com 4 núcleos.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrar um Banco de Dados SQL do Azure existente para a camada de serviço em Hiperescala

Você pode mover seus Bancos de Dados SQL do Azure existentes em Hiperescala usando o [portal do Azure](https://portal.azure.com), o [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), o [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) ou a [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). Neste momento, essa é uma migração unidirecional. Você não pode mover bancos de dados da Hiperescala para outro nível de serviço. Recomendamos que você faça uma cópia de seus bancos de dados de produção e migre para a Hiperescala para prova de conceitos (POCs).

O comando T-SQL a seguir move um banco de dados para a camada de serviço em Hiperescala. Você deve especificar tanto o objetivo do serviço quanto a edição na instrução `ALTER DATABASE`.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Conectar-se a uma réplica em escala de leitura de um banco de dados em Hiperescala

Em bancos de dados em Hiperescala, o argumento `ApplicationIntent` na cadeia de conexão fornecida pelo cliente determina se a conexão é roteada para a réplica de gravação ou para uma réplica secundária somente leitura. Se o `ApplicationIntent` definido como `READONLY` e o banco de dados não tiverem uma réplica secundária, a conexão será roteada para a réplica primária e o padrão será o comportamento `ReadWrite`.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```
## <a name="disaster-recovery-for-hyperscale-databases"></a>Recuperação de desastre para bancos de dados de hiperescala
### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Restaurando um banco de dados de hiperescala para uma geografia diferente
Se você precisar restaurar um BD de hiperescala do banco de dados SQL do Azure para uma região que não seja a que está hospedada no momento, como parte de uma operação de recuperação de desastre ou de análise, realocação ou qualquer outro motivo, o método principal é fazer uma restauração geográfica do banco de dados.  Isso envolve exatamente as mesmas etapas que você usaria para restaurar qualquer outro banco de BD SQL do AZURE para uma região diferente:
1. Crie um servidor de banco de dados SQL na região de destino se você ainda não tiver um servidor apropriado.  Esse servidor deve pertencer à mesma assinatura que o servidor original (origem).
2. Siga as instruções no tópico de [restauração geográfica](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) da página sobre como restaurar bancos de dados SQL do Azure de backups automáticos.

> [!NOTE]
> Como a origem e o destino estão em regiões separadas, o banco de dados não pode compartilhar o armazenamento de instantâneos com o banco de dados de origem como em restaurações não geográficas, o que é concluído de maneira extremamente rápida.  No caso de uma restauração geográfica de um banco de dados de hiperescala, ela será uma operação de tamanho de dado, mesmo que o destino esteja na região emparelhada do armazenamento replicado geograficamente.  Isso significa que fazer uma restauração geográfica levará tempo proporcional ao tamanho do banco de dados que está sendo restaurado.  Se o destino estiver na região emparelhada, a cópia estará dentro de um datacenter, que será significativamente mais rápido do que uma cópia de longa distância pela Internet, mas ainda copiará todos os bits.

## <a name=regions></a>Regiões disponíveis

No momento, a camada de hiperescala do banco de dados SQL do Azure está disponível nas seguintes regiões:

- Leste da Austrália
- Sudeste da Austrália
- Sul do Brasil
- Canadá Central
- Centro dos EUA
- Leste da China 2
- Norte da China 2
- Ásia Oriental
- East US
- Leste dos EUA 2
- França Central
- Leste do Japão
- Oeste do Japão
- Coreia Central
- Sul da Coreia
- Centro-Norte dos EUA
- Norte da Europa
- Norte da África do Sul
- Centro-Sul dos Estados Unidos
- Sudeste Asiático
- Sul do Reino Unido
- Oeste do Reino Unido
- Europa Ocidental
- Oeste dos EUA
- Oeste dos EUA 2

Se desejar criar um banco de dados de hiperescala em uma região que não esteja listada como com suporte, você poderá enviar uma solicitação de integração via portal do Azure. Estamos trabalhando para expandir a lista de regiões com suporte; portanto, verifique novamente a lista de regiões mais recente.

Para solicitar a capacidade de criar bancos de dados de hiperescala em regiões não listadas:

1. Navegue até a [folha de ajuda e suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

2. Clique em [ **nova solicitação de suporte**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

    ![Folha de ajuda e suporte do Azure](media/sql-database-service-tier-hyperscale/whitelist-request-screen-1.png)

3. Para **tipo de problema**, selecione **limites de serviço e assinatura (cotas)**

4. Escolha a assinatura que você usaria para criar os bancos de dados

5. Para **tipo de cota**, selecione **banco de dados SQL**

6. Clique em **Avançar: Soluções**

1. Clique em **fornecer detalhes**

    ![Detalhes do problema](media/sql-database-service-tier-hyperscale/whitelist-request-screen-2.png)

8. Escolha o **tipo de cota do banco de dados SQL**: **Outra solicitação de cota**

9. Preencha o seguinte modelo:

    ![Detalhes da cota](media/sql-database-service-tier-hyperscale/whitelist-request-screen-3.png)

    No modelo, forneça as seguintes informações

    > Solicitação para criar o banco de dados SQL de hiperescala do Azure em uma nova região<br/> Região: [preencha sua região solicitada]  <br/>
    > Calcular SKU/total de núcleos, incluindo réplicas legíveis <br/>
    > Número de TB estimado 
    >

10. Escolha **Gravidade C**

11. Escolha o método de contato apropriado e preencha os detalhes.

12. Clique em **salvar** e **continuar**

## <a name="known-limitations"></a>Limitações conhecidas
Essas são as limitações atuais da camada de serviço de hiperescala a partir do GA.  Estamos trabalhando ativamente para remover o máximo possível de limitações.

| Problema | DESCRIÇÃO |
| :---- | :--------- |
| O painel gerenciar backups de um servidor lógico não mostra que os bancos de dados de hiperescala serão filtrados do SQL Server  | O hiperscale tem um método separado para gerenciar backups e, como tal, a retenção de longo prazo e as configurações de retenção de backup point-in-time não se aplicam/são invalidadas. Da mesma forma, os bancos de dados da Hiperescala não aparecem no painel Gerenciar Backup. |
| Restauração pontual | Depois que um banco de dados é migrado para a camada de serviço de hiperescala, não há suporte para a restauração para um ponto no tempo antes da migração.|
| Restauração de BD não hiperescala para Hypserscale e vice-versa | Você não pode restaurar um banco de dados de hiperescala em um banco de dados não hiperescala, nem pode restaurar um banco de dados que não seja de hiperescala em um banco de dados de hiperescala.|
| Se um arquivo de banco de dados aumentar durante a migração devido a uma carga de trabalho ativa e ultrapassar 1 TB por limite de arquivo, a migração falhará | Atenuações: <br> – Se possível, migre o banco de dados quando não houver nenhuma carga de trabalho de atualização em execução.<br> – Tente novamente a migração, ela terá êxito desde que o limite de 1 TB não seja ultrapassado durante a migração.|
| Instância Gerenciada | No momento, não há suporte para Instância Gerenciada do Banco de Dados SQL do Azure com bancos de dados de hiperescala. |
| Pools elásticos |  Atualmente, não há suporte para pools elásticos com a hiperescala do banco de dados SQL.|
| Migração para Hiperescala é, no momento, uma operação unidirecional | Depois que um banco de dados é migrado para Hiperescala, ele não pode ser migrado diretamente para uma camada de serviço que não esteja em Hiperescala. No momento, a única maneira de migrar um banco de dados em Hiperescala para não Hiperescala é importar/exportar usando um arquivo BACPAC.|
| Migração de bancos de dados com objetos na memória persistentes | O hiperscale dá suporte apenas a objetos não persistentes na memória (tipos de tabela, SPs nativos e funções).  As tabelas persistentes na memória e outros objetos devem ser descartados e recriados como objetos não na memória antes de migrar um banco de dados para a camada de serviço de hiperescala.|
| Controle de dados de alteração | Você não poderá usar o controle de dados de alteração com bancos de dado de hiperescala. |
| Replicação geográfica  | Você ainda não pode configurar a replicação geográfica para a hiperescala do banco de dados SQL do Azure.  Você pode executar restaurações geográficas (restaurando o banco de dados em uma geografia diferente, para recuperação de desastres ou outras finalidades) |
| Integração do TDE/AKV | A criptografia de banco de dados transparente usando Azure Key Vault (comumente conhecida como traga sua própria chave ou BYOK) ainda não tem suporte para a hiperescala do banco de dados SQL do Azure, no entanto, TDE com chaves gerenciadas por serviço tem suporte total. |
|Recursos de banco de dados inteligente | 1. Criar índice, os modelos do alerta drop index não são treinados para bancos de los de hiperescala. <br/>2. Problema de esquema, não há suporte para os consultores adicionados recentemente DbParameterization para o banco de dados de hiperescala.|



## <a name="next-steps"></a>Próximas etapas

- Para perguntas frequentes sobre Hiperescala, confira [Perguntas frequentes sobre Hiperescala](sql-database-service-tier-hyperscale-faq.md).
- Para obter informações sobre as camadas de serviço, consulte [camadas de serviço](sql-database-service-tiers.md)
- Consulte [Visão geral dos limites de recursos em um servidor lógico](sql-database-resource-limits-logical-server.md) para obter informações sobre limites nos níveis de servidor e assinatura.
- Para comprar os limites de modelo para um banco de dados individual, confira [Limites de modelo de compra baseado em vCore do Banco de Dados SQL do Azure para um banco de dados individual](sql-database-vcore-resource-limits-single-databases.md).
- Para obter uma lista de recursos e de comparação, consulte [Recursos comuns do SQL](sql-database-features.md).
