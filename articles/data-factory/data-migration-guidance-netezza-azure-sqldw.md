---
title: Migrar dados de um servidor Netezza local para o Azure
description: Use Azure Data Factory para migrar dados de um servidor Netezza local para o Azure.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: 36ca7b709e0ec945f1fb4a9a8b745d20e6a58fe1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367768"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Usar Azure Data Factory para migrar dados de um servidor Netezza local para o Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

O Azure Data Factory fornece um mecanismo eficaz, robusto e econômico para migrar dados em escala de um servidor Netezza local para sua conta de armazenamento do Azure ou para o banco de dados do Azure Synapse Analytics. 

Este artigo fornece as seguintes informações para desenvolvedores e engenheiros de dados:

> [!div class="checklist"]
> * Desempenho 
> * Resiliência de cópia
> * Segurança de rede
> * Arquitetura da solução de alto nível 
> * Melhores práticas de implementação  

## <a name="performance"></a>Desempenho

O Azure Data Factory oferece uma arquitetura sem servidor que permite paralelismo em vários níveis. Se você for um desenvolvedor, isso significará que você pode criar pipelines para usar totalmente a largura de banda de rede e de banco de dados para maximizar a taxa de transferência de movimentação do seu ambiente.

![Diagrama de desempenho](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

O diagrama anterior pode ser interpretado da seguinte maneira:

- Uma única atividade de cópia pode aproveitar os recursos de computação escalonáveis. Ao usar Azure Integration Runtime, você pode especificar [até 256 DIUs](./copy-activity-performance.md#data-integration-units) para cada atividade de cópia de maneira sem servidor. Com um tempo de execução de integração auto-hospedado (IR auto-hospedado), você pode escalar verticalmente o computador manualmente ou escalar horizontalmente para vários computadores ([até quatro nós](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)), e uma única atividade de cópia distribui sua partição em todos os nós. 

- Uma única atividade de cópia lê e grava no armazenamento de dados usando vários threads. 

- Azure Data Factory fluxo de controle pode iniciar várias atividades de cópia em paralelo. Por exemplo, ele pode iniciá-los usando um [loop for each](./control-flow-for-each-activity.md). 

Para obter mais informações, consulte [Guia de desempenho e escalabilidade da atividade de cópia](./copy-activity-performance.md).

## <a name="resilience"></a>Resiliência

Em uma execução de atividade de cópia única, Azure Data Factory tem um mecanismo de repetição interno, que permite que ele manipule um certo nível de falhas transitórias nos armazenamentos de dados ou na rede subjacente.

Com Azure Data Factory atividade de cópia, ao copiar dados entre armazenamentos de dados de origem e de coletor, você tem duas maneiras de lidar com linhas incompatíveis. Você pode anular e reprovar a atividade de cópia ou continuar a copiar o restante dos dados, ignorando as linhas de dados incompatíveis. Além disso, para saber a causa da falha, você pode registrar em log as linhas incompatíveis no armazenamento de BLOBs do Azure ou Azure Data Lake Store, corrigir os dados na fonte de dados e repetir a atividade de cópia.

## <a name="network-security"></a>Segurança de rede 

Por padrão, Azure Data Factory transfere dados do servidor Netezza local para uma conta de armazenamento do Azure ou um banco de dados do Azure Synapse Analytics usando uma conexão criptografada por HTTPS (Hypertext Transfer Protocol Secure). O HTTPS fornece a criptografia de dados em trânsito e impede ataques de interceptação e man-in-the-middle.

Como alternativa, se você não quiser que os dados sejam transferidos pela Internet pública, poderá ajudar a obter maior segurança transferindo dados por meio de um link de emparelhamento privado por meio da rota expressa do Azure. 

A próxima seção discute como obter maior segurança.

## <a name="solution-architecture"></a>Arquitetura da solução

Esta seção aborda duas maneiras de migrar seus dados.

### <a name="migrate-data-over-the-public-internet"></a>Migrar dados pela Internet pública

![Migrar dados pela Internet pública](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

O diagrama anterior pode ser interpretado da seguinte maneira:

- Nessa arquitetura, você transfere os dados com segurança usando HTTPS pela Internet pública.

- Para obter essa arquitetura, você precisa instalar o Azure Data Factory o tempo de execução de integração (auto-hospedado) em um computador Windows atrás de um firewall corporativo. Verifique se esse Integration Runtime pode acessar diretamente o servidor Netezza. Para usar totalmente a largura de banda de armazenamento de dados e de rede para copiar dados, você pode escalar verticalmente seu computador ou escalar horizontalmente para vários computadores.

- Usando essa arquitetura, você pode migrar dados de instantâneo inicial e dados Delta.

### <a name="migrate-data-over-a-private-network"></a>Migrar dados por uma rede privada 

![Migrar dados por uma rede privada](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

O diagrama anterior pode ser interpretado da seguinte maneira:

- Nessa arquitetura, você migra dados por um link de emparelhamento privado por meio da rota expressa do Azure e os dados nunca percorrem pela Internet pública. 

- Para obter essa arquitetura, você precisa instalar o Azure Data Factory o tempo de execução de integração (auto-hospedado) em uma VM (máquina virtual) do Windows em sua rede virtual do Azure. Para usar totalmente a largura de banda de armazenamento de dados e de rede para copiar dados, você pode escalar verticalmente sua VM ou escalar horizontalmente para várias VMs.

- Usando essa arquitetura, você pode migrar dados de instantâneo inicial e dados Delta.

## <a name="implement-best-practices"></a>Implementar práticas recomendadas 

### <a name="manage-authentication-and-credentials"></a>Gerenciar autenticação e credenciais 

- Para autenticar no Netezza, você pode usar a [autenticação ODBC por meio da cadeia de conexão](./connector-netezza.md#linked-service-properties). 

- Para autenticar no armazenamento de BLOBs do Azure: 

   - É altamente recomendável usar [identidades gerenciadas para recursos do Azure](./connector-azure-blob-storage.md#managed-identity). Criado com base em uma identidade de Azure Data Factory gerenciada automaticamente no Azure Active Directory (AD do Azure), as identidades gerenciadas permitem que você configure pipelines sem precisar fornecer credenciais na definição de serviço vinculado.  

   - Como alternativa, você pode autenticar no armazenamento de BLOBs do Azure usando a [entidade de serviço](./connector-azure-blob-storage.md#service-principal-authentication), uma [assinatura de acesso compartilhado](./connector-azure-blob-storage.md#shared-access-signature-authentication)ou uma [chave de conta de armazenamento](./connector-azure-blob-storage.md#account-key-authentication). 

- Para autenticar para o Azure Data Lake Storage Gen2: 

   - É altamente recomendável usar [identidades gerenciadas para recursos do Azure](./connector-azure-data-lake-storage.md#managed-identity).
   
   - Você também pode usar a [entidade de serviço](./connector-azure-data-lake-storage.md#service-principal-authentication) ou uma chave de conta de [armazenamento](./connector-azure-data-lake-storage.md#account-key-authentication). 

- Para autenticar no Azure Synapse Analytics:

   - É altamente recomendável usar [identidades gerenciadas para recursos do Azure](./connector-azure-sql-data-warehouse.md#managed-identity).
   
   - Você também pode usar a [entidade de serviço](./connector-azure-sql-data-warehouse.md#service-principal-authentication) ou a autenticação do [SQL](./connector-azure-sql-data-warehouse.md#sql-authentication).

- Quando você não estiver usando identidades gerenciadas para recursos do Azure, é altamente recomendável [armazenar as credenciais em Azure Key Vault](./store-credentials-in-key-vault.md) para facilitar o gerenciamento e a rotação centralizado das chaves sem precisar modificar Azure data Factory serviços vinculados. Essa também é uma das [melhores práticas de CI/CD](./continuous-integration-deployment.md#best-practices-for-cicd). 

### <a name="migrate-initial-snapshot-data"></a>Migrar dados de instantâneo inicial 

Para tabelas pequenas (ou seja, tabelas com um volume de menos de 100 GB ou que podem ser migradas para o Azure dentro de duas horas), você pode fazer com que cada trabalho de cópia carregue dados por tabela. Para obter maior taxa de transferência, você pode executar vários Azure Data Factory trabalhos de cópia para carregar tabelas separadas simultaneamente. 

Em cada trabalho de cópia, para executar consultas paralelas e copiar dados por partições, você também pode alcançar algum nível de paralelismo usando a [ `parallelCopies` configuração de propriedade](./copy-activity-performance.md#parallel-copy) com uma das seguintes opções de partição de dados:

- Para obter ajuda para obter maior eficiência, recomendamos que você inicie a partir de uma fatia de dados.  Certifique-se de que o valor na `parallelCopies` configuração seja menor que o número total de partições de fatia de dados em sua tabela no servidor Netezza.  

- Se o volume de cada partição de fatia de dados ainda for grande (por exemplo, 10 GB ou mais), recomendamos que você alterne para uma partição de intervalo dinâmico. Essa opção oferece maior flexibilidade para definir o número de partições e o volume de cada partição por coluna de partição, limite superior e limite inferior.

Para tabelas maiores (ou seja, tabelas com um volume de 100 GB ou mais ou que *não podem* ser migradas para o Azure dentro de duas horas), recomendamos que você Particione os dados por consulta personalizada e, em seguida, faça com que cada cópia-trabalho Copie uma partição por vez. Para obter uma melhor taxa de transferência, você pode executar vários Azure Data Factory trabalhos de cópia simultaneamente. Para cada destino de cópia-trabalho de carregar uma partição por consulta personalizada, você pode aumentar a taxa de transferência habilitando o paralelismo por meio da fatia de dados ou do intervalo dinâmico. 

Se qualquer trabalho de cópia falhar devido a um problema transitório de rede ou armazenamento de dados, você poderá executar novamente o trabalho de cópia com falha para recarregar essa partição específica da tabela. Outros trabalhos de cópia que carregam outras partições não são afetados.

Ao carregar dados em um banco de dado do Azure Synapse Analytics, sugerimos que você habilite o polybase dentro do trabalho de cópia com o armazenamento de BLOBs do Azure como preparo.

### <a name="migrate-delta-data"></a>Migrar dados Delta 

Para identificar as linhas novas ou atualizadas de sua tabela, use uma coluna timestamp ou uma chave de incremento dentro do esquema. Em seguida, você pode armazenar o valor mais recente como uma marca d' água alta em uma tabela externa e, em seguida, usá-lo para filtrar os dados Delta na próxima vez em que carregar os dados. 

Cada tabela pode usar uma coluna de marca d' água diferente para identificar suas linhas novas ou atualizadas. Sugerimos que você crie uma tabela de controle externo. Na tabela, cada linha representa uma tabela no servidor Netezza com seu nome de coluna de marca d' água específico e o valor de marca d' água alta. 

### <a name="configure-a-self-hosted-integration-runtime"></a>Configurar um tempo de execução de integração auto-hospedado

Se você estiver migrando dados do servidor Netezza para o Azure, se o servidor estiver no local por trás de seu firewall da empresa ou em um ambiente de rede virtual, você precisará instalar um IR hospedado em um computador ou VM do Windows, que é o mecanismo usado para mover dados. Como você está instalando o IR auto-hospedado, recomendamos a seguinte abordagem:

- Para cada máquina ou VM do Windows, comece com uma configuração de 32 vCPU e 128-GB de memória. Você pode continuar monitorando o uso de CPU e de memória do computador IR durante a migração de dados para ver se você precisa escalar verticalmente o computador para melhorar o desempenho ou reduzir verticalmente o computador para economizar o custo.

- Você também pode escalar horizontalmente associando até quatro nós com um único IR auto-hospedado. Um único trabalho de cópia em execução em um IR de hospedagem interna aplica automaticamente todos os nós de VM para copiar os dados em paralelo. Para alta disponibilidade, comece com quatro nós de VM para evitar um único ponto de falha durante a migração de dados.

### <a name="limit-your-partitions"></a>Limitar suas partições

Como prática recomendada, realize uma prova de conceito de desempenho (POC) com um conjunto de exemplo representativo, para que você possa determinar um tamanho de partição apropriado para cada atividade de cópia. Sugerimos que você carregue cada partição no Azure dentro de duas horas.  

Para copiar uma tabela, comece com uma única atividade de cópia com uma única máquina de IR hospedada internamente. Aumente gradualmente a `parallelCopies` configuração com base no número de partições de fatia de dados em sua tabela. Veja se a tabela inteira pode ser carregada no Azure dentro de duas horas, de acordo com a taxa de transferência resultante do trabalho de cópia. 

Se ele não puder ser carregado no Azure dentro de duas horas, e a capacidade do nó IR auto-hospedado e o armazenamento de dados não forem totalmente usados, Aumente gradualmente o número de atividades de cópia simultâneas até alcançar o limite de sua rede ou o limite de largura de banda dos armazenamentos de dados. 

Continue monitorando o uso de CPU e de memória no computador IR auto-hospedado e esteja pronto para escalar verticalmente o computador ou escalar horizontalmente para vários computadores quando você vir que a CPU e a memória são totalmente usadas. 

Quando você encontrar erros de limitação, conforme relatado por Azure Data Factory atividade de cópia, reduza a simultaneidade ou a `parallelCopies` configuração em Azure data Factory ou considere aumentar a largura de banda ou os limites de IOPS (operações por segundo) da rede e dos armazenamentos de dados. 


### <a name="estimate-your-pricing"></a>Estime seus preços 

Considere o pipeline a seguir, que é construído para migrar dados do servidor Netezza local para um banco de dado do Azure Synapse Analytics:

![O pipeline de preços](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Vamos supor que as seguintes instruções sejam verdadeiras: 

- O volume de dados total é de 50 terabytes (TB). 

- Estamos migrando dados usando a arquitetura de primeira solução (o servidor Netezza é local, por trás do firewall).

- O volume de 50 TB é dividido em 500 partições e cada atividade de cópia move uma partição.

- Cada atividade de cópia é configurada com um IR auto-hospedado em quatro máquinas e atinge uma taxa de transferência de 20 megabytes por segundo (MBps). (Na atividade de cópia, `parallelCopies` é definido como 4, e cada thread para carregar dados da tabela alcança uma taxa de transferência de 5 Mbps.)

- A simultaneidade ForEach é definida como 3 e a taxa de transferência agregada é de 60 MBps.

- No total, leva 243 horas para concluir a migração.

Com base nas suposições anteriores, aqui está o preço estimado: 

![A tabela de preços](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> O preço mostrado na tabela anterior é hipotético. O preço real depende da taxa de transferência real no ambiente. O preço do computador com Windows (com o IR hospedado automaticamente instalado) não está incluído. 

### <a name="additional-references"></a>Referências adicionais

Para obter mais informações, consulte os seguintes artigos e guias:

- [Migre dados de uma data warehouse relacional local para o Azure usando Azure Data Factory](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Conector do Netezza](./connector-netezza.md)
- [Conector do ODBC](./connector-odbc.md)
- [Conector do armazenamento de BLOBs do Azure](./connector-azure-blob-storage.md)
- [Conector do Azure Data Lake Store Gen2](./connector-azure-data-lake-storage.md)
- [Conector do Azure Synapse Analytics](./connector-azure-sql-data-warehouse.md)
- [Guia de ajuste de desempenho da atividade de cópia](./copy-activity-performance.md)
- [Criar e configurar um runtime de integração auto-hospedada](./create-self-hosted-integration-runtime.md)
- [HA e escalabilidade do runtime de integração auto-hospedada](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Considerações sobre segurança da movimentação de dados](./data-movement-security-considerations.md)
- [Armazenar credenciais no Azure Key Vault](./store-credentials-in-key-vault.md)
- [Copiar dados incrementalmente de uma tabela](./tutorial-incremental-copy-portal.md)
- [Copiar dados incrementalmente de várias tabelas](./tutorial-incremental-copy-multiple-tables-portal.md)
- [Página de preços do Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Próximas etapas

- [Copiar arquivos de vários contêineres usando Azure Data Factory](solution-template-copy-files-multiple-containers.md)