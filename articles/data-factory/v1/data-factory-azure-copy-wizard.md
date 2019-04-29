---
title: Assistente de Cópia do Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como usar o Assistente de Cópia do Azure Data Factory para copiar dados de fontes de dados com suporte para coletores.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d13e304b0d10e8bd34d306426f1f9164bcc6be94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567666"
---
# <a name="azure-data-factory-copy-wizard"></a>Assistente de Cópia do Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

O Assistente de Cópia do Azure Data Factory facilita o processo de ingerir dados, que geralmente é a primeira etapa no cenário de integração completa de dados. Ao executar o Assistente de Cópia do Azure Data Factory, você não precisa entender qualquer definição de JSON para serviços vinculados, conjuntos de dados e pipelines. O assistente cria automaticamente um pipeline para copiar dados da fonte de dados selecionada para o destino selecionado. Além disso, o Assistente de Cópia ajuda você a validar os dados que estão sendo ingeridos no momento da criação. Isso poupa tempo, especialmente quando você está ingerindo dados pela primeira vez da fonte de dados. Para iniciar o Assistente de Cópia, clique no bloco **Copiar dados** na home page da sua fábrica de dados.

![Assistente de Cópia](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Projetado para Big Data
Esse assistente permite mover dados facilmente de uma ampla variedade de fontes para destinos em minutos. Depois de concluir o assistente, um pipeline com uma atividade de cópia é criado automaticamente para você com entidades de Data Factory dependentes (serviços vinculados e conjuntos de dados). Não há etapas adicionais necessárias para criar o pipeline.   

![Selecione uma fonte de dados](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Para obter as instruções passo a passo de como criar um pipeline de exemplo para copiar dados de um blob do Azure em uma tabela de Banco de Dados SQL do Azure, veja o [tutorial do Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md).
>
>

O assistente foi projetado com o Big Data em mente desde o começo, com suporte para diversos tipos de dados e objeto. Você pode criar pipelines do Data Factory que movem centenas de pastas, arquivos ou tabelas. O assistente permite a visualização automática de dados, a captura e o mapeamento de esquemas, e a filtragem de dados.

## <a name="automatic-data-preview"></a>Visualização automática de dados
É possível visualizar parte dos dados na fonte de dados selecionada para validar se os dados são o que você deseja copiar. Além disso, se os dados da fonte estiverem em um arquivo de texto, o Assistente de Cópia analisará o arquivo de texto para aprender automaticamente sobre o esquema e os delimitadores de linha e coluna.

![Configurações de formato de arquivo](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Captura e mapeamento do esquema
O esquema de dados de entrada pode não coincidir com o esquema dos dados de saída em alguns casos. Nesse cenário, você precisa mapear as colunas do esquema de origem para colunas do esquema de destino.

> [!TIP]
> Ao copiar dados do Banco de Dados SQL do Azure ou do SQL Server para o Azure SQL Data Warehouse, se a tabela não existir no repositório de destino, o suporte do Data Factory criará a tabela automaticamente usando o esquema da origem. Saiba mais sobre [Mover dados para e do SQL Data Warehouse do Azure usando o Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Use uma lista suspensa para selecionar uma coluna do esquema de origem a ser mapeada para uma coluna no esquema de destino. O Assistente de Cópia tenta entender seu padrão para mapeamento de coluna. Ele aplica o mesmo padrão ao restante das colunas, de modo que você não precisa selecionar cada uma das colunas individualmente para concluir o mapeamento do esquema. Se preferir, você pode substituir esses mapeamentos usando as listas suspensas para mapear as colunas individualmente. O padrão se torna mais preciso à medida que você mapeia mais colunas. O Assistente de Cópia atualiza constantemente o padrão e, por fim, atinge o padrão certo para o mapeamento de coluna que você deseja atingir.     

![Mapeamento de esquema](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrando dados
É possível filtrar os dados de origem para selecionar apenas os dados que precisam ser copiados no repositório de dados do coletor. A filtragem reduz o volume de dados a ser copiado para o repositório de dados do coletor e, portanto, melhora a produtividade da operação de cópia. Ela fornece uma maneira flexível de filtrar dados em um banco de dados relacional usando a linguagem de consulta SQL ou arquivos em uma pasta de blobs do Azure usando [variáveis e funções do Data Factory](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtragem de dados em um banco de dados
A captura de tela a seguir mostra uma consulta SQL usando a função `Text.Format` e a variável `WindowStart`.

![Validar expressões](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtragem de dados em uma pasta de blobs do Azure
Você pode usar variáveis no caminho da pasta para copiar dados de uma pasta determinada em tempo de execução com base nas [variáveis do sistema](data-factory-functions-variables.md#data-factory-system-variables). As variáveis com suporte são: **{ano}**, **{mês}**, **{dia}**, **{hora}**, **{minuto}** e **{personalizado}**. Por exemplo: pastadeentrada/{ano}/{mês}/{dia}.

Suponha que você tenha pastas de entrada no seguinte formato:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Clique no botão **Procurar** de **Arquivo ou pasta**, navegue até uma dessas pastas (por exemplo, 2016->03->01->02) e clique em **Escolher**. Você verá `2016/03/01/02` na caixa de texto. Agora, substitua **2016** por **{ano}**, **03** por **{mês}**, **01** por **{dia}**, **02** por **{hora}** e pressione a tecla **Tab**. Você deverá ver listas suspensas para escolher o formato dessas quatro variáveis:

![Usando variáveis de sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Conforme mostrado na captura de tela abaixo, você também pode usar uma variável **personalizada** , além de usar qualquer [cadeia de caracteres com formato compatível](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Para selecionar uma pasta com essa estrutura, use primeiro o botão **Procurar** . Em seguida, substitua um valor por **{personalizado}** e pressione a tecla **Tab** para ver a caixa de texto em que você poderá digitar a cadeia de caracteres de formato.     

![Usando variáveis personalizadas](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Opções de agendamento
Você pode executar a operação de cópia uma vez ou segundo um agendamento (por hora, por dia e assim por diante). Ambas as opções podem ser usadas para a ampla variedade de conectores em ambientes, incluindo cópia de área de trabalho local, da nuvem e locais.

Uma operação de cópia única permite, uma única vez, a movimentação de dados de uma origem para um destino. Ela se aplica aos dados de qualquer tamanho e em qualquer formato com suporte. A cópia programada permite copiar dados com uma recorrência prescrita. Você pode aproveitar as configurações avançadas (como repetição, tempo limite, alertas etc.) para configurar a cópia agendada.

![Propriedades de agendamento](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Próximas etapas
Confira um breve passo a passo sobre como usar o Assistente de Cópia do Data Factory para criar um pipeline com uma Atividade de Cópia no [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md).
