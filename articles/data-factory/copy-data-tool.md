---
title: Ferramenta Copiar Dados do Azure Data Factory | Microsoft Docs
description: Fornece informações sobre a ferramenta Copiar Dados na interface de usuário do Azure Data Factory
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: yexu
ms.openlocfilehash: 107687c785433f81870449d1445136b5148a4d2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60787623"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Ferramenta Copiar Dados no Azure Data Factory
A ferramenta Copiar Dados do Azure Data Factory facilita e otimiza o processo de ingerir dados em um data lake, que geralmente é a primeira etapa no cenário de integração completa de dados.  Ele poupa tempo, especialmente ao usar o Azure Data Factory para ingestão de dados de uma fonte de dados pela primeira vez. Estes são alguns dos benefícios de usar essa ferramenta:

- Ao usar a ferramenta Copiar Dados do Azure Data Factory, você não precisa entender as definições do Data Factory para serviços vinculados, conjuntos de dados, pipelines, atividades e gatilhos. 
- O fluxo da ferramenta Copiar Dados é intuitivo para carregar dados em um data lake. A ferramenta cria automaticamente todos os recursos de Data Factory necessários para copiar dados do repositório de dados de origem selecionado para o repositório de dados de destino selecionado/coletor. 
- A ferramenta Copiar Dados ajuda a validar os dados que estão sendo incluídos no momento da criação, o que ajuda a evitar possíveis erros no início em si.
- Se você precisa implementar a lógica de negócios complexa para carregar dados em um data lake, ainda poderá editar os recursos do Data Factory criados pela ferramenta Copiar Dados usando a criação por atividade na interface de usuário do Data Factory. 

A tabela a seguir fornece orientação sobre quando usar a ferramenta Copiar Dados versus criação por atividade na interface de usuário do Data Factory: 

| Ferramenta Copiar Dados | Criação por atividade (Copiar atividade) |
| -------------- | -------------------------------------- |
| Você deseja criar uma tarefa de carregamento de dados sem precisar aprender sobre as entidades do Azure Data Factory (serviços vinculados, conjuntos de dados, pipelines etc.) | Você deseja implementar a lógica complexa e flexível para carregar dados em um lake. |
| Você deseja carregar rapidamente um grande número de artefatos de dados em um data lake. | Você deseja encadear a atividade Copiar com as atividades subsequentes para limpeza ou processamento de dados. |

Para iniciar a ferramenta Copiar Dados, clique no bloco **Copiar Dados** na home page do seu data factory.

