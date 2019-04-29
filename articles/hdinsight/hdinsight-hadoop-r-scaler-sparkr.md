---
title: Usar o ScaleR e o SparkR com o Azure HDInsight
description: Use ScaleR e SparkR com serviços ML no HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2017
ms.openlocfilehash: 78ea29b9b37c55a588a44f8d4b69486b89ee2fee
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761355"
---
# <a name="combine-scaler-and-sparkr-in-hdinsight"></a>Combinar o ScaleR e o SparkR no HDInsight

Este documento mostra como prever atrasos na chegada de voos usando um modelo de regressão logística **ScaleR**. O exemplo usa os dados meteorológicos e atraso de voo, ingressados usando **SparkR**.

Embora ambos os pacotes são executados no mecanismo de execução do Spark do Apache Hadoop, eles são bloqueados do compartilhamento de dados na memória já que cada um deles exige suas próprias respectivas sessões Spark. Até que esse problema seja resolvido em uma versão futura do ML Server, a solução é manter as sessões Spark não sobrepostas e trocar dados por meio de arquivos intermediários. As instruções aqui mostram que esses requisitos são simples de obter.

Este exemplo foi inicialmente compartilhado em uma palestra no Strata 2016, por Mario Inchiosa e Roni Burd. Você pode encontrar essa palestra em [Construindo uma Plataforma de Ciência de Dados Escalável com R](https://event.on24.com/eventRegistration/console/EventConsoleNG.jsp?uimode=nextgeneration&eventid=1160288&sessionid=1&key=8F8FB9E2EB1AEE867287CD6757D5BD40&contenttype=A&eventuserid=305999&playerwidth=1000&playerheight=650&caller=previewLobby&text_language_id=en&format=fhaudio).

O código foi originalmente gravado para Microsoft ML Server em execução no Spark em um cluster HDInsight no Azure. Mas o conceito de misturar o uso de SparkR e ScaleR em um script também é válido no contexto de ambientes locais.

As etapas neste documento consideram que você possui um nível intermediário de conhecimento do R e R da biblioteca [ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-user-guide-introduction) do Microsoft ML Server. Você será apresentado ao [SparkR](https://spark.apache.org/docs/2.1.0/sparkr.html) enquanto percorrer por esse cenário.

## <a name="the-airline-and-weather-datasets"></a>Os conjuntos de dados de linhas aéreas e clima

Os dados do voo estão disponíveis nas [camadas de acesso aos arquivos do governo dos EUA](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236). Também está disponível como um zip de [AirOnTimeCSV.zip](https://packages.revolutionanalytics.com/datasets/AirOnTime87to12/AirOnTimeCSV.zip).

Os dados meteorológicos podem ser baixados como arquivos zip em formato raw, por mês, do [repositório da Administração Oceânica e Atmosférica Nacional](https://www.ncdc.noaa.gov/orders/qclcd/). Para este exemplo, baixe os dados de maio de 2007 – dezembro de 2012. Use os arquivos de dados por hora e o arquivo `YYYYMMMstation.txt` dentro de cada um dos zips. 

## <a name="setting-up-the-spark-environment"></a>Configurando o ambiente do Spark

Use o código a seguir para configurar o ambiente do Spark:

```
workDir        <- '~'  
myNameNode     <- 'default' 
myPort         <- 0
inputDataDir   <- 'wasb://hdfs@myAzureAccount.blob.core.windows.net'
hdfsFS         <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# create a persistent Spark session to reduce startup times 
#   (remember to stop it later!)
 
sparkCC        <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort, persistentRun=TRUE)

# create working directories 

rxHadoopMakeDir('/user')
rxHadoopMakeDir('user/RevoShare')
rxHadoopMakeDir('user/RevoShare/remoteuser')

(dataDir <- '/share')
rxHadoopMakeDir(dataDir)
rxHadoopListFiles(dataDir) 

setwd(workDir)
getwd()

# version of rxRoc that runs in a local CC 
rxRoc <- function(...){
  rxSetComputeContext(RxLocalSeq())
  roc <- RevoScaleR::rxRoc(...)
  rxSetComputeContext(sparkCC)
  return(roc)
}

logmsg <- function(msg) { cat(format(Sys.time(), "%Y-%m-%d %H:%M:%S"),':',msg,'\n') } 
t0 <- proc.time() 

#..start 

logmsg('Start') 
(trackers <- system("mapred job -list-active-trackers", intern = TRUE))
logmsg(paste('Number of task nodes=',length(trackers)))
```

Em seguida, adicione `Spark_Home` ao caminho de pesquisa dos pacotes R. Adicioná-lo ao caminho de pesquisa permite usar o SparkR e inicializar uma sessão do SparkR:

```
#..setup for use of SparkR  

logmsg('Initialize SparkR') 

.libPaths(c(file.path(Sys.getenv("SPARK_HOME"), "R", "lib"), .libPaths()))
library(SparkR)

sparkEnvir <- list(spark.executor.instances = '10',
                   spark.yarn.executor.memoryOverhead = '8000')

sc <- sparkR.init(
  sparkEnvir = sparkEnvir,
  sparkPackages = "com.databricks:spark-csv_2.10:1.3.0"
)

sqlContext <- sparkRSQL.init(sc)
```

## <a name="preparing-the-weather-data"></a>Preparando os dados meteorológicos

Para preparar os dados meteorológicos, crie um subconjunto para as colunas necessárias para modelagem: 

- "Visibilidade"
- "DryBulbCelsius"
- "DewPointCelsius"
- "RelativeHumidity"
- "WindSpeed"
- "Altímetro"

Em seguida, adicione um código de aeroporto associado à estação meteorológica e converta as medidas da hora local para UTC.

Comece criando um arquivo para mapear as informações da estação meteorológica (WBAN) para um código de aeroporto. O código a seguir lê todos os arquivos de dados brutos de clima por hora, cria subconjuntos para as colunas das quais precisamos, mescla o arquivo de mapeamento da estação meteorológica, ajusta as datas e horas das medidas para UTC e grava uma nova versão do arquivo:

```
# Look up AirportID and Timezone for WBAN (weather station ID) and adjust time

adjustTime <- function(dataList)
{
  dataset0 <- as.data.frame(dataList)
  
  dataset1 <- base::merge(dataset0, wbanToAirIDAndTZDF1, by = "WBAN")

  if(nrow(dataset1) == 0) {
    dataset1 <- data.frame(
      Visibility = numeric(0),
      DryBulbCelsius = numeric(0),
      DewPointCelsius = numeric(0),
      RelativeHumidity = numeric(0),
      WindSpeed = numeric(0),
      Altimeter = numeric(0),
      AdjustedYear = numeric(0),
      AdjustedMonth = numeric(0),
      AdjustedDay = integer(0),
      AdjustedHour = integer(0),
      AirportID = integer(0)
    )
    
    return(dataset1)
  }
  
  Year <- as.integer(substr(dataset1$Date, 1, 4))
  Month <- as.integer(substr(dataset1$Date, 5, 6))
  Day <- as.integer(substr(dataset1$Date, 7, 8))
  
  Time <- dataset1$Time
  Hour <- ceiling(Time/100)
  
  Timezone <- as.integer(dataset1$TimeZone)
  
  adjustdate = as.POSIXlt(sprintf("%4d-%02d-%02d %02d:00:00", Year, Month, Day, Hour), tz = "UTC") + Timezone * 3600

  AdjustedYear = as.POSIXlt(adjustdate)$year + 1900
  AdjustedMonth = as.POSIXlt(adjustdate)$mon + 1
  AdjustedDay   = as.POSIXlt(adjustdate)$mday
  AdjustedHour  = as.POSIXlt(adjustdate)$hour
  
  AirportID = dataset1$AirportID
  Weather = dataset1[,c("Visibility", "DryBulbCelsius", "DewPointCelsius", "RelativeHumidity", "WindSpeed", "Altimeter")]
  
  data.set = data.frame(cbind(AdjustedYear, AdjustedMonth, AdjustedDay, AdjustedHour, AirportID, Weather))
  
  return(data.set)
}

wbanToAirIDAndTZDF <- read.csv("wban-to-airport-id-tz.csv")

colInfo <- list(
  WBAN = list(type="integer"),
  Date = list(type="character"),
  Time = list(type="integer"),
  Visibility = list(type="numeric"),
  DryBulbCelsius = list(type="numeric"),
  DewPointCelsius = list(type="numeric"),
  RelativeHumidity = list(type="numeric"),
  WindSpeed = list(type="numeric"),
  Altimeter = list(type="numeric")
)

weatherDF <- RxTextData(file.path(inputDataDir, "WeatherRaw"), colInfo = colInfo)

weatherDF1 <- RxTextData(file.path(inputDataDir, "Weather"), colInfo = colInfo,
                filesystem=hdfsFS)

rxSetComputeContext("localpar")
rxDataStep(weatherDF, outFile = weatherDF1, rowsPerRead = 50000, overwrite = T,
           transformFunc = adjustTime,
           transformObjects = list(wbanToAirIDAndTZDF1 = wbanToAirIDAndTZDF))
```

## <a name="importing-the-airline-and-weather-data-to-spark-dataframes"></a>Importando os dados de linhas aéreas e clima para Spark DataFrames

Agora, usamos a função [read.df()](https://docs.databricks.com/spark/1.6/sparkr/functions/read.df.html#read-df) do SparkR para importar os dados meteorológicos e de linhas aéreas para o Spark DataFrames. Essa função, assim como muitos outros métodos do Spark, é executada lentamente, o que significa que eles são enfileirados para execução, mas não executados até que isso seja necessário.

```
airPath     <- file.path(inputDataDir, "AirOnTime08to12CSV")
weatherPath <- file.path(inputDataDir, "Weather") # pre-processed weather data
rxHadoopListFiles(airPath) 
rxHadoopListFiles(weatherPath) 

# create a SparkR DataFrame for the airline data

logmsg('create a SparkR DataFrame for the airline data') 
# use inferSchema = "false" for more robust parsing
airDF <- read.df(sqlContext, airPath, source = "com.databricks.spark.csv", 
                 header = "true", inferSchema = "false")

# Create a SparkR DataFrame for the weather data

logmsg('create a SparkR DataFrame for the weather data') 
weatherDF <- read.df(sqlContext, weatherPath, source = "com.databricks.spark.csv", 
                     header = "true", inferSchema = "true")
```

## <a name="data-cleansing-and-transformation"></a>Limpeza de dados e transformação

Em seguida, fazemos uma limpeza nos dados de linhas áreas que você importou para renomear colunas. Mantemos apenas as variáveis necessárias e arrendondamos os horários de partida agendados para a hora mais próxima para habilitar a mesclagem com os dados meteorológicos mais recentes na partida:

```
logmsg('clean the airline data') 
airDF <- rename(airDF,
                ArrDel15 = airDF$ARR_DEL15,
                Year = airDF$YEAR,
                Month = airDF$MONTH,
                DayofMonth = airDF$DAY_OF_MONTH,
                DayOfWeek = airDF$DAY_OF_WEEK,
                Carrier = airDF$UNIQUE_CARRIER,
                OriginAirportID = airDF$ORIGIN_AIRPORT_ID,
                DestAirportID = airDF$DEST_AIRPORT_ID,
                CRSDepTime = airDF$CRS_DEP_TIME,
                CRSArrTime =  airDF$CRS_ARR_TIME
)

# Select desired columns from the flight data. 
varsToKeep <- c("ArrDel15", "Year", "Month", "DayofMonth", "DayOfWeek", "Carrier", "OriginAirportID", "DestAirportID", "CRSDepTime", "CRSArrTime")
airDF <- select(airDF, varsToKeep)

# Apply schema
coltypes(airDF) <- c("character", "integer", "integer", "integer", "integer", "character", "integer", "integer", "integer", "integer")

# Round down scheduled departure time to full hour.
airDF$CRSDepTime <- floor(airDF$CRSDepTime / 100)
```

Agora, executamos operações semelhantes nos dados meteorológicos:

```
# Average weather readings by hour
logmsg('clean the weather data') 
weatherDF <- agg(groupBy(weatherDF, "AdjustedYear", "AdjustedMonth", "AdjustedDay", "AdjustedHour", "AirportID"), Visibility="avg",
                  DryBulbCelsius="avg", DewPointCelsius="avg", RelativeHumidity="avg", WindSpeed="avg", Altimeter="avg"
                  )

weatherDF <- rename(weatherDF,
                    Visibility = weatherDF$'avg(Visibility)',
                    DryBulbCelsius = weatherDF$'avg(DryBulbCelsius)',
                    DewPointCelsius = weatherDF$'avg(DewPointCelsius)',
                    RelativeHumidity = weatherDF$'avg(RelativeHumidity)',
                    WindSpeed = weatherDF$'avg(WindSpeed)',
                    Altimeter = weatherDF$'avg(Altimeter)'
)
```

## <a name="joining-the-weather-and-airline-data"></a>Reunindo os dados de clima e linhas aéreas

Agora, usamos a função [join()](https://docs.databricks.com/spark/1.6/sparkr/functions/join.html#join) do SparkR para fazer uma junção externa esquerda dos dados de linhas aéreas e meteorológicos por AirportID e datetime de partida. A associação externa permite manter todos os registros de dados de linhas aéreas, mesmo que não haja dados de clima correspondentes. Após a união, removemos algumas colunas redundantes e renomeamos as colunas mantidas para remover o prefixo DataFrame de entrada introduzido pela união.

```
logmsg('Join airline data with weather at Origin Airport')
joinedDF <- SparkR::join(
  airDF,
  weatherDF,
  airDF$OriginAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF1 <- select(joinedDF, varsToKeep)

joinedDF2 <- rename(joinedDF1,
                    VisibilityOrigin = joinedDF1$Visibility,
                    DryBulbCelsiusOrigin = joinedDF1$DryBulbCelsius,
                    DewPointCelsiusOrigin = joinedDF1$DewPointCelsius,
                    RelativeHumidityOrigin = joinedDF1$RelativeHumidity,
                    WindSpeedOrigin = joinedDF1$WindSpeed,
                    AltimeterOrigin = joinedDF1$Altimeter
)
```

De maneira semelhante, reuniremos os dados meteorológicos e de linhas aéreas com base em AirportID e datetime de chegada:

```
logmsg('Join airline data with weather at Destination Airport')
joinedDF3 <- SparkR::join(
  joinedDF2,
  weatherDF,
  airDF$DestAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF3)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF4 <- select(joinedDF3, varsToKeep)

joinedDF5 <- rename(joinedDF4,
                    VisibilityDest = joinedDF4$Visibility,
                    DryBulbCelsiusDest = joinedDF4$DryBulbCelsius,
                    DewPointCelsiusDest = joinedDF4$DewPointCelsius,
                    RelativeHumidityDest = joinedDF4$RelativeHumidity,
                    WindSpeedDest = joinedDF4$WindSpeed,
                    AltimeterDest = joinedDF4$Altimeter
                    )
```

## <a name="save-results-to-csv-for-exchange-with-scaler"></a>Salvar os resultados em CSV para permuta com ScaleR

Isso conclui as junções que precisamos executar com o SparkR. Salvamos os dados do Spark DataFrame final "joinedDF5" em um CSV para entrada no ScaleR e, em seguida, fechamos a sessão do SparkR. Instruímos explicitamente o SparkR a salvar o CSV resultante em 80 partições separadas para habilitar paralelismo suficiente no processamento de ScaleR:

```
logmsg('output the joined data from Spark to CSV') 
joinedDF5 <- repartition(joinedDF5, 80) # write.df below will produce this many CSVs

# write result to directory of CSVs
write.df(joinedDF5, file.path(dataDir, "joined5Csv"), "com.databricks.spark.csv", "overwrite", header = "true")

# We can shut down the SparkR Spark context now
sparkR.stop()

# remove non-data files
rxHadoopRemove(file.path(dataDir, "joined5Csv/_SUCCESS"))
```

## <a name="import-to-xdf-for-use-by-scaler"></a>Importar para XDF para uso por ScaleR

Poderíamos usar o arquivo CSV dos dados de linha aérea e meteorológicos como estão para modelagem por meio de uma fonte de dados de texto do ScaleR. Mas nós o importamos para o XDF primeiro, pois ele é mais eficiente ao executar várias operações no conjunto de dados:

```
logmsg('Import the CSV to compressed, binary XDF format') 

# set the Spark compute context for ML Services 
rxSetComputeContext(sparkCC)
rxGetComputeContext()

colInfo <- list(
  ArrDel15 = list(type="numeric"),
  Year = list(type="factor"),
  Month = list(type="factor"),
  DayofMonth = list(type="factor"),
  DayOfWeek = list(type="factor"),
  Carrier = list(type="factor"),
  OriginAirportID = list(type="factor"),
  DestAirportID = list(type="factor"),
  RelativeHumidityOrigin = list(type="numeric"),
  AltimeterOrigin = list(type="numeric"),
  DryBulbCelsiusOrigin = list(type="numeric"),
  WindSpeedOrigin = list(type="numeric"),
  VisibilityOrigin = list(type="numeric"),
  DewPointCelsiusOrigin = list(type="numeric"),
  RelativeHumidityDest = list(type="numeric"),
  AltimeterDest = list(type="numeric"),
  DryBulbCelsiusDest = list(type="numeric"),
  WindSpeedDest = list(type="numeric"),
  VisibilityDest = list(type="numeric"),
  DewPointCelsiusDest = list(type="numeric")
)

joinedDF5Txt <- RxTextData(file.path(dataDir, "joined5Csv"),
                           colInfo = colInfo, fileSystem = hdfsFS)
rxGetInfo(joinedDF5Txt) 

destData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

rxImport(inData = joinedDF5Txt, destData, overwrite = TRUE)

rxGetInfo(destData, getVarInfo = T)

# File name: /user/RevoShare/dev/delayDataLarge/joined5XDF 
# Number of composite data files: 80 
# Number of observations: 148619655 
# Number of variables: 22 
# Number of blocks: 320 
# Compression type: zlib 
# Variable information: 
#   Var 1: ArrDel15, Type: numeric, Low/High: (0.0000, 1.0000)
# Var 2: Year
# 26 factor levels: 1987 1988 1989 1990 1991 ... 2008 2009 2010 2011 2012
# Var 3: Month
# 12 factor levels: 10 11 12 1 2 ... 5 6 7 8 9
# Var 4: DayofMonth
# 31 factor levels: 1 3 4 5 7 ... 29 30 2 18 31
# Var 5: DayOfWeek
# 7 factor levels: 4 6 7 1 3 2 5
# Var 6: Carrier
# 30 factor levels: PI UA US AA DL ... HA F9 YV 9E VX
# Var 7: OriginAirportID
# 374 factor levels: 15249 12264 11042 15412 13930 ... 13341 10559 14314 11711 10558
# Var 8: DestAirportID
# 378 factor levels: 13303 14492 10721 11057 13198 ... 14802 11711 11931 12899 10559
# Var 9: CRSDepTime, Type: integer, Low/High: (0, 24)
# Var 10: CRSArrTime, Type: integer, Low/High: (0, 2400)
# Var 11: RelativeHumidityOrigin, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 12: AltimeterOrigin, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 13: DryBulbCelsiusOrigin, Type: numeric, Low/High: (-46.1000, 47.8000)
# Var 14: WindSpeedOrigin, Type: numeric, Low/High: (0.0000, 81.0000)
# Var 15: VisibilityOrigin, Type: numeric, Low/High: (0.0000, 90.0000)
# Var 16: DewPointCelsiusOrigin, Type: numeric, Low/High: (-41.7000, 29.0000)
# Var 17: RelativeHumidityDest, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 18: AltimeterDest, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 19: DryBulbCelsiusDest, Type: numeric, Low/High: (-46.1000, 53.9000)
# Var 20: WindSpeedDest, Type: numeric, Low/High: (0.0000, 136.0000)
# Var 21: VisibilityDest, Type: numeric, Low/High: (0.0000, 88.0000)
# Var 22: DewPointCelsiusDest, Type: numeric, Low/High: (-43.0000, 29.0000)

finalData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

```

## <a name="splitting-data-for-training-and-test"></a>Dividindo dados para treinamento e teste

Usamos rxDataStep para dividir os dados de 2012 para testes e manter o restante para treinamento:

```
# split out the training data

logmsg('split out training data as all data except year 2012')
trainDS <- RxXdfData( file.path(dataDir, "finalDataTrain" ),fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = trainDS,
            rowSelection = ( Year != 2012 ), overwrite = T )

# split out the testing data

logmsg('split out the test data for year 2012') 
testDS <- RxXdfData( file.path(dataDir, "finalDataTest" ), fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = testDS,
            rowSelection = ( Year == 2012 ), overwrite = T )

rxGetInfo(trainDS)
rxGetInfo(testDS)
```

## <a name="train-and-test-a-logistic-regression-model"></a>Treinar e testar um modelo de regressão logística

Agora, estamos prontos para criar um modelo. Para ver a influência de dados meteorológicos sobre o atraso no horário de chegada, usamos a rotina de regressão logística do ScaleR. Nós a usamos para modelar se um atraso de chegada de mais de 15 minutos é influenciado pelas condições meteorológicas nos aeroportos de partida e de e chegada:

```
logmsg('train a logistic regression model for Arrival Delay > 15 minutes') 
formula <- as.formula(ArrDel15 ~ Year + Month + DayofMonth + DayOfWeek + Carrier +
                     OriginAirportID + DestAirportID + CRSDepTime + CRSArrTime + 
                     RelativeHumidityOrigin + AltimeterOrigin + DryBulbCelsiusOrigin +
                     WindSpeedOrigin + VisibilityOrigin + DewPointCelsiusOrigin + 
                     RelativeHumidityDest + AltimeterDest + DryBulbCelsiusDest +
                     WindSpeedDest + VisibilityDest + DewPointCelsiusDest
                   )

# Use the scalable rxLogit() function but set max iterations to 3 for the purposes of 
# this exercise 

logitModel <- rxLogit(formula, data = trainDS, maxIterations = 3)

base::summary(logitModel)
```

Agora vamos ver como ele faz isso nos dados de teste, fazendo algumas previsões e observando ROC e AUC.

```
# Predict over test data (Logistic Regression).

logmsg('predict over the test data') 
logitPredict <- RxXdfData(file.path(dataDir, "logitPredict"), fileSystem = hdfsFS)

# Use the scalable rxPredict() function

rxPredict(logitModel, data = testDS, outData = logitPredict,
          extraVarsToWrite = c("ArrDel15"), 
          type = 'response', overwrite = TRUE)

# Calculate ROC and Area Under the Curve (AUC).

logmsg('calculate the roc and auc') 
logitRoc <- rxRoc("ArrDel15", "ArrDel15_Pred", logitPredict)
logitAuc <- rxAuc(logitRoc)
head(logitAuc)
logitAuc

plot(logitRoc)
```

## <a name="scoring-elsewhere"></a>Pontuação em outro lugar

Também podemos usar o modelo para pontuação de dados em outra plataforma. Fazemos isso salvando-os em um arquivo RDS e transferindo e importando esse RDS para o ambiente de pontuação de destino, tal como Microsoft SQL Server R Services. É importante garantir que os níveis de fator dos dados a serem pontuados correspondam às informações em que o modelo foi baseado. Essa correspondência pode ser obtida, extraindo e salvando as informações da coluna associadas aos dados de modelagem por meio da função `rxCreateColInfo()` do ScaleR e, em seguida, aplicando essas informações da coluna à fonte de dados de entrada para previsão. A seguir, salvaremos algumas linhas do conjunto de dados de teste e extrairemos e usaremos as informações da coluna deste exemplo no script de previsão:

```
# save the model and a sample of the test dataset 

logmsg('save serialized version of the model and a sample of the test data')
rxSetComputeContext('localpar') 
saveRDS(logitModel, file = "logitModel.rds")
testDF <- head(testDS, 1000)  
saveRDS(testDF    , file = "testDF.rds"    )
list.files()

rxHadoopListFiles(file.path(inputDataDir,''))
rxHadoopListFiles(dataDir)

# stop the spark engine 
rxStopEngine(sparkCC) 

logmsg('Done.')
elapsed <- (proc.time() - t0)[3]
logmsg(paste('Elapsed time=',sprintf('%6.2f',elapsed),'(sec)\n\n'))
```

## <a name="summary"></a>Resumo

Neste artigo, mostramos como é possível combinar o uso de SparkR para manipulação de dados com ScaleR para o desenvolvimento de modelos no Spark do Hadoop. Esse cenário requer que você mantenha sessões do Spark separadas, executando somente uma sessão por vez e que troque dados por meio de arquivos CSV. Embora seja simples, esse processo deve ser mais fácil em uma versão futura do ML Server, quando SparkR e ScaleR puderem compartilhar uma sessão do Spark e, assim, compartilhar Spark DataFrames.

## <a name="next-steps-and-more-information"></a>Próximas etapas e mais informações

- Para saber mais sobre o uso do Microsoft Machine Learning Server no Apache Spark, veja o [Guia de introdução](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started).

- Para informações gerais sobre o ML Server, consulte o artigo [ Introdução ao R ](https://msdn.microsoft.com/microsoft-r/microsoft-r-get-started-node).

- Para obter informações sobre os Serviços ML no HDInsight, consulte [ Visão geral dos serviços ML no HDInsight ](r-server/r-server-overview.md) e [ Introdução aos Serviços ML no HDInsight do Azure](r-server/r-server-get-started.md).

Para obter mais informações sobre o uso de SparkR, veja:

- [Documento do Apache SparkR](https://spark.apache.org/docs/2.1.0/sparkr.html).

- [Visão geral do SparkR](https://docs.databricks.com/spark/latest/sparkr/overview.html) da Databricks.
