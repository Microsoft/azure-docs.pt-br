---
title: "Transformação Dividir Coluna por Exemplo usando o Azure Machine Learning Workbench"
description: "O documento de referência para a transformação “Dividir Coluna por Exemplo”"
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
ms.openlocfilehash: 013c99045621e4651a44ab99c9f695fff6004654
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="split-column-by-example-transformation"></a>Transformação Dividir coluna por exemplo
Essa transformação divide preditivamente o conteúdo de uma coluna em limites significativos sem a necessidade de entrada do usuário. O algoritmo de divisão seleciona os limites depois de analisar o conteúdo da coluna. Esses limites podem ser definidos por
* Um delimitador fixo,
* Vários delimitadores arbitrários que aparecem em contextos específicos ou
* Padrões de dados ou certos tipos de entidade

Os usuários também podem controlar o comportamento de divisão no Modo avançado, em que podem especificar os delimitadores ou fornecem exemplos da divisão desejada.

Em teoria, as operações de divisão também podem ser executadas no Workbench usando uma série de transformações *Derivar Coluna por Exemplo*. No entanto, se houver várias colunas, derivar cada uma delas individualmente mesmo usando a abordagem por exemplo pode ser muito demorado. A divisão preditiva permite a divisão fácil sem o usuário precisar fornecer exemplos para cada uma das colunas. 

## <a name="how-to-perform-this-transformation"></a>Como executar essa transformação

1. Selecione a coluna que deseja dividir. 
2. Selecione **Dividir Coluna por Exemplo** no menu **Transformações**. Ou clique com o botão direito do mouse no cabeçalho da coluna selecionada e selecione **Dividir Coluna por Exemplo** no menu de contexto. O Editor de Transformação é aberto e as novas colunas são adicionadas ao lado da coluna selecionada. Neste ponto, o Workbench analisa a coluna de entrada determina os limites de divisão e sintetiza um programa para dividir a coluna, conforme exibido na grade. O programa sintetizado é executado em todas as linhas na coluna. Os delimitadores, se houver, são excluídos do resultado final.
3. Você pode clicar no **Modo avançado** para ter maior controle sobre a transformação de divisão. 
4. Examine a saída e clique em **OK** para aceitar a transformação. 

A transformação tem como objetivo produzir o mesmo número de colunas resultantes para todas as linhas. Se qualquer linha não puder ser dividida nos limites determinados, ela gerará *null* para todas as colunas por padrão. Esse comportamento pode ser alterado no **Modo Avançado**.

### <a name="transform-editor-advanced-mode"></a>Editor de transformação: modo avançado
O **Modo Avançado** fornece uma experiência mais avançada para a divisão de colunas. 

Selecionar **Manter o Delimitador de Colunas** inclui os delimitadores no resultado final. Por padrão, os delimitadores são excluídos.

Especificar **Delimitadores** substitui a lógica de seleção automática de delimitadores. Vários delimitadores, um em cada linha, podem ser especificados como **Delimitadores**. Todos os caracteres são usados como delimitadores para dividir a coluna.

Às vezes, dividir um valor em determinados limites produz um número diferente de colunas que a maioria dos outros. Nesses casos, a **Direção de Preenchimento** é usada para decidir a ordem na qual as colunas devem ser preenchidas.

Clicar em **Mostrar exemplos sugeridos** mostra as linhas representativas para a qual o usuário deve fornecer um exemplo de divisão. O usuário pode clicar na seta **Para cima** à direita da linha sugerida para promover a linha como um exemplo. 

O usuário pode **Excluir Coluna** ou **Inserir novas Colunas** clicando com o botão direito do mouse no cabeçalho da **Tabela de Exemplos**.

O usuário pode copiar e colar os valores de uma célula para outra para fornecer um exemplo de divisão.

O usuário pode mudar entre o **Modo Básico** e o **Modo Avançado** clicando nos links no Editor de Transformação.

### <a name="editing-an-existing-transformation"></a>Editando uma transformação existente

Um usuário pode editar uma transformação **Dividir Coluna por Exemplo** existente selecionando a opção **Editar** da Etapa de Transformação. Clicar em **Editar** abre o Editor de Transformação no **Modo Avançado** e todos os exemplos que foram fornecidos durante a criação da transformação são mostrados.

## <a name="examples-of-splitting-on-a-fixed-single-character-delimiter"></a>Exemplos de divisão em um delimitador fixo de caractere único

É comum para os campos de dados serem separados por um único delimitador fixo, como uma vírgula em um formato CSV. A transformação de divisão tenta inferir esses delimitadores automaticamente. Por exemplo, no cenário a seguir ele automaticamente infere o "." como um delimitador.

### <a name="splitting-ip-addresses"></a>Dividindo endereços IP

Os valores na primeira coluna são divididos preditivamente em quatro colunas.

