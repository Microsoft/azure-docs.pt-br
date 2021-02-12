---
title: Transformação com o Azure Databricks
description: Saiba como usar um modelo de solução para transformar dados usando um notebook Databricks no Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/27/2020
ms.openlocfilehash: 1c20508d27d03c00a6842979731fb905bbaa9def
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461248"
---
# <a name="transformation-with-azure-databricks"></a>Transformação com o Azure Databricks

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, você cria um pipeline de ponta a ponta que contém a **validação**, a **cópia de dados** e as atividades do **Notebook** em Azure data Factory.

- A **validação** garante que o conjunto de fonte de origem esteja pronto para o consumo de downstream antes de disparar o trabalho de cópia e de análise.

- **Copy data** duplica o conjunto de dados de origem para o armazenamento do coletor, que é montado como DBFS no bloco de anotações Azure Databricks. Dessa forma, o conjunto de entrada pode ser diretamente consumido pelo Spark.

- O **Notebook** dispara o bloco de anotações do databricks que transforma o conjunto de os. Ele também adiciona o conjunto de um a uma pasta processada ou a análise de Synapse do Azure Azure.

Para simplificar, o modelo neste tutorial não cria um gatilho agendado. Você pode adicionar um se necessário.

![Diagrama do pipeline](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta de armazenamento de BLOBs do Azure com um contêiner chamado `sinkdata` para uso como um coletor.

  Anote o nome da conta de armazenamento, o nome do contêiner e a chave de acesso. Você precisará desses valores posteriormente no modelo.

- Um espaço de trabalho Azure Databricks.

## <a name="import-a-notebook-for-transformation"></a>Importar um bloco de anotações para transformação

Para importar um bloco de anotações de **transformação** para seu espaço de trabalho do databricks:

1. Entre no seu espaço de trabalho do Azure Databricks e, em seguida, selecione **importar**.
       ![Comando de menu para importar um espaço de trabalho ](media/solution-template-Databricks-notebook/import-notebook.png) seu caminho de espaço de trabalho pode ser diferente daquele mostrado, mas lembre-se dele para mais tarde.
1. Selecione **importar de: URL**. Na caixa de texto, digite `https://adflabstaging1.blob.core.windows.net/share/Transformations.html` .

   ![Seleções para importar um bloco de anotações](media/solution-template-Databricks-notebook/import-from-url.png)

1. Agora, vamos atualizar o bloco de anotações de **transformação** com suas informações de conexão de armazenamento.

   No bloco de anotações importado, vá para o **comando 5** , conforme mostrado no trecho de código a seguir.

   - Substitua `<storage name>` e `<access key>` por suas próprias informações de conexão de armazenamento.
   - Use a conta de armazenamento com o `sinkdata` contêiner.

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
   1. No espaço de trabalho do databricks, selecione o ícone de perfil de usuário no canto superior direito.
   1. Selecione **configurações do usuário**.
    ![Comando de menu para configurações de usuário](media/solution-template-Databricks-notebook/user-setting.png)
   1. Selecione **gerar novo token** na guia **tokens de acesso** .
   1. Selecione **Gerar**.

    ![Botão "gerar"](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Salve o token de acesso* para uso posterior na criação de um serviço vinculado do databricks. O token de acesso é semelhante a `dapi32db32cbb4w6eee18b7d87e45exxxxxx` .

## <a name="how-to-use-this-template"></a>Como usar este modelo

1. Vá para a **transformação com Azure Databricks** modelo e crie novos serviços vinculados para as conexões a seguir.

   ![Configuração de conexões](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Conexão de blob de origem** -para acessar os dados de origem.

       Para este exercício, você pode usar o armazenamento de blob público que contém os arquivos de origem. Referencie a seguinte captura de tela para a configuração. Use a **URL SAS** a seguir para se conectar ao armazenamento de origem (acesso somente leitura):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![Seleções para o método de autenticação e URL SAS](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Conexão de blob de destino** -para armazenar os dados copiados.

       Na janela **novo serviço vinculado** , selecione o blob de armazenamento do coletor.

       ![Blob de armazenamento do coletor como um novo serviço vinculado](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** -para se conectar ao cluster do databricks.

        Crie um serviço vinculado do databricks usando a chave de acesso que você gerou anteriormente. Você pode optar por selecionar um *cluster interativo* se tiver um. Este exemplo usa a **nova** opção de cluster de trabalho.

        ![Seleções para conexão com o cluster](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Selecione **Usar este modelo**. Você verá um pipeline criado.

    ![Criar um pipeline](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>Introdução e configuração de pipeline

No novo pipeline, a maioria das configurações é configurada automaticamente com valores padrão. Examine as configurações do seu pipeline e faça as alterações necessárias.

1. No **sinalizador disponibilidade** da atividade de **validação** , verifique se o valor do **conjunto** de fonte de origem está definido como `SourceAvailabilityDataset` que você criou anteriormente.

   ![Valor do conjunto de fonte de origem](media/solution-template-Databricks-notebook/validation-settings.png)

1. No arquivo de atividade **copiar dados** **-para-blob**, verifique as guias **origem** e **coletor** . Altere as configurações, se necessário.

   - **Guia origem da guia origem** ![](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - Guia coletor da guia **coletor** ![](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. Na **transformação** atividade do **Notebook** , examine e atualize os caminhos e as configurações conforme necessário.

   O **serviço vinculado do databricks** deve ser preenchido previamente com o valor de uma etapa anterior, conforme mostrado: ![ valor preenchido para o serviço vinculado do databricks](media/solution-template-Databricks-notebook/notebook-activity.png)

   Para verificar as configurações do **bloco de anotações** :
  
    1. Selecione a guia **configurações** . Para **caminho do bloco de anotações**, verifique se o caminho padrão está correto. Talvez seja necessário procurar e escolher o caminho correto do bloco de anotações.

       ![Caminho do bloco de anotações](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Expanda o seletor de **parâmetros de base** e verifique se os parâmetros correspondem ao que é mostrado na captura de tela a seguir. Esses parâmetros são passados para o databricks notebook a partir de Data Factory.

       ![Parâmetros de base](media/solution-template-Databricks-notebook/base-parameters.png)

1. Verifique se os **parâmetros de pipeline** correspondem ao que é mostrado na seguinte captura de tela: ![ parâmetros de pipeline](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Conecte-se a seus conjuntos de os.

    >[!NOTE]
    >Nos conjuntos de valores abaixo, o caminho do arquivo foi especificado automaticamente no modelo. Se forem necessárias alterações, certifique-se de especificar o caminho para o **contêiner** e o **diretório** em caso de erro de conexão.

   - **SourceAvailabilityDataset** -para verificar se os dados de origem estão disponíveis.

     ![Seleções para o serviço vinculado e o caminho do arquivo para SourceAvailabilityDataset](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** -para acessar os dados de origem.

       ![Seleções para o serviço vinculado e o caminho do arquivo para SourceFilesDataset](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** -para copiar os dados para o local de destino do coletor. Use os seguintes valores:

     - **Serviço vinculado**  -  `sinkBlob_LS` , criado em uma etapa anterior.

     - **Caminho do arquivo**  -  `sinkdata/staged_sink` .

       ![Seleções para o serviço vinculado e o caminho do arquivo para DestinationFilesDataset](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Selecione **depurar** para executar o pipeline. Você pode encontrar o link para os logs do databricks para logs do Spark mais detalhados.

    ![Vincular a logs do databricks da saída](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Você também pode verificar o arquivo de dados usando Gerenciador de Armazenamento do Azure.

    > [!NOTE]
    > Para correlacionar com Data Factory pipeline é executado, este exemplo acrescenta a ID de execução de pipeline do data factory à pasta de saída. Isso ajuda a manter o controle de arquivos gerados por cada execução.
    > ![ID de execução de pipeline acrescentada](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Data Factory](introduction.md)
