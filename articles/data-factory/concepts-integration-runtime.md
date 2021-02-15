---
title: runtime de integração
description: Saiba mais sobre o Integration Runtime no Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: 911674a80b531a50cfb429c5dc0ff41f1aaceb08
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389936"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration Runtime no Azure Data Factory 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O IR (Integration Runtime) é a infraestrutura de computação usada pelo Azure Data Factory para fornecer as seguintes funcionalidades de integração de dados entre diferentes ambientes de rede:

- **Fluxo de dados**: execute um [fluxo de dados](concepts-data-flow-overview.md) no ambiente de computação gerenciado do Azure.  
- **Movimentação de dados**: copiar dados entre armazenamentos de dados em rede pública e armazenamentos de dados na rede privada (rede virtual privada ou local). Ele fornece suporte para conectores internos, conversão de formato, mapeamento de coluna e transferência de dados de alto desempenho e escalonáveis.
- **Expedição de atividade**: despache e monitore atividades de transformação em execução em uma variedade de serviços de computação, como Azure Databricks, Azure HDInsight, Azure Machine Learning, banco de dados SQL do azure, SQL Server e muito mais.
- **Execução de pacote SSIS**: execute nativamente pacotes do SSIS (SQL Server Integration Services) em um ambiente de computação gerenciada do Azure.

No Data Factory, uma atividade define a ação a ser realizada. Um serviço vinculado define um armazenamento de dados de destino ou um serviço de computação. Um Integration Runtime fornece a ponte entre a atividade e os serviços vinculados.  Ele é referenciado pelo serviço vinculado ou pela atividade e fornece o ambiente de computação no qual a atividade é executada ou expedida do. Desse modo, a atividade pode ser executada na região mais próxima possível do serviço de computação ou armazenamento de dados de destino, da maneira que proporciona o mais alto desempenho e atendendo às necessidades de segurança e de conformidade.

Os tempos de execução de integração podem ser criados no Azure Data Factory UX por meio do [Hub de gerenciamento](author-management-hub.md) e de quaisquer atividades, conjuntos de dados ou fluxos de dado que façam referência a eles.

## <a name="integration-runtime-types"></a>Tipos de Integration Runtime

O Data Factory oferece três tipos de Integration Runtime (IR) e você deve escolher o tipo que melhor atenda aos recursos de integração de dados e às necessidades de ambiente de rede que você está procurando.  Esses três tipos são:

- Azure
- Auto-hospedado
- Azure-SSIS

A tabela a seguir descreve as funcionalidades e o suporte de rede para cada um dos tipos de Integration Runtime:

Tipo de IR | Rede pública | Rede privada
------- | -------------- | ---------------
Azure | Fluxo de Dados<br/>Movimentação de dados<br/>Expedição de atividade | Fluxo de Dados<br/>Movimentação de dados<br/>Expedição de atividade
Auto-hospedado | Movimentação de dados<br/>Expedição de atividade | Movimentação de dados<br/>Expedição de atividade
Azure-SSIS | Execução do pacote SSIS | Execução do pacote SSIS


## <a name="azure-integration-runtime"></a>Azure Integration runtime

Um tempo de execução de integração do Azure pode:

- Executar fluxos de dados no Azure 
- Executar atividade de cópia entre armazenamentos de dados de nuvem
- Despache as seguintes atividades de transformação na rede pública: atividade do databricks Notebook/jar/Python, atividade de hive do hdinsight, atividade do HDInsight Pig, atividade de MapReduce do HDInsight, atividade do HDInsight Spark, atividade de streaming do HDInsight, atividade de execução em lote do Azure Machine Learning Studio (clássica), atividade de atualização de Azure Machine Learning Studio (clássica), atividade de pesquisa data Lake Analytics e obtenção de metadados.

### <a name="azure-ir-network-environment"></a>Ambiente de rede do IR do Azure

O Azure Integration Runtime dá suporte à conexão com armazenamentos de dados e computa serviços com pontos de extremidade acessíveis públicos. Habilitando a rede virtual gerenciada, o Azure Integration Runtime dá suporte à conexão a armazenamentos de dados usando o serviço de vínculo privado no ambiente de rede privada.

### <a name="azure-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure
O Integration Runtime do Azure fornece uma computação totalmente gerenciada e sem servidor no Azure.  Você não precisa se preocupar com o provisionamento de infraestrutura, instalação de software, aplicação de patch ou dimensionamento de capacidade.  Além disso, você só paga pela duração da utilização real.

O Integration Runtime do Azure fornece a computação nativa de para mover dados entre armazenamentos de dados de nuvem de maneira segura, confiável e de alto desempenho.  Você pode definir quantas unidades de integração de dados usar na atividade de cópia e o tamanho da computação do IR do Azure é expandido elasticamente de modo adequado sem que você precise ajustar explicitamente o tamanho do Integration Runtime do Azure. 