![Página de introdução - link para a ferramenta Copiar Dados](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Fluxo intuitivo para carregar dados em um data lake
Essa ferramenta permite mover dados facilmente de uma ampla variedade de fontes para destinos em minutos com um fluxo intuitivo:  

1. Defina as configurações para essa **origem**.
2. Defina as configurações para esse **destino**. 
3. Defina as **configurações avançadas** para a operação de cópia como mapeamento de coluna, configurações de desempenho e configurações de tolerância a falhas. 
4. Especifique um **cronograma** para a tarefa de carregamento de dados. 
5. Analise o **resumo** de entidades do Data Factory a ser criado. 
6. **Edite** o pipeline para atualizar as configurações para a atividade de cópia, conforme o necessário. 

   A ferramenta foi projetada com o Big Data em mente desde o começo, com suporte para diversos tipos de dados e objeto. Você pode usá-lo para mover a centenas de pastas, arquivos ou tabelas. A ferramenta permite a visualização automática de dados, a captura e o mapeamento automático de esquemas, além da filtragem de dados.

![Ferramenta Copiar Dados](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Visualização automática de dados
Você pode visualizar parte dos dados do armazenamento de dados de origem selecionado, o que permite que você valide os dados que estão sendo copiados. Além disso, se os dados da fonte estiverem em um arquivo de texto, a ferramenta Copiar Dados analisará o arquivo de texto para detectar automaticamente o esquema e os delimitadores de linha e coluna.

![Configurações de arquivo](./media/copy-data-tool/file-format-settings.png)

Após a detecção:

![Configurações e visualização de arquivo detectadas](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Captura e mapeamento automático do esquema
O esquema de fonte de dados não pode ser igual ao esquema de destino de dados em muitos casos. Nesse cenário, você precisa mapear as colunas do esquema de origem para colunas do esquema de destino.

A ferramenta Copiar Dados monitora e aprende o comportamento quando você está mapeando colunas entre repositórios de origem e destino. Depois de escolher uma ou algumas colunas do repositório de dados de origem e mapeá-los para o esquema de destino, a ferramenta Copiar Dados é iniciada para analisar o padrão para os pares de coluna selecionadas em ambos os lados. Em seguida, ele aplica o mesmo padrão ao restante das colunas. Portanto, você vê que todas as colunas foram mapeadas para o destino da forma desejada com apenas alguns cliques.  Se você não estiver satisfeito com a opção de mapeamento de coluna fornecido pela ferramenta Copiar Dados, ignore-o e continue com o mapeamento manual das colunas. Enquanto isso, a ferramenta Copiar dados aprende e atualiza constantemente o padrão e, por fim, atinge o padrão certo para o mapeamento de coluna que você deseja atingir. 

> [!NOTE]
> Ao copiar dados do Banco de Dados SQL do Azure ou do SQL Server no SQL Data Warehouse do Azure, se a tabela não existir no repositório de destino, a ferramenta Copiar Dados dá suporte à criação da tabela automaticamente usando o esquema da origem. 

## <a name="filter-data"></a>Filtrar dados
É possível filtrar os dados de origem para selecionar apenas os dados que precisam ser copiados no repositório de dados do coletor. A filtragem reduz o volume de dados a ser copiado para o repositório de dados do coletor e, portanto, melhora a produtividade da operação de cópia. A ferramenta Copiar Dados fornece uma maneira flexível de filtrar dados em um banco de dados relacional usando a linguagem de consulta SQL ou arquivos em uma pasta de blobs do Azure. 

### <a name="filter-data-in-a-database"></a>Filtrar dados em um banco de dados
A captura de tela a seguir mostra uma consulta SQL para filtrar os dados.

![Filtrar dados em um banco de dados](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrar dados em uma pasta de blobs do Azure
Você pode usar variáveis no caminho da pasta para copiar dados de uma pasta. As variáveis com suporte são: **{ano}**, **{mês}**, **{dia}**, **{hora}** e **{minuto}**. Por exemplo: pastadeentrada/{ano}/{mês}/{dia}. 

Suponha que você tenha pastas de entrada no seguinte formato: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Clique no botão **Procurar** de **Arquivo ou pasta**, navegue até uma dessas pastas (por exemplo, 2016->03->01->02) e clique em **Escolher**. Você verá 2016/03/01/02 na caixa de texto. 

Em seguida, substitua **2016** por **{ano}**, **03** por **{mês}**, **01** por **{dia}**, **02** por **{hora}** e pressione a tecla **Tab**. Você deverá ver listas suspensas para escolher o formato dessas quatro variáveis:

![Filtrar arquivo ou pasta](./media/copy-data-tool/filter-file-or-folder.png)

A ferramenta Copiar Dados gera parâmetros com expressões, funções e variáveis de sistema que podem ser usados para representar {ano}, {mês}, {dia}, {hora} e {minuto} durante a criação do pipeline. Para saber mais, veja o artigo [Como ler e gravar dados de partição](how-to-read-write-partitioned-data.md).

## <a name="scheduling-options"></a>Opções de agendamento
Você pode executar a operação de cópia uma vez ou segundo um agendamento (por hora, por dia e assim por diante). Essas opções podem ser usadas para os conectores em ambientes diferentes, incluindo área de trabalho local e na nuvem. 

Uma operação de cópia única permite, uma única vez, a movimentação de dados de uma origem para um destino. Ela se aplica aos dados de qualquer tamanho e em qualquer formato com suporte. A cópia programada permite copiar dados com uma recorrência que você especificar. Você pode aproveitar as configurações avançadas (como repetição, tempo limite, alertas etc.) para configurar a cópia agendada.

![Opções de agendamento](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Próximas etapas
Experimente estes tutoriais que usam a ferramenta Copiar Dados:

- [Início Rápido: Criar um data factory usando a ferramenta Copiar Dados](quickstart-create-data-factory-copy-data-tool.md)
- [Tutorial: Copiar dados no Azure usando a ferramenta Copiar Dados](tutorial-copy-data-tool.md) 
- [Tutorial: Copiar dados locais para o Azure usando a ferramenta Copiar Dados](tutorial-hybrid-copy-data-tool.md)
