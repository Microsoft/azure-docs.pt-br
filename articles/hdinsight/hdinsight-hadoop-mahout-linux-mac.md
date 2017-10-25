---
title: "Gerar recomendações usando o Mahout e o HDInsight (SSH) – Azure | Microsoft Docs"
description: "Saiba como usar a biblioteca de aprendizado de máquina do Apache Mahout para gerar recomendações de vídeos com o HDInsight (Hadoop)."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c78ec37c-9a8c-4bb6-9e38-0bdb9e89fbd7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: larryfr
ms.openlocfilehash: 28450d72f19a5467d88bc787d11f6c37c5afbf9a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight-ssh"></a>Gerar recomendações de filmes usando o Apache Mahout com o Hadoop para Linux no HDInsight (SSH)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Saiba como usar a biblioteca de aprendizado de máquina do [Apache Mahout](http://mahout.apache.org) com o Azure HDInsight para gerar recomendações de vídeos.

O Mahout é uma biblioteca de [machine learning][ml] para o Apache Hadoop. O Mahout contém algoritmos para processamento de dados, como filtragem, classificação e clustering. Neste artigo, você utiliza um mecanismo de recomendação para gerar recomendações de filmes baseadas nos vídeos que seus amigos assistiram.

## <a name="prerequisites"></a>Pré-requisitos

* Criar um cluster HDInsight baseado em Linux. Para saber mais sobre como criar um, confira [Introdução ao uso do Hadoop baseado em Linux no HDInsight][getstarted].

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Um cliente SSH. Para saber mais, consulte o documento [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="mahout-versioning"></a>Versões do Mahout

Para obter mais informações sobre a versão do Mahout incluída com o cluster HDInsight, consulte [Versões do HDInsight e componentes do Hadoop](hdinsight-component-versioning.md).

## <a name="recommendations"></a>Entendendo as recomendações

Uma das funções oferecidas pelo Mahout é um mecanismo de recomendação. Esse mecanismo aceita dados no formato de `userID`, `itemId` e `prefValue` (o usuário escolhe o item de sua preferência). O Mahout, então, pode realizar análises de coocorrência, para determinar que *usuários que têm preferência por um item também têm preferência por esses outros itens*. O Mahout determinará, então, usuários com preferências de item similares, que podem ser utilizadas para fazer recomendações.

O fluxo de trabalho a seguir é um exemplo simplificado que usa dados de filmes:

* **Co-ocorrência**: Joe, Alice e Bob, todos gostaram de *Guerra nas Estrelas*, *O Império Contra-ataca* e *O Retorno de Jedi*. O Mahout determina que usuários que gostam de qualquer um desses filmes também gostam dos outros dois.

* **Co-ocorrência**: Bob e Alice também gostaram de *A Ameaça Fantasma*, *A Guerra dos Clones* e *A Vingança dos Sith*. O Mahout determina que usuários que gostam de qualquer um dos três filmes citados anteriormente também gostam destes últimos três filmes.

* **Recomendação por similaridade**: já que Joe gostou dos primeiros três, o Mahout pesquisará os filmes que outros com preferências similares gostaram, mas que Joe não assistiu (curtiu/classificou). Nesse caso, o Mahout recomendaria *A Ameaça Fantasma*, *A Guerra dos Clones* e *A Vingança dos Sith*.

### <a name="understanding-the-data"></a>Compreendendo os dados

Convenientemente, a [GroupLens Research][movielens] oferece dados de classificação para filmes em um formato compatível com o Mahout. Esses dados estão disponíveis no armazenamento padrão do cluster em `/HdiSamples/HdiSamples/MahoutMovieData`.

Existem dois arquivos, `moviedb.txt` e `user-ratings.txt`. O arquivo ratings.txt do usuário é usado durante a análise, enquanto moviedb.txt é usado para fornecer informações de texto amigável ao exibir os resultados da análise.

Os dados contidos em user-ratings.txt têm uma estrutura de `userID`, `movieID`, `userRating` e `timestamp`, que informa a classificação que cada usuário deu a um filme. Aqui está um exemplo dos dados:

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Executar a análise

Em uma conexão SSH ao cluster, use o comando a seguir para executar o trabalho de recomendação:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]
> O trabalho pode levar vários minutos para ser concluído e pode executar vários trabalhos do MapReduce.

## <a name="view-the-output"></a>Exibir a saída

1. Quando o trabalho for concluído, use o seguinte comando para exibir a saída gerada:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    A saída é exibida da seguinte maneira:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    A primeira coluna é a `userID`. Os valores contidos em '[' and ']' são `movieId`:`recommendationScore`.

2. Você pode usar a saída juntamente com o moviedb.txt para fornecer mais informações sobre as recomendações. Primeiro, é necessário copiar os arquivos localmente usando os seguintes comandos:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Esse comando copiará os dados de saída em um arquivo chamado **recommendations.txt** no diretório atual, juntamente com os arquivos de dados de filme.

3. Use o comando a seguir para criar um script Python que pesquise nomes de filmes para os dados na saída de recomendações:

    ```bash
    nano show_recommendations.py
    ```

    Quando o editor for aberto, use o texto a seguir como conteúdo do arquivo:

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Pressione **Ctrl-X**, **Y** e finalmente **Enter** para salvar os dados.

4. Execute o script Python. O comando a seguir pressupõe que você esteja no diretório em que todos os arquivos foram baixados:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Esse comando examina as recomendações geradas para o usuário ID 4.

    * O arquivo **user-ratings.txt** é usado para recuperar filmes que foram classificados.

    * O arquivo **user-ratings.txt** é usado para recuperar os nomes dos filmes.

    * O arquivo **recommendations.txt** é usado para recuperar as recomendações de filmes deste usuário.

     A saída desse comando deve ser semelhante a este texto:

        Seven Years in Tibet (1997), score=5.0   Indiana Jones and the Last Crusade (1989), score=5.0   Jaws (1975), score=5.0   Sense and Sensibility (1995), score=5.0   Independence Day (ID4) (1996), score=5.0   My Best Friend's Wedding (1997), score=5.0   Jerry Maguire (1996), score=5.0   Scream 2 (1997), score=5.0   Time to Kill, A (1996), score=5.0

## <a name="delete-temporary-data"></a>Excluir dados temporários

Os trabalhos do Mahout não removem dados temporários criados durante o processamento do trabalho. O parâmetro `--tempDir` é especificado no trabalho de exemplo para isolar os arquivos temporários em um caminho específico para fácil exclusão. Para remover os arquivos temporários, use o seguinte comando:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]
> Se você quiser executar o comando novamente, você também deve excluir o diretório de saída. Use o seguinte para excluir esse diretório:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como usar o Mahout, descubra outras maneiras de trabalhar com dados no HDInsight:

* [Hive com o HDInsight](hdinsight-use-hive.md)
* [Pig com o HDInsight](hdinsight-use-pig.md)
* [MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