|IP|IP_1|IP_2|IP_3|IP_4|
|:-----|:-----|:-----|:-----|:-----|
|192.168.0.102|192|168|0|102|
|192.138.0.101|192|138|0|101|
|192.168.0.102|192|168|0|102|
|192.158.1.202|192|158|1|202|
|192.168.0.102|192|168|0|102|
|192.169.1.102|192|169|1|102|

## <a name="examples-of-splitting-on-multiple-delimiters-within-particular-contexts"></a>Exemplos de divisão em vários delimitadores em contextos específicos

Os dados do usuário podem incluir vários delimitadores diferentes separando campos diferentes. Além disso, apenas algumas ocorrências de uma cadeia de caracteres de delimitação podem ser um delimitador, mas não todas. Por exemplo, no caso a seguir o conjunto de delimitadores necessário é "-","," e ":" para produzir a saída desejada. No entanto, nem toda ocorrência de ":" deve ser um ponto de divisão, pois não queremos dividir a hora, mas mantê-la em uma única coluna. A transformação de divisão infere os delimitadores nos contextos em que ocorrem nos dados de entrada em vez de qualquer possível ocorrência do delimitador. A transformação também está ciente dos tipos de dados comuns, como datas e horas.   

### <a name="splitting-store-opening-timings"></a>Divisão dos horários de abertura de loja

Os valores na coluna *Timings* a seguir são divididos preditivamente em nove colunas mostradas na tabela abaixo dela.

|Timings|
|:-----|
|Monday - Friday: 7:00 am - 6:00 pm,Saturday: 9:00 am - 5:00 pm,Sunday: Closed|
|Monday - Friday: 9:00 am - 6:00 pm,Saturday: 4:00 am - 4:00 pm,Sunday: Closed|
|Monday - Friday: 8:30 am - 7:00 pm,Saturday: 3:00 am - 2:30 pm,Sunday: Closed|
|Monday - Friday: 8:00 am - 6:00 pm,Saturday: 2:00 am - 3:00 pm,Sunday: Closed|
|Monday - Friday: 4:00 am - 7:00 pm,Saturday: 9:00 am - 4:00 pm,Sunday: Closed|
|Monday - Friday: 8:30 am - 4:30 pm,Saturday: 9:00 am - 5:00 pm,Sunday: Closed|
|Monday - Friday: 5:30 am - 6:30 pm,Saturday: 5:00 am - 4:00 pm,Sunday: Closed|
|Monday - Friday: 8:30 am - 8:30 pm,Saturday: 6:00 am - 5:00 pm,Sunday: Closed|
|Monday - Friday: 8:00 am - 9:00 pm,Saturday: 9:00 am - 8:00 pm,Sunday: Closed|
|Monday - Friday: 10:00 am - 9:30 pm,Saturday: 9:30 am - 3:00 pm,Sunday: Closed|

|Timings_1|Timings_2|Timings_3|Timings_4|Timings_5|Timings_6|Timings_7|Timings_8|Timings_9|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|Monday|Friday|7:00 am|6:00 pm|Sábado|9:00 am|5:00 pm|Sunday|Fechado|
|Monday|Friday|9:00 am|6:00 pm|Sábado|4:00 am|4:00 pm|Sunday|Fechado|
|Monday|Friday|8:30 am|7:00 pm|Sábado|3:00 am|2:30 pm|Sunday|Fechado|
|Monday|Friday|8:00 am|6:00 pm|Sábado|2:00 am|3:00 pm|Sunday|Fechado|
|Monday|Friday|4:00 am|7:00 pm|Sábado|9:00 am|4:00 pm|Sunday|Fechado|
|Monday|Friday|8:30 am|4:30 pm|Sábado|9:00 am|5:00 pm|Sunday|Fechado|
|Monday|Friday|5:30 am|6:30 pm|Sábado|5:00 am|4:00 pm|Sunday|Fechado|
|Monday|Friday|8:30 am|8:30 pm|Sábado|6:00 am|5:00 pm|Sunday|Fechado|
|Monday|Friday|8:00 am|9:00 pm|Sábado|9:00 am|8:00 pm|Sunday|Fechado|
|Monday|Friday|10:00 am|9:30 pm|Sábado|9:30 am|3:00 pm|Sunday|Fechado|

### <a name="splitting-iis-log"></a>Divisão de log de IIS

Aqui está outro exemplo de vários delimitadores arbitrários. Este exemplo também inclui um delimitador contextual "/", que não deve ser dividido em URLs ou caminhos de arquivo. É entediante executar essa divisão usando muitas transformações *Derivar Coluna por Exemplo* e fornecer exemplos para cada campo. Usando a transformação de divisão, podemos executar a divisão preditiva sem fornecer nenhum exemplo.

