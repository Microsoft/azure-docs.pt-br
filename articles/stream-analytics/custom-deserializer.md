---
title: Tutorial – Desserializadores .NET personalizados para trabalhos de nuvem do Azure Stream Analytics
description: Este tutorial demonstra como criar um desserializador .NET personalizado para um trabalho de nuvem do Azure Stream Analytics usando o Visual Studio.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/17/2020
ms.openlocfilehash: 6db65d85b5d5d72e7b4764949abcffed004cae85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018200"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Tutorial: Desserializadores .NET personalizados para o Azure Stream Analytics

O Azure Stream Analytics tem [suporte interno para três formatos de dados](stream-analytics-parsing-json.md): JSON, CSV e Avro. Com os desserializadores .NET personalizados, você pode ler dados de outros formatos, como [Buffer de Protocolo](https://developers.google.com/protocol-buffers/), [Bond](https://github.com/Microsoft/bond) e outros formatos definidos pelo usuário para trabalhos na nuvem e na borda.

Este tutorial demonstra como criar um desserializador .NET personalizado para um trabalho de nuvem do Azure Stream Analytics usando o Visual Studio. Para aprender a criar desserializadores do .NET no Visual Studio Code, confira [Criar desserializadores do .NET para trabalhos do Azure Stream Analytics no Visual Studio Code](visual-studio-code-custom-deserializer.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * criar um desserializador personalizado para o buffer de protocolo.
> * criar um trabalho do Azure Stream Analytics no Visual Studio
> * configurar seu trabalho do Stream Analytics para usar o desserializador personalizado.
> * Executar seu trabalho do Stream Analytics localmente para testar e depurar o desserializador personalizado.


## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Instale o [Visual Studio 2019 (recomendado)](https://www.visualstudio.com/downloads/) ou o [Visual Studio 2017](https://www.visualstudio.com/vs/older-downloads/). As edições Enterprise (Ultimate/Premium), Professional, Community têm suporte. Não há suporte para a edição Express. 

* [Instale as ferramentas do Stream Analytics para o Visual Studio](stream-analytics-tools-for-visual-studio-install.md) ou atualize para a versão mais recente. 

* Abra o **Cloud Explorer** no Visual Studio e entre na sua assinatura do Azure.

* Crie um contêiner em sua Conta de Armazenamento do Azure.
O contêiner que você criar será usado para armazenar os ativos relacionados ao seu trabalho do Stream Analytics. Se você já tiver uma conta de armazenamento com contêineres existentes, você pode usá-los. Se não, você precisará [criar um novo contêiner](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="create-a-custom-deserializer"></a>Criar um desserializador personalizado

1. Abra o Visual Studio e selecione **Arquivo -> Novo -> Projeto**. Pesquise pelo **Stream Analytics** e selecione **Projeto de Desserializador Personalizado do Azure Stream Analytics (.NET)**. Dê um nome ao projeto, como **Protobuf Deserializer**.

   ![Criar projeto de biblioteca de classes padrão do Visual Studio dotnet](./media/custom-deserializer/create-dotnet-library-project.png)

2. No Gerenciador de Soluções, clique com o botão direito do mouse em seu projeto **Protobuf Deserializer** e selecione **Gerenciar Pacotes NuGet** no menu. Em seguida, instale os pacotes NuGet **Microsoft.Azure.StreamAnalytics** e **Google.Protobuf**.

3. Adicione a [classe MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) e a [classe MessageBodyDeserializer](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) ao seu projeto.

4. Compile o projeto **Desserializador Protobuf**.

## <a name="add-an-azure-stream-analytics-project"></a>Adicionar um projeto do Azure Stream Analytics

1. No Gerenciador de Soluções, clique com o botão direito do mouse na solução **Protobuf Deserializer** e selecione **Adicionar > Novo Projeto**. Em **Azure Stream Analytics > Stream Analytics**, escolha **Aplicativo Azure Stream Analytics**. Dê a ele o nome **ProtobufCloudDeserializer** e selecione **OK**. 

2. Clique com o botão direito do mouse em **Referências** no projeto **ProtobufCloudDeserializer** do Azure Stream Analytics. Em **Projetos**, adicione **Protobuf Deserializer**. Ele deve ser preenchido automaticamente para você.

## <a name="configure-a-stream-analytics-job"></a>Configurar um trabalho do Stream Analytics

1. Clique duas vezes em **JobConfig.json**. Use as configurações padrão, exceto pelas seguintes:

   |Configuração|Valor sugerido|
   |-------|---------------|
   |Recurso de Configurações de Armazenamento Global|Escolha fonte de dados da conta atual|
   |Assinatura de Configurações de Armazenamento Global| <sua assinatura>|
   |Conta de armazenamento das Configurações de Armazenamento Global| <sua conta de armazenamento>|
   |Recurso de Configurações de Armazenamento de Código Personalizado|Escolha fonte de dados da conta atual|
   |Conta de Armazenamento de Configurações de Armazenamento de Código Personalizado|<sua conta de armazenamento>|
   |Contêiner de Configurações de Armazenamento de Código Personalizado|<seu contêiner de armazenamento>|

2. Em **Entradas**, clique duas vezes em **Input.json**. Use as configurações padrão, exceto pelas seguintes:

   |Configuração|Valor sugerido|
   |-------|---------------|
   |Fonte|Armazenamento de Blobs|
   |Recurso|Escolha fonte de dados da conta atual|
   |Assinatura|<sua assinatura>|
   |Conta de Armazenamento|<sua conta de armazenamento>|
   |Contêiner|<seu contêiner de armazenamento>|
   |Formato de serialização do evento|Outro (Protobuf, XML, proprietário…)|
   |Recurso|Carregar de Referência de Projeto ASA ou CodeBehind|
   |Nome do Assembly CSharp|ProtobufDeserializer.dll|
   |Nome da Classe|MessageBodyProto.MessageBodyDeserializer|
   |Tipo de Compactação do Evento|Nenhum|

3. Adicione a consulta a seguir ao arquivo **Script.asaql**.

   ```sql
   SELECT * FROM Input
   ```

4. Baixe o [arquivo de entrada do exemplo protobuf](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). Na pasta **Entradas**, clique com botão direito do mouse em **Input.json** e selecione **Adicionar Entrada Local**. Em seguida, clique duas vezes em **local_Input.json** e defina as seguintes configurações:

   |Configuração|Valor sugerido|
   |-------|---------------|
   |Alias de entrada|Entrada|
   |Tipo de Fonte|Fluxo de dados|
   |Formato de serialização do evento|Outro (Protobuf, XML, proprietário…)|
   |Nome do Assembly CSharp|ProtobufDeserializer.dll|
   |Nome da Classe|MessageBodyProto.MessageBodyDeserializer|
   |Caminho do Arquivo de Entrada Local|< o caminho para o arquivo de entrada protobuf de exemplo baixado >|

## <a name="execute-the-stream-analytics-job"></a>Executar o trabalho do Stream Analytics

1. Abra **Script.asaql** e selecione **Executar Localmente**.

2. Observe os resultados em **Resultados de Execução Local do Stream Analytics**.

Você implementou com êxito um desserializador personalizado para seu trabalho do Stream Analytics! Neste tutorial, você testou o desserializador personalizado localmente. Para seus dados reais, você configuraria corretamente a entrada e a saída. Em seguida, enviaria o trabalho para o Azure do Visual Studio para executá-lo na nuvem usando o desserializador personalizado que acabou de implementar.

## <a name="debug-your-deserializer"></a>Depurar o desserializador

Você pode depurar seu desserializador .NET localmente da mesma maneira que depura o código .NET padrão.

1. Clique com o botão direito do mouse no nome do projeto **ProtobufCloudDeserializer** e defina-o como projeto de inicialização.

2. Adicionar pontos de interrupção à sua função.

3. Pressione **F5** para iniciar a depuração. O programa irá parar em seus pontos de interrupção conforme o esperado.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua o grupo de recursos, o trabalho de streaming e todos os recursos relacionados. A exclusão do trabalho evita a cobrança das unidades de streaming consumidas por ele. Se você está planejando usar o trabalho no futuro, pode interrompê-lo e reiniciar mais tarde, quando necessário. Se você não for mais usar o trabalho, exclua todos os recursos criados neste tutorial usando as seguintes etapas:

1. No menu à esquerda no Portal do Azure, selecione **Grupos de recursos** e selecione o nome do recurso criado.  

2. Em sua página de grupo de recursos, selecione **Excluir**, digite o nome do recurso para excluir na caixa de texto e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a implementar um desserializador .NET personalizado para a serialização de entrada do buffer de protocolo. Para saber mais sobre como criar desserializadores personalizados, vá para o seguinte artigo:

> [!div class="nextstepaction"]
> [Criar desserializadores .NET diferentes para trabalhos do Azure Stream Analytics](custom-deserializer-examples.md)