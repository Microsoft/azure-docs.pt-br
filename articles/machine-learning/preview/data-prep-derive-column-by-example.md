---
title: "Transformação Derivar Coluna por Exemplo usando o Azure Machine Learning Workbench"
description: "O documento de referência para a transformação “Derivar Coluna por Exemplo”"
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
ms.openlocfilehash: a02f5e827345a1d28f01d691e1b6fbccfc03ae8a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="derive-column-by-example-transformation"></a>Transformação Derivar coluna por exemplo

A transformação **Derivar Coluna por Exemplo** permite que os usuários criem um derivado de uma ou mais colunas existentes usando os exemplos fornecidos pelo usuário do resultado derivado. A derivada pode ser qualquer combinação das transformações de cadeia de caracteres, de data e de número com suporte. 

As seguintes transformações de cadeia de caracteres, data e número têm suporte:

**Transformações de cadeia de caracteres:** 

Subcadeia de caracteres incluindo a extração inteligente de número e datas, concatenação, manipulação de caso e valores de constante de mapeamento.

**Transformações de data:** 

Alteração de formato de dada, extração de partes de data, hora de mapeamento para compartimentos de hora.

As transformações de data são razoavelmente genéricas com algumas limitações importantes:
* Os fusos horários não têm suporte.
* Alguns formatos comuns que não têm suporte:
    * Formato da semana do ano da ISO 8601 (por exemplo, “2009-W53-7”) 
    * Hora da época do Unix.
* Todos os formatos diferenciam maiúsculas de minúsculas (em especial "4am" não é reconhecido como uma hora, embora "4AM" seja).

**Transformações de número:** 

Arredondamento, base, limite máximo, compartimentalização, preenchimento com zeros ou espaço, divisão ou multiplicação por uma potência de 1000.

**Transformações de composição:** 

Qualquer combinação de transformações de cadeia de caracteres, número ou data.

## <a name="how-to-use-this-transformation"></a>Como usar essa transformação

Para executar essa transformação, siga essas etapas:
1. Selecione uma ou mais colunas das quais você deseja derivar o valor. 
2. Selecione **Derivar Coluna por Exemplo** no menu **Transformações**. Ou clique com o botão direito do mouse no cabeçalho de qualquer uma das colunas selecionadas e selecione **Derivar Coluna por Exemplo** no menu de contexto. O Editor de Transformação é aberto e uma nova coluna é adicionada ao lado da coluna selecionada mais à direita. As colunas selecionadas podem ser identificadas pelas caixas de seleção nos cabeçalhos de coluna. A adição e a remoção de colunas da seleção podem ser feitas usando as caixas de seleção nos cabeçalhos de coluna.
3. Digite um exemplo de como o *saída* em uma linha e pressione Enter. Neste ponto, o Workbench analisa a coluna de entrada, bem como a saída fornecida para sintetizar um programa que pode transformar as entradas fornecidas na saída. O programa sintetizado é executado em todas as linhas na grade de dados. Para casos ambíguos e complicados, podem ser necessários vários exemplos. Dependendo de se você está no Modo Básico ou no Modo Avançado, vários exemplos podem ser fornecidos de formas diferentes.
4. Examine a saída e clique em **OK** para aceitar a transformação.

### <a name="transform-editor-basic-mode"></a>Editor de transformação: modo básico

O Modo Básico fornece uma experiência de edição embutida na grade de dados. Você pode fornecer exemplos da saída navegando até a célula de interesse e digitando o valor. 

O Workbench analisa os dados e tenta identificar os casos extremos que devem ser examinados pelo usuário. Enquanto os dados estão sendo analisados, **Analisando Dados** é mostrado no cabeçalho do Editor de Transformação. Quando a análise é concluída, **Sem Sugestões** ou **Examinar a próxima linha sugerida** é exibido no cabeçalho. Você pode navegar pelos casos extremos clicando em **Examinar a próxima linha sugerida**. Caso o valor esteja incorreto para uma linha, você deverá digitar o valor correto como um exemplo adicional. 

