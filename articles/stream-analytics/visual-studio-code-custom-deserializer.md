---
title: Criar desserializadores .NET personalizados para Azure Stream Analytics trabalhos de nuvem usando Visual Studio Code
description: Este tutorial demonstra como criar um desserializador do .NET personalizado para um Azure Stream Analytics trabalho de nuvem usando Visual Studio Code.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/22/2020
ms.openlocfilehash: 1813fb222bca74f355fec52252ce3d77fef06e5d
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013916"
---
# <a name="create-custom-net-deserializers-for-azure-stream-analytics-in-visual-studio-code"></a>Criar desserializadores .NET personalizados para Azure Stream Analytics no Visual Studio Code

Azure Stream Analytics tem [suporte interno para três formatos de dados](stream-analytics-parsing-json.md): JSON, CSV e Avro. Com os desserializadores do .NET personalizados, você pode ler dados de outros formatos, como [buffer de protocolo](https://developers.google.com/protocol-buffers/), [títulos](https://github.com/Microsoft/bond) e outros formatos definidos pelo usuário para trabalhos de nuvem.

## <a name="custom-net-deserializers-in-visual-studio-code"></a>Desserializadores do .NET personalizados no Visual Studio Code

Você pode criar, testar e depurar um desserializador personalizado do .NET para um trabalho Azure Stream Analytics Cloud usando Visual Studio Code.

### <a name="prerequisites"></a>Pré-requisitos

* Instale o [SDK do .NET Core](https://dotnet.microsoft.com/download) e reinicie o Visual Studio Code.

* Use este guia de [início rápido](quick-create-visual-studio-code.md) para aprender a criar um trabalho de Stream Analytics usando Visual Studio Code.

### <a name="create-a-custom-deserializer"></a>Criar um desserializador personalizado

1. Abra um terminal e execute o comando a seguir para criar uma biblioteca de classes .NET em Visual Studio Code para seu desserializador personalizado chamado **ProtobufDeserializer**.

   ```dotnetcli
   dotnet new classlib -o ProtobufDeserializer
   ```

2. Vá para o diretório do projeto **ProtobufDeserializer** e instale os pacotes NuGet [Microsoft. Azure. StreamAnalytics](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics/) e [Google. Protobuf](https://www.nuget.org/packages/Google.Protobuf/) .

   ```dotnetcli
   dotnet add package Microsoft.Azure.StreamAnalytics
   ```

   ```dotnetcli
   dotnet add package Google.Protobuf
   ```

3. Adicione a [classe MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) e a [classe MessageBodyDeserializer](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) ao seu projeto.

4. Compile o projeto **ProtobufDeserializer** .

### <a name="add-an-azure-stream-analytics-project"></a>Adicionar um projeto do Azure Stream Analytics

1. Abra Visual Studio Code e selecione **Ctrl + Shift + P** para abrir a paleta de comandos. Em seguida, insira ASA e selecione **ASA: Criar Novo Projeto**. Nomeie-o como **ProtobufCloudDeserializer**.

### <a name="configure-a-stream-analytics-job"></a>Configurar um trabalho do Stream Analytics

1. Clique duas vezes em **JobConfig.json**. Use as configurações padrão, exceto pelas seguintes:

   |Configuração|Valor sugerido|
   |-------|---------------|
   |Recurso de Configurações de Armazenamento Global|Escolha fonte de dados da conta atual|
   |Assinatura de Configurações de Armazenamento Global| <sua assinatura>|
   |Conta de armazenamento das Configurações de Armazenamento Global| <sua conta de armazenamento>|
   |Conta de armazenamento das configurações do CustomCodeStorage|<sua conta de armazenamento>|
   |Contêiner de configurações do CustomCodeStorage|<seu contêiner de armazenamento>|

2. Na pasta **entradas** , abra **input.jsem**. Selecione **Adicionar entrada ao vivo** e adicione uma entrada de armazenamento de Azure data Lake Storage Gen2/BLOB, escolha **selecionar em sua assinatura do Azure**. Use as configurações padrão, exceto pelas seguintes:

   |Configuração|Valor sugerido|
   |-------|---------------|
   |Nome|Entrada|
   |Subscription|<sua assinatura>|
   |Conta de Armazenamento|<sua conta de armazenamento>|
   |Contêiner|<seu contêiner de armazenamento>|
   |Tipo de serialização|Escolher **personalizado**|
   |SerializationProjectPath|Selecione **escolher biblioteca caminho do projeto** em CodeLens e selecione o projeto de biblioteca **ProtobufDeserializer** criado na seção anterior. Selecione **Compilar projeto** para compilar o projeto|
   |SerializationClassName|Selecione **Selecionar classe de desserialização** de CodeLens para preencher automaticamente o nome da classe e o caminho da dll|
   |Nome da Classe|MessageBodyProto.MessageBodyDeserializer|

   :::image type="content" source="./media/custom-deserializer/create-input-vscode.png" alt-text="Adicionar entrada de desserializador personalizado.":::

3. Adicione a consulta a seguir ao arquivo **ProtobufCloudDeserializer. asaql** .

   ```sql
   SELECT * FROM Input
   ```

4. Baixe o [arquivo de entrada do exemplo protobuf](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). Na pasta **entradas** , clique com o botão direito do mouse em **input.js** e selecione **Adicionar entrada local**. Em seguida, clique duas vezes em **local_input1.js** e use as configurações padrão, exceto as configurações a seguir.

   |Configuração|Valor sugerido|
   |-------|---------------|
   |Selecionar caminho do arquivo local|Clique em CodeLens para selecionar < o caminho de arquivo para o arquivo de entrada protobuf de exemplo baixado>|

### <a name="execute-the-stream-analytics-job"></a>Executar o trabalho do Stream Analytics

1. Abra **ProtobufCloudDeserializer. asaql** e selecione **executar localmente** em CodeLens, em seguida, escolha **usar entrada local** na lista suspensa.

2. Observe **a guia resultados na exibição de diagrama** de trabalho à direita. Você também pode clicar nas etapas no diagrama de trabalho para exibir o resultado intermediário. Para obter mais detalhes, consulte [depurar localmente usando o diagrama de trabalho](debug-locally-using-job-diagram-vs-code.md).

   :::image type="content" source="./media/custom-deserializer/check-local-run-result-vscode.png" alt-text="Verifique o resultado da execução local.":::

Você implementou com êxito um desserializador personalizado para seu trabalho do Stream Analytics! Neste tutorial, você testou o desserializador personalizado localmente com dados de entrada locais. Você também pode testá-lo [usando a entrada de dados dinâmicos na nuvem](visual-studio-code-local-run-live-input.md). Para executar o trabalho na nuvem, você configurará corretamente a entrada e a saída. Em seguida, envie o trabalho para o Azure de Visual Studio Code para executar seu trabalho na nuvem usando o desserializador personalizado que você acabou de implementar.

### <a name="debug-your-deserializer"></a>Depurar o desserializador

Você pode depurar seu desserializador .NET localmente da mesma maneira que depura o código .NET padrão. 

1. Adicione pontos de interrupção em sua função do .NET.

2. Clique em **executar** na barra de atividade Visual Studio Code e selecione **criar uma launch.jsno arquivo**.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode.png" alt-text="Criar arquivo de inicialização.":::

   Escolha **ProtobufCloudDeserializer** e, em seguida, **Azure Stream Analytics** na lista suspensa.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode-2.png" alt-text="Criar arquivo de inicialização 2.":::

   Edite o **launch.jsno** arquivo para Replace <ASAScript> . asaql com ProtobufCloudDeserializer. asaql.
   :::image type="content" source="./media/custom-deserializer/configure-launch-file-vscode.png" alt-text="Configure o arquivo de inicialização.":::

3. Pressione **F5** para iniciar a depuração. O programa irá parar em seus pontos de interrupção conforme o esperado. Isso funciona tanto para entrada local quanto para dados de entrada ao vivo.

   :::image type="content" source="./media/custom-deserializer/debug-vscode.png" alt-text="Depurar desserializador personalizado.":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua o grupo de recursos, o trabalho de streaming e todos os recursos relacionados. A exclusão do trabalho evita a cobrança das unidades de streaming consumidas por ele. Se você está planejando usar o trabalho no futuro, pode interrompê-lo e reiniciar mais tarde, quando necessário. Se você não for mais usar o trabalho, exclua todos os recursos criados neste tutorial usando as seguintes etapas:

1. No menu à esquerda no Portal do Azure, selecione **Grupos de recursos** e selecione o nome do recurso criado.  

2. Em sua página de grupo de recursos, selecione **Excluir**, digite o nome do recurso para excluir na caixa de texto e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a implementar um desserializador .NET personalizado para a serialização de entrada do buffer de protocolo. Para saber mais sobre como criar desserializadores personalizados, vá para o seguinte artigo:

> [!div class="nextstepaction"]
> * [Criar desserializadores .NET diferentes para trabalhos do Azure Stream Analytics](custom-deserializer-examples.md)
> * [Testar Azure Stream Analytics trabalhos localmente com a entrada ao vivo usando Visual Studio Code](visual-studio-code-local-run-live-input.md)