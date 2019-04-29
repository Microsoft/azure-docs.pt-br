---
title: Integration Runtime no Azure Data Factory | Microsoft Docs
description: Saiba mais sobre o Integration Runtime no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: abnarain
ms.openlocfilehash: d63ede800f7e60db44072234f5ec74910e4c70f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261874"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration Runtime no Azure Data Factory
O IR (tempo de execução de integração) é a infraestrutura de computação usada pelo Azure Data Factory para fornecer as seguintes funcionalidades de integração de dados entre diferentes ambientes de rede:

- **Movimentação de dados**: Copie os dados entre os armazenamentos de dados na rede pública e armazenamento de dados na rede privada (rede local ou privada virtual). Ele fornece suporte para conectores internos, conversão de formato, mapeamento de coluna e transferência de dados de alto desempenho e escalonáveis.
- **Expedição de atividade**:  Expeça e monitore atividades de transformação executadas em uma variedade de serviços de computação, como o Azure HDInsight, o Azure Machine Learning, o Banco de Dados SQL do Azure, o SQL Server e muitos mais.
- **Execução de pacote SSIS**: Execute nativamente pacotes do SSIS (SQL Server Integration Services) em um ambiente de computação gerenciada do Azure.

No Data Factory, uma atividade define a ação a ser realizada. Um serviço vinculado define um armazenamento de dados de destino ou um serviço de computação. Um Integration Runtime fornece a ponte entre a atividade e os serviços vinculados.  Ele é referenciado pelo serviço vinculado e fornece o ambiente de computação em que a atividade é executada ou da qual é expedida.  Desse modo, a atividade pode ser executada na região mais próxima possível do serviço de computação ou armazenamento de dados de destino, da maneira que proporciona o mais alto desempenho e atendendo às necessidades de segurança e de conformidade.

## <a name="integration-runtime-types"></a>Tipos de Integration Runtime
O Data Factory oferece três tipos de Integration Runtime e você deve escolher o tipo que melhor atende as funcionalidades de integração de dados e as necessidades de ambiente de rede pelas quais você está procurando.  Esses três tipos são:

- Azure
- Auto-hospedado
- Azure-SSIS

A tabela a seguir descreve as funcionalidades e o suporte de rede para cada um dos tipos de Integration Runtime:

Tipo de IR | Rede pública | Rede privada
------- | -------------- | ---------------
Azure | Movimentação de dados<br/>Expedição de atividade | &nbsp;
Auto-hospedado | Movimentação de dados<br/>Expedição de atividade | Movimentação de dados<br/>Expedição de atividade
Azure-SSIS | Execução de pacote SSIS | Execução de pacote SSIS

O diagrama a seguir mostra como os diferentes tempos de execução de integração podem ser usados em combinação para oferecer funcionalidades de integração de dados e suporte de rede avançados:

