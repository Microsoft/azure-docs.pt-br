---
title: "Copiar dados facilmente com o Assistente de Cópia - Azure | Microsoft Docs"
description: "Saiba mais sobre como usar o Assistente de Cópia do Data Factory para copiar dados de fontes de dados com suporte para coletores."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 2302f4bfe98c3e731685f5c63774b060caa8c3a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Copiar ou mover dados facilmente com o Assistente de Cópia do Azure Data Factory
O Assistente de cópia do Azure Data Factory serve para facilitar o processo de ingestão de dados, que geralmente é uma primeira etapa em um cenário de integração de dados de ponta a ponta. Ao executar o Assistente de cópia do Azure Data Factory, você não precisa entender nenhuma definição de JSON para serviços vinculados, conjuntos de dados e pipelines. No entanto, depois de concluir todas as etapas no assistente, o assistente cria automaticamente um pipeline para copiar dados da fonte de dados selecionada para o destino selecionado. Além disso, o Assistente de cópia ajuda a validar os dados que estão sendo ingeridos no momento da criação, o que poupa muito tempo, especialmente quando você estiver ingerindo dados pela primeira vez da fonte de dados. Para iniciar o Assistente de Cópia, clique no bloco **Copiar dados** na home page da sua fábrica de dados.

![Assistente de Cópia](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Um assistente intuitivo para copiar dados
Este assistente permite mover dados facilmente de uma ampla variedade de fontes para destinos em minutos. Depois de concluir o assistente, um pipeline com uma atividade de cópia é criado automaticamente para você com entidades de Data Factory dependentes (serviços vinculados e conjuntos de dados). Não há etapas adicionais necessárias para criar o pipeline.   

![Selecione uma fonte de dados](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Consulte artigo [tutorial do Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter instruções passo a passo de como criar um pipeline de exemplo para copiar dados de um blob do Azure para uma tabela de banco de dados SQL do Azure. 
> 
> 

A assistente foi projetado tendo em mente Big Data desde o início. É simples e eficiente de criar pipelines de Data Factory que movem centenas de pastas, arquivos ou tabelas usando o assistente de cópia de dados. O assistente oferece suporte a estes três recursos: visualização de dados automática, captura e mapeamento do esquema e filtragem de dados. 

## <a name="automatic-data-preview"></a>Visualização de dados automática
O Assistente de cópia permite que você examine parte dos dados da fonte de dados selecionada para validar se os dados são os dados certos que você deseja copiar. Além disso, se a fonte de dados estiver em um arquivo de texto, o assistente de cópia analisa o arquivo de texto para saber o esquema e os delimitadores de coluna e linha automaticamente. 

![Configurações de formato de arquivo](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Captura e mapeamento do esquema
O esquema de dados de entrada pode não coincidir com o esquema dos dados de saída em alguns casos. Nesse cenário, você precisa mapear as colunas do esquema de origem para colunas do esquema de destino. 

O assistente de cópia automaticamente mapeia colunas no esquema de origem para colunas no esquema de destino. Você pode substituir os mapeamentos usando as listas suspensas (ou) especificar se uma coluna precisa ser ignorada durante a cópia dos dados.   

![Mapeamento de esquema](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrando dados
O assistente permite filtrar os dados de origem para selecionar apenas os dados que precisam ser copiados para o armazenamento de dados de destino/coletor. A filtragem reduz o volume de dados a ser copiado para o armazenamento de dados do coletor e, portanto, melhora a taxa de transferência da operação de cópia. Ela fornece uma maneira flexível para filtrar dados em um banco de dados relacional usando SQL (ou) arquivos em uma pasta de blobs do Azure usando [variáveis e funções de Data Factory](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtragem de dados em um banco de dados
No exemplo, a consulta SQL usa a função `Text.Format` e variável `WindowStart`. 

![Validar expressões](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtragem de dados em uma pasta de blobs do Azure
Você pode usar variáveis no caminho da pasta para copiar dados de uma pasta determinada em tempo de execução com base nas [variáveis do sistema](data-factory-functions-variables.md#data-factory-system-variables). As variáveis com suporte são: **{ano}**, **{mês}**, **{dia}**, **{hora}**, **{minuto}** e **{personalizado}**. Exemplo: pastadeentrada/{ano}/{mês}/{dia}.

Suponha que você tenha pastas de entrada no seguinte formato:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Clique no botão **Procurar** de **Arquivo ou pasta**, navegue até uma dessas pastas (por exemplo, 2016->03->01->02) e clique em **Escolher**. Você verá `2016/03/01/02` na caixa de texto. Agora, substitua **2016** por **{ano}**, **03** por **{mês}**, **01** por **{dia}**, **02** por **{hora}** e pressione Tab. Você deverá ver listas suspensas para escolher o formato dessas quatro variáveis:

![Usando variáveis de sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Conforme mostrado na captura de tela abaixo, você também pode usar uma variável **personalizada** , além de usar qualquer [cadeia de caracteres com formato compatível](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Para selecionar uma pasta com essa estrutura, use primeiro o botão **Procurar** . Em seguida, substitua um valor com **{personalizado}**e pressione Tab para ver a caixa de texto em que você poderá digitar a cadeia de caracteres de formato.     

![Usando variáveis personalizadas](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Suporte para tipos de dados e objetos diversificados
Usando o Assistente para cópia, você pode mover com eficiência centenas de pastas, arquivos ou tabelas.

![Selecione as tabelas das quais copiar dados](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Opções de agendamento
Você pode executar a operação de cópia uma vez ou segundo um agendamento (por hora, por dia e assim por diante). Ambas essas opções podem ser usadas para a variedade de conectores em cópias de área de trabalho local, da nuvem e locais.

Uma operação de cópia única permite, uma única vez, a movimentação de dados de uma origem para um destino. Ela se aplica aos dados de qualquer tamanho e em qualquer formato com suporte. A cópia programada permite copiar dados com uma recorrência prescrita. Você pode aproveitar as configurações avançadas (como repetição, tempo limite, alertas etc.) para configurar a cópia agendada.

![Propriedades de agendamento](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Próximas etapas
Para ver um passo a passo rápido sobre como usar o Assistente de Cópia do Data Factory para criar um pipeline com uma Atividade de Cópia, confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md).