### <a name="transform-editor-advanced-mode"></a>Editor de transformação: modo avançado

O Modo Avançado fornece uma experiência mais avançada para a Derivação de colunas por exemplo. Todos os exemplos são mostrados em um único lugar. Você também pode examinar todos os casos extremos em um só lugar clicando em **Mostrar exemplos sugeridos**. 

No modo avançado, você pode adicionar qualquer linha como uma linha de exemplo clicando duas vezes na linha na grade. Após uma linha ser copiada como uma linha de exemplo, você também pode editar os dados nas colunas de origem para criar um exemplo sintético. Fazendo isso, você pode adicionar casos que não estão presentes no momento nos dados de exemplo.

O usuário pode mudar entre o **Modo Básico** e o **Modo Avançado** clicando nos links no Editor de Transformação.

### <a name="editing-existing-transformation"></a>Editando a transformação existente

Um usuário pode editar uma transformação **Derivar Coluna por Exemplo** existente selecionando a opção **Editar** da Etapa de Transformação. Clicar em **Editar** abre o Editor de Transformação no **Modo Avançado** e todos os exemplos que foram fornecidos durante a criação da transformação são mostrados.

## <a name="examples-of-string-transformations-by-example"></a>Exemplos de transformações de cadeia de caracteres por exemplo


>[!NOTE] 
>Os valores em **negrito** representam os exemplos que foram fornecidos para concluir a transformação no conjunto de dados mostrado.


### <a name="s1-extracting-file-names-from-file-paths"></a>S1. Extraindo nomes de arquivo dos caminhos de arquivo

Número de exemplos que foram necessários para esse caso: 2

|Entrada|Saída|
|:-----|:-----|
|C:\Python35\Tools\pynche\TypeinViewer.py|**TypeinViewer.py**|
|C:\Python35\Tools\pynche\webcolors.txt|webcolors.txt|
|C:\Python35\Tools\pynche\websafe.txt|websafe.txt|
|C:\Python35\Tools\pynche\X\rgb.txt|rgb.txt|
|C:\Python35\Tools\pynche\X\xlicense.txt|xlicense.txt|
|C:\Python35\Tools\Scripts\2to3.py|2to3.py|
|C:\Python35\Tools\Scripts\analyze_dxp.py|**analyze_dxp.py**|
|C:\Python35\Tools\Scripts\byext.py|byext.py|
|C:\Python35\Tools\Scripts\byteyears.py|byteyears.py|
|C:\Python35\Tools\Scripts\checkappend.py|checkappend.py|

### <a name="s2-case-manipulation-during-string-extraction"></a>S2. Manipulação de caso durante a extração de cadeia de caracteres

Número de exemplos que foram necessários para esse caso: 3

|Entrada|Saída|
|:-----|:-----|
|REINDEER CT & DEAD END;  NEW HANOVER; Station 332; 2015-12-10 @ 17:10:52;|**New Hanover**|
|BRIAR PATH & WHITEMARSH LN;  HATFIELD TOWNSHIP; Station 345; 2015-12-10 @ 17:29:21;|Hatfield Township|
|HAWS AVE; NORRISTOWN; 2015-12-10 @ 14:39:21-Station:STA27;|**Norristown**|
|AIRY ST & SWEDE ST;  NORRISTOWN; Station 308A; 2015-12-10 @ 16:47:36;|**Norristown**|
|CHERRYWOOD CT & DEAD END;  LOWER POTTSGROVE; Station 329; 2015-12-10 @ 16:56:52;|Lower Pottsgrove|
|CANNON AVE & W 9TH ST;  LANSDALE; Station 345; 2015-12-10 @ 15:39:04;|Lansdale|
|LAUREL AVE & OAKDALE AVE;  HORSHAM; Station 352; 2015-12-10 @ 16:46:48;|Horsham|
|COLLEGEVILLE RD & LYWISKI RD;  SKIPPACK; Station 336; 2015-12-10 @ 16:17:05;|Skippack|
|MAIN ST & OLD SUMNEYTOWN PIKE;  LOWER SALFORD; Station 344; 2015-12-10 @ 16:51:42;|Lower Salford|
|BLUEROUTE  & RAMP I476 NB TO CHEMICAL RD; PLYMOUTH; 2015-12-10 @ 17:35:41;|Plymouth|
|RT202 PKWY & KNAPP RD; MONTGOMERY; 2015-12-10 @ 17:33:50;|Montgomery|
|BROOK RD & COLWELL LN; PLYMOUTH; 2015-12-10 @ 16:32:10;|Plymouth|

