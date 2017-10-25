---
title: "Usando a extensibilidade do Python com a preparação de dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece a visão geral e alguns exemplos detalhados de como usar o código Python para estender a funcionalidade de preparação de dados"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 4e1935a7830b8174796ac12792fbbc0ed110d081
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="data-prep-python-extensions"></a>Extensões do Python para preparação de dados
Como uma forma de preencher as lacunas de funcionalidade entre os recursos internos, a preparação de dados inclui a extensibilidade em muitos níveis. Neste documento, descrevemos a extensibilidade por meio de script do Python. 

## <a name="custom-code-steps"></a>Etapas de código personalizado 
A preparação de dados tem as seguintes etapas personalizadas em que os usuários podem gravar o código: 
1. Leitor de arquivo *
2. Gravador*
3. Adicionar Coluna
4. Filtro Avançado
5. Transformar fluxo de dados
6. Transformar Partição

* Essas etapas não têm suporte no momento em uma execução do Spark. 

## <a name="code-block-types"></a>Tipos de bloco de código 
Para cada uma dessas etapas, há suporte para dois tipos de bloco de código. Primeiro, há suporte para uma expressão de Python básica que é executada no estado em que se encontra. Em segundo lugar, há suporte para um módulo de Python em que podemos chamar uma função específica com uma assinatura conhecida no código que você fornecer.

Por exemplo, você pode adicionar uma nova coluna que calcula o log de outra coluna de uma das duas maneiras a seguir: Expressão: 

```python    
    math.log(row["Score"])
```

Módulo: 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


A transformação Adicionar Coluna no modo de módulo espera encontrar uma função chamada `newvalue` que aceita uma variável de linha e retorna o valor da coluna. Esse módulo pode incluir qualquer quantidade de código Python com outras funções, importações etc. 

Os detalhes de cada ponto de extensão são discutidos nas seções a seguir: 

## <a name="imports"></a>Importações 
Se você estiver usando o tipo de bloco de Expressão, você ainda poderá adicionar instruções de importação ao código, mas elas ainda deverão ser agrupadas nas linhas superiores do código. Corrigir: 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

Erro:  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
Se você estiver usando o tipo de bloco do Módulo, poderá seguir todas as regras de Python normais para usar a instrução 'import'. 

## <a name="default-imports"></a>Importações padrão
As importações a seguir são sempre incluídas e utilizáveis no código. Não é necessário reimportá-las. 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="installing-new-packages"></a>Instalando novos pacotes
Para usar um pacote que não está instalado por padrão, primeiro é necessário instalá-lo nos ambientes que a preparação de dados usa. Essa instalação precisa ser feita em seu computador local e em qualquer destino de computação em que desejar executar.

Para instalar os pacotes em um destino de computação, você precisa modificar o arquivo conda_dependencies.yml localizado na pasta aml_config sob a raiz do seu projeto.

### <a name="windows"></a>Windows 
A maneira de encontrar a localização no Windows é encontrar a instalação específica de aplicativo do Python e seu diretório de scripts, o padrão é:  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts.` 

Em seguida, execute um dos comandos a seguir: 

`conda install <libraryname>` 

ou o 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Para encontrar a localização no Mac, encontre a instalação específica do aplicativo do Python e seu diretório de scripts, a localização padrão é: 

`/Users/<user>/Library/Caches/AmlWorkbench>/Python/bin` 

Em seguida, execute um dos comandos a seguir: 

`./conda install <libraryname>`

ou o 

`./pip install <libraryname>`

## <a name="column-data"></a>Dados de coluna 
Os dados de coluna podem ser acessados de uma linha usando a notação de ponto ou usando a notação de chave/valor. Os nomes de colunas que contêm espaços ou caracteres especiais não podem ser acessados usando a notação de ponto. A variável `row` sempre deve ser definida em ambos os modos de extensões do Python (Módulo e Expressão). 

Exemplos: 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="file-reader"></a>Leitor de arquivo 
### <a name="purpose"></a>Finalidade 
Esse ponto de extensão lhe permite controlar totalmente o processo de leitura de um arquivo em um fluxo de dados. O sistema chama o código, passando a lista de arquivos que você deve processar e seu código precisa criar e retornar um dataframe Pandas. 

>[!NOTE]
>Esse ponto de extensão não funciona no Spark. 


### <a name="how-to-use"></a>Como usar 
Você acessa esse ponto de extensão do assistente para Abrir Fonte de Dados. Escolha Arquivo na primeira página e, em seguida, escolha o local do arquivo. Na página 'Escolher Parâmetros do Arquivo', abra a lista suspensa Tipo de Arquivo e escolha ‘Arquivo Personalizado (Script)’. 

O código recebe um dataframe Pandas chamado ‘df’ que contém informações sobre os arquivos que você precisa ler. Se você optar por abrir um diretório que contém vários arquivos, o dataframe contém mais de uma linha.  

Esse dataframe tem as seguintes colunas: 
- Caminho – o arquivo a ser lido.
- PathHint – informa onde o arquivo está localizado. Valores: 'Local', 'AzureBlobStorage', 'AzureDataLakeStorage'
- AuthenticationType – o tipo de autenticação usado para acessar o arquivo. Valores: 'None', 'SasToken', 'OAuthToken'
- AuthenticationValue – contém None ou o token a ser usado.

### <a name="syntax"></a>Sintaxe 
Expressão: 

```python
    paths = df['Path'].tolist()  
    df = pd.read_csv(paths[0])
```


Módulo:  
```python
PathHint = Local  
def read(df):  
    paths = df['Path'].tolist()  
    filedf = pd.read_csv(paths[0])  
    return filedf  
