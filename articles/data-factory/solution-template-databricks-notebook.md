---
title: Transformação com o Azure Databricks
description: Saiba como usar um modelo de solução para transformar dados usando um notebook Databricks no Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/27/2020
ms.openlocfilehash: ee663423071458605f37f07293693dbc91f592bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100362107"
---
# <a name="transformation-with-azure-databricks"></a>Transformação com o Azure Databricks

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, você criará um pipeline de ponta a ponta que contém as atividades **Validação**, **Copiar dados** e **Notebook** no Azure Data Factory.

- A atividade **Validação** garante que o conjunto de dados de origem esteja pronto para o consumo downstream antes de você disparar o trabalho de cópia e de análise.

- A atividade **Copiar dados** duplica o conjunto de dados de origem para o armazenamento do coletor, que é montado como um DBFS no notebook do Azure Databricks. Dessa forma, o conjunto de dados pode ser diretamente consumido pelo Spark.

- A atividade **Notebook** dispara o notebook do Databricks que transforma o conjunto de dados. Ela também adiciona o conjunto de dados a uma pasta processada ou ao Azure Synapse Analytics.

Para simplificar, o modelo deste tutorial não cria um gatilho agendado. Você pode adicionar um, se necessário.

![Diagrama do pipeline](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Armazenamento de Blobs do Azure com um contêiner chamado `sinkdata` para uso como coletor.

  Anote o nome da conta de armazenamento, o nome do contêiner e a chave de acesso. Você precisará desses valores mais adiante no modelo.

- Um workspace do Azure Databricks.

## <a name="import-a-notebook-for-transformation"></a>Importar um notebook para transformação

Para importar um notebook de **Transformação** para seu workspace do Databricks:

1. Entre no seu workspace do Azure Databricks e selecione **Importar**.
       ![Comando de menu para importar um workspace](media/solution-template-Databricks-notebook/import-notebook.png) O caminho do workspace pode ser diferente daquele mostrado, mas lembre-se dele para uso posterior.
1. Selecione **Importar de: URL**. Na caixa de texto, insira `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`.

   ![Seleções para importação de um notebook](media/solution-template-Databricks-notebook/import-from-url.png)

1. Agora, vamos atualizar o notebook de **Transformação** com as informações de conexão de armazenamento.

   No notebook importado, acesse o **comando 5**, conforme mostrado no snippet de código a seguir.

   - Substitua `<storage name>` e `<access key>` por informações de conexão de armazenamento próprias.
   - Use a conta de armazenamento com o contêiner `sinkdata`.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. Gere um **token de acesso do Databricks** para o Data Factory acessá-lo.
   1. No workspace do Databricks, selecione o ícone do seu perfil de usuário no canto superior direito.
   1. Selecione **Configurações de Usuário**.
    ![Comando de menu para as configurações de usuário](media/solution-template-Databricks-notebook/user-setting.png)
   1. Selecione **Gerar Novo Token** na guia **Tokens de Acesso**.
   1. Selecione **Gerar**.

    ![Botão "Gerar"](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Salve o token de acesso* para uso posterior na criação de um serviço vinculado do Databricks. O token de acesso é semelhante a `dapi32db32cbb4w6eee18b7d87e45exxxxxx`.

## <a name="how-to-use-this-template"></a>Como usar este modelo

1. Acesse o modelo **Transformação com o Azure Databricks** e crie serviços vinculados para as conexões a seguir.

   ![Configuração de conexões](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Conexão de Blob de Origem**: para acessar os dados de origem.

       Para este exercício, você pode usar o armazenamento de blobs público que contém os arquivos de origem. Use a captura de tela a seguir como referência para a configuração. Use a seguinte **URL de SAS** para se conectar ao armazenamento de origem (acesso somente leitura):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![Seleções para o método de autenticação e a URL de SAS](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Conexão de Blob de Destino**: para armazenar os dados copiados.

       Na janela **Novo serviço vinculado**, selecione o blob de armazenamento do coletor.

       ![Blob de armazenamento do coletor como um novo serviço vinculado](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks**: para se conectar ao cluster do Databricks.

        Crie um serviço vinculado do Databricks usando a chave de acesso que você gerou anteriormente. Você pode optar por selecionar um *cluster interativo* caso tenha um. Este exemplo usa a opção **Novo cluster de trabalho**.

        ![Seleções para se conectar ao cluster](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Selecione **Usar este modelo**. Você verá um pipeline criado.

    ![Criar um pipeline](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>Introdução e configuração de pipeline

No novo pipeline, a maioria das configurações é definida automaticamente com valores padrão. Examine as configurações do pipeline e faça as alterações necessárias.

1. No **sinalizador de Disponibilidade** da atividade **Validação**, verifique se o valor do **Conjunto de dados** de origem está definido como o `SourceAvailabilityDataset` criado anteriormente.

   ![Valor do conjunto de dados de origem](media/solution-template-Databricks-notebook/validation-settings.png)

1. Em **Arquivo para blob** da atividade **Copiar dados**, verifique as guias **Origem** e **Coletor**. Altere as configurações, se necessário.

   - Guia **Origem** ![guia Origem](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - Guia **Coletor** ![guia Coletor](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. Em **Transformação** da atividade **Notebook**, examine e atualize os caminhos e as configurações conforme necessário.

   A opção **Serviço vinculado do Databricks** será preenchido previamente com o valor de uma etapa anterior, conforme mostrado: ![valor preenchido em Serviço vinculado do Databricks](media/solution-template-Databricks-notebook/notebook-activity.png)

   Para verificar as configurações do **Notebook**:
  
    1. Selecione a guia **Configurações**. em **Caminho do notebook**, verifique se o caminho padrão está correto. Talvez seja necessário procurar e escolher o caminho correto do notebook.

       ![Caminho do notebook](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Expanda o seletor de **Parâmetros Base** e verifique se os parâmetros correspondem ao que é mostrado na captura de tela a seguir. Esses parâmetros são transmitidos para o notebook do Databricks por meio do Data Factory.

       ![Parâmetros base](media/solution-template-Databricks-notebook/base-parameters.png)

1. Verifique se os **Parâmetros de Pipeline** correspondem ao que é mostrado na seguinte captura de tela: ![Parâmetros de pipeline](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Conecte-se aos seus conjuntos de dados.

    >[!NOTE]
    >Nos conjuntos de dados abaixo, o caminho do arquivo foi especificado automaticamente no modelo. Se forem necessárias alterações, especifique o caminho para o **contêiner** e o **diretório**, em caso de erro de conexão.

   - **SourceAvailabilityDataset**: para verificar se os dados de origem estão disponíveis.

     ![Seleções de serviço vinculado e caminho do arquivo em SourceAvailabilityDataset](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset**: para acessar os dados de origem.

       ![Seleções de serviço vinculado e caminho do arquivo em SourceFilesDataset](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset**: para copiar os dados para a localização de destino do coletor. Use os seguintes valores:

     - **Serviço vinculado** - `sinkBlob_LS`, criado em uma etapa anterior.

     - **Caminho do arquivo** - `sinkdata/staged_sink`.

       ![Seleções de serviço vinculado e caminho do arquivo em DestinationFilesDataset](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Selecione **Depurar** para executar o pipeline. Encontre o link para os logs do Databricks para obter logs mais detalhados do Spark.

    ![Vínculo aos logs do Databricks por meio da saída](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Verifique também o arquivo de dados usando o Gerenciador de Armazenamento do Azure.

    > [!NOTE]
    > Para correlação com as execuções de pipeline do Data Factory, este exemplo acrescenta a ID da execução de pipeline do Data Factory à pasta de saída. Isso ajuda a manter o controle dos arquivos gerados por execução.
    > ![ID da execução de pipeline acrescentada](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Data Factory](introduction.md)