### <a name="s3-date-format-manipulation-during-string-extraction"></a>S3. Manipulação de formato de data durante a extração de cadeia de caracteres

Número de exemplos que foram necessários para esse caso: 1

|Entrada|Saída|
|:-----|:-----|
|MONTGOMERY AVE & WOODSIDE RD;  LOWER MERION; Station 313; 2015-12-11 @ 04:11:35;|**12 Nov 2015 4AM**|
|DREYCOTT LN & W LANCASTER AVE;  LOWER MERION; Station 313; 2015-12-11 @ 01:29:52;|12 Nov 2015 1AM|
|E LEVERING MILL RD & CONSHOHOCKEN STATE RD; LOWER MERION; 2015-12-11 @ 07:29:58;|12 Nov 2015 7AM|
|PENN VALLEY RD & MANOR RD;  LOWER MERION; Station 313; 2015-12-10 @ 20:53:30;|12 Oct 2015 8PM|
|BELMONT AVE & OVERHILL RD; LOWER MERION; 2015-12-10 @ 23:02:27;|12 Oct 2015 11PM|
|W MONTGOMERY AVE & PENNSWOOD RD; LOWER MERION; 2015-12-10 @ 19:25:22;|12 Oct 2015 7PM|
|ROSEMONT AVE & DEAD END;  LOWER MERION; Station 313; 2015-12-10 @ 18:43:07;|12 Oct 2015 6PM|
|AVIGNON DR & DEAD END; LOWER MERION; 2015-12-10 @ 20:01:29-Station:STA24;|12 Oct 2015 8PM|

### <a name="s4-concatenating-strings"></a>S4. Concatenando cadeias de caracteres

Número de exemplos que foram necessários para esse caso: 1

>[!NOTE] 
>Neste exemplo, o caractere especial · representa os espaços na coluna de saída.

|Nome|Inicial do meio|Sobrenome|Saída|
|:-----|:-----|:-----|:-----|
|Laquanda||Lohmann|Laquanda··Lohmann|
|Claudio|Uma|Chew|**Claudio·A·Chew**|
|Sarah-Jane|S|Smith|Sarah-Jane·S·Smith|
|Brandi||Blumenthal|Brandi··Blumenthal|
|Jesusita|R|Journey|Jesusita·R·Journey|
|Hermina||Hults|Hermina··Hults|
|Anne-Marie|W|Jones|Anne-Marie·W·Jones|
|Rico||Ropp|Rico··Ropp|
|Lauren-May||Fullmer|Lauren-May··Fullmer|
|Marc|T|Maine|Marc·T·Maine|
|Angie||Adelman|Angie··Adelman|
|John-Paul||Smith|John-Paul··Smith|
|Song|W|Staller|Song·W·Staller|
|Jill||Jefferies|Jill··Jefferies|
|Ruby-Grace|M|Simmons|Ruby-Grace·M·Simmons|

### <a name="s5-generating-initials"></a>S5. Gerando iniciais

Número de exemplos que foram necessários para esse caso: 2

