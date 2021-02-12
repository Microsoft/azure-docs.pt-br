---
title: Data Factory o assistente de cópia do Azure
description: Saiba mais sobre como usar o Assistente de Cópia do Azure Data Factory para copiar dados de fontes de dados com suporte para coletores.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 99c7490c4bf842da119a3a0abd4623c64d720ac8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462210"
---
# <a name="azure-data-factory-copy-wizard"></a>Assistente de Cópia do Azure Data Factory

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

O Assistente de Cópia do Azure Data Factory facilita o processo de ingerir dados, que geralmente é a primeira etapa no cenário de integração completa de dados. Ao executar o Assistente de Cópia do Azure Data Factory, você não precisa entender qualquer definição de JSON para serviços vinculados, conjuntos de dados e pipelines. O assistente cria automaticamente um pipeline para copiar dados da fonte de dados selecionada para o destino selecionado. Além disso, o Assistente de Cópia ajuda você a validar os dados que estão sendo ingeridos no momento da criação. Isso poupa tempo, especialmente quando você está ingerindo dados pela primeira vez da fonte de dados. Para iniciar o Assistente de Cópia, clique no bloco **Copiar dados** na home page da sua fábrica de dados.

![Assistente de Cópia](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Projetado para Big Data
Esse assistente permite mover dados facilmente de uma ampla variedade de fontes para destinos em minutos. Depois de concluir o assistente, um pipeline com uma atividade de cópia é criado automaticamente para você com entidades de Data Factory dependentes (serviços vinculados e conjuntos de dados). Não há etapas adicionais necessárias para criar o pipeline.   

![Selecionar fonte de dados](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Para obter as instruções passo a passo de como criar um pipeline de exemplo para copiar dados de um blob do Azure em uma tabela de Banco de Dados SQL do Azure, veja o [tutorial do Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md).

O assistente foi projetado com o Big Data em mente desde o começo, com suporte para diversos tipos de dados e objeto. Você pode criar pipelines do Data Factory que movem centenas de pastas, arquivos ou tabelas. O assistente permite a visualização automática de dados, a captura e o mapeamento de esquemas, e a filtragem de dados.

## <a name="automatic-data-preview"></a>Visualização automática de dados
É possível visualizar parte dos dados na fonte de dados selecionada para validar se os dados são o que você deseja copiar. Além disso, se os dados da fonte estiverem em um arquivo de texto, o Assistente de Cópia analisará o arquivo de texto para aprender automaticamente sobre o esquema e os delimitadores de linha e coluna.

![Configurações de formato de arquivo](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Captura e mapeamento do esquema
O esquema de dados de entrada pode não coincidir com o esquema dos dados de saída em alguns casos. Nesse cenário, você precisa mapear as colunas do esquema de origem para colunas do esquema de destino.

> [!TIP]
> Ao copiar dados do SQL Server ou do banco de dados SQL do Azure para o Azure Synapse Analytics, se a tabela não existir no repositório de destino, Data Factory suporte à criação de tabela automática usando o esquema de origem. Saiba mais em [mover dados de e para a análise de Synapse do Azure usando Azure data Factory](./data-factory-azure-sql-data-warehouse-connector.md).

Use uma lista suspensa para selecionar uma coluna do esquema de origem a ser mapeada para uma coluna no esquema de destino. O Assistente de Cópia tenta entender seu padrão para mapeamento de coluna. Ele aplica o mesmo padrão ao restante das colunas, de modo que você não precisa selecionar cada uma das colunas individualmente para concluir o mapeamento do esquema. Se preferir, você pode substituir esses mapeamentos usando as listas suspensas para mapear as colunas individualmente. O padrão se torna mais preciso à medida que você mapeia mais colunas. O Assistente de Cópia atualiza constantemente o padrão e, por fim, atinge o padrão certo para o mapeamento de coluna que você deseja atingir.     

![Mapeamento de esquema](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrando dados
É possível filtrar os dados de origem para selecionar apenas os dados que precisam ser copiados no repositório de dados do coletor. A filtragem reduz o volume de dados a ser copiado para o repositório de dados do coletor e, portanto, melhora a produtividade da operação de cópia. Ela fornece uma maneira flexível de filtrar dados em um banco de dados relacional usando a linguagem de consulta SQL ou arquivos em uma pasta de blobs do Azure usando [variáveis e funções do Data Factory](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtragem de dados em um banco de dados
A captura de tela a seguir mostra uma consulta SQL usando a função `Text.Format` e a variável `WindowStart`.

![Validar expressões](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtragem de dados em uma pasta de blobs do Azure
Você pode usar variáveis no caminho da pasta para copiar dados de uma pasta determinada em runtime com base nas [variáveis do sistema](data-factory-functions-variables.md#data-factory-system-variables). As variáveis com suporte são: **{ano}**, **{mês}**, **{dia}**, **{hora}**, **{minuto}** e **{personalizado}**. Por exemplo: pastadeentrada/{ano}/{mês}/{dia}.

Suponha que você tenha pastas de entrada no seguinte formato:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Clique no botão **Procurar** de **Arquivo ou pasta**, navegue até uma dessas pastas (por exemplo, 2016->03->01->02) e clique em **Escolher**. Você verá `2016/03/01/02` na caixa de texto. Agora, substitua **2016** por **{ano}**, **03** por **{mês}**, **01** por **{dia}**, **02** por **{hora}** e pressione a tecla **Tab**. Você deverá ver listas suspensas para escolher o formato dessas quatro variáveis:

![Usando variáveis de sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Conforme mostrado na captura de tela abaixo, você também pode usar uma variável **personalizada** , além de usar qualquer [cadeia de caracteres com formato compatível](/dotnet/standard/base-types/custom-date-and-time-format-strings). Para selecionar uma pasta com essa estrutura, use primeiro o botão **Procurar** . Em seguida, substitua um valor por **{personalizado}** e pressione a tecla **Tab** para ver a caixa de texto em que você poderá digitar a cadeia de caracteres de formato.     

![Usando variáveis personalizadas](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Opções de agendamento
Você pode executar a operação de cópia uma vez ou segundo um agendamento (por hora, por dia e assim por diante). Ambas as opções podem ser usadas para a ampla variedade de conectores em ambientes, incluindo cópia de área de trabalho local, da nuvem e locais.

Uma operação de cópia única permite, uma única vez, a movimentação de dados de uma origem para um destino. Ela se aplica aos dados de qualquer tamanho e em qualquer formato com suporte. A cópia programada permite copiar dados com uma recorrência prescrita. Você pode aproveitar as configurações avançadas (como repetição, tempo limite, alertas etc.) para configurar a cópia agendada.

![Propriedades de agendamento](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="troubleshooting"></a>Solução de problemas

Esta seção explora métodos comuns de solução de problemas do assistente de cópia no Azure Data Factory.

> [!NOTE] 
> Essas dicas de solução de problemas se aplicam ao assistente de cópia na versão 1 do Data Factory. Para Data Factory v2, consulte o guia de solução de problemas em [Azure data Factory de solução de problemas](../data-factory-ux-troubleshoot-guide.md).

### <a name="error-code-unable-to-validate-in-copy-wizard"></a>Código de erro: não é possível validar no assistente de cópia

- **Sintomas**: na primeira etapa do assistente de cópia, você encontra a mensagem de aviso "não é possível validar".
- **Causas**: isso pode acontecer quando todos os cookies de terceiros estiverem desabilitados.
- **Resolução**: 
    - Use o Internet Explorer ou o navegador Microsoft Edge.
    - Se você estiver usando o navegador Chrome, siga as instruções abaixo para adicionar a exceção de cookies para *microsoftonline.com* e *Windows.net*.
        1.  Abra o navegador Chrome.
        2.  Clique na chave inglesa ou em três linhas à direita (personalizar e controlar o Google Chrome).
        3.  Clique em **Configurações**.
        4.  Pesquise **cookies** ou vá para **privacidade** em configurações avançadas.
        5.  Selecione **configurações de conteúdo**.    
        6.  Os cookies devem ser definidos para **permitir que os dados locais sejam definidos (recomendado)**.
        7.  Clique em **gerenciar exceções**. Em **padrão de nome de host** , insira o seguinte e certifique-se de **que permitir** é o conjunto de comportamento.
            - login.microsoftonline.com
            - login.windows.net
        8.  Feche o navegador e reinicie.
    - Se você estiver usando o navegador Firefox, siga as instruções abaixo para adicionar a exceção de cookies.
        1. No menu Firefox, vá para **ferramentas**  >  **Opções**.
        2. Em **Privacy**  >  **histórico** de privacidade, seu pode ver que a configuração atual é **usar configurações personalizadas para o histórico**.
        3. Em **aceitar cookies de terceiros**, sua configuração atual pode **nunca** ser, em seguida, clique em **exceções** à direita para adicionar os sites a seguir.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Feche o navegador e reinicie. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Código de erro: não é possível abrir a página de logon e digitar a senha

- **Sintomas**: o assistente de cópia redireciona você para a página de logon, mas a página de logon não é mostrada com êxito.
- **Causas**: esse problema pode ocorrer se você alterou o ambiente de rede da rede do Office para a rede doméstica. Há alguns caches em navegadores. 
- **Resolução**: 
    1.  Feche o navegador e tente novamente. Vá para a próxima etapa se o problema ainda existir.   
    2.  Se você estiver usando o navegador Internet Explorer, tente abri-lo no modo particular (pressione "CTRL" + "Shift" + "P"). Se você estiver usando o navegador Chrome, tente abri-lo no modo Incognito (pressione "CTRL" + "Shift" + "N"). Vá para a próxima etapa se o problema ainda existir. 
    3.  Tente usar outro navegador. 


## <a name="next-steps"></a>Próximas etapas
Para ver um passo a passo rápido sobre como usar o Assistente de Cópia do Data Factory para criar um pipeline com uma Atividade de Cópia, confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md).