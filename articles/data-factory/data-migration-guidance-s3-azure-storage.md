---
title: Migrar dados do Amazon S3 para o Armazenamento do Azure
description: Use o Azure Data Factory para migrar dados do Amazon S3 para o Armazenamento do Azure.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: 2be8a9c7476bda6952ed1eaa15d29fe9c01b59a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371304"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Use o Azure Data Factory para migrar dados do Amazon S3 para o Armazenamento do Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

O Azure Data Factory fornece um mecanismo de desempenho, robusto e econômico para migrar dados em escala do Amazon S3 para o Armazenamento de Blobs do Azure ou do Azure Data Lake Storage Gen2.  Este artigo fornece as seguintes informações para desenvolvedores e engenheiros de dados: 

> [!div class="checklist"]
> * Desempenho 
> * Resiliência de cópia
> * Segurança de rede
> * Arquitetura da solução de alto nível 
> * Melhores práticas de implementação  

## <a name="performance"></a>Desempenho

O ADF oferece uma arquitetura sem servidor que permite o paralelismo em diferentes níveis, possibilitando aos desenvolvedores criar pipelines para utilizar totalmente a largura de banda da rede, bem como a IOPS de armazenamento e a largura de banda para maximizar a taxa de transferência da movimentação de dados para o seu ambiente. 

Os clientes migraram petabytes de dados com êxito que consistiam em centenas de milhões de arquivos do Amazon S3 para o Armazenamento de Blobs do Azure, com uma taxa de transferência sustentada de 2 GBps e superior. 

![O diagrama mostra várias partições de arquivo em um armazenamento AWS S3 com as ações de cópia associadas ao ADLS Gen2 do Armazenamento de Blobs do Azure.](media/data-migration-guidance-s3-to-azure-storage/performance.png)

A figura acima ilustra como obter grandes velocidades de movimentação de dados por meio de diferentes níveis de paralelismo:
 