|Nome completo|Saída|
|:-----|:-----|
|Laquanda Lohmann|**L.L.**|
|Claudio Chew|C.C.|
|Sarah-Jane Smith|S.S.|
|Brandi Blumenthal|B.B.|
|Jesusita Journey|J.J.|
|Hermina Hults|H.H.|
|Anne-Marie Jones|A.J.|
|Rico Ropp|R.R.|
|Lauren-May Fullmer|L.F.|
|Marc Maine|M.M.|
|Angie Adelman|A.A.|
|John-Paul Smith|**J.S.**|
|Song Staller|S.S.|
|Jill Jefferies|J.J.|
|Ruby-Grace Simmons|R.S.|


### <a name="s6-mapping-constant-values"></a>S6. Mapeando valores de constante

Número de exemplos que foram necessários para esse caso: 3

|Gênero administrativo|Saída|
|:-----|:-----:|
|Masculino|**0**|
|Feminino|**1**|
|Desconhecido|**2**|
|Feminino|1|
|Feminino|1|
|Masculino|0|
|Desconhecido|2|
|Masculino|0|
|Feminino|1|

## <a name="examples-of-number-transformations-by-example"></a>Exemplos de transformações de número por exemplo

>[!NOTE] 
>Os valores em **negrito** representam os exemplos que foram fornecidos para concluir a transformação no conjunto de dados mostrado.


### <a name="n1-rounding-to-nearest-10"></a>N1. Arredondamento para os 10 mais próximos

Número de exemplos que foram necessários para esse caso: 1

|Entrada|Saída|
|-----:|-----:|
|112|**110**|
|117|120|
|11112|11110|
|11119|11120|
|548|550|

### <a name="n2-rounding-down-to-nearest-10"></a>N2. Arredondamento para baixo para os 10 mais próximos

Número de exemplos que foram necessários para esse caso: 2

|Entrada|Saída|
|-----:|-----:|
|112|**110**|
|117|**110**|
|11112|11110|
|11119|11110|
|548|540|

### <a name="n3-rounding-to-nearest-005"></a>N3. Arredondamento para os 0,05 mais próximos

Número de exemplos que foram necessários para esse caso: 2

|Entrada|Saída|
|-----:|-----:|
|-75.5812935|**-75.60**|
|-75.2646799|-75.25|
|-75.3519752|-75.35|
|-75.343513|**-75.35**|
|-75.6033497|-75.60|
|-75.283245|-75.30|

### <a name="n4-binning"></a>N4. Compartimentalização

Número de exemplos que foram necessários para esse caso: 1

|Entrada|Saída|
|-----:|:-----:|
|20.16|**20-25**|
|14.32|10-15|
|5.44|5-10|
|3.84|0-5|
|3.73|0-5|
|7.36|5-10|

### <a name="n5-scaling-by-1000"></a>N5. Dimensionamento por 1000

Número de exemplos que foram necessários para esse caso: 1

|Entrada|Saída|
|-----:|-----:|
|-243|**-243000**|
|-12.5|-12500|
|-2345.23292|-2345232.92|
|-1202.3433|-1202343.3|
|1202.3433|1202343.3|

### <a name="n6-padding"></a>N6. Preenchimento

Número de exemplos que foram necessários para esse caso: 1

|Código|Saída|
|-----:|-----:|
|5828|**05828**|
|44130|44130|
|49007|49007|
|29682|29682|
|4759|04759|
|10029|10029|
|7204|07204|

## <a name="examples-of-date-transformations-by-example"></a>Exemplos de transformações de data por exemplo

>[!NOTE] 
>Os valores em **negrito** representam os exemplos que foram fornecidos para concluir a transformação no conjunto de dados mostrado.


### <a name="d1-extracting-date-parts"></a>D1. Extrair partes de data

Essas partes de data foram extraídas usando transformações por exemplo diferentes no mesmo conjunto de dados. As cadeias de caracteres em negrito representam os exemplos fornecidos em suas respectivas transformações.