|logtext|
|:-----|
|192.128.138.20 - - [16/Oct/2016 16:22:33 -0200] "GET /images/picture.gif HTTP/1.1" 234 343 www.yahoo.com "http://www.example.com/" "Mozilla/4.0 (compatible; MSIE 4)" "-"|
|10.128.72.213 - - [17/Oct/2016 12:43:12 +0300] "GET /news/stuff.html HTTP/1.1" 200 6233 www.aol.com "http://www.sample.com/" "Mozilla/5.0 (MSIE)" "-"|
|192.165.71.165 - - [12/Nov/2016 14:22:44 -0500] "GET /sample.ico HTTP/1.1" 342 7342 www.facebook.com "-" "Mozilla/5.0 (Windows; U; Windows NT 5.1; rv:1.7.3)" "-"|
|10.166.64.165 - - [23/Nov/2016 01:52:45 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.167.1.193 - - [16/Jan/2017 22:34:56 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.147.76.193 - - [28/Jan/2017 26:36:16 +0800] "GET /search.php HTTP/1.1" 400 1777 www.bing.com "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)" "-"|
|192.166.64.165 - - [23/Mar/2017 01:55:25 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|11.167.1.193 - - [16/Apr/2017 11:34:36 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|

É dividido em:

|logtext_1|logtext_2|logtext_3|logtext_4|logtext_5|logtext_6|logtext_7|logtext_8|logtext_9|logtext_10|logtext_11|logtext_12|logtext_13|logtext_14|logtext_15|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|192.128.138.20|16/Oct/2016|16:22:33|-0200|GET|images/picture.gif|HTTP|1,1|234|343|www.yahoo.com|http://www.example.com/|Mozilla|4,0|compatible; MSIE 4|
|10.128.72.213|17/Oct/2016|12:43:12|+0300|GET|news/stuff.html|HTTP|1,1|200|6233|www.aol.com|http://www.sample.com/|Mozilla|5.0|MSIE|
|192.165.71.165|12/Nov/2016|14:22:44|-0500|GET|sample.ico|HTTP|1,1|342|7342|www.facebook.com|-|Mozilla|5.0|Windows; U; Windows NT 5.1; rv:1.7.3|
|10.166.64.165|23/Nov/2016|01:52:45|-0800|GET|style.css|HTTP|1,1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|192.167.1.193|16/Jan/2017|22:34:56|+0200|GET|js/ads.js|HTTP|1,1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|
|192.147.76.193|28/Jan/2017|26:36:16|+0800|GET|search.php|HTTP|1,1|400|1777|www.bing.com|-|Mozilla|4,0|compatible; MSIE 6.0; Windows NT 5.1|
|192.166.64.165|23/Mar/2017|01:55:25|-0800|GET|style.css|HTTP|1,1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|11.167.1.193|16/Apr/2017|11:34:36|+0200|GET|js/ads.js|HTTP|1,1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|

## <a name="examples-of-splitting-without-delimiters"></a>Exemplos de divisão sem delimitadores
Em alguns casos, não há nenhum delimitador reais e os campos de dados podem ocorrer de forma contígua ao lado um do outro. Nesse caso, a transformação de divisão automaticamente detecta padrões nos dados para inferir pontos de divisão prováveis. Por exemplo, no cenário a seguir, desejamos separar o valor do tipo de moeda e a divisão infere automaticamente o limite entre os dados numéricos e os não numéricos como o ponto de divisão.

### <a name="splitting-amount-with-currency-symbol"></a>Dividindo o valor do símbolo da moeda

|Amount|Amount_1|Amount_2|
|:-----|:-----|:-----|
|\$14|$|14|
|£9|£|9|
|\$34|$|34|
|€ 18|€ |18|
|\$42|$|42|
|\$7|$|7|
|£42|£|42|
|\$16|$|16|
|€ 16|€ |16|
|\$15|$|15|
|\$16|$|16|
|€ 64|€ |64|

No exemplo a seguir, gostaríamos de separar os valores de peso das unidades de medida. Novamente a inferência da divisão detecta o limite significativo automaticamente e o prefere em relação a outros delimitadores possíveis como o caractere ".". 

### <a name="splitting-weights-with-units"></a>Dividindo os pesos com unidades

|Peso|Weight_1|Weight_2|
|:-----|:-----|:-----|
|2,27 KG|2.27|KG|
|1L|1|L|
|2,5 KG|2.5|KG|
|2 KG|2|KG|
|1.7KGA|1.7|KGA|
|3 KG|3|KG|
|2 KG|2|KG|
|125G|125|G|
|500G|500|G|
|1.5KGA|1.5|KGA|

## <a name="technical-notes"></a>Observações técnicas

O recurso de transformação de divisão se baseia na técnica de **Síntese de Programa Preditiva**. Nessa técnica, os programas de transformação de dados são aprendidos automaticamente com base nos dados de entrada. Os programas são sintetizados em uma linguagem específica de domínio. O DSL se baseia nos delimitadores e campos que ocorrem em contextos de expressão regular específicos. É possível encontrar mais informações sobre essa tecnologia em uma [publicação recente sobre esse tópico](https://www.microsoft.com/en-us/research/publication/automated-data-extraction-using-predictive-program-synthesis/). 