![Tipos diferentes de tempos de execução de integração](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Integration Runtime do Azure
Um Integration Runtime do Azure é capaz de:

- Executar a atividade de cópia entre armazenamentos de dados de nuvem
- Distribua as seguintes atividades de transformação na rede pública: Atividade de Hive do HDInsight, atividade de Pig do HDInsight, atividade de MapReduce do HDInsight, atividade do Spark do HDInsight, atividade de Streaming do HDInsight, atividade de execução de lote do Machine Learning, atividades de recurso de atualização do Machine Learning, atividade de procedimento armazenado Atividade do data Lake Analytics U-SQL, atividade personalizada do .NET, atividade da Web, atividade de pesquisa e obter metadados atividade.

### <a name="azure-ir-network-environment"></a>Ambiente de rede do IR do Azure
O Integration Runtime do Azure dá suporte à conexão a armazenamentos de dados e serviços de computação em rede pública com pontos de extremidade publicamente acessíveis. Use um Integration Runtime auto-hospedado para o ambiente de Rede Virtual do Azure.

### <a name="azure-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure
O Integration Runtime do Azure fornece uma computação totalmente gerenciada e sem servidor no Azure.  Você não precisa se preocupar sobre provisionamento de infraestrutura, instalação de software, aplicação de patch ou dimensionamento de capacidade.  Além disso, você só paga pela duração da utilização real.

O Integration Runtime do Azure fornece a computação nativa de para mover dados entre armazenamentos de dados de nuvem de maneira segura, confiável e de alto desempenho.  Você pode definir quantas unidades de integração de dados usar na atividade de cópia e o tamanho da computação do IR do Azure é expandido elasticamente de modo adequado sem que você precise ajustar explicitamente o tamanho do Integration Runtime do Azure.

A expedição de atividade é uma operação simples para rotear a atividade para o serviço de computação de destino, de modo que não há necessidade de expandir o tamanho da computação para esse cenário.

Para obter informações sobre como criar e configurar um IR do Azure, consulte Como criar e configurar o IR do Azure nos guias de instruções. 

## <a name="self-hosted-integration-runtime"></a>Integration Runtime auto-hospedado
Um IR auto-hospedado é capaz de:

- Executar a atividade de cópia entre um armazenamento de dados de nuvem e um armazenamento de dados na rede privada.
- Distribua as atividades de transformação a seguir contra os recursos do computador no local ou Rede Virtual do Microsoft Azure: Atividade de Hive do HDInsight (BYOC – traga seu próprio Cluster), a atividade de Pig de HDInsight (BYOC), a atividade de MapReduce do HDInsight (BYOC), atividade do HDInsight Spark (BYOC), HDInsight atividade de Streaming (BYOC), atividade de execução de lote do Machine Learning, Machine Learning Atividades de recurso, atividade de procedimento armazenado, atividade U-SQL do Data Lake Analytics, atividade personalizada do .NET, atividade de pesquisa e atividade de obter metadados de atualização.

> [!NOTE] 
> Use o Integration Runtime auto-hospedado para dar suporte a armazenamentos de dados que requer traga seu próprio driver como SAP Hana, MySQL, etc.  Para obter mais informações, consulte [fontes de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

### <a name="self-hosted-ir-network-environment"></a>Ambiente de rede do IR auto-hospedado
Se você deseja realizar a integração de dados com segurança em um ambiente de rede privada, que não tem uma linha de visão direta do ambiente de nuvem pública, você pode instalar um IR auto-hospedado no ambiente local por trás de seu firewall corporativo ou então em uma rede virtual privada.  O Integration Runtime auto-hospedado só faz conexões de saída com base em HTTP com a Internet aberta.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR auto-hospedado
O IR auto-hospedado precisa ser instalado em um computador local ou em uma máquina virtual dentro de uma rede privada. Atualmente, só há suporte para a execução do IR auto-hospedado em um sistema operacional Windows.  

Para alta disponibilidade e escalabilidade, você pode expandir o IR auto-hospedado associando a instância lógica a vários computadores locais no modo ativo-ativo.  Para obter mais informações e detalhes, veja o artigo correspondente nos guias de instruções, Como criar e configurar IR auto-hospedado.

## <a name="azure-ssis-integration-runtime"></a>Integration Runtime do Azure-SSIS
Para fazer lift-and-shift da carga de trabalho existente do SSIS, você pode criar um IR Azure-SSIS para executar pacotes do SSIS nativamente.

### <a name="azure-ssis-ir-network-environment"></a>Ambiente de rede do IR do Azure-SSIS
O IR Azure-SSIS pode ser provisionado na rede pública ou na rede privada.  Há suporte para o acesso a dados locais unindo o IR do Azure-SSIS a uma Rede Virtual conectada à sua rede local.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure-SSIS
O IR do SSIS do Azure é um cluster totalmente gerenciado das VMs do Azure dedicado para executar os pacotes de SSIS. É possível colocar seu próprio servidor do Banco de Dados SQL do Azure ou Instância Gerenciada para hospedar o catálogo de projetos/pacotes do SSIS (SSISDB) que será anexado a ele. Você pode aumentar a potência de computação especificando o tamanho do nó e escalá-la horizontalmente especificando o número de nós no cluster. Você pode gerenciar o custo da execução do Integration Runtime do Azure SSIS, parando-o e iniciando-o como considerar adequado.

Para saber mais, veja o artigo sobre como criar e configurar IR do Azure-SSIS, encontrado nos guias de instruções.  Depois de criado, você pode implantar e gerenciar seus pacotes SSIS existentes com pouca ou nenhuma alteração usando ferramentas familiares, como o SSDT (SQL Server Data Tools) e o SSMS (SQL Server Management Studio), assim como usando o SSIS localmente.

Confira estes artigos para obter mais informações sobre o tempo de execução do Azure-SSIS: 

- [Tutorial: implantar pacotes do SSIS para o Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo fornece instruções passo a passo para criar um IR do Azure-SSIS e usa um banco de dados SQL do Azure para hospedar o catálogo do SSIS. 
- [Como: Criar um Integration Runtime do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar a Instância Gerenciada do Banco de Dados SQL do Azure e unir o IR a uma rede virtual. 
- [Monitore um IR do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como recuperar informações sobre um IR do Azure-SSIS e descrições de status nas informações retornadas. 
- [Gerencie um IR do Azure-SSIS](manage-azure-ssis-integration-runtime.md). Este artigo mostra como parar, iniciar ou remover um IR do Azure-SSIS. Ele também mostra como expandir o IR do Azure-SSIS adicionando mais nós ao IR. 
- [Unir um IR do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo fornece informações conceituais sobre como unir um IR do Azure-SSIS a uma rede virtual do Azure. Ele também apresenta as etapas para usar o portal do Azure para configurar a rede virtual para que o IR do Azure-SSIS possa unir-se à rede virtual. 

## <a name="integration-runtime-location"></a>Localização do Integration Runtime
É na localização do Data Factory que os metadados do data factory são armazenados e é dela que o disparo do pipeline é iniciado. No entanto, um data factory pode acessar armazenamentos de dados e serviços de computação em outras regiões do Azure para mover dados entre armazenamentos de dados ou processar dados usando os serviços de computação. Esse comportamento é realizado por meio do [IR disponível globalmente](https://azure.microsoft.com/global-infrastructure/services/) para garantir a conformidade de dados, a eficiência e os custos de saída de rede reduzidos.

A localização do IR define a localização da respectiva computação de back-end e, essencialmente, a localização em que a movimentação de dados, a expedição de atividades e a execução de pacotes SSIS são executadas. A localização de IR pode ser diferente da localização do data factory ao qual ele pertence. 

### <a name="azure-ir-location"></a>Localização do IR do Azure
Você pode definir um determinado local de um IR do Azure, onde a movimentação de dados ou a expedição de atividade ocorrerá nessa região específica. 

Se você optar por usar a IR do Azure de resolução automática, que é o padrão, 

- Para a atividade de cópia, o ADF fará um melhor esforço para detectar automaticamente o armazenamento de dados do coletor e da fonte para escolher o melhor local na mesma região, se disponível, ou o mais próximo na mesma geografia. Ou, se não for detectável, usar a região do data factory como alternativa.
- Para distribuição de atividade de transformação e execução de atividade de Pesquisa/GetMetadata, o ADF usará o IR na região do data factory.

Você pode monitorar quais locais do IR entram em vigor durante a execução da atividade na exibição do monitoramento de atividades de pipeline na interface do usuário ou no conteúdo de monitoramento de atividades.

>[!TIP]
>Se você tiver requisitos de conformidade de dados restritos e precisar garantir que os dados não saiam de uma determinada geografia, você pode criar explicitamente um IR do Azure em uma determinada região e apontar o Serviço Vinculado a esse IR usando a propriedade ConnectVia. Por exemplo, se você quiser copiar dados de Blob no Sul do Reino Unido para SQL DW no Sul do Reino Unido e quiser garantir que os dados não saiam do Reino Unido, crie um IR do Azure no Sul do Reino Unido e vincule os dois Serviços Vinculados a este IR.

### <a name="self-hosted-ir-location"></a>Local do IR auto-hospedado
O IR auto-hospedado está logicamente registrado para o Data Factory e a computação usada para dar suporte às funcionalidades dele é fornecido por você. Portanto, não há nenhuma propriedade de localização explícita para IR auto-hospedado. 

Quando usado para realizar a movimentação de dados, o IR auto-hospedado extrai dados da origem e grava-os no destino.

### <a name="azure-ssis-ir-location"></a>Local do Azure-SSIS IR
Selecionar a localização certa para o IR do Azure-SSIS é essencial para alcançar alto desempenho em seus fluxos de trabalho de ETL (extrair, transformar e carregar).

- A localização do seu Azure-SSIS IR não precisa ser a mesma do Data Factory, mas deve ser a mesma do seu próprio servidor de Banco de Dados SQL do Azure/Instância Gerenciada em que o SSISDB deve ser hospedado. Desse modo, o Integration Runtime do Azure-SSIS pode acessar o SSISDB facilmente sem incorrer em tráfegos excessivos entre localizações diferentes.
- Se você não tiver um servidor de Banco de Dados SQL do Azure/Instância Gerenciada existente para hospedar o SSISDB, mas tem fontes/destinos de dados locais, deverá criar um novo servidor de Banco de Dados SQL do Azure/Instância Gerenciada na mesma localização de uma rede virtual conectada à rede local.  Desse modo, é possível criar o Azure-SSIS IR usando o novo servidor de Banco de Dados SQL do Azure/Instância Gerenciada e ingressando nessa rede virtual, tudo no mesmo local, minimizando efetivamente as movimentações de dados entre diferentes localizações.
- Se a localização do seu servidor de Banco de Dados SQL do Azure/Instância Gerenciada existente em que o SSISDB está hospedado não for a mesma que a localização de uma rede virtual conectada à rede local, primeiro crie o Azure-SSIS IR usando um servidor de Banco de Dados SQL do Azure/Instância Gerenciada e ingressando em outra rede virtual na mesma localização e, em seguida, configure uma conexão de rede virtual a rede virtual entre localizações diferentes.

O diagrama a seguir mostra as configurações de localização de Data Factory e os respectivos tempos de execução de integração:

![Localização do Integration Runtime](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Determinando qual IR usar

### <a name="copy-activity"></a>Atividade de cópia

Para a atividade de cópia, ela requer que serviços vinculados de origem e de coletor definam a direção do fluxo de dados. A lógica a seguir é usada para determinar qual instância do Integration Runtime é usada para realizar a cópia: 

- **Copiando entre duas fontes de dados de nuvem**: quando os serviços vinculados de origem e o de coletor estiverem usando o IR do Azure, o ADF usará o IR do Azure regional, se especificado, ou determinará automaticamente um local do IR do Azure se você escolher a resolução automática do IR (padrão), conforme descrito na seção [Local de tempo de execução de integração](#integration-runtime-location).
- **Copiando entre uma fonte de dados de nuvem e uma fonte de dados na rede privada**: se o serviço vinculado de origem ou de coletor aponta para um IR auto-hospedado, a atividade de cópia é executada nesse Integration Runtime auto-hospedado.
- **Copiando entre duas fontes de dados na rede privada**: o serviço vinculado de origem e o de coletor devem apontar para a mesma instância de tempo de execução de integração, e esse tempo de execução de integração é usado para executar a Atividade de cópia.

### <a name="lookup-and-getmetadata-activity"></a>Atividade de pesquisa e GetMetadata

A atividade de pesquisa e GetMetadata é executada no tempo de execução de integração associado ao serviço vinculado de armazenamento de dados.

### <a name="transformation-activity"></a>Atividade de transformação

Cada atividade de transformação tem um serviço vinculado de computação de destino, que aponta para um Integration Runtime. É dessa instância do Integration Runtime que a atividade de transformação é expedida.

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

- [Criar um Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md)
- [Criar um Integration Runtime do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar a Instância Gerenciada do Banco de Dados SQL do Azure e unir o IR a uma rede virtual. 