|Datetime|Weekday|Data|Mês|Ano|Hora|Minuto|Segundo|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**Fri**|**31**|**Jan**|**2031**|**5**|**54**|**18**|
|17-Jan-1990 13:32:01|Wed|17|Jan|1990|13|32|01|
|14-Feb-2034 05:36:07|Tue|14|Feb|2034|5|36|07|
|14-Mar-2002 13:16:16|Thu|14|Mar|2002|13|16|16|
|21-Jan-1985 05:44:43|Mon|21|Jan|1985|5|44|**43**|
|16-Aug-1985 01:11:56|Fri|16|Aug|1985|1|11|56|
|20-Dec-2033 18:36:29|Tue|20|Dec|2033|18|36|29|
|16-Jul-1984 10:21:59|Mon|16|Jul|1984|10|21|59|
|13-Jan-2038 10:59:36|Wed|13|Jan|2038|10|59|36|
|14-Aug-1982 15:13:54|Sat|14|Aug|1982|15|13|54|
|22-Nov-2030 08:18:08|Fri|22|Nov|2030|8|18|08|
|21-Oct-1997 08:42:58|Tue|21|Oct|1997|8|42|58|
|28-Nov-2006 14:19:15|Tue|28|Nov|2006|14|19|15|
|29-Apr-2031 04:59:45|Tue|29|Apr|2031|4|59|45|
|29-Jan-2032 02:38:36|Thu|29|Jan|2032|2|38|36|
|11-May-2028 15:31:52|Thu|11|May|2028|15|31|52|
|15-Jul-1977 12:45:39|Fri|15|Jul|1977|12|45|11,8|
|27-Jan-2029 05:55:41|Sat|27|Jan|2029|5|55|41|
|03-Mar-2024 10:17:49|Sun|3|Mar|2024|10|17|49|
|14-Apr-2010 00:23:13|Wed|14|Apr|2010|0|23|13|

### <a name="d2-formatting-dates"></a>D2. Formatando datas

Essas formatações de data foram feitas usando diferentes transformações por exemplo no mesmo conjunto de dados. As cadeias de caracteres em negrito representam os exemplos fornecidos em suas respectivas transformações.

|Datetime|Format1|Format2|Format3|Format4|Format5|
|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**1/31/2031**|**Friday, January 31, 2031**|**01312031 5:54**|**31/1/2031 5:54 AM**|**Q1 2031**|
|17-Jan-1990 13:32:01|1/17/1990|Wednesday, January 17, 1990|01171990 13:32|17/1/1990 1:32 PM|Q1 1990|
|14-Feb-2034 05:36:07|2/14/2034|Tuesday, February 14, 2034|02142034 5:36|14/2/2034 5:36 AM|Q1 2034
|14-Mar-2002 13:16:16|3/14/2002|Thursday, March 14, 2002|03142002 13:16|14/3/2002 1:16 PM|Q1 2002
|21-Jan-1985 05:44:43|1/21/1985|Monday, January 21, 1985|01211985 5:44|21/1/1985 5:44 AM|Q1 1985
|16-Aug-1985 01:11:56|8/16/1985|Friday, August 16, 1985|08161985 1:11|16/8/1985 1:11 AM|Q3 1985
|20-Dec-2033 18:36:29|12/20/2033|Tuesday, December 20, 2033|12202033 18:36|20/12/2033 6:36 PM|Q4 2033
|16-Jul-1984 10:21:59|7/16/1984|Monday, July 16, 1984|07161984 10:21|16/7/1984 10:21 AM|Q3 1984
|13-Jan-2038 10:59:36|1/13/2038|Wednesday, January 13, 2038|01132038 10:59|13/1/2038 10:59 AM|Q1 2038
|14-Aug-1982 15:13:54|8/14/1982|Saturday, August 14, 1982|08141982 15:13|14/8/1982 3:13 PM|Q3 1982
|22-Nov-2030 08:18:08|11/22/2030|Friday, November 22, 2030|11222030 8:18|22/11/2030 8:18 AM|Q4 2030
|21-Oct-1997 08:42:58|10/21/1997|Tuesday, October 21, 1997|10211997 8:42|21/10/1997 8:42 AM|Q4 1997
|28-Nov-2006 14:19:15|11/28/2006|Tuesday, November 28, 2006|11282006 14:19|28/11/2006 2:19 PM|Q4 2006
|29-Apr-2031 04:59:45|4/29/2031|Tuesday, April 29, 2031|04292031 4:59|29/4/2031 4:59 AM|Q2 2031
|29-Jan-2032 02:38:36|1/29/2032|Thursday, January 29, 2032|01292032 2:38|29/1/2032 2:38 AM|Q1 2032
|11-May-2028 15:31:52|5/11/2028|Thursday, May 11, 2028|05112028 15:31|11/5/2028 3:31 PM|Q2 2028
|15-Jul-1977 12:45:39|7/15/1977|Friday, July 15, 1977|07151977 12:45|15/7/1977 12:45 PM|Q3 1977
|27-Jan-2029 05:55:41|1/27/2029|Saturday, January 27, 2029|01272029 5:55|27/1/2029 5:55 AM|Q1 2029
|03-Mar-2024 10:17:49|3/3/2024|Sunday, March 3, 2024|03032024 10:17|3/3/2024 10:17 AM|Q1 2024
|14-Apr-2010 00:23:13|4/14/2010|Wednesday, April 14, 2010|04142010 0:23|14/4/2010 12:23 AM|Q2 2010


