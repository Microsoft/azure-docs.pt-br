---
title: Integration Runtime no Azure Data Factory | Microsoft Docs
description: Saiba mais sobre o Integration Runtime no Azure Data Factory.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/15/2017
ms.author: shlo
ms.openlocfilehash: 82b1dc036b53fbc1f0159d77ce102dcd054ea722
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration Runtime no Azure Data Factory
O IR (Integration Runtime) é a infraestrutura de computação usada pelo Azure Data Factory para fornecer as seguintes funcionalidades de integração de dados entre diferentes ambientes de rede:

- **Movimentação de dados**: mover dados entre armazenamentos de dados em rede pública e armazenamentos de dados em rede privada (rede privada local ou virtual). Ele fornece suporte para conectores internos, conversão de formato, mapeamento de coluna e transferência de dados de alto desempenho e escalonáveis.
- **Expedição de atividade**: expeça e monitore atividades de transformação executadas em uma variedade de serviços de computação, como o Azure HDInsight, o Azure Machine Learning, o Banco de Dados SQL do Azure, o SQL Server e muitos mais.
- **Execução de pacote SSIS**: execute nativamente pacotes do SSIS (SQL Server Integration Services) em um ambiente de computação gerenciada do Azure.


> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte a [Documentação do Data Factory versão 1](v1/data-factory-introduction.md).

No Data Factory, uma atividade define a ação a ser realizada. Um serviço vinculado define um armazenamento de dados de destino ou um serviço de computação. Um Integration Runtime fornece a ponte entre a atividade e os serviços vinculados.  Ele é referenciado pelo serviço vinculado e fornece o ambiente de computação em que a atividade é executada ou da qual é expedida.  Desse modo, a atividade pode ser executada na região mais próxima possível do serviço de computação ou armazenamento de dados de destino, da maneira que proporciona o mais alto desempenho e atendendo às necessidades de segurança e de conformidade.

## <a name="integration-runtime-types"></a>Tipos de Integration Runtime
O Data Factory oferece três tipos de Integration Runtime e você deve escolher o tipo que melhor atende as funcionalidades de integração de dados e as necessidades de ambiente de rede pelas quais você está procurando.  Esses três tipos são:

- As tabelas
- Auto-hospedado
- Azure-SSIS

A tabela a seguir descreve as funcionalidades e o suporte de rede para cada um dos tipos de Integration Runtime:

Tipo de IR | Rede pública | Rede privada
------- | -------------- | ---------------
As tabelas | Movimentação de dados<br/>Expedição de atividade | &nbsp;
Auto-hospedado | Movimentação de dados<br/>Expedição de atividade | Movimentação de dados<br/>Expedição de atividade
Azure-SSIS | Execução de pacote SSIS | Execução de pacote SSIS

O diagrama a seguir mostra como os diferentes tempos de execução de integração podem ser usados em combinação para oferecer funcionalidades de integração de dados e suporte de rede avançados:

![Tipos diferentes de tempos de execução de integração](media\concepts-integration-runtime\different-integration-runtimes.png)


## <a name="azure-integration-runtime"></a>Integration Runtime do Azure
Um Integration Runtime do Azure é capaz de:

- Executar a atividade de cópia entre armazenamentos de dados de nuvem
- Expedindo as seguintes atividades de transformação na rede pública: atividade de Hive do HDInsight, atividade de Pig do HDInsight, atividade de MapReduce do HDInsight, atividade de Spark do HDInsight, atividade de Streaming do HDInsight, atividade de execução em lote do Machine Learning, atividades de recurso de atualização do Machine Learning, atividade de procedimento armazenado, atividade U-SQL do Data Lake Analytics, atividade personalizada do .Net, atividade da Web, atividade de pesquisa e atividade de obtenção de metadados.

### <a name="network-environment"></a>Ambiente de rede
O Integration Runtime do Azure dá suporte à conexão a armazenamentos de dados e serviços de computação em rede pública com pontos de extremidade publicamente acessíveis. Use um Integration Runtime auto-hospedado para o ambiente de Rede Virtual do Azure.

### <a name="compute-resource-and-scaling"></a>Recurso de computação e dimensionamento
O Integration Runtime do Azure fornece uma computação totalmente gerenciada e sem servidor no Azure.  Você não precisa se preocupar sobre provisionamento de infraestrutura, instalação de software, aplicação de patch ou dimensionamento de capacidade.  Além disso, você só paga pela duração da utilização real.

O Integration Runtime do Azure fornece a computação nativa de para mover dados entre armazenamentos de dados de nuvem de maneira segura, confiável e de alto desempenho.  Você pode definir quantas unidades de movimentação de dados usar na atividade de cópia e o tamanho da computação do IR do Azure é expandido elasticamente de modo adequado sem que você precise ajustar explicitamente o tamanho do Integration Runtime do Azure.