- Uma só atividade de cópia pode aproveitar os recursos de computação escalonáveis: ao usar o Azure Integration Runtime, você pode especificar [até 256 DIUs](./copy-activity-performance.md#data-integration-units) para cada atividade de cópia por meio da computação sem servidor; ao usar o runtime de integração auto-hospedada, você pode escalar verticalmente o computador no modo manual ou escalá-lo horizontalmente para vários computadores ([até quatro nós](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)) e uma só atividade de cópia particionará o conjunto de arquivos em todos os nós. 
- Uma só atividade de cópia faz leituras e gravações no armazenamento de dados usando vários threads. 
- O fluxo de controle do ADF pode iniciar várias atividades de cópia em paralelo, por exemplo, usando o [loop For Each](./control-flow-for-each-activity.md). 

## <a name="resilience"></a>Resiliência

Em uma só execução de atividade de cópia, o ADF tem um mecanismo de repetição interno para lidar com um certo nível de falhas transitórias nos armazenamentos de dados ou na rede subjacente. 

Ao fazer cópias binárias do S3 para o Blob e do S3 para o ADLS Gen2, o ADF executa automaticamente a definição de pontos de verificação.  Se uma execução de atividade de cópia falhar ou expirar, em uma nova tentativa seguinte, a cópia será retomada do último ponto de falha em vez de começar do início. 

## <a name="network-security"></a>Segurança de rede 

Por padrão, o ADF transfere dados do Amazon S3 para o Armazenamento de Blobs do Azure ou o Azure Data Lake Storage Gen2 usando a conexão criptografada via protocolo HTTPS.  O HTTPS fornece a criptografia de dados em trânsito e impede ataques de interceptação e man-in-the-middle. 

Como alternativa, se você não quiser que os dados sejam transferidos pela Internet pública, obtenha maior segurança transferindo os dados por um link de emparelhamento privado entre o AWS Direct Connect e o Azure ExpressRoute.  Veja a arquitetura da solução abaixo e descubra como isso pode ser feito. 

## <a name="solution-architecture"></a>Arquitetura da solução

Migrar dados pela Internet pública:

![O diagrama mostra a migração pela Internet por HTTP de um repositório AWS S3 por meio do Azure Integration Runtime em um ADF do Azure para o Armazenamento do Azure. O runtime tem um canal de controle com o Data Factory.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- Nessa arquitetura, os dados são transferidos com segurança via HTTPS pela Internet pública. 
- O Amazon S3 de origem e o Armazenamento de Blobs do Azure ou o Azure Data Lake Storage Gen2 de destino estão configurados para permitir o tráfego de todos os endereços IP de rede.  Veja a segunda arquitetura abaixo e descubra como você pode restringir o acesso à rede para um intervalo de IP específico. 
- Aumente com facilidade a quantidade de potência por meio da computação sem servidor para utilizar totalmente a rede e a largura de banda de armazenamento e obter a melhor taxa de transferência para o seu ambiente. 
- A migração de instantâneo inicial e a migração de dados delta podem ser obtidas usando essa arquitetura. 

Migrar dados por link privado: 

![O diagrama mostra a migração em uma conexão de emparelhamento privado de um repositório AWS S3 por meio do runtime de integração auto-hospedada em máquinas virtuais do Azure para pontos de extremidade de serviço da VNet para o Armazenamento do Azure. O runtime tem um canal de controle com o Data Factory.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- Nessa arquitetura, a migração de dados é feita por meio de um link de emparelhamento privado entre o AWS Direct Connect e o Azure ExpressRoute, de modo que os dados nunca percorram a Internet pública.  Ele exige o uso da AWS VPC e da Rede Virtual do Azure. 
- Você precisa instalar o runtime de integração auto-hospedada do ADF em uma VM do Windows na rede virtual do Azure para obter essa arquitetura.  Escale verticalmente suas VMs do IR auto-hospedado manualmente ou escale-as horizontalmente para várias VMs (até 4 nós) para utilizar totalmente a rede e a IOPS/a largura de banda de armazenamento. 
- Se for aceitável transferir dados via HTTPS, mas você quiser bloquear o acesso à rede ao S3 de origem para um intervalo de IP específico, adote uma variação dessa arquitetura removendo a AWS VPC e substituindo o link privado por HTTPS.  O ideal é manter o IR auto-hospedado e a Rede Virtual do Azure na VM do Azure para que você possa ter um IP roteável publicamente estático para fins de filtragem. 
- A migração de dados de instantâneo inicial e a migração de dados delta podem ser obtidas por meio dessa arquitetura. 

## <a name="implementation-best-practices"></a>Melhores práticas de implementação 

### <a name="authentication-and-credential-management"></a>Autenticação e gerenciamento de credenciais 

- Para se autenticar na conta do Amazon S3, você precisará usar a [chave de acesso para a conta do IAM](./connector-amazon-simple-storage-service.md#linked-service-properties). 
- Há suporte para vários tipos de autenticação para se conectar ao Armazenamento de Blobs do Azure.  O uso de [identidades gerenciadas para recursos do Azure](./connector-azure-blob-storage.md#managed-identity) é altamente recomendável: criado com base em uma identificação do ADF gerenciada automaticamente no Azure AD, ele permite que você configure pipelines sem fornecer credenciais na definição de serviço vinculado.  Como alternativa, você pode se autenticar no Armazenamento de Blobs do Azure usando a [entidade de serviço](./connector-azure-blob-storage.md#service-principal-authentication), a [Assinatura de Acesso Compartilhado](./connector-azure-blob-storage.md#shared-access-signature-authentication) ou a [chave da conta de armazenamento](./connector-azure-blob-storage.md#account-key-authentication). 
- Também há suporte para vários tipos de autenticação para se conectar ao Azure Data Lake Storage Gen2.  O uso de [identidades gerenciadas para recursos do Azure](./connector-azure-data-lake-storage.md#managed-identity) é altamente recomendável, embora a [entidade de serviço](./connector-azure-data-lake-storage.md#service-principal-authentication) ou a [chave da conta de armazenamento](./connector-azure-data-lake-storage.md#account-key-authentication) também possa ser usada. 
- Quando você não estiver usando identidades gerenciadas para recursos do Azure, o [armazenamento das credenciais no Azure Key Vault](./store-credentials-in-key-vault.md) é altamente recomendável para facilitar o gerenciamento centralizado e a rotação das chaves sem modificar os serviços vinculados do ADF.  Essa também é uma das [melhores práticas de CI/CD](./continuous-integration-deployment.md#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migração de dados de instantâneo inicial 

A partição de dados é recomendada especialmente ao migrar mais de 100 TB de dados.  Para particionar os dados, aproveite a configuração de ‘prefixo’ para filtrar as pastas e os arquivos no Amazon S3 por nome e, depois, cada trabalho de cópia do ADF pode copiar uma partição de cada vez.  Você pode executar vários trabalhos de cópia do ADF simultaneamente para obter uma melhor taxa de transferência. 

Se um dos trabalhos de cópia falhar devido a um problema transitório de rede ou de armazenamento de dados, você poderá executar novamente o trabalho de cópia com falha para recarregar essa partição específica novamente no AWS S3.  Todos os outros trabalhos de cópia que carregam outras partições não serão afetados. 

### <a name="delta-data-migration"></a>Migração de dados delta 

A maneira mais eficaz de identificar arquivos novos ou alterados no AWS S3 é usar a convenção de nomenclatura particionadas por tempo, quando os dados no AWS S3 tiverem sido particionados com informações de fração de tempo no nome do arquivo ou da pasta (por exemplo, /yyyy/mm/dd/file.csv), o pipeline poderá identificar com facilidade quais arquivos/pastas serão copiados de maneira incremental. 

Como alternativa, se os seus dados do AWS S3 não forem particionados por tempo, o ADF poderá identificar arquivos novos ou alterados pela LastModifiedDate.   A maneira como isso funciona é que o ADF examinará todos os arquivos do AWS S3 e copiará apenas o arquivo novo e atualizado cujo carimbo de data/hora da última modificação for maior que determinado valor.  Lembre-se de que, se você tiver um grande número de arquivos no S3, a verificação de arquivo inicial poderá levar muito tempo, independentemente de quantos arquivos corresponderem à condição do filtro.  Nesse caso, recomendamos particionar os dados primeiro, usando a mesma configuração de ‘prefixo’ para a migração de instantâneo inicial, de modo que a verificação de arquivo possa ocorrer em paralelo.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Para os cenários que exigem o runtime de integração auto-hospedada na VM do Azure 

Se você estiver migrando dados no link privado ou se quiser permitir um intervalo IP específico no firewall do Amazon S3, será necessário instalar o runtime de integração auto-hospedada na VM do Windows no Azure. 

- A configuração inicial recomendada para cada VM do Azure é Standard_D32s_v3 com 32 vCPU e 128 GB de memória.  Continue monitorando a utilização de CPU e de memória da VM do IR durante a migração de dados para ver se você precisa escalar verticalmente a VM visando aprimorar o desempenho ou reduzi-la verticalmente para reduzir os custos. 
- Escale-a também horizontalmente associando até quatro nós de VM a um só IR auto-hospedado.  Um só trabalho de cópia em execução em um IR auto-hospedado particionará automaticamente o conjunto de arquivos e utilizará todos os nós de VM para copiar os arquivos em paralelo.  Para alta disponibilidade, recomendamos começar com dois nós de VM a fim de evitar o ponto único de falha durante a migração de dados. 

### <a name="rate-limiting"></a>Limitação de taxa 

Como melhor prática, realize um POC de desempenho com um conjunto de dados de exemplo representativo, para determinar um tamanho de partição apropriado. 

Comece com uma só partição e uma só atividade de cópia com a configuração padrão de DIU.  Aumente gradualmente a configuração de DIU até atingir o limite de largura de banda da sua rede ou o limite de IOPS/largura de banda dos armazenamentos de dados ou se você tiver atingido o máximo de 256 DIU permitido em uma só atividade de cópia. 

Em seguida, aumente gradualmente o número de atividades de cópia simultâneas até atingir os limites do ambiente. 

Quando encontrar erros de limitação relatados pela atividade de cópia do ADF, reduza a configuração de simultaneidade ou de DIU no ADF ou considere a possibilidade de aumentar os limites de largura de banda/IOPS da rede e dos armazenamentos de dados.  

### <a name="estimating-price"></a>Como estimar o preço 

> [!NOTE]
> Este é um exemplo de preço hipotético.  O preço real depende da taxa de transferência real no ambiente.

Considere o seguinte pipeline construído para migrar dados do S3 para o Armazenamento de Blobs do Azure: 

![O diagrama mostra um pipeline para migrar dados, com um gatilho manual sendo transmitido para Pesquisa, para ForEach e para um subpipeline para cada partição que contém uma Cópia que está sendo transmitida para o Procedimento Armazenado. Fora do pipeline, o Procedimento Armazenado é transmitido para o DB SQL do Azure, que é transmitido para a Pesquisa e o AWS S3 transmite para a Cópia, que transmite para o Armazenamento de Blobs.](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Vamos supor o seguinte: 

- O volume de dados total é 2 PB 
- Como migrando dados via HTTPS usando a primeira arquitetura da solução 
- Os 2 PB são divididos em partições de 1 K e cada cópia move uma partição 
- Cada cópia é configurada com DIU = 256 e atinge uma taxa de transferência de 1 GBps 
- A simultaneidade de ForEach está definida como 2, e a taxa de transferência agregada é de 2 GBps 
- No total, são necessárias 292 horas para concluir a migração 

Este é o preço estimado com base nas suposições acima: 

![A captura de tela de uma tabela mostra um preço estimado.](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Referências adicionais 
- [Conector do Amazon Simple Storage Service](./connector-amazon-simple-storage-service.md)
- [Azure Blob Storage connector](./connector-azure-blob-storage.md) (Conector do Armazenamento de Blobs do Azure)
- [Conector do Azure Data Lake Store Gen2](./connector-azure-data-lake-storage.md)
- [Guia de ajuste de desempenho da atividade de cópia](./copy-activity-performance.md)
- [Como criar e configurar o runtime de integração auto-hospedada](./create-self-hosted-integration-runtime.md)
- [HA e escalabilidade do runtime de integração auto-hospedada](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Considerações sobre segurança da movimentação de dados](./data-movement-security-considerations.md)
- [Armazenar credenciais no Azure Key Vault](./store-credentials-in-key-vault.md)
- [Copiar arquivo de maneira incremental com base no nome do arquivo particionado por tempo](./tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)
- [Copiar arquivos novos e alterados com base na LastModifiedDate](./tutorial-incremental-copy-lastmodified-copy-data-tool.md)
- [Página de preços do ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Modelo

Veja a seguir o [modelo](solution-template-migration-s3-azure.md) que você pode usar para começar a migrar petabytes de dados que consistem em centenas de milhões de arquivos do Amazon S3 para o Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Próximas etapas

- [Copiar arquivos de vários contêineres com o Azure Data Factory](solution-template-copy-files-multiple-containers.md)