### <a name="d3-mapping-time-to-time-periods"></a>D3. Mapeamento de hora para períodos

Esses mapeamentos de datetime para período foram feitos usando diferentes transformações por exemplo no mesmo conjunto de dados. As cadeias de caracteres em negrito representam os exemplos fornecidos em suas respectivas transformações.

|Datetime|Period(Seconds)|Period(Minutes)|Period(Two Hours)|Period(30 Minutes)|
|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**0-20**|**45-60**|**5AM-7AM**|**5:30-6:00**|
|17-Jan-1990 13:32:01|**0-20**|30-45|1PM-3PM|13:30-14:00|
|14-Feb-2034 05:36:07|0-20|30-45|5AM-7AM|5:30-6:00|
|14-Mar-2002 13:16:16|0-20|15-30|1PM-3PM|13:00-13:30|
|21-Jan-1985 05:44:43|40-60|30-45|5AM-7AM|5:30-6:00|
|16-Aug-1985 01:11:56|40-60|0-15|1AM-3AM|1:00-1:30|
|20-Dec-2033 18:36:29|20-40|30-45|5PM-7PM|18:30-19:00|
|16-Jul-1984 10:21:59|40-60|15-30|9AM-11AM|10:00-10:30|
|13-Jan-2038 10:59:36|20-40|45-60|9AM-11AM|10:30-11:00|
|14-Aug-1982 15:13:54|40-60|0-15|3PM-5PM|15:00-15:30|
|22-Nov-2030 08:18:08|0-20|15-30|7AM-9AM|8:00-8:30|
|21-Oct-1997 08:42:58|40-60|30-45|7AM-9AM|8:30-9:00|
|28-Nov-2006 14:19:15|0-20|15-30|1PM-3PM|14:00-14:30|
|29-Apr-2031 04:59:45|40-60|45-60|3AM-5AM|4:30-5:00|
|29-Jan-2032 02:38:36|20-40|30-45|1AM-3AM|2:30-3:00|
|11-May-2028 15:31:52|40-60|30-45|3PM-5PM|15:30-16:00|
|15-Jul-1977 12:45:39|20-40|45-60|11AM-1PM|12:30-13:00|
|27-Jan-2029 05:55:41|40-60|45-60|5AM-7AM|5:30-6:00|
|03-Mar-2024 10:17:49|40-60|15-30|9AM-11AM|10:00-10:30|
|14-Apr-2010 00:23:13|0-20|15-30|11PM-1AM|0:00-0:30|

