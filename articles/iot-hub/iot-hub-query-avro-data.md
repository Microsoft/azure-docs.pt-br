---
title: Consultar dados do Avro usando o Azure Data Lake Analytics | Microsoft Docs
description: Use propriedades do corpo de mensagem a telemetria de dispositivo de rota para o armazenamento de Blob e consultar os dados de formato Avro gravados no armazenamento de Blob.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: asrastog
ms.openlocfilehash: 69c890cfc3db04fe625ed7ad008f545c01844834
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61441460"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Consultar dados Avro usando a análise do Azure Data Lake Analytics

Este artigo discute como consultar dados da Avro para roteamento eficiente de mensagens do Azure Hub Iot aos serviços do Azure. [Roteamento de mensagem](iot-hub-devguide-messages-d2c.md) lhe permite filtrar dados usando consultas avançadas com base no corpo da mensagem, as propriedades da mensagem, marcas do dispositivo gêmeo e propriedades do dispositivo gêmeo. Para saber mais sobre as funcionalidades de consulta no roteamento de mensagem, consulte o artigo sobre a sintaxe de consulta de roteamento de mensagem. 
<!--[Message Routing Query Syntax](iot-hub-devguide-routing-query-syntax.md). I don't have this article yet. -->

O desafio foi que quando o Azure IoT Hub roteia mensagens para o armazenamento de Blob do Microsoft Azure, o Hub IoT grava o conteúdo no formato Avro, que tem o corpo da mensagem e propriedades da mensagem. O Hub IoT oferece suporte a gravação de dados para armazenamento de BLOBs no formato de dados Avro, e esse formato não é usado para outros pontos de extremidade. Para obter mais informações, consulte um artigo sobre o uso de contêineres de Armazenamento do Microsoft Azure. Embora o formato Avro seja ótimo para preservação de dados e mensagem, é um desafio usá-lo para consultar dados. Em comparação, o formato JSON ou CSV é muito mais fácil para consultar dados.

<!-- https://review.docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c?branch=pr-en-us-51566#azure-blob-storage  NEW LINK FOR 'WHEN USING STORAGE CONTAINERS' -->

Para resolver necessidades de dados grandes não relacionadas e formatos e superar esse desafio você pode usar muitos padrões de dados grandes para transformar e escalonar dados. Um dos padrões, "pagar por consulta," é o Azure Data Lake Analytics, que é o foco deste artigo. Embora facilmente você possa executar a consulta no Hadoop ou outras soluções,o Data Lake Analytics é geralmente mais adequado para essa abordagem "pagar por consulta". 

Há um "extrator" para Avro em U-SQL. Para saber mais, consulte o exemplo [U-SQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Consultar e exportar dados da Avro para um arquivo CSV
Nesta seção, você consulta os dados Avro e exporta em um arquivo CSV no armazenamento de Blobs do Azure, embora você pode colocar facilmente os dados em outros repositórios ou armazenamento de dados.

1. Configure o Azure Hub IoT para dados de rota para um ponto de extremidade de armazenamento de BLOBs do Azure usando uma propriedade no corpo da mensagem para selecionar as mensagens.

   ![Seção “Ponto de extremidade personalizados”](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![Regras de roteamento](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Para obter mais informações sobre configurações de rotas e pontos de extremidade personalizados, consulte [Roteamento de mensagens para um hub IoT](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub).

2. Certifique-se de que o dispositivo tenha a codificação, o tipo de conteúdo e os dados necessários no corpo da mensagem, conforme mencionado na documentação do produto ou as propriedades. Ao exibir esses atributos no Device Explorer, conforme exibido aqui, você pode verificar se estão definidos corretamente.

   ![Painel de Dados do Hub de eventos](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Configure uma instância do Azure Data Lake Store e uma instância de análise do Azure Data Lake Analytics. O Hub IoT do Azure não faz roteamento para uma instância do repositório Azure Data Lake Store, mas uma instância de análise Azure Data Lake Analytics requer um.

   ![Instâncias do repositório Azure Data Lake Data Lake Store instâncias do Data Lake Analytics](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. No Data Lake Analytics, configure o armazenamento de BLOBs do Azure como um armazenamento adicional, o mesmo armazenamento de Blob do Azure Hub IoT  encaminha dados.

   ![Painel “Fonte de dados”](./media/iot-hub-query-avro-data/query-avro-data-4.png)
 
5. Conforme discutido no exemplo [Avro U-SQL](https://github.com/Azure/usql/tree/master/Examples/AvroExamples), você precisa de quatro arquivos DLL. Carregue esses arquivos para um local no seu Azure Data Lake Store.

   ![Quatro arquivos DLL carregados](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. No Visual Studio, crie um projeto de U-SQL.
 
   !Criar um projeto U-SQL](./media/iot-hub-query-avro-data/query-avro-data-6.png)

7. Cole o conteúdo do script a seguir e cole- arquivo no arquivo recém-criado. Modifique as três seções realçadas: sua conta do Data Lake Analytics, caminhos das DLLs associadas e o caminho correto para sua conta de armazenamento.
    
   ![As três seções a serem modificadas](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   O script U-SQL real para uma saída simples para o arquivo CSV:
    
    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
            ""type"":""record"",
            ""name"":""Message"",
            ""namespace"":""Microsoft.Azure.Devices"",
            ""fields"":
           [{
                ""name"":""EnqueuedTimeUtc"",
                ""type"":""string""
            },
            {
                ""name"":""Properties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""SystemProperties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""Body"",
                ""type"":[""null"",""bytes""]
            }]
        }"
        );

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```    

    O Data Lake Analytics levou cinco minutos para executar o script a seguir, que foi limitado a 10 unidades analíticas e processados 177 arquivos. O resultado é mostrado na saída do arquivo CSV que é exibida na imagem a seguir:
    
    ![Resultados para o arquivo CSV](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![Saída convertida para arquivo CSV](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    Vá para JSON, continue para a etapa 8.
    
8. A maioria das mensagens de IoT estão no formato de arquivo JSON. Ao adicionar linhas a seguir, você pode analisar a mensagem em um arquivo JSON, que permite que você adicione ONDE as cláusulas e saída apenas dos dados necessários.

    ```sql
       @jsonify = 
         SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body)) 
           AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                   message["event"] AS msgevent,
                   message["object"] AS msgobject,
                   message["status"] AS msgstatus,
                   message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    A saída exibe uma coluna para cada item de `SELECT` comando. 
    
    ![Saindo mostrando uma colunada para cada item](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como consultar dados Avro para roteamento eficiente de mensagens do Azure Hub IoT aos serviços do Azure.

Para obter exemplos de soluções completas de ponta a ponta que usam o IoT Hub, consulte a documentação [ de aceleraores de soluções de IoT do Azure](/azure/iot-accelerators).

Para saber mais sobre como desenvolver soluções com o Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md).

Para saber mais sobre o roteamento de mensagens no Hub IoT, confira [Enviar e receber mensagens com o Hub IoT](iot-hub-devguide-messaging.md).
