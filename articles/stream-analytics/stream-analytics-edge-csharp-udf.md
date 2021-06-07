---
title: Tutorial – Escrever funções em C# definidas pelo usuário para trabalhos do Azure Stream Analytics no Visual Studio (versão prévia)
description: Este tutorial mostra como escrever funções em C# definidas pelo usuário para trabalhos do Stream Analytics no Visual Studio.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 851229e441aa2fbdf7b6eec05390c0ce2b149da2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020461"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-job-preview"></a>Tutorial: Escrever uma função em C# definida pelo usuário para um trabalho do Azure Stream Analytics (versão prévia)

C#-funções definidas pelo usuário (UDFs) criados no Visual Studio permitem que você estender a linguagem de consulta do Azure Stream Analytics com suas próprias funções. Você pode reutilizar o código existente (incluindo DLLs) e usar a lógica de matemática ou complexa com o c#. Há três maneiras de implementar UDFs: Arquivos CodeBehind em um projeto do Stream Analytics, UDFs de um projeto C# local ou UDFs de um pacote existente de uma conta de armazenamento. Este tutorial usa o método de code-behind para implementar uma função c# básica. O recurso UDF para trabalhos do Stream Analytics está atualmente em versão prévia e não deve ser usado nas cargas de trabalho de produção.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie uma função de definidas pelo usuário em c# usando o code-behind.
> * Teste seu trabalho do Stream Analytics localmente.
> * Publique seu trabalho no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se que você concluiu os pré-requisitos a seguir:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Instale as ferramentas [Stream Analytics para o Visual Studio](stream-analytics-tools-for-visual-studio-install.md) e as cargas de trabalho do **desenvolvimento do Azure** ou **do Armazenamento e Processamento de Dados**.
* Veja o [guia de desenvolvimento do Stream Analytics Edge](stream-analytics-tools-for-visual-studio-edge-jobs.md) se você estiver criando um trabalho do IoT Edge.

## <a name="create-a-container-in-your-azure-storage-account"></a>Criar um contêiner na sua conta de armazenamento do Azure

O contêiner que você criar será usado para armazenar o pacote C# compilado. Se você criar um trabalho de borda, essa conta de armazenamento também será usada para implantar o pacote em seu dispositivo de IoT Edge. Use um contêiner dedicado para cada trabalho do Stream Analytics. Não há suporte para reutilizar o mesmo contêiner para vários trabalhos de borda do Stream Analytics. Se você já tiver uma conta de armazenamento com contêineres existentes, você pode usá-los. Se não, você precisará [criar um novo contêiner](../storage/blobs/storage-quickstart-blobs-portal.md). 

## <a name="create-a-stream-analytics-project-in-visual-studio"></a>Criar um projeto do Stream Analytics no Visual Studio

1. Inicie o Visual Studio.

2. Selecione **Arquivo > Novo > Projeto**.

3. Na lista de modelos à esquerda, selecione **Stream Analytics** e, em seguida, selecione **aplicativo do Azure Stream Analytics Edge** ou **aplicativo do Azure Stream Analytics**.