## <a name="examples-of-composite-transformations-by-example"></a>Exemplos de transformações de composição por exemplo

|tripduration|starttime|start station id|start station latitude|start station longitude|usertype|Coluna|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|61|2016-01-08 16:09:32|107|42.3625|-71.08822|Subscriber|**Um assinante pegou uma bicicleta da estação 107, lat/long (42,363, -71,088), em 08 de janeiro de 2016 em torno das 16h. A duração da viagem foi de 61 minutos**|
|61|2016-01-17 09:28:10|74|42.373268|-71.118579|Cliente|Um cliente pegou uma bicicleta da estação 74, lat/long (42,373, -71,119), em 17 de janeiro de 2016 em torno das 9h. A duração da viagem foi de 61 minutos|
|62|2016-01-25 08:10:26|176|42.386748020450561|-71.119018793106079|Subscriber|Um assinante pegou uma bicicleta da estação 176, lat/long (42,387, -71,119), em 25 de janeiro de 2016 em torno das 8h. A duração da viagem foi de 62 minutos|
|63|2016-01-08 10:10:29|107|42.3625|-71.08822|Subscriber|Um assinante pegou uma bicicleta da estação 107, lat/long (42,363, -71,088), em 08 de janeiro de 2016 em torno das 10h. A duração da viagem foi de 63 minutos|
|64|2016-01-15 19:42:08|68|42.36507|-71.1031|Subscriber|Um assinante pegou uma bicicleta da estação 68, lat/long (42,365, -71,103), em 15 de janeiro de 2016 em torno das 19h. A duração da viagem foi de 64 minutos|
|64|2016-01-22 18:16:13|115|42.387995|-71.119084|Subscriber|Um assinante pegou uma bicicleta da estação 115, lat/long (42,388, -71,119), em 22 de janeiro de 2016 em torno das 18h. A duração da viagem foi de 64 minutos|
|68|2016-01-18 09:51:52|178|42.359573201090441|-71.101294755935669|Subscriber|Um assinante pegou uma bicicleta da estação 178, lat/long (42,360, -71,101), em 18 de janeiro de 2016 em torno das 9h. A duração da viagem foi de 68 minutos|
|69|2016-01-14 08:57:55|176|42.386748020450561|-71.119018793106079|Subscriber|Um assinante pegou uma bicicleta da estação 176, lat/long (42,387, -71,119), em 14 de janeiro de 2016 em torno das 8h. A duração da viagem foi de 69 minutos|
|69|2016-01-13 22:12:55|141|42.363560158429884|-71.08216792345047|Subscriber|Um assinante pegou uma bicicleta da estação 141, lat/long (42,364, -71,082), em 13 de janeiro de 2016 em torno das 22h. A duração da viagem foi de 69 minutos|
|69|2016-01-15 08:13:09|176|42.386748020450561|-71.119018793106079|Subscriber|Um assinante pegou uma bicicleta da estação 176, lat/long (42,387, -71,119), em 15 de janeiro de 2016 em torno das 8h. A duração da viagem foi de 69 minutos|


## <a name="technical-notes"></a>Observações técnicas

### <a name="conditional-transformations"></a>Transformações condicionais
Em alguns casos, não é possível encontrar uma única transformação que satisfaça os exemplos fornecidos. Nesses casos, a transformação Derivar Coluna por Exemplo tenta agrupar as entradas com base em algum padrão e aprende a separar a transformação para cada grupo. Chamamos isso de **transformação condicional**. A **transformação condicional** é tentada somente para transformações com uma única coluna de entrada. 

### <a name="reference"></a>Referência
É possível obter mais informações sobre a tecnologia de transformação de cadeia de caracteres por exemplo [nesta publicação](https://www.microsoft.com/en-us/research/publication/automating-string-processing-spreadsheets-using-input-output-examples/).
