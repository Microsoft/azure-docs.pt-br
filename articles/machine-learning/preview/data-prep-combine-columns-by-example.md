---
title: "Transformação Combinar Colunas por Exemplo usando o Azure Machine Learning Workbench"
description: "O documento de referência para a transformação “Combinar Colunas por Exemplo”"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 3ca1710c969b9bc5a1f56dc53f52c706e1ed07cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="combine-columns-by-example-transformation"></a>Transformação Combinar colunas por exemplo
Essa transformação permite que o usuário adicione uma nova coluna combinando valores de várias colunas. O usuário pode especificar um separador ou fornecer exemplos de valores combinados para realizar essa transformação. Quando o usuário fornece exemplos de combinação, a transformação é tratada pelo mesmo mecanismo **Por exemplo** usado na transformação **Derivar coluna por exemplo**.

## <a name="how-to-perform-this-transformation"></a>Como executar essa transformação

Para executar essa transformação, siga essas etapas:
1. Selecione duas ou mais colunas que você deseja combinar em uma coluna. 
2. Selecione **Combinar Colunas por Exemplo** no menu **Transformações**. Ou clique com o botão direito do mouse no cabeçalho de qualquer uma das colunas selecionadas e selecione **Combinar Colunas por Exemplo** no menu de contexto. O Editor de Transformação é aberto e uma nova coluna é adicionada ao lado da coluna selecionada mais à direita. A nova coluna contém os valores combinados separados por um separador padrão. As colunas selecionadas podem ser identificadas pelas caixas de seleção nos cabeçalhos de coluna. A adição e a remoção de colunas da seleção podem ser feitas usando as caixas de seleção.
3. Você pode atualizar o valor combinado na coluna recém-criada. O valor atualizado é usado como um exemplo para aprender a transformação.
4. Clique em **OK** para aceitar a transformação.

### <a name="transform-editor-advanced-mode"></a>Editor de transformação: modo avançado

O Modo Avançado fornece uma experiência mais avançada para a combinação de colunas. 

Selecionar **Separador** em **Combinar Colunas por** permite que o usuário especifique Cadeias de Caracteres na caixa de texto **Separador**. Saia da caixa de texto **Separador** para visualizar os resultados na grade de dados. Pressione **OK** para confirmar a transformação.

Selecionar **Exemplos** em **Combinar Colunas por** permite que o usuário forneça exemplos de valores combinados. Para promover uma linha como um exemplo, clique duas vezes nas linhas na grade. Digite a saída esperada na caixa de texto em relação à linha promovida. Saia da caixa de texto **Separador** para visualizar os resultados na grade de dados. Pressione **OK** para confirmar a transformação. 

O usuário pode mudar entre o **Modo Básico** e o **Modo Avançado** clicando nos links no Editor de Transformação.

### <a name="editing-existing-transformation"></a>Editando a transformação existente

Um usuário pode editar uma transformação **Combinar Coluna por Exemplo** existente selecionando a opção **Editar** da Etapa de Transformação. Clicar em **Editar** abre o Editor de Transformação no **Modo Básico**. O usuário pode entrar no **Modo Avançado** clicando no link no cabeçalho. Todos os exemplos que foram fornecidos durante a criação da transformação são mostrados lá.

## <a name="example-using-separators"></a>Exemplo de uso de separadores

Uma vírgula seguida por um espaço é usada como separador neste exemplo para combinar as colunas *Street*, *City*, *State* e *ZIP*.

|Street|City|Estado|ZIP|Coluna|
|:----|:----|:----|:----|:----|
|16011 N.E. 36th Way|REDMOND|WA|98052|16011 N.E. 36th Way, REDMOND, WA, 98052|
|16021 N.E. 36th Way|REDMOND|WA|98052|16021 N.E. 36th Way, REDMOND, WA, 98052|
|16031 N.E. 36th Way|REDMOND|WA|98052|16031 N.E. 36th Way, REDMOND, WA, 98052|
|16041 N.E. 36th Way|REDMOND|WA|98052|16041 N.E. 36th Way, REDMOND, WA, 98052|
|16051 N.E. 36th Way|REDMOND|WA|98052|16051 N.E. 36th Way, REDMOND, WA, 98052|
|16061 N.E. 36th Way|REDMOND|WA|98052|16061 N.E. 36th Way, REDMOND, WA, 98052|
|3460 157th Avenue NE|REDMOND|WA|98052|3460 157th Avenue NE, REDMOND, WA, 98052|
|3350 157th Ave N.E.|REDMOND|WA|98052|3350 157th Ave N.E., REDMOND, WA, 98052|
|3240 157th Avenue N.E.|REDMOND|WA|98052|3240 157th Avenue N.E., REDMOND, WA, 98052|

## <a name="example-using-by-example"></a>Exemplo usando a tecnologia de Por exemplo

O valor em **negrito** foi fornecido como um exemplo.

|Data|Mês|Ano|Hora|Minuto|Segundo|Coluna combinada|
|:----|:----|:----|:----|:----|:----|:----|
|13|Oct|2016|15|01|23|**13-Oct-2016 15:01:23 PDT**|
|16|Oct|2016|16|22|33|16-Oct-2016 15:01:33 PDT|
|17|Oct|2016|12|43|12|17-Oct-2016 15:01:12 PDT|
|12|Nov|2016|14|22|44|12-Nov-2016 15:01:44 PDT|
|23|Nov|2016|01|52|45|23-Nov-2016 15:01:45 PDT|
|16|Jan|2017|22|34|56|16-Jan-2016 15:01:56 PDT|
|23|Mar|2017|01|55|25|23-Mar-2016 15:01:25 PDT|
|16|Apr|2017|11|34|36|16-Apr-2016 15:01:36 PDT|