4.  Digite o **Nome** e **Local** do projeto e o **Nome da solução** e selecione **OK**.

    ![Criar um projeto de borda do Stream Analytics do Azure no Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Configurar o caminho do pacote de montagem

1. Abra o Visual Studio e navegue até o **Solution Explorer**.

2. Clique duas vezes no arquivo de configuração de trabalho, `EdgeJobConfig.json`.

3. Expanda o **a configuração de código definidos pelo usuário** seção e, em seguida, preencha a configuração com os seguintes valores sugeridos:

   |**Configuração**|**Valor Sugerido**|
   |-------|---------------|
   |Recurso de Configurações de Armazenamento Global|Escolha fonte de dados da conta atual|
   |Assinatura de Configurações de Armazenamento Global| <sua assinatura>|
   |Conta de armazenamento das Configurações de Armazenamento Global| <sua conta de armazenamento>|
   |Recurso de Configurações de Armazenamento de Código Personalizado|Escolha fonte de dados da conta atual|
   |Conta de Armazenamento de Configurações de Armazenamento de Código Personalizado|<sua conta de armazenamento>|
   |Contêiner de Configurações de Armazenamento de Código Personalizado|<seu contêiner de armazenamento>|


## <a name="write-a-c-udf-with-codebehind"></a>Escreva um UDF C# com o código atrás
Um arquivo CodeBehind é um arquivo C# associado a um único script de consulta do ASA. Ferramentas do Visual Studio serão automaticamente o arquivo code-behind zip e carregá-lo à sua conta de armazenamento do Azure após o envio. Todas as classes devem ser definidas como *pública* e todos os objetos devem ser definidos como *estáticos públicos*.

1. Na **Gerenciador de soluções**, expanda **Script.asql** para encontrar o **Script.asaql.cs** arquivo code-behind.

2. Substitua o código com o exemplo a seguir:

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>Implementar o UDF

1. Na **Gerenciador de soluções**, abra o **asaql** arquivo.

2. Substitua a consulta existente pelo seguinte:

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>Teste local

1. Baixe o [arquivo de dados de exemplo de simulador de temperatura](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json).

2. Na **Gerenciador de soluções**, expanda **entradas**, clique com botão direito **Input** e selecione **Adicionar entrada Local**.

   ![Adicionar entrada local para o trabalho do Stream Analytics no Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. Especifique o caminho do arquivo de entrada local para os dados de exemplo que você baixou e **salvar**.

    ![Configuração de entrada de local de trabalho do Stream Analytics no Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. Clique em **executar localmente** no editor de scripts. Quando a execução local tiver salvo com sucesso os resultados de saída, pressione qualquer tecla para ver os resultados no formato de tabela. 

    ![Executar o trabalho do Azure Stream Analytics localmente com o Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. Você também pode selecionar **Abrir pasta de resultados** para ver os arquivos brutos no formato JSON e CSV.

    ![Exibir resultados de trabalho local do Azure Stream Analytics com o Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>Depurar um UDF
Você pode depurar seu c# UDF localmente da mesma maneira que você depura o código c# padrão. 

1. Adicione pontos de interrupção na função do c#.

    ![Adicionar pontos de interrupção para a função definida pelo usuário do Stream Analytics no Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Pressione **F5** para iniciar a depuração. O programa irá parar em seus pontos de interrupção conforme o esperado.

    ![Exibir resultados de depuração da função de definida pelo usuário no Stream Analytics](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>Publique seu trabalho no Azure
Depois de testar sua consulta localmente, selecione **enviar para o Azure** no editor de scripts para publicar o trabalho no Azure.

![Enviar o trabalho de borda do Stream Analytics do Azure do Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>Implantar em dispositivos IoT Edge
Se você optar por criar um trabalho do Edge do Stream Analytics, agora ele poderá ser implantado como um módulo do IoT Edge. Siga as [guia de início rápido do IoT Edge](../iot-edge/quickstart.md) para criar um IoT Hub, registrar um dispositivo IoT Edge e instalar e iniciar o runtime do IoT Edge em seu dispositivo. Em seguida, siga as [implantar o trabalho](../iot-edge/tutorial-deploy-stream-analytics.md#deploy-the-job) tutorial para implantar o trabalho do Stream Analytics como um módulo IoT Edge. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma simples função em C# definida pelo usuário utilizando CodeBehind, publicou seu trabalho no Azure e implantou o trabalho no Azure ou no dispositivo IoT Edge. 

Para saber mais sobre as diferentes maneiras de usar funções em C# definidas pelo usuário para trabalhos do Stream Analytics, vá para este artigo:

> [!div class="nextstepaction"]
> [Gravar funções C# para o Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)