---
title: 'Importar dados: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo importar dados no Azure Machine Learning para carregar dados em um pipeline de Machine Learning de serviços de dados em nuvem existentes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 69d27c102ca059974da87224e44f0ad7aa103fff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94592627"
---
# <a name="import-data-module"></a>Importar módulo de dados

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para carregar dados em um pipeline de Machine Learning dos serviços de dados de nuvem existentes. 

> [!Note]
> Toda a funcionalidade fornecida por esse módulo pode ser feita pelo **armazenamento de datastore** e por **conjuntos de valores** na página de aterrissagem espaço. É recomendável que você use o **armazenamento** de dados e o **DataSet** , que inclui recursos adicionais, como o monitoramento de dados. Para obter mais informações, consulte [o artigo como acessar dados](../how-to-access-data.md) e [como registrar os DataSets](../how-to-create-register-datasets.md) .
> Depois de registrar um conjunto de registros, você pode encontrá-lo na categoria **conjuntos** de os  ->  **meus conjuntos** de valores na interface do designer. Este módulo é reservado para usuários do Studio (clássicos) para uma experiência familiar. 
>

O módulo **importar dados** dá suporte à leitura de dados das seguintes fontes:

- URL via HTTP
- Armazenamentos em nuvem do Azure por meio de [**repositórios de armazenamento**](../how-to-access-data.md))
    - Contêiner de blob do Azure
    - Compartilhamento de arquivos do Azure
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Banco de Dados SQL do Azure
    - PostgreSQL do Azure    

Antes de usar o armazenamento em nuvem, você precisa registrar um repositório de armazenamento em seu espaço de trabalho Azure Machine Learning primeiro. Para obter mais informações, consulte [como acessar dados](../how-to-access-data.md). 

Depois de definir os dados desejados e conectar-se à origem, **[importar dados](./import-data.md)** infere o tipo de dados de cada coluna com base nos valores que ele contém e carrega os dados em seu pipeline de designer. A saída de **Import data** é um conjunto de dados que pode ser usado com qualquer pipeline de designer.

Se os dados de origem forem alterados, você poderá atualizar o conjunto de dados e adicionar novos data executando novamente os [dados de importação](./import-data.md).

> [!WARNING]
> Se o seu espaço de trabalho estiver em uma rede virtual, você deverá configurar seus armazenamentos de dados para usar os recursos de visualização do dado do designer. Para obter mais informações sobre como usar armazenamentos de dados e conjuntos de dados em uma rede virtual, consulte [usar o Azure Machine Learning Studio em uma rede virtual do Azure](../how-to-enable-studio-virtual-network.md).


## <a name="how-to-configure-import-data"></a>Como configurar dados de importação

1. Adicione o módulo **importar dados** ao seu pipeline. Você pode encontrar esse módulo na categoria **entrada e saída de dados** no designer.

1. Selecione o módulo para abrir o painel direito.

1. Selecione **fonte de dados** e escolha o tipo de fonte de dados. Pode ser HTTP ou datastore.

    Se você escolher repositório de armazenamento, poderá selecionar os repositórios de armazenamento existentes que já estão registrados em seu espaço de trabalho Azure Machine Learning ou criar um novo repositório de armazenamento. Em seguida, defina o caminho dos dados a serem importados no datastore. Você pode navegar facilmente pelo caminho clicando em **procurar caminho** ![ captura de tela mostra o link procurar caminho, que abre a caixa de diálogo seleção de caminho.](media/module/import-data-path.png)

    > [!NOTE]
    > O módulo **importar dados** destina-se somente a dados **tabulares** .
    > Se você quiser importar vários arquivos de dados de tabela uma vez, ele exigirá as seguintes condições; caso contrário, ocorrerão erros:
    > 1. Para incluir todos os arquivos de dados na pasta, você precisa inserir `folder_name/**` o **caminho**.
    > 2. Todos os arquivos de dados devem ser codificados em Unicode-8.
    > 3. Todos os arquivos de dados devem ter os mesmos números de coluna e nomes de coluna.
    > 4. O resultado da importação de vários arquivos de dados é concatenar todas as linhas de vários arquivos na ordem.

1. Selecione o esquema de visualização para filtrar as colunas que você deseja incluir. Você também pode definir configurações avançadas como delimitador nas opções de análise.

    ![importação-dados-visualização](media/module/import-data.png)

1. A caixa de seleção, **regenerar saída**, decide se o módulo deve ser executado para regenerar a saída em tempo de execução. 

    Ele é, por padrão, desmarcado, o que significa que, se o módulo tiver sido executado com os mesmos parâmetros anteriormente, o sistema reutilizará a saída da última execução para reduzir o tempo de execução. 

    Se estiver selecionado, o sistema executará o módulo novamente para regenerar a saída. Portanto, selecione essa opção quando os dados subjacentes no armazenamento forem atualizados, ele poderá ajudar a obter os dados mais recentes.


1. Envie o pipeline.

    Quando os dados de importação carregam os dados no designer, ele infere o tipo de dados de cada coluna com base nos valores que ele contém, numéricos ou categóricos.

    Se houver um cabeçalho, ele será usado para nomear as colunas do conjunto de dados de saída.

    Se não houver nenhum cabeçalho de coluna existente nos dados, novos nomes de coluna serão gerados usando o formato Col1, Col2,... , coln*.

## <a name="results"></a>Resultados

Quando a importação for concluída, clique com o botão direito do mouse no conjunto de dados de saída e selecione **Visualizar** para ver se os dados foram importados com êxito.

Se você quiser salvar os dados para reutilização, em vez de importar um novo conjunto de dados cada vez que o pipeline for executado, selecione o ícone **registrar conjunto de registros** na guia **saídas + logs** no painel direito do módulo. Escolha um nome para o conjunto de um. O DataSet salvo preserva os dados no momento de salvá-los, o DataSet não é atualizado quando o pipeline é executado novamente, mesmo que o conjunto de dados no pipeline seja alterado. Isso pode ser útil para tirar instantâneos de dados.

Depois de importar os dados, talvez sejam necessários alguns preparativos adicionais para modelagem e análise:

- Use [Editar metadados](./edit-metadata.md) para alterar nomes de coluna, para manipular uma coluna como um tipo de dados diferente ou para indicar que algumas colunas são rótulos ou recursos.

- Use [selecionar colunas no conjunto](./select-columns-in-dataset.md) de linhas para selecionar um subconjunto de colunas para transformar ou usar na modelagem. As colunas transformadas ou removidas podem ser facilmente reassociadas ao conjunto de os originais usando o módulo [adicionar colunas](./add-columns.md) .  

- Use [Partition e Sample](./partition-and-sample.md) para dividir o conjunto de registros, executar amostragem ou obter as n primeiras linhas.

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
