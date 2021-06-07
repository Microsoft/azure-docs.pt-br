---
title: Configurar módulos IoT Edge no SQL do Azure no Edge
description: Na segunda parte deste tutorial do SQL do Azure no Edge de três partes para prever as impurezas do minério de ferro, você configurará módulos e conexões do IoT Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sourabha, sstein
ms.date: 09/22/2020
ms.openlocfilehash: 5d768fdc2540496769883d839cfbb4f009a2000c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077628"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>Configurar módulos e conexões de IoT Edge

Na segunda parte deste tutorial de três partes para prever as impurezas do minério de ferro no SQL do Azure no Edge, você configurará os seguintes módulos do IoT Edge:

- SQL do Azure no Edge
- Módulo gerador de dados do IoT Edge

## <a name="specify-container-registry-credentials"></a>Especificar credenciais do registro de contêiner

As credenciais de acesso aos registros de contêiner que hospedam imagens do módulo precisam ser especificadas. Elas podem ser encontradas no registro de contêiner criado no seu grupo de recursos. Navegue até a seção **Chave de acesso**. Anote os seguintes campos:

- Nome do registro
- Servidor de logon
- Nome de Usuário
- Senha

Agora, especifique as credenciais do contêiner no módulo do IoT Edge.

1. Navegue até o hub IoT criado no seu grupo de recursos.

2. Na seção **IoT Edge**, em **Gerenciamento de Dispositivo Automático**, clique em **ID do dispositivo**. Para este tutorial, o ID é `IronOrePredictionDevice`.

3. Selecione a seção **Definir módulos**.

4. Em **Credenciais de Registro de Contêiner**, insira os seguintes valores:

   | _Campo_   | _Valor_       |
   | -------   | -------       |
   | Nome      | Nome do registro |
   | Endereço   | Servidor de logon  | 
   | Nome do Usuário | Nome de Usuário      | 
   | Senha  | Senha      | 
  
## <a name="build-push-and-deploy-the-data-generator-module"></a>Criar, efetuar push e implantar um Módulo Gerador de Dados