A expedição de atividade é uma operação leve para rotear a atividade para o serviço de computação de destino, portanto, não é necessário escalar verticalmente o tamanho da computação para esse cenário.

Para obter informações sobre como criar e configurar um Azure IR, consulte [How to Create and configure Azure Integration Runtime](create-azure-integration-runtime.md). 

> [!NOTE] 
> O tempo de execução de integração do Azure tem propriedades relacionadas ao tempo de execução de fluxo de dados, que definem a infraestrutura de computação subjacente que seria usada para executar os fluxos de dados no. 

## <a name="self-hosted-integration-runtime"></a>runtime de integração auto-hospedada

Um IR auto-hospedado é capaz de:

- Executar a atividade de cópia entre um armazenamento de dados de nuvem e um armazenamento de dados na rede privada.
- Expedindo as seguintes atividades de transformação em relação aos recursos de computação no local ou na rede virtual do Azure: atividade do hive do HDInsight (BYOC-traga seu próprio cluster), BYOC (Pig de atividades do HDInsight), atividade do HDInsight MapReduce (BYOC), atividade do HDInsight Spark (BYOC), atividade de streaming do HDInsight (BYOC), atividade de execução em lote do Azure Machine Learning Studio (clássico), atividades de recurso de atualização de Azure Machine Learning Studio (clássico), atividade de procedimento armazenado Data Lake Analytics atividade U-SQL , Atividade personalizada (executado no lote do Azure), atividade de pesquisa e atividade obter metadados.

> [!NOTE] 
> Use o tempo de execução de integração auto-hospedado para dar suporte a armazenamentos de dados que exigem o seu próprio Driver, como SAP Hana, MySQL etc.  Para obter mais informações, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> O Java Runtime Environment (JRE) é uma dependência do IR hospedado internamente. Certifique-se de que o JRE está instalado no mesmo host.

### <a name="self-hosted-ir-network-environment"></a>Ambiente de rede do IR auto-hospedado

Se você quiser executar a integração de dados com segurança em um ambiente de rede privada, que não tem uma linha de visão direta do ambiente de nuvem pública, você pode instalar um ambiente local de IR por hospedagem própria por trás do seu firewall corporativo ou dentro de uma rede virtual privada.  O Integration Runtime auto-hospedado só faz conexões de saída com base em HTTP com a Internet aberta.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR auto-hospedado

Instale o IR auto-hospedado em um computador local ou em uma máquina virtual dentro de uma rede privada. Atualmente, só há suporte para a execução do IR auto-hospedado em um sistema operacional Windows.  

Para alta disponibilidade e escalabilidade, você pode expandir o IR auto-hospedado associando a instância lógica a vários computadores locais no modo ativo-ativo.  Para obter mais informações, consulte o artigo como [criar e configurar o ir auto-hospedado](create-self-hosted-integration-runtime.md) em como guias para obter detalhes.

## <a name="azure-ssis-integration-runtime"></a>Integration Runtime do Azure-SSIS

Para fazer lift-and-shift da carga de trabalho existente do SSIS, você pode criar um IR Azure-SSIS para executar pacotes do SSIS nativamente.

### <a name="azure-ssis-ir-network-environment"></a>Ambiente de rede do IR do Azure-SSIS

O IR Azure-SSIS pode ser provisionado na rede pública ou na rede privada.  Há suporte para o acesso a dados locais unindo o IR do Azure-SSIS a uma Rede Virtual conectada à sua rede local.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure-SSIS

O IR do SSIS do Azure é um cluster totalmente gerenciado das VMs do Azure dedicado para executar os pacotes de SSIS. Você pode colocar seu próprio banco de dados SQL do Azure ou SQL Instância Gerenciada para o catálogo de projetos/pacotes SSIS (SSISDB). Você pode aumentar a potência de computação especificando o tamanho do nó e escalá-la horizontalmente especificando o número de nós no cluster. Você pode gerenciar o custo da execução do Integration Runtime do Azure SSIS, parando-o e iniciando-o como considerar adequado.

Para saber mais, veja o artigo sobre como criar e configurar IR do Azure-SSIS, encontrado nos guias de instruções.  Depois de criado, você pode implantar e gerenciar seus pacotes SSIS existentes com pouca ou nenhuma alteração usando ferramentas familiares, como o SSDT (SQL Server Data Tools) e o SSMS (SQL Server Management Studio), assim como usando o SSIS localmente.

Confira estes artigos para obter mais informações sobre o runtime do Azure-SSIS: 

