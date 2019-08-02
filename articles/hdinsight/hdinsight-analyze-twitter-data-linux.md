---
title: Analisar dados do Twitter com o Apache Hive – Azure HDInsight
description: Aprenda como usar o Apache Hive e o Apache Hadoop no HDInsight para transformar dados brutos do TWitter em uma tabela Hive pesquisável.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 8c7f6695880cfdb0a350edc37d61e771d03b92df
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543712"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>Analisar dados do Twitter usando o Apache Hive e Apache Hadoop no HDInsight

Saiba como usar o [Apache Hive](https://hive.apache.org/) para processar dados do Twitter. O resultado será uma lista de usuários do Twitter que enviaram a maioria dos tweets que contêm uma determinada palavra.

> [!IMPORTANT]  
> As etapas deste documento foram testadas no HDInsight 3.6.

## <a name="get-the-data"></a>Obter os dados

O Twitter permite que você recupere os dados de cada tweet como um documento JSON (JavaScript Object Notation) por meio de uma API REST. [OAuth](https://oauth.net) é necessário para autenticação na API.

### <a name="create-a-twitter-application"></a>Criar um aplicativo do Twitter

1. A partir de um navegador da Web, entre em [https://apps.twitter.com/](https://apps.twitter.com/). Clique no link **Inscreva-se agora** se você não tem uma conta do Twitter.

2. Clique em **Criar Novo Aplicativo**.

3. Digite o **Nome**, a **Descrição** e o **Site**. Você pode fazer uma URL para o campo **Site** . A tabela a seguir mostra alguns valores de exemplo para usar:

   | Campo | Valor |
   |:--- |:--- |
   | NOME |MyHDInsightApp |
   | DESCRIÇÃO |MyHDInsightApp |
   | Site |https:\//www.myhdinsightapp.com |

4. Marque **Sim, eu concordo** e, em seguida, clique em **Criar seu aplicativo do Twitter**.

5. Clique na guia **Permissões** . A permissão padrão é **Somente leitura**.

6. Clique na guia **Chaves e Tokens de acesso** .

7. Clique em **Criar meu token de acesso**.

8. Clique em **OAuth de teste** no canto superior direito da página.

9. Anote a **chave do consumidor**, o **Segredo do consumidor**, o **Token de acesso** e o **Segredo do token de acesso**.

### <a name="download-tweets"></a>Baixar tweets

O código Python a seguir baixa 10.000 tweets do Twitter e os salva em um arquivo chamado **tweets.txt**.

> [!NOTE]  
> As etapas a seguir são executadas no cluster HDInsight, já que o Python já está instalado.

1. Conecte-se ao cluster HDInsight usando SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Use os seguintes comandos para instalar [Tweepy](https://www.tweepy.org/), [barra de progresso](https://pypi.python.org/pypi/progressbar/2.2)e outros pacotes necessários:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

4. Use o comando a seguir para criar um arquivo chamado **gettweets.py**:

   ```bash
   nano gettweets.py
   ```

5. Use o texto a seguir como o conteúdo do arquivo **gettweets.py**:

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=10000

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!IMPORTANT]  
    > Substitua o texto de espaço reservado para os seguintes itens com as informações do seu aplicativo do twitter:
    >
    > * `consumer_secret`
    > * `consumer_key`
    > * `access_token`
    > * `access_token_secret`

    > [!TIP]  
    > Ajuste o filtro de tópicos na última linha para rastrear palavras-chave populares. Usar palavras-chave populares quando você executa o script possibilita uma captura de dados mais rápida.

6. Use **Ctrl + X** e **Y** para salvar o arquivo.

7. Use o comando a seguir para executar o arquivo e baixar os tweets:

    ```bash
    python gettweets.py
    ```

    Aparece um indicador de progresso. Ele conta até 100%, conforme os tweets são baixados.

   > [!NOTE]  
   > Se estiver demorando muito tempo para a barra de progresso Avançar, você deverá alterar o filtro para rastrear os tópicos mais populares. Quando há muitos tweets sobre o tópico no filtro, você pode obter rapidamente os 10.000 tweets necessários.

### <a name="upload-the-data"></a>Carregar os dados

Para carregar os dados no armazenamento do HDInsight, use os seguintes comandos:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Esses comandos armazenam os dados em um local que todos os nós no cluster podem acessar.

## <a name="run-the-hiveql-job"></a>Executar o trabalho HiveQL

1. Use o comando a seguir para criar um arquivo com instruções [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual):

   ```bash
   nano twitter.hql
   ```

    Use o texto a seguir como o conteúdo do arquivo:

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
           when "Jan" then "01"
           when "Feb" then "02"
           when "Mar" then "03"
           when "Apr" then "04"
           when "May" then "05"
           when "Jun" then "06"
           when "Jul" then "07"
           when "Aug" then "08"
           when "Sep" then "09"
           when "Oct" then "10"
           when "Nov" then "11"
           when "Dec" then "12" end,
       substr (get_json_object(json_response, '$.created_at'),9,2),
       substr (get_json_object(json_response, '$.created_at'),12,8),
       get_json_object(json_response, '$.in_reply_to_user_id_str'),
       get_json_object(json_response, '$.text'),
       get_json_object(json_response, '$.contributors'),
       get_json_object(json_response, '$.retweeted'),
       get_json_object(json_response, '$.truncated'),
       get_json_object(json_response, '$.coordinates'),
       get_json_object(json_response, '$.source'),
       cast (get_json_object(json_response, '$.retweet_count') as INT),
       get_json_object(json_response, '$.entities.display_url'),
       array(
           trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
       array(
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
       trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
       trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
       get_json_object(json_response, '$.user.screen_name'),
       get_json_object(json_response, '$.user.name'),
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

2. Pressione **Ctrl + X** e pressione **Y** para salvar o arquivo.
3. Use o comando a seguir para executar o HiveQL contido no arquivo:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Esse comando executa o arquivo **twitter.hql**. Quando a consulta for concluída, você verá um prompt `jdbc:hive2//localhost:10001/>`.

4. No prompt de beeline, use a consulta a seguir para verificar se os dados foram importados:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Essa consulta retornará no máximo 10 tweets com a palavra **Azure** no texto da mensagem.

    > [!NOTE]  
    > Se você tiver alterado o filtro no script `gettweets.py`, substitua **Azure** por um dos filtros usados.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como transformar um conjunto de dados JSON não estruturado uma em tabela estruturada do [Apache Hive](https://hive.apache.org/). Para saber mais sobre o Hive no HDInsight, consulte os documentos a seguir:

* [Introdução ao HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Analisar dados de atraso de voo usando o HDInsight](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)

[curl]: https://curl.haxx.se
[curl-download]: https://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