1. Clone os [arquivos de projeto](https://github.com/microsoft/sqlsourabh/tree/main/SQLEdgeSamples/IoTEdgeSamples/IronOreSilica) em seu computador.
2. Abra o arquivo **IronOre_Silica_Predict.sln** usando o Visual Studio 2019
3. Atualize os detalhes do registro de contêiner em **deployment.template.json** 
   ```json
   "registryCredentials":{
        "RegistryName":{
            "username":"",
            "password":""
            "address":""
        }
    }
   ```
4. Atualize o arquivo **modules.json** para especificar o registro de contêiner de destino (ou o repositório do módulo)
   ```json
   "image":{
        "repository":"samplerepo.azurecr.io/ironoresilicapercent",
        "tag":
    }
   ```
5. Execute o projeto no modo de depuração ou versão para garantir que o projeto seja executado sem problemas 
6. Efetue push do projeto para o registro de contêiner clicando com o botão direito do mouse no nome do projeto e selecionando **Criar e efetuar push de Módulos do IoT Edge**.
7. Implante o módulo Gerador de Dados como um módulo do IoT Edge em seu dispositivo do Edge. 

## <a name="deploy-the-azure-sql-edge-module"></a>Implantar o módulo de SQL do Azure no Edge

1. Implante o módulo do SQL do Azure no Edge clicando em **+ Adicionar** e em **Módulo do Marketplace**. 

2. Na folha **Módulo do Marketplace do IoT Edge**, pesquise *SQL do Azure no Edge* e escolha *Desenvolvedor do SQL do Azure no Edge*. 

3. Clique no módulo *SQL do Azure no Edge* recém-adicionado em **Módulos do IoT Edge** para configurar o módulo do SQL do Azure no Edge. Para obter mais informações sobre as opções de configuração, confira [Implantar o SQL do Azure no Edge](./deploy-portal.md).

4. Adicione a variável de ambiente `MSSQL_PACKAGE` à implantação do módulo *SQL do Azure no Edge* e especifique a URL da SAS do arquivo dacpac do banco de dados criado na etapa 8 da [Parte um](tutorial-deploy-azure-resources.md) deste tutorial.

5. Clique em **Atualizar**

6. Na página **Definir módulos no dispositivo**, clique em **Avançar: Rotas >** .

7. No painel de rotas da página **Definir módulos no dispositivo**, especifique as rotas para o módulo para comunicação do hub do IoT Edge, conforme descrito abaixo. Atualize os nomes de módulo nas definições de rota abaixo.

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/IronOreMeasures INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/IronOreMeasures")
   ```

   Por exemplo:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/IronOreMeasures")
   ```


7. Na página **Definir módulos no dispositivo**, clique em **Avançar: Examinar + criar >**

8. Na página **Definir módulos no dispositivo**, clique em **Criar**

## <a name="create-and-start-the-t-sql-streaming-job-in-azure-sql-edge"></a>Criar e iniciar o trabalho de Streaming do T-SQL no SQL do Azure no Edge.

1. Abra o Azure Data Studio.

2. Na guia de **boas-vindas**, inicie uma nova conexão com os seguintes dados:

   |_Campo_|_Valor_|
   |-------|-------|
   |Tipo de conexão| Microsoft SQL Server|
   |Servidor|Endereço IP público mencionado na VM que foi criado para esta demonstração.|
   |Nome de Usuário|sa|
   |Senha|A senha forte que foi usada ao criar a instância do SQL do Azure no Edge.|
   |Banco de dados|Padrão|
   |Grupo de servidor|Padrão|
   |Nome (opcional)|Forneça um nome opcional.|

3. Clique em **Conectar**

4. Na guia do menu **Arquivo**, abra um novo notebook ou use o atalho de teclado Ctrl+N.

5. Na janela Consulta nova, execute o script abaixo para criar o trabalho de Streaming do T-SQL. Antes de executar o script, altere as variáveis a seguir. 
   - *SQL_SA_Password:* o valor de MSSQL_SA_PASSWORD especificado ao implantar o Módulo do SQL do Azure no Edge. 
   
   ```sql
   Use IronOreSilicaPrediction
   Go

   Declare @SQL_SA_Password varchar(200) = '<SQL_SA_Password>'
   declare @query varchar(max) 

   /*
   Create Objects Required for Streaming
   */

   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'MyStr0ng3stP@ssw0rd';

   If NOT Exists (select name from sys.external_file_formats where name = 'JSONFormat')
   Begin
      CREATE EXTERNAL FILE FORMAT [JSONFormat]  
      WITH ( FORMAT_TYPE = JSON)
   End 


   If NOT Exists (select name from sys.external_data_sources where name = 'EdgeHub')
   Begin
      Create EXTERNAL DATA SOURCE [EdgeHub] 
      With(
         LOCATION = N'edgehub://'
      )
   End 

   If NOT Exists (select name from sys.external_streams where name = 'IronOreInput')
   Begin
      CREATE EXTERNAL STREAM IronOreInput WITH 
      (
         DATA_SOURCE = EdgeHub,
         FILE_FORMAT = JSONFormat,
         LOCATION = N'IronOreMeasures'
       )
   End


   If NOT Exists (select name from sys.database_scoped_credentials where name = 'SQLCredential')
   Begin
       set @query = 'CREATE DATABASE SCOPED CREDENTIAL SQLCredential
                 WITH IDENTITY = ''sa'', SECRET = ''' + @SQL_SA_Password + ''''
       Execute(@query)
   End 

   If NOT Exists (select name from sys.external_data_sources where name = 'LocalSQLOutput')
   Begin
      CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
      LOCATION = 'sqlserver://tcp:.,1433',CREDENTIAL = SQLCredential)
   End

   If NOT Exists (select name from sys.external_streams where name = 'IronOreOutput')
   Begin
      CREATE EXTERNAL STREAM IronOreOutput WITH 
      (
         DATA_SOURCE = LocalSQLOutput,
         LOCATION = N'IronOreSilicaPrediction.dbo.IronOreMeasurements'
      )
   End

   EXEC sys.sp_create_streaming_job @name=N'IronOreData',
   @statement= N'Select * INTO IronOreOutput from IronOreInput'

   exec sys.sp_start_streaming_job @name=N'IronOreData'
   ```

6. Use a consulta a seguir para verificar se os dados do módulo de geração de dados estão sendo transmitidos para o banco de dados. 

   ```sql
   Select Top 10 * from dbo.IronOreMeasurements
   order by timestamp desc
   ```


Neste tutorial, implantamos o módulo gerador de dados e o módulo SQL no Edge. Em seguida, criamos um trabalho de streaming para transmitir os dados gerados pelo módulo de geração de dados para SQL. 

## <a name="next-steps"></a>Próximas etapas

- [Implantar o modelo de ML no SQL do Azure no Edge usando ONNX](tutorial-run-ml-model-on-sql-edge.md)