```
 

## <a name="writer"></a>Gravador 
### <a name="purpose"></a>Finalidade 
Esse ponto de extensão do gravador lhe permite controlar totalmente o processo de gravação de dados de um fluxo de trabalho. O sistema chama o código, passando um dataframe e seu código pode usar o dataframe para gravar dados como você quiser. 

>[!NOTE]
>O ponto de extensão de gravador não funciona no Spark. 


### <a name="how-to-use"></a>Como usar 
Você pode adicionar esse ponto de extensão usando o bloco 'Gravar Fluxo de Dados (Script)'. Ele está disponível no menu Transformações de nível superior. 

### <a name="syntax"></a>Sintaxe 
Expressão: 

```python
    df.to_csv('c:\\temp\\output.csv')
```

Módulo:

```python
def write(df):  
    df.to_csv('c:\\temp\\output.csv')  
    return df
```
 
 
Este bloco de gravação personalizado pode existir no meio de uma lista de etapas, portanto, se usar um Módulo, a função de gravação deverá retornar o dataframe que é a entrada para a etapa seguinte. 

## <a name="add-column"></a>Adicionar coluna 
### <a name="purpose"></a>Finalidade
Esse ponto de extensão permite que você escreva o Python para calcular uma nova coluna. O código que você grava tem acesso à linha inteira. Ele deve retornar um novo valor de coluna para cada linha. 

### <a name="how-to-use"></a>Como usar
Você pode adicionar esse ponto de extensão usando o bloco 'Adicionar Coluna (Script)'. Ele está disponível no menu Transformações de nível superior, bem como no menu de contexto de coluna. 

### <a name="syntax"></a>Sintaxe
Expressão: 

```python
    math.log(row["Score"])
```

Módulo: 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>Filtro avançado
### <a name="purpose"></a>Finalidade 
Esse ponto de extensão permite que você grave um filtro personalizado. Você tem acesso a toda a linha e seu código deve retornar True (inclua a linha) ou False (exclua a linha). 

### <a name="how-to-use"></a>Como usar
Você pode adicionar esse ponto de extensão usando o bloco 'Filtro Avançado (Script)'. Ele está disponível no menu Transformações de nível superior. 

### <a name="syntax"></a>Sintaxe

Expressão: 

```python
    row["Score"] > 95
```

Módulo:  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>Transformar fluxo de dados
### <a name="purpose"></a>Finalidade 
O ponto de extensão permite que você transforme completamente o fluxo de dados. Você tem acesso a um dataframe Pandas que contém todas as colunas e linhas que você está processando e seu código deve retornar um dataframe Pandas com os novos dados. 

>[!NOTE]
>No Python, todos os dados deverão ser carregados na memória em um dataframe Pandas se essa extensão for usada. 

No Spark, todos os dados são coletados em um nó único trabalhador. Isso poderá resultar em um trabalho ficar sem memória se os dados forem muito grandes. Use com cuidado.

### <a name="how-to-use"></a>Como usar 
Você pode adicionar esse ponto de extensão usando o bloco 'Transformar Fluxo de Dados (Script)'. Ele está disponível no menu Transformações de nível superior. 
### <a name="syntax"></a>Sintaxe 

Expressão: 

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Módulo: 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>Transformar partição  
### <a name="purpose"></a>Finalidade 
Esse ponto de extensão permite transformar uma partição do fluxo de dados. Você tem acesso a um dataframe Pandas que contém todas as colunas e linhas para a partição e seu código deve retornar um dataframe Pandas com os novos dados. 

>[!NOTE]
>No Python, você pode acabar com uma única partição ou várias partições, dependendo do tamanho dos dados. No Spark, você está trabalhando com um dataframe que contém os dados para uma partição em um determinado nó de trabalho. Em ambos os casos, você não pode assumir que tem acesso a todo o conjunto de dados. 


### <a name="how-to-use"></a>Como usar
Você pode adicionar esse ponto de extensão usando o bloco 'Transformar Partição (Script)'. Ele está disponível no menu Transformações de nível superior. 

### <a name="syntax"></a>Sintaxe 

Expressão: 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Módulo: 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>Valores de erro  
Na preparação de dados, existe o conceito de valores de erro. Eles são a criação e o motivo para a existência é abordado aqui <link to error values doc>. 

É possível encontrar os valores de erro no código Python personalizado. Eles são instâncias de uma classe de Python chamada `DataPrepError`. Esta classe encapsula uma exceção do Python e tem algumas propriedades que contêm informações sobre o erro que ocorreu ao processar o valor original, bem como o valor original. 


### <a name="datapreperror-class-definition"></a>Definição de classe DataPrepError
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
A criação de um DataPrepError no framework do Python de preparação de dados geralmente tem esta aparência: 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>Como usar 
É possível para o Python sendo executado em um ponto de extensão gerar DataPrepErrors como valores retornados usando o método de criação de antes. É muito mais provável que DataPrepErrors será encontrado ao processar dados em um ponto de extensão. Neste ponto, o código Python personalizado precisa tratar DataPrepError como um tipo de dados válido. 

#### <a name="syntax"></a>Sintaxe 
Expressão:  
```python 
    if (isinstance(row["Score"], DataPrepError)): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
```python 
    if (hasattr(row["Score"], "originalValue")): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
Módulo:  
```python 
def newvalue(row): 
    if (isinstance(row["Score"], DataPrepError)): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
``` 
```python 
def newvalue(row): 
    if (hasattr(row["Score"], "originalValue")): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
```  
