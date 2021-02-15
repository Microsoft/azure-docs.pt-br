---
title: Migrar dados de um cluster Hadoop local para o armazenamento do Azure
description: Saiba como usar Azure Data Factory para migrar dados do cluster Hadoop local para o armazenamento do Azure.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/30/2019
ms.openlocfilehash: 9959a37d9b68d756437a3b4f0d75a2d63385758e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367785"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>Usar Azure Data Factory para migrar dados de um cluster Hadoop local para o armazenamento do Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

O Azure Data Factory fornece um mecanismo eficaz, robusto e econômico para migrar dados em escala do HDFS local para o armazenamento de BLOBs do Azure ou Azure Data Lake Storage Gen2. 

O Data Factory oferece duas abordagens básicas para migrar dados do HDFS local para o Azure. Você pode selecionar a abordagem com base em seu cenário. 

- **Data Factory modo DistCp** (recomendado): em data Factory, você pode usar [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (cópia distribuída) para copiar arquivos no estado em que se encontram para o armazenamento de BLOBs do Azure (incluindo [cópia em etapas](./copy-activity-performance.md#staged-copy)) ou Azure data Lake Store Gen2. Use Data Factory integrado com o DistCp para tirar proveito de um cluster potente existente para obter a melhor taxa de transferência de cópia. Você também obtém o benefício do agendamento flexível e uma experiência de monitoramento unificada da Data Factory. Dependendo de sua configuração de Data Factory, a atividade de cópia construirá automaticamente um comando DistCp, enviará os dados para o cluster Hadoop e, em seguida, monitorará o status da cópia. É recomendável Data Factory modo DistCp para migrar dados de um cluster Hadoop local para o Azure.
- **Data Factory modo de tempo de execução de integração nativo**: o DistCp não é uma opção em todos os cenários. Por exemplo, em um ambiente de redes virtuais do Azure, a ferramenta DistCp não dá suporte ao emparelhamento privado do Azure ExpressRoute com um ponto de extremidade de rede virtual do armazenamento do Azure. Além disso, em alguns casos, você não deseja usar seu cluster Hadoop existente como um mecanismo para migrar dados para que você não coloque cargas pesadas no cluster, o que pode afetar o desempenho de trabalhos ETL existentes. Em vez disso, você pode usar a funcionalidade nativa do tempo de execução de integração do Data Factory como o mecanismo que copia dados do HDFS local para o Azure.

Este artigo fornece as seguintes informações sobre as duas abordagens:
> [!div class="checklist"]
> * Desempenho 
> * Resiliência de cópia
> * Segurança de rede
> * Arquitetura da solução de alto nível 
> * Melhores práticas de implementação  

## <a name="performance"></a>Desempenho

No modo de Data Factory DistCp, a taxa de transferência é a mesma de se você usar a ferramenta DistCp de forma independente. Data Factory modo DistCp maximiza a capacidade de seu cluster Hadoop existente. Você pode usar o DistCp para a cópia grande entre clusters ou dentro do cluster. 

O DistCp usa o MapReduce para afetar sua distribuição, tratamento de erros e recuperação e relatórios. Ele expande uma lista de arquivos e diretórios em entrada para mapeamento de tarefas. Cada tarefa copia uma partição de arquivo que é especificada na lista de origem. Você pode usar o Data Factory integrado ao DistCp para criar pipelines para utilizar totalmente a largura de banda da rede, o IOPS de armazenamento e a largura de banda para maximizar a taxa de transferência de movimentação de dados para seu ambiente.  

Data Factory modo de tempo de execução de integração nativa também permite paralelismo em diferentes níveis. Você pode usar o paralelismo para utilizar totalmente sua largura de banda de rede, IOPS de armazenamento e largura de banda para maximizar a taxa de transferência de movimentação de dados:

- Uma única atividade de cópia pode aproveitar os recursos de computação escalonáveis. Com um tempo de execução de integração auto-hospedado, você pode escalar verticalmente o computador manualmente ou escalar horizontalmente para vários computadores ([até quatro nós](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)). Uma única atividade de cópia particiona seu arquivo definido em todos os nós. 
- Uma única atividade de cópia lê e grava no armazenamento de dados usando vários threads. 
- Data Factory fluxo de controle pode iniciar várias atividades de cópia em paralelo. Por exemplo, você pode usar um [loop for each](./control-flow-for-each-activity.md). 

Para obter mais informações, consulte o [Guia de desempenho da atividade de cópia](./copy-activity-performance.md).

## <a name="resilience"></a>Resiliência

No modo de Data Factory DistCp, você pode usar diferentes parâmetros de linha de comando do DistCp (por exemplo, `-i` ignorar falhas ou `-update` gravar dados quando o arquivo de origem e o arquivo de destino diferem no tamanho) para diferentes níveis de resiliência.

No modo de tempo de execução de integração nativo do Data Factory, em uma única execução da atividade de cópia, Data Factory tem um mecanismo de repetição interno. Ele pode lidar com um certo nível de falhas transitórias nos armazenamentos de dados ou na rede subjacente. 

Ao fazer a cópia binária do HDFS local para o armazenamento de BLOBs e do HDFS local para Data Lake Store Gen2, Data Factory executa automaticamente o ponto de verificação em uma grande extensão. Se uma execução da atividade de cópia falhar ou expirar, em uma nova tentativa subsequente (verifique se a contagem de repetição é > 1), a cópia será retomada do último ponto de falha em vez de iniciar no início.

## <a name="network-security"></a>Segurança de rede 

Por padrão, Data Factory transfere dados do HDFS local para o armazenamento de BLOBs ou Azure Data Lake Storage Gen2 usando uma conexão criptografada sobre o protocolo HTTPS. O HTTPS fornece a criptografia de dados em trânsito e impede ataques de interceptação e man-in-the-middle. 

Como alternativa, se você não quiser que os dados sejam transferidos pela Internet pública, para maior segurança, você pode transferir dados por um link de emparelhamento privado por meio do ExpressRoute. 

## <a name="solution-architecture"></a>Arquitetura da solução

Esta imagem ilustra a migração de dados pela Internet pública:

![Diagrama que mostra a arquitetura da solução para migrar dados por uma rede pública](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- Nessa arquitetura, os dados são transferidos com segurança usando HTTPS pela Internet pública. 
- É recomendável usar Data Factory modo DistCp em um ambiente de rede pública. Você pode aproveitar um cluster eficiente existente para obter a melhor taxa de transferência de cópia. Você também obtém o benefício da programação flexível e da experiência de monitoramento unificada da Data Factory.
- Para essa arquitetura, você deve instalar o Data Factory o tempo de execução de integração auto-hospedado em um computador Windows atrás de um firewall corporativo para enviar o comando DistCp para o cluster Hadoop e monitorar o status da cópia. Como o computador não é o mecanismo que moverá os dados (apenas para fins de controle), a capacidade do computador não afeta a taxa de transferência da movimentação de dados.
- Há suporte para os parâmetros existentes do comando DistCp.

Esta imagem ilustra a migração de dados por um link privado: 

![Diagrama que mostra a arquitetura da solução para migrar dados por uma rede privada](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- Nessa arquitetura, os dados são migrados por um link de emparelhamento privado por meio do Azure ExpressRoute. Os dados nunca percorrem pela Internet pública.
- A ferramenta DistCp não dá suporte ao emparelhamento privado do ExpressRoute com um ponto de extremidade de rede virtual do armazenamento do Azure. Recomendamos que você use o recurso nativo do Data Factory por meio do Integration Runtime para migrar os dados.
- Para essa arquitetura, você deve instalar o Data Factory o tempo de execução de integração auto-hospedado em uma VM do Windows em sua rede virtual do Azure. Você pode escalar verticalmente sua VM ou escalar horizontalmente para várias VMs a fim de utilizar totalmente sua rede e IOPS de armazenamento ou largura de banda.
- A configuração recomendada para começar com o para cada VM do Azure (com o Data Factory tempo de execução de integração auto-hospedado instalado) é Standard_D32s_v3 com 32 vCPU e 128 GB de memória. Você pode monitorar o uso de CPU e de memória da VM durante a migração de dados para ver se você precisa escalar verticalmente a VM para melhorar o desempenho ou reduzir verticalmente a VM para diminuir o custo.
- Você também pode escalar horizontalmente associando até quatro nós de VM com um único tempo de execução de integração auto-hospedado. Um único trabalho de cópia em execução em um tempo de execução de integração auto-hospedado particiona automaticamente o conjunto de arquivos e usa todos os nós de VM para copiar os arquivos em paralelo. Para alta disponibilidade, recomendamos que você comece com dois nós de VM para evitar um cenário de ponto único de falha durante a migração de dados.
- Quando você usa essa arquitetura, a migração de dados de instantâneo inicial e a migração de dados Delta estão disponíveis para você.

## <a name="implementation-best-practices"></a>Melhores práticas de implementação

Recomendamos que você siga estas práticas recomendadas ao implementar a migração de dados.

### <a name="authentication-and-credential-management"></a>Autenticação e gerenciamento de credenciais 

- Para autenticar no HDFS, você pode usar [o Windows (Kerberos) ou anônimo](./connector-hdfs.md#linked-service-properties). 
- Há suporte para vários tipos de autenticação para conexão com o armazenamento de BLOBs do Azure.  É altamente recomendável usar [identidades gerenciadas para recursos do Azure](./connector-azure-blob-storage.md#managed-identity). Criado com base em uma identidade de Data Factory gerenciada automaticamente no Azure Active Directory (AD do Azure), as identidades gerenciadas permitem que você configure pipelines sem fornecer credenciais na definição de serviço vinculado. Como alternativa, você pode autenticar no armazenamento de BLOBs usando uma [entidade de serviço](./connector-azure-blob-storage.md#service-principal-authentication), uma [assinatura de acesso compartilhado](./connector-azure-blob-storage.md#shared-access-signature-authentication)ou uma [chave de conta de armazenamento](./connector-azure-blob-storage.md#account-key-authentication). 
- Também há suporte para vários tipos de autenticação para conexão com o Data Lake Storage Gen2.  É altamente recomendável usar [identidades gerenciadas para recursos do Azure](./connector-azure-data-lake-storage.md#managed-identity), mas você também pode usar uma [entidade de serviço](./connector-azure-data-lake-storage.md#service-principal-authentication) ou uma chave de conta de [armazenamento](./connector-azure-data-lake-storage.md#account-key-authentication). 
- Quando você não estiver usando identidades gerenciadas para recursos do Azure, é altamente recomendável [armazenar as credenciais em Azure Key Vault](./store-credentials-in-key-vault.md) para facilitar o gerenciamento e a rotação central de chaves sem modificar data Factory serviços vinculados. Essa também é uma [prática recomendada para CI/CD](./continuous-integration-deployment.md#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migração de dados de instantâneo inicial 

No modo de Data Factory DistCp, você pode criar uma atividade de cópia para enviar o comando DistCp e usar parâmetros diferentes para controlar o comportamento inicial de migração de dados. 

Em Data Factory modo de tempo de execução de integração nativo, recomendamos a partição de dados, especialmente quando você migrar mais de 10 TB de dados. Para particionar os dados, use os nomes de pasta no HDFS. Em seguida, cada trabalho de cópia Data Factory pode copiar uma partição de pasta de cada vez. Você pode executar vários Data Factory trabalhos de cópia simultaneamente para obter uma melhor taxa de transferência.

Se qualquer um dos trabalhos de cópia falhar devido a problemas transitórios de rede ou armazenamento de dados, você poderá executar novamente o trabalho de cópia com falha para recarregar essa partição específica do HDFS. Outros trabalhos de cópia que estão carregando outras partições não são afetados.

### <a name="delta-data-migration"></a>Migração de dados delta 

No modo de Data Factory DistCp, você pode usar o parâmetro de linha de comando DistCp `-update` , gravar dados quando o arquivo de origem e o arquivo de destino diferem em tamanho, para migração de dados Delta.

No modo de integração nativa Data Factory, a maneira mais eficaz de identificar arquivos novos ou alterados do HDFS é usando uma Convenção de nomenclatura com particionamento de tempo. Quando os dados no HDFS tiverem sido particionados por tempo com informações de fatia de tempo no nome do arquivo ou da pasta (por exemplo, */yyyy/mm/dd/file.csv*), seu pipeline poderá identificar facilmente quais arquivos e pastas copiar incrementalmente.

Como alternativa, se os dados no HDFS não tiverem o particionamento de tempo, Data Factory poderá identificar arquivos novos ou alterados usando seu valor **LastModifiedDate** . Data Factory examina todos os arquivos do HDFS e copia somente arquivos novos e atualizados que têm um carimbo de data/hora modificado pela última vez que é maior que um valor definido. 

Se você tiver um grande número de arquivos no HDFS, a verificação de arquivo inicial poderá levar muito tempo, independentemente de quantos arquivos corresponderem à condição do filtro. Nesse cenário, recomendamos que você primeiro Particione os dados usando a mesma partição usada para a migração de instantâneo inicial. Em seguida, a verificação de arquivo pode ocorrer em paralelo.

### <a name="estimate-price"></a>Estimar preço 

Considere o seguinte pipeline para migrar dados do HDFS para o armazenamento de BLOBs do Azure: 

![Diagrama que mostra o pipeline de preços](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Vamos supor as seguintes informações: 

- O volume de dados total é de 1 PB.
- Você migra dados usando o modo de tempo de execução de integração nativa Data Factory.
- 1 PB é dividido em 1.000 partições e cada cópia move uma partição.
- Cada atividade de cópia é configurada com um tempo de execução de integração auto-hospedado associado a quatro computadores e que atinge a taxa de transferência de 500 MBps.
- A simultaneidade ForEach é definida como **4** e a taxa de transferência agregada é de 2 Gbps.
- No total, leva 146 horas para concluir a migração.

Este é o preço estimado com base em nossas suposições: 

![Tabela que mostra os cálculos de preços](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Este é um exemplo de preço hipotético. O preço real depende da taxa de transferência real no ambiente.
> O preço de uma VM do Windows do Azure (com o tempo de execução de integração auto-hospedado instalado) não está incluído.

### <a name="additional-references"></a>Referências adicionais

- [HDFS connector](./connector-hdfs.md) (Conector de HDFS)
- [Conector do armazenamento de BLOBs do Azure](./connector-azure-blob-storage.md)
- [Conector do Azure Data Lake Store Gen2](./connector-azure-data-lake-storage.md)
- [Guia de ajuste de desempenho da atividade de cópia](./copy-activity-performance.md)
- [Criar e configurar um runtime de integração auto-hospedada](./create-self-hosted-integration-runtime.md)
- [Alta disponibilidade e escalabilidade do tempo de execução de integração auto-hospedado](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Considerações sobre segurança da movimentação de dados](./data-movement-security-considerations.md)
- [Armazenar credenciais no Azure Key Vault](./store-credentials-in-key-vault.md)
- [Copiar um arquivo incrementalmente com base em um nome de arquivo particionado por tempo](./tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)
- [Copiar arquivos novos e alterados com base na LastModifiedDate](./tutorial-incremental-copy-lastmodified-copy-data-tool.md)
- [Página de preços do Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Próximas etapas

- [Copiar arquivos de vários contêineres usando Azure Data Factory](solution-template-copy-files-multiple-containers.md)