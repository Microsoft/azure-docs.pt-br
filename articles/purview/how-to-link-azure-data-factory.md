---
title: Conectar-se ao Azure Data Factory
description: Este artigo descreve como conectar Azure Data Factory e o Azure alcance para acompanhar a linhagem de dados.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/22/2020
ms.openlocfilehash: 010cfc307d2b2c10c31168fce73673fb1fb611b8
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807641"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>Como conectar Azure Data Factory e o Azure alcance

Este documento explica as etapas necessárias para conectar uma conta de Azure Data Factory com uma conta do Azure alcance para acompanhar a linhagem de dados. O documento também obtém os detalhes do escopo de cobertura e padrões de linhagem com suporte.

## <a name="view-existing-data-factory-connections"></a>Exibir conexões de Data Factory existentes

Várias fábricas de dados do Azure podem se conectar a um único catálogo de dados do Azure alcance para enviar informações de linhagem por push. O limite atual permite que você conecte dez contas de Data Factory de cada vez no centro de gerenciamento do alcance. Para mostrar a lista de contas de Data Factory conectadas ao catálogo de dados do alcance, faça o seguinte:

1. Selecione **centro de gerenciamento** no painel de navegação esquerdo.
2. Em **conexões externas**, selecione **Data Factory conexão**.
3. A lista de conexões de Data Factory é exibida.

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="Captura de tela mostrando uma lista de conexão data factory." lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. Observe os vários valores de **status** da conexão:

    - **Conectado**: o data Factory está conectado ao catálogo de dados.
    - **Desconectado**: o data Factory tem acesso ao catálogo, mas está conectado a outro catálogo. Como resultado, a linhagem de dados não será relatada para o catálogo automaticamente.
    - **CannotAccess**: o usuário atual não tem acesso ao data Factory, portanto, o status da conexão é desconhecido.
 >[!Note]
 >Para exibir as conexões de Data Factory, você precisa ser atribuído a qualquer uma das funções alcance:
 >- Colaborador
 >- Proprietário
 >- Leitor
 >- Administrador de Acesso do Usuário

## <a name="create-new-data-factory-connection"></a>Criar nova conexão Data Factory

>[!Note]
>Para adicionar ou remover as conexões de Data Factory, você precisa ser atribuído a qualquer uma das funções alcance:
>- Proprietário
>- Administrador de Acesso do Usuário
>
> Além disso, requer que os usuários sejam o "proprietário" ou "colaborador" do data factory. 

Siga as etapas abaixo para conectar uma conta de Data Factory existente ao catálogo de dados do alcance.

1. Selecione **centro de gerenciamento** no painel de navegação esquerdo.
2. Em **conexões externas**, selecione **Data Factory conexão**.
3. Na página **conexão do data Factory** , selecione **novo**.

4. Selecione sua conta de Data Factory na lista e selecione **OK**. Você também pode filtrar por nome de assinatura para limitar sua lista.

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="Captura de tela mostrando como conectar Azure Data Factory." lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    Algumas instâncias de Data Factory poderão ser desabilitadas se o data factory já estiver conectado à conta atual do alcance ou o data factory não tiver uma identidade gerenciada.

    Uma mensagem de aviso será exibida se qualquer uma das fábricas de dados selecionadas já estiver conectada a outra conta do alcance. Ao selecionar OK, a conexão Data Factory com a outra conta do alcance será desconectada. Não são necessárias confirmações adicionais.


    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Captura de tela mostrando aviso para desconectar Azure Data Factory." lightbox="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png":::

>[!Note]
>Agora, damos suporte à adição de até 10 fábricas de dados de uma vez. Se você quiser adicionar mais de 10 fábricas de dados de uma vez, registre um tíquete de suporte.

### <a name="how-does-the-authentication-work"></a>Como funciona a autenticação?

Quando um usuário alcance registra um Data Factory ao qual eles têm acesso, acontece o seguinte no back-end:

1. A **Data Factory MSI** é adicionada à função RBAC alcance: **curador de dados alcance**.

    :::image type="content" source="./media/how-to-link-azure-data-factory/adf-msi.png" alt-text="Captura de tela mostrando Azure Data Factory MSI." lightbox="./media/how-to-link-azure-data-factory/adf-msi.png":::
     
2. O pipeline de Data Factory precisa ser executado novamente para que os metadados de linhagem possam ser enviados de volta ao alcance.
3. Pós-execução o Data Factory metadados é enviado para o alcance.

### <a name="remove-data-factory-connections"></a>Remover conexões data factory
Para remover uma conexão data factory, faça o seguinte:

1. Na página **conexão Data Factory** , selecione o botão **remover** ao lado de uma ou mais conexões de data Factory.
2. Selecione **confirmar** no pop-up para excluir as conexões de data Factory selecionadas.

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="Captura de tela mostrando como selecionar fábricas de dados para remover a conexão." lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="configure-a-self-hosted-ir-to-collect-lineage-from-on-prem-sql"></a>Configurar um IR auto-hospedado para coletar a linhagem do SQL local

A linhagem para a Data Factory atividade de cópia está disponível para bancos de dados SQL locais. Se você estiver executando o tempo de execução de integração auto-hospedado para a movimentação de dados com Azure Data Factory e quiser capturar a linhagem no Azure alcance, verifique se a versão é 4.8.7418.1 ou posterior. Para obter mais informações sobre o Integration Runtime de hospedagem interna, consulte [criar e configurar um tempo de execução de integração auto-hospedado](../data-factory/create-self-hosted-integration-runtime.md).

## <a name="supported-azure-data-factory-activities"></a>Atividades de Azure Data Factory com suporte

O Azure alcance captura a linhagem de tempo de execução das seguintes atividades de Azure Data Factory:

- Copiar Dados
- Fluxo de Dados
- Executar o pacote SSIS

> [!IMPORTANT]
> O Azure alcance remove a linhagem se a origem ou o destino usa um sistema de armazenamento de dados sem suporte.

A integração entre Data Factory e alcance dá suporte apenas a um subconjunto dos sistemas de dados aos quais Data Factory dá suporte, conforme descrito nas seções a seguir.

### <a name="data-factory-copy-data-support"></a>Suporte ao Data Factory Copiar Dados

| Sistema de armazenamento de dados | Tem suporte como origem | 
| ------------------- | ------------------- | 
| ADLS Gen1 | Sim | 
| ADLS Gen2 | Sim | 
| Blob do Azure | Sim |
| Azure Cosmos DB (API do SQL) | Sim | 
| Azure Cosmos DB (API Mongo) | Sim |
| Azure Cognitive Search | Sim | 
| Azure Data Explorer | Sim | 
| Banco de dados do Azure para Maria DB \* | Sim | 
| Banco de dados do Azure para MYSQL \* | Sim | 
| Banco de dados do Azure para PostgreSQL \* | Sim |
| Armazenamento de Arquivos do Azure | Sim | 
| Armazenamento de Tabelas do Azure | Sim |
| Banco de dados SQL do Azure \* | Sim | 
| MI SQL do Azure \* | Sim | 
| Azure Synapse Analytics (antigo SQL DW) \* | Sim | 
| SQL Server local  \* | Sim | 
| Amazon S3 | Sim | 
| Teradata | Sim | 
| Conector de tabela SAP | Sim |
| SAP ECC | Sim | 
| Hive | Sim | 

> [!Note]
> O recurso de linhagem tem determinada sobrecarga de desempenho na Data Factory atividade de cópia. Para aqueles que configuram data factory conexões no alcance, você pode observar que alguns trabalhos de cópia demoram mais para serem concluídos. Na maioria das vezes, o impacto é nenhum para insignificante. Entre em contato com o suporte com a comparação de tempo se os trabalhos de cópia demorarem significativamente mais para concluir do que o normal.

### <a name="data-factory-data-flow-support"></a>Suporte a Data Factory de fluxo de dados

| Sistema de armazenamento de dados | Com suporte |
| ------------------- | ------------------- | 
| ADLS Gen1 | Sim |
| ADLS Gen2 | Sim |
| Blob do Azure | Sim |
| Banco de dados SQL do Azure \* | Sim |
| Azure Synapse Analytics (antigo SQL DW) \* | Sim |

### <a name="data-factory-execute-ssis-package-support"></a>Data Factory executar suporte a pacotes SSIS

| Sistema de armazenamento de dados | Com suporte |
| ------------------- | ------------------- |
| Blob do Azure | Sim |
| ADLS Gen1 | Sim |
| ADLS Gen2 | Sim |
| Banco de dados SQL do Azure \* | Sim |
| MI SQL do Azure \*| Sim |
| Azure Synapse Analytics (antigo SQL DW) \* | Sim |
| SQL Server local \* | Sim |
| Armazenamento de Arquivos do Azure | Sim |

*\* Para cenários do SQL (Azure e local), o Azure alcance não dá suporte a procedimentos armazenados ou scripts para linhagem ou verificação. A linhagem é limitada apenas a fontes de tabela e exibição.*