- [Tutorial: implantar pacotes do SSIS para o Azure](./tutorial-deploy-ssis-packages-azure.md). Este artigo fornece instruções passo a passo para criar um Azure-SSIS IR e usa um banco de dados SQL do Azure para hospedar o catálogo do SSIS. 
- [Como: Criar um runtime de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar o SQL Instância Gerenciada e ingressar o IR em uma rede virtual. 
- [Monitore um IR do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como recuperar informações sobre um IR do Azure-SSIS e descrições de status nas informações retornadas. 
- [Gerencie um IR do Azure-SSIS](manage-azure-ssis-integration-runtime.md). Este artigo mostra como parar, iniciar ou remover um IR do Azure-SSIS. Ele também mostra como expandir o IR do Azure-SSIS adicionando mais nós ao IR. 
- [Unir um IR do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo fornece informações conceituais sobre como unir um IR do Azure-SSIS a uma rede virtual do Azure. Ele também apresenta as etapas para usar o portal do Azure para configurar a rede virtual para que o IR do Azure-SSIS possa unir-se à rede virtual. 

## <a name="integration-runtime-location"></a>Localização do Integration Runtime

### <a name="relationship-between-factory-location-and-ir-location"></a>Relação entre o local de fábrica e o local de IR

Quando o cliente cria uma instância de data factory, ele precisa especificar o local para o data factory. É na localização do Data Factory que os metadados do data factory são armazenados e é dela que o disparo do pipeline é iniciado. Os metadados para a fábrica são armazenados somente na região da escolha do cliente e não serão armazenados em outras regiões.

No entanto, um data factory pode acessar armazenamentos de dados e serviços de computação em outras regiões do Azure para mover dados entre armazenamentos de dados ou processar dados usando os serviços de computação. Esse comportamento é realizado por meio do [IR disponível globalmente](https://azure.microsoft.com/global-infrastructure/services/) para garantir a conformidade de dados, a eficiência e os custos de saída de rede reduzidos.

A localização do IR define a localização da respectiva computação de back-end e, essencialmente, a localização em que a movimentação de dados, a expedição de atividades e a execução de pacotes SSIS são executadas. A localização de IR pode ser diferente da localização do data factory ao qual ele pertence. 

### <a name="azure-ir-location"></a>Localização do IR do Azure

Você pode definir um determinado local de um Azure IR, caso em que a execução da atividade ou expedição ocorrerá nessa região específica.

Se você optar por usar a resolução automática Azure IR na rede pública, que é o padrão,

- Para a atividade de cópia, o ADF fará um melhor esforço para detectar automaticamente o local do repositório de dados do coletor e, em seguida, usará o IR na mesma região, se disponível ou mais próximo na mesma Geografia; se a região do repositório de dados do coletor não for detectável, o IR na região de data factory como alternativa será usado.

  Por exemplo, você tem sua fábrica criada no leste dos EUA, 
  
  - Ao copiar dados para o blob do Azure no oeste dos EUA, se o ADF detectar com êxito que o blob está no oeste dos EUA, a atividade de cópia será executada no IR no oeste dos EUA; se a detecção de região falhar, a atividade de cópia será executada no IR no leste dos EUA.
  - Quando os dados são copiados para o Salesforce do qual a região não é detectável, a atividade de cópia é executada no IR no leste dos EUA.

  >[!TIP] 
  >Se você tiver requisitos de conformidade de dados restritos e precisar garantir que os dados não saiam de uma determinada geografia, você pode criar explicitamente um IR do Azure em uma determinada região e apontar o Serviço Vinculado a esse IR usando a propriedade ConnectVia. Por exemplo, se você quiser copiar dados do blob em Sul do Reino Unido para o Azure Synapse Analytics no Sul do Reino Unido e desejar garantir que os dados não saiam do Reino Unido, crie um Azure IR no Sul do Reino Unido e vincule os dois serviços vinculados a esse IR.

- Para pesquisa/GetMetadata/excluir execução de atividade (também conhecida como atividades de pipeline), expedição de atividade de transformação (também conhecida como atividades externas) e operações de criação (testar conexão, procurar lista de pastas e lista de tabelas, Visualizar dados), ADF usa o IR na região de data factory.

- Para o fluxo de dados, o ADF usa o IR na região de data factory. 

  > [!TIP] 
  > Uma prática recomendada seria garantir que o fluxo de dados seja executado na mesma região que os armazenamentos de dados correspondentes (se possível). Você pode fazer isso por meio da resolução automática de Azure IR (se o local do repositório de dados for o mesmo Data Factory local) ou criando uma nova instância Azure IR na mesma região que os armazenamentos de dados e, em seguida, executar o fluxo de dados nele. 

Se você habilitar a rede virtual gerenciada para resolução automática Azure IR, o ADF usará o IR na região data factory. 

Você pode monitorar quais locais do IR entram em vigor durante a execução da atividade na exibição do monitoramento de atividades de pipeline na interface do usuário ou no conteúdo de monitoramento de atividades.

### <a name="self-hosted-ir-location"></a>Local do IR auto-hospedado

O IR auto-hospedado está logicamente registrado para o Data Factory e a computação usada para dar suporte às funcionalidades dele é fornecido por você. Portanto, não há nenhuma propriedade de localização explícita para IR auto-hospedado. 

Quando usado para realizar a movimentação de dados, o IR auto-hospedado extrai dados da origem e grava-os no destino.

### <a name="azure-ssis-ir-location"></a>Local do Azure-SSIS IR

Selecionar a localização certa para o IR do Azure-SSIS é essencial para alcançar alto desempenho em seus fluxos de trabalho de ETL (extrair, transformar e carregar).

- O local do seu Azure-SSIS IR não precisa ser o mesmo que o local do seu data factory, mas deve ser o mesmo que o local do seu próprio banco de dados SQL do Azure ou SQL Instância Gerenciada em que o SSISDB. Desse modo, o Integration Runtime do Azure-SSIS pode acessar o SSISDB facilmente sem incorrer em tráfegos excessivos entre localizações diferentes.
- Se você não tiver um banco de dados SQL existente ou o SQL Instância Gerenciada, mas tiver fontes/destinos locais, você deverá criar um novo Azure SQL Database ou SQL Instância Gerenciada no mesmo local de uma rede virtual conectada à sua rede local.  Dessa forma, você pode criar seu Azure-SSIS IR usando o novo banco de dados SQL do Azure ou o SQL Instância Gerenciada e unindo essa rede virtual, tudo no mesmo local, minimizando efetivamente os movimentos de dados em diferentes locais.
- Se o local do banco de dados SQL do Azure ou do SQL Instância Gerenciada existente não for o mesmo que o local de uma rede virtual conectada à sua rede local, primeiro crie seu Azure-SSIS IR usando um banco de dados SQL do Azure ou Instância Gerenciada SQL existente e unindo outra rede virtual no mesmo local e, em seguida, configure uma rede virtual para a conexão de rede virtual entre locais diferentes.

O diagrama a seguir mostra as configurações de localização de Data Factory e os respectivos tempos de execução de integração:

![Localização do Integration Runtime](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Determinando qual IR usar

### <a name="copy-activity"></a>Atividade de cópia

Para a atividade de cópia, ela requer que serviços vinculados de origem e de coletor definam a direção do fluxo de dados. A lógica a seguir é usada para determinar qual instância do Integration Runtime é usada para realizar a cópia: 

- **Copiando entre duas fontes de dados de nuvem**: quando os serviços vinculados de origem e de coletor estiverem usando Azure ir, o ADF usará o Azure ir regional se você tiver especificado ou determinará automaticamente um local de Azure ir se escolher o ir de resolução automática (padrão), conforme descrito na seção [local do Integration Runtime](#integration-runtime-location) .
- **Copiando entre uma fonte de dados de nuvem e uma fonte de dados na rede privada**: se o serviço vinculado de origem ou de coletor aponta para um IR auto-hospedado, a atividade de cópia é executada nesse Integration Runtime auto-hospedado.
- **Copiando entre duas fontes de dados na rede privada**: o serviço vinculado de origem e de coletor deve apontar para a mesma instância do Integration Runtime e esse tempo de execução de integração é usado para executar a atividade de cópia.

### <a name="lookup-and-getmetadata-activity"></a>Atividade de pesquisa e GetMetadata

A atividade de pesquisa e GetMetadata é executada no runtime de integração associado ao serviço vinculado de armazenamento de dados.

### <a name="external-transformation-activity"></a>Atividade de transformação externa

Cada atividade de transformação externa que utiliza um mecanismo de computação externo tem um serviço vinculado de computação de destino, que aponta para um tempo de execução de integração. Essa instância do Integration Runtime determina o local do qual a atividade de transformação codificada externa é expedida.

### <a name="data-flow-activity"></a>Atividade de Fluxo de Dados

As atividades de fluxo de dados são executadas no tempo de execução de integração do Azure associado a ela. A computação do Spark utilizada por fluxos de dados é determinada pelas propriedades de fluxo de dados em seu Azure Integration Runtime e são totalmente gerenciadas pelo ADF.

## <a name="next-steps"></a>Próximas etapas

Veja os artigos a seguir:

- [Criar tempo de execução de integração do Azure](create-azure-integration-runtime.md)
- [Criar um Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md)
- [Crie um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar o SQL Instância Gerenciada e ingressar o IR em uma rede virtual.