A expedição de atividade é uma operação simples para rotear a atividade para o serviço de computação de destino, de modo que não há necessidade de expandir o tamanho da computação para esse cenário.

Para obter informações sobre como criar e configurar um IR do Azure, consulte Como criar e configurar o IR do Azure nos guias de instruções. 

## <a name="self-hosted-integration-runtime"></a>Integration Runtime auto-hospedado
Um IR auto-hospedado é capaz de:

- Executar a atividade de cópia entre um armazenamento de dados de nuvem e um armazenamento de dados na rede privada.
- Expedindo as seguintes atividades de transformação para recursos de computação na rede virtual local ou do Azure: atividade de Hive do HDInsight (BYOC), atividade de Pig do HDInsight (BYOC), atividade de MapReduce do HDInsight (BYOC), atividade de Spark do HDInsight (BYOC), atividade de Streaming do HDInsight (BYOC), atividade de execução em lote do Machine Learning, atividades de recurso de atualização do Machine Learning, atividade de procedimento armazenado, atividade U-SQL do Data Lake Analytics, atividade personalizada do .Net, atividade de pesquisa e atividade de obtenção de metadados.

> [!NOTE] 
> Use o Integration Runtime auto-hospedado para dar suporte a armazenamentos de dados que requer traga seu próprio driver como SAP Hana, MySQL, etc.  Para obter mais informações, consulte [fontes de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

### <a name="network-environment"></a>Ambiente de rede
Se você deseja realizar a integração de dados com segurança em um ambiente de rede privada, que não tem uma linha de visão direta do ambiente de nuvem pública, você pode instalar um IR auto-hospedado no ambiente local por trás de seu firewall corporativo ou então em uma rede virtual privada.  O Integration Runtime auto-hospedado só faz conexões de saída com base em HTTP com a Internet aberta.

### <a name="compute-resource-and-scaling"></a>Recurso de computação e dimensionamento
O IR auto-hospedado precisa ser instalado em um computador local ou em uma máquina virtual dentro de uma rede privada. Atualmente, só há suporte para a execução do IR auto-hospedado em um sistema operacional Windows.  

Para alta disponibilidade e escalabilidade, você pode expandir o IR auto-hospedado associando a instância lógica a vários computadores locais no modo ativo-ativo.  Para obter mais informações e detalhes, consulte o tópico correspondente nos guias de instruções, Como criar e configurar IR auto-hospedado.

## <a name="azure-ssis-integration-runtime"></a>Integration Runtime do Azure-SSIS
Para fazer lift-and-shift da carga de trabalho existente do SSIS, você pode criar um IR Azure-SSIS para executar pacotes do SSIS nativamente.

### <a name="network-environment"></a>Ambiente de rede
O IR Azure-SSIS pode ser provisionado na rede pública ou na rede privada.  Há suporte para o acesso a dados locais unindo o IR do Azure-SSIS a uma VNet (Rede Virtual) conectada à sua rede local. Atualmente, há suporte para apenas para a VNet clássica. 

### <a name="compute-resource-and-scaling"></a>Recurso de computação e dimensionamento
O IR do SSIS do Azure é um cluster totalmente gerenciado das VMs do Azure dedicado para executar os pacotes de SSIS. Você pode colocar seu próprio servidor do Banco de Dados SQL do Azure ou Instância Gerenciada (versão prévia privada) para hospedar o catálogo de projetos/pacotes do SSIS (SSISDB) que será anexado a ele. Você pode aumentar a potência de computação especificando o tamanho do nó e escalá-la horizontalmente especificando o número de nós no cluster. Você pode gerenciar o custo da execução do Integration Runtime do Azure SSIS, parando-o e iniciando-o como considerar adequado.

Para obter mais informações, consulte o tópico Como criar e configurar IR do Azure-SSIS, encontrado nos guias de instruções.  Depois de criado, você pode implantar e gerenciar seus pacotes SSIS existentes com pouca ou nenhuma alteração usando ferramentas familiares, como o SSDT (SQL Server Data Tools) e o SSMS (SQL Server Management Studio), assim como usando o SSIS localmente.

Confira estes artigos para obter mais informações sobre o tempo de execução do Azure-SSIS: 

- [Tutorial: implantar pacotes do SSIS para o Azure](tutorial-deploy-ssis-packages-azure.md). Este artigo fornece instruções passo a passo para criar um IR do Azure-SSIS e usa um banco de dados SQL do Azure para hospedar o catálogo do SSIS. 
- [Como: Criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar a Instância Gerenciada do SQL do Azure (visualização particular) e unir o IR para uma VNet. 
- [Monitore um IR do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como recuperar informações sobre um IR do Azure-SSIS e descrições de status nas informações retornadas. 
- [Gerencie um IR do Azure-SSIS](manage-azure-ssis-integration-runtime.md). Este artigo mostra como parar, iniciar ou remover um IR do Azure-SSIS. Ele também mostra como expandir o IR do Azure-SSIS adicionando mais nós ao IR. 
- [Unir um IR do Azure-SSIS a uma VNet](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo fornece informações conceituais sobre como unir um IR do Azure-SSIS a uma rede virtual (VNet) do Azure. Ele também apresenta as etapas para usar o portal do Azure para configurar a VNet para que o IR do Azure-SSIS possa unir-se à VNet. 

## <a name="determining-which-ir-to-use"></a>Determinando qual IR usar
Cada atividade de transformação tem um serviço vinculado de computação de destino, que aponta para um Integration Runtime. É dessa instância do Integration Runtime que a atividade de transformação é expedida.

Para a atividade de cópia, ela requer que serviços vinculados de origem e de coletor definam a direção do fluxo de dados. A lógica a seguir é usada para determinar qual instância do Integration Runtime é usada para realizar a cópia: 

- **Copiando entre duas fontes de dados de nuvem**: quando tanto o serviço vinculado de origem quanto o de coletor estão usando o IR do Azure, o Integration Runtime usado pelo serviço vinculado de coletor é usado para executar a atividade de cópia.
- **Copiando entre uma fonte de dados de nuvem e uma fonte de dados na rede privada**: se o serviço vinculado de origem ou de coletor aponta para um IR auto-hospedado, a atividade de cópia é executada nesse Integration Runtime auto-hospedado.
- **Copiando entre duas fontes de dados na rede privada**: o serviço vinculado de origem e o de coletor devem apontar para a mesma instância de Integration Runtime e esse em Integration Runtime é usado para executar a atividade de cópia.

O diagrama a seguir mostra duas amostras de atividade de cópia:

- Para a atividade de cópia 1, a origem é um serviço vinculado do SQL Server referenciando um IR auto-hospedado A e o coletor é um serviço vinculado do Armazenamento do Azure referenciando um IR do Azure B. Quando a atividade de cópia é executada, ela é executada no IR auto-hospedado A.
- Para a atividade de cópia 2, a origem é um serviço vinculado de Banco de Dados SQL do Azure referenciando um IR do Azure C, enquanto o coletor é um serviço vinculado do Armazenamento do Azure referenciando um IR do Azure B. Quando a atividade de cópia é executada, ela é executada no IR do Azure B, pois trata-se do Integration Runtime usado pelo serviço vinculado de coletor.

![Qual IR usar](media/concepts-integration-runtime/which-integration-runtime-to-use.png)

## <a name="integration-runtime-location"></a>Localização do Integration Runtime
É na localização do Data Factory que os metadados do data factory são armazenados e é dela que o disparo do pipeline é iniciado. Atualmente, as localizações do Data Factory com suporte são: Leste dos EUA, Leste dos EUA 2. No entanto, uma fábrica de dados pode acessar repositórios de dados e serviços de computação em outras regiões do Azure para mover dados entre repositórios de dados ou processar dados usando serviços de computação. Esse comportamento é realizado por meio do IR disponível globalmente em várias regiões para garantir conformidade de dados, eficiência e custos de saída de rede reduzidos.

A localização do IR define a localização da respectiva computação de back-end e, essencialmente, a localização em que a movimentação de dados, a expedição de atividades e a execução de pacotes SSIS são executadas. A localização de IR pode ser diferente da localização do data factory ao qual ele pertence. O diagrama a seguir mostra as configurações de localização de data factory e os respectivos tempos de execução de integração:

![Localização do Integration Runtime](media/concepts-integration-runtime/integration-runtime-location.png)

### <a name="azure-ir"></a>IR do Azure
O Data Factory usa um IR do Azure na região mais próxima ao coletor na mesma geografia para mover os dados. Consulte a tabela a seguir para ver o mapeamento:

Geografia do armazenamento de dados de coletor | Localização do armazenamento de dados de coletor | Localização usada para o Integration Runtime do Azure
-------------------------------| ----------------| ------------------
Estados Unidos | Leste dos EUA | Leste dos EUA
&nbsp; | Leste dos EUA 2 | Leste dos EUA 2
&nbsp; | Centro dos EUA | Centro dos EUA
&nbsp; | Centro-Norte dos EUA | Centro-Norte dos EUA
&nbsp; | Centro-Sul dos Estados Unidos | Centro-Sul dos Estados Unidos
&nbsp; | Centro-Oeste dos EUA | Centro-Oeste dos EUA
&nbsp; | Oeste dos EUA | Oeste dos EUA
&nbsp; | Oeste dos EUA 2 | Oeste dos EUA
Canadá | Leste do Canadá | Canadá Central
&nbsp; | Canadá Central | Canadá Central
Brasil | Sul do Brasil | Sul do Brasil
Europa | Norte da Europa | Norte da Europa
&nbsp; | Europa Ocidental | Europa Ocidental
Reino Unido | Oeste do Reino Unido | Sul do Reino Unido
&nbsp; | Sul do Reino Unido | Sul do Reino Unido
Pacífico Asiático | Sudeste Asiático | Sudeste Asiático
&nbsp; | Ásia Oriental | Sudeste Asiático
Austrália | Leste da Austrália | Leste da Austrália
&nbsp; | Sudeste da Austrália | Sudeste da Austrália
Japão | Leste do Japão | Leste do Japão
&nbsp; | Oeste do Japão | Leste do Japão
Coreia | Coreia Central | Sul da Coreia
&nbsp; | Sul da Coreia | Sul da Coreia
Índia | Índia Central | Índia Central
&nbsp; | Índia Ocidental | Índia Central
&nbsp; | Sul da Índia | Índia Central

Você também pode definir o local de um IR do Azure para resolver automaticamente, que significa que o Data Factory faz o possível para, com base na definição do serviço vinculado, detectar automaticamente a melhor localização para usar.

> [!NOTE] 
> Se a região do armazenamento de dados de destino não estiver na lista ou não puder ser detectada, a atividade falhará em vez de passar por uma região alternativa, por motivos de conformidade. Nesse caso, indique explicitamente a localização alternativa a ser usada para executar a cópia.
 
A imagem a seguir mostra um exemplo da localização efetiva quando a localização do IR do Azure é definida como resolver automaticamente. Quando uma atividade de cópia é executada, ela detecta a localização do destino dos dados, que neste exemplo é Oeste do Japão.  Com base na tabela, um IR do Azure no Leste do Japão é usado para executar a cópia de dados propriamente dita. Quando o mesmo IR é usado para conectar-se ao HDInsight para uma atividade do Spark, o envio de aplicativo Spark ocorre da localização do Data Factory, que neste exemplo é Leste dos EUA, e a execução do aplicativo Spark propriamente dita ocorre na localização do servidor HDInsight. 

![Localização efetiva](media/concepts-integration-runtime/effective-location.png)

### <a name="self-hosted-ir"></a>IR auto-hospedado
O IR auto-hospedado está logicamente registrado para o Data Factory e a computação usada para dar suporte às funcionalidades dele é fornecido por você. Portanto, não há nenhuma propriedade de localização explícita para IR auto-hospedado. 

Quando usado para realizar a movimentação de dados, o IR auto-hospedado extrai dados da origem e grava-os no destino.

### <a name="azure-ssis-ir"></a>IR Azure-SSIS
Selecionar a localização certa para o IR do Azure-SSIS é essencial para alcançar alto desempenho em seus fluxos de trabalho de ETL (extrair, transformar e carregar).  Duas localizações estão inicialmente disponíveis para a versão prévia (Leste dos EUA e Europa Setentrional).

- A localização do IR do Azure-SSIS não precisa ser a mesma que a do data factory, mas deve ser a mesma que a localização do seu próprio servidor de Banco de Dados SQL do Azure/Instância Gerenciada (versão prévia privada) em que o SSISDB está hospedado. Desse modo, o Integration Runtime do Azure-SSIS pode acessar o SSISDB facilmente sem incorrer em tráfegos excessivos entre localizações diferentes.
- Se você não tem um servidor de Banco de Dados SQL do Azure/Instância Gerenciada (versão prévia privada) existente para hospedar o SSISDB mas tem fontes/destinos de dados locais, você deve criar um novo servidor de Banco de Dados SQL do Azure/Instância Gerenciada (versão prévia privada) na mesma localização de uma VNet conectada à rede local.  Desse modo, você pode criar o IR do Azure-SSIS usando o novo servidor de Banco de Dados SQL do Azure/Instância Gerenciada (versão prévia privada) e ingressando nessa VNet, tudo no mesmo local, minimizando efetivamente as movimentações de dados entre diferentes localizações.
- Se a localização do seu servidor de Banco de Dados SQL do Azure/Instância Gerenciada (versão prévia privada) existente em que o SSISDB está hospedado não é a mesma que a localização de uma VNet conectada à rede local, primeiro crie o IR do Azure-SSIS usando um servidor de Banco de Dados SQL do Azure/Instância Gerenciada (versão prévia privada) e ingressando em outra VNet na mesma localização e, em seguida, configure uma conexão de VNet a VNet entre localizações diferentes.


## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

- [Criar um Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md)
- [Criar um Integration Runtime do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar a Instância Gerenciada do SQL do Azure (visualização particular) e unir o IR para uma VNet. 