> [!Note]
> O Azure Data Lake Storage Gen2 já se encontra disponível ao público em geral. É recomendável que você comece a usá-lo hoje mesmo. Para saber mais, consulte a [página do produto](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="supported-lineage-patterns"></a>Padrões de linhagem com suporte

Há vários padrões de linhagem com suporte do Azure alcance. Os dados de linhagem gerados baseiam-se no tipo de fonte e no coletor usados nas atividades de Data Factory. Embora Data Factory dê suporte a mais de 80 fontes e coletores, o Azure alcance dá suporte a apenas um subconjunto, conforme listado em [atividades de Azure data Factory com suporte](#supported-azure-data-factory-activities).

Para configurar Data Factory para enviar informações de linhagem, consulte Introdução [à linhagem](catalog-lineage-user-guide.md#get-started-with-lineage).

Algumas maneiras adicionais de localizar informações no modo de exibição de linhagem incluem o seguinte:

- Na guia **linhagem** , focalize as formas para visualizar informações adicionais sobre o ativo na dica de ferramenta.
- Selecione o nó ou a borda para ver o tipo de ativo que ele pertence ou para alternar ativos.
- As colunas de um conjunto de um DataSet são exibidas no lado esquerdo da guia **linhagem** . Para obter mais informações sobre a linhagem em nível de coluna, consulte [linhagem em nível de coluna](catalog-lineage-user-guide.md#column-level-lineage).

### <a name="data-lineage-for-11-operations"></a>Linhagem de dados para operações de 1:1

O padrão mais comum para capturar a linhagem de dados é mover dados de um único conjunto de dado de entrada para um único DataSet de saída, com um processo no between.

Um exemplo desse padrão seria o seguinte:

- 1 origem/entrada: *cliente* (tabela SQL)
- 1 coletor/saída: *Customer1.csv* (blob do Azure)
- 1 processo: *CopyCustomerInfo1 \#Customer1.csv* (data Factory atividade de cópia)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="Captura de tela mostrando a linhagem de um para um Data Factory operação de cópia." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage.png":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>Movimentação de dados com suporte de linhagem 1:1 e curinga

Outro cenário comum para capturar a linhagem, está usando um caractere curinga para copiar arquivos de um único conjunto de dados de entrada para um único DataSet de saída. O curinga permite que a atividade de cópia corresponda a vários arquivos para copiar usando uma parte comum do nome do arquivo. O Azure alcance captura a linhagem em nível de arquivo para cada arquivo individual copiado pela atividade de cópia correspondente.

Um exemplo desse padrão seria o seguinte:

* Origem/entrada: *CustomerCall \* . csv* (caminho de ADLS Gen2)
* Coletor/saída: *CustomerCall \* . csv* (arquivo de blob do Azure)
* 1 processo: *CopyGen2ToBlob \#CustomerCall.csv* (data Factory atividade de cópia)  

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="Captura de tela mostrando a linhagem para uma operação de cópia de um para um com suporte a curinga." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>Movimentação de dados com linhagem n:1

Você pode usar atividades de fluxo de dados para executar operações de dados como mesclagem, junção e assim por diante. Mais de um conjunto de informações de origem pode ser usado para produzir um conjunto de um DataSet de destino. Neste exemplo, o Azure alcance captura a linhagem em nível de arquivo para arquivos de entrada individuais em uma tabela SQL que faz parte de uma atividade de fluxo de dados.

Um exemplo desse padrão seria o seguinte:

* 2 fontes/entradas: *Customer.csv*, *Sales. parquet* (ADLS Gen2 caminho)
* 1 coletor/saída: *dados da empresa* (tabela SQL do Azure)
* 1 processo: *DataFlowBlobsToSQL* (data Factory atividade de fluxo de dados)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="Captura de tela mostrando a linhagem de um n para uma operação de fluxo de dados D F." lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>Linhagem para conjuntos de recursos

Um conjunto de recursos é um objeto lógico no catálogo que representa muitos arquivos de partição no armazenamento subjacente. Para obter mais informações, consulte [noções básicas sobre conjuntos de recursos](concept-resource-sets.md). Quando o Azure alcance captura a linhagem da Azure Data Factory, ele aplica as regras para normalizar os arquivos de partição individuais e criar um único objeto lógico.

No exemplo a seguir, um conjunto de recursos Azure Data Lake Gen2 é produzido de um blob do Azure:

* 1 origem/entrada: *funcionário \_management.csv* (blob do Azure)
* 1 coletor/saída: *\_management.csvde funcionários* (Azure data Lake Gen 2)
* 1 processo: *CopyBlobToAdlsGen2 \_ RS* (data Factory atividade de cópia)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="Captura de tela mostrando a linhagem de um conjunto de recursos." lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>Próximas etapas

- [Guia do usuário de linhagem de catálogo](catalog-lineage-user-guide.md)
- [Link para o compartilhamento de dados do Azure para linhagem](how-to-link-azure-data-share.md)
