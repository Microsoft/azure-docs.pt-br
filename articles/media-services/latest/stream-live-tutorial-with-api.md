---
Título: Transmissão ao vivo com os Serviços de Mídia v3: Descrição dos Serviços de Mídia do Azure: Saiba como transmitir ao vivo com os Serviços de Mídia do Azure v3.
services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: media ms.tgt_pltfrm: na ms.devlang: na ms.topic: tutorial ms.custom: "mvc, devx-track-csharp" ms.date: 06/13/2019 ms.author: inhenkel

---

# <a name="tutorial-stream-live-with-media-services"></a>Tutorial: Transmissão ao vivo com os Serviços de Mídia

> [!NOTE]
> Embora o tutorial use exemplos do [SDK do .NET](/dotnet/api/microsoft.azure.management.media.models.liveevent), as etapas gerais são as mesmas para [API REST](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event) ou outros [SDKs](media-services-apis-overview.md#sdks) com suporte. 

Nos Serviços de Mídia do Azure, [Eventos ao Vivo](/rest/api/media/liveevents) são responsáveis pelo processamento do conteúdo de transmissão ao vivo. Um Evento ao Vivo fornece um ponto de extremidade de entrada (URL de entrada) que você fornece a um codificador dinâmico. O Evento ao Vivo recebe fluxos de entrada ao vivo do codificador dinâmico e o disponibiliza para streaming por meio de um ou mais [Pontos de Extremidade de Streaming](/rest/api/media/streamingendpoints). Eventos ao Vivo também fornecem um ponto de extremidade de visualização (URL de visualização) usado para visualizar e validar o fluxo antes de processamento e entrega adicionais. Este tutorial mostra como usar o .NET Core para criar um tipo de **passagem** de um evento ao vivo.

Este tutorial mostra como:

> [!div class="checklist"]
> * Baixar o aplicativo de exemplo descrito no tópico.
> * Examinar o código que executa a transmissão ao vivo.
> * Assistir ao evento com o [Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) em [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net).
> * Limpe os recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes itens são necessários para concluir o tutorial:

- Instale o Visual Studio Code ou o Visual Studio.
- [Crie uma conta de Serviços de Mídia](./account-create-how-to.md).<br/>Não deixe de copiar os detalhes de acesso à API no formato JSON ou armazenar os valores necessários para se conectar à conta dos Serviços de Mídia no formato de arquivo .env usado neste exemplo.
- Siga as etapas em [Acessar API de Serviços de Mídia com a CLI do Azure](./access-api-howto.md) e salve as credenciais. Você precisará usá-las para acessar a API neste exemplo ou inseri-las no formato de arquivo .env. 
- Uma câmera ou um dispositivo (como um laptop) usado para transmitir um evento.
- Um codificador de software local que codifica seu fluxo de câmera e o envia ao serviço de transmissão ao vivo dos Serviços de Mídia usando o protocolo RTMP, confira [codificadores dinâmicos locais recomendados](encode-recommended-on-premises-live-encoders.md). O fluxo deve estar no formato **RTMP** ou **Smooth Streaming**.  
- Para este exemplo, é recomendável começar com um codificador de software como o [OBS (Open Broadcast Software) Studio](https://obsproject.com/download) (gratuito) para simplificar os primeiros passos. 

> [!TIP]
> Certifique-se de revisar [Transmissão ao vivo com Serviços de Mídia v3](stream-live-streaming-concept.md) antes de continuar. 

## <a name="download-and-configure-the-sample"></a>Baixar e configurar o exemplo

Clone o repositório do GitHub a seguir, que contém o exemplo de transmissão ao vivo do .NET em seu computador usando o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

A amostra de transmissão ao vivo está localizada na pasta [Dinâmica](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live).

Abra [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/appsettings.json) no projeto que você baixou. Substitua os valores pelas credenciais que você obteve de [acesso às APIs](./access-api-howto.md).

Observe que você também pode usar o formato de arquivo .env na raiz do projeto para definir suas variáveis de ambiente somente uma vez para todos os projetos no repositório de exemplos do .NET. Basta copiar o arquivo .env de exemplo, preencher as informações que você obtém na página de acesso à API dos Serviços de Mídia do portal do Azure ou na CLI do Azure.  Renomeie o arquivo .env de exemplo para apenas ".env" para usá-lo em todos os projetos.
O arquivo .gitignore já está configurado para evitar a publicação do conteúdo desse arquivo em seu repositório bifurcado. 

> [!IMPORTANT]
> Esta amostra usa um sufixo exclusivo para cada recurso. Se você cancelar a depuração ou encerrar o aplicativo sem executá-lo, finalizará vários Eventos ao Vivo em sua conta. <br/>Interrompa a execução de Eventos ao Vivo. Caso contrário, você será **cobrado**!

## <a name="examine-the-code-that-performs-live-streaming"></a>Examinar o código que executa a transmissão ao vivo

Esta seção examina as funções definidas no arquivo [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs) do projeto *LiveEventWithDVR*.

A amostra cria um sufixo exclusivo para cada recurso, de modo que não ocorra conflitos de nome se executar a amostra várias vezes sem limpeza.


### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a usar as APIs de Serviços de Mídia com a SDK .NET

Para começar a usar a APIs de Serviços de Mídia do Azure com o .NET, é necessário criar um objeto **AzureMediaServicesClient**. Para criar o objeto, você precisa fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Microsoft Azure Active Directory. No código que você clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no arquivo de configuração local (appsettings.json) ou por meio do arquivo de variáveis de ambiente .env localizado na raiz do repositório.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Criar um evento ao vivo

Esta seção mostra como criar um tipo de **passagem** do Evento ao Vivo (LiveEventEncodingType definido como Nenhum). Para obter mais informações sobre os outros tipos de eventos ao vivo disponíveis, confira [Tipos de evento ao vivo](live-event-outputs-concept.md#live-event-types). Além da passagem, você pode usar um evento ao vivo de transcodificação dinâmica para codificação de nuvem de taxa de bits adaptável de 720P ou 1080P. 
 
Algumas coisas que talvez você queira especificar ao criar o evento ao vivo são:

* O protocolo de ingestão para o evento ao vivo (atualmente, os protocolos RTMP e Smooth Streaming são compatíveis).<br/>Não é possível alterar a opção de protocolo enquanto o Evento ao Vivo ou suas Saídas ao Vivo associadas estiverem em execução. Se precisar de protocolos diferentes, crie um Evento ao Vivo separado para cada protocolo de streaming.  
* Restrições de IP sobre a ingestão e versão prévia. É possível definir os endereços IP que têm permissão para ingerir um vídeo neste Evento ao vivo. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo, '10.0.0.1'), um intervalo IP usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, '10.0.0.1/22) ou um intervalo IP usando um endereço IP e uma máscara de sub-rede com notação decimal com ponto (por exemplo, '10.0.0.1(255.255.252.0)').<br/>Se nenhum endereço IP for especificado e não houver definição de regra, nenhum endereço IP será permitido. Para permitir qualquer endereço IP, crie uma regra e defina 0.0.0.0/0.<br/>Os endereços IP devem estar em um dos formatos a seguir: endereço IPv4 com quatro números e intervalo de endereços CIDR.
* Ao criar o evento, é possível especificar sua inicialização automática. <br/>Quando a inicialização automática é definida como verdadeira, o evento em tempo real será iniciado após a criação. Isso significa que a cobrança começa assim que o Evento ao vivo começa a ser transmitido. É necessário chamar explicitamente o recurso Parar no Evento ao vivo para parar a cobrança adicional. Para saber mais, confira [Estados e cobrança do Evento ao vivo](live-event-states-billing-concept.md).
Também há modos de espera disponíveis para iniciar o evento ao vivo em um estado "alocado" de menor custo que acelera a mudança para um estado "Em execução". Isso é útil para situações como hotpools que precisam distribuir canais rapidamente para os streamers.
* Para que uma URL de ingestão seja preditiva e mais fácil de manter em um codificador dinâmico baseado em hardware, defina a propriedade "useStaticHostname" para true. Para obter informações detalhadas, consulte [URLs de ingestão de eventos ao vivo](live-event-outputs-concept.md#live-event-ingest-urls).

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Obter URLs de ingestão

Depois que o Evento ao Vivo é criado, você pode obter URLs de ingestão que você fornecerá ao codificador dinâmico. O codificador usa essas URLs para gerar entrada de um fluxo ao vivo.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Obter a URL de versão prévia

Use o previewEndpoint para visualizar e verificar se a entrada do codificador está realmente sendo recebida.

> [!IMPORTANT]
> Certifique-se de que o vídeo esteja fluindo para a URL de visualização antes de continuar.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Criar e gerenciar Eventos ao Vivo e Saídas ao Vivo

Uma vez que o fluxo está fluindo para o Evento ao Vivo, é possível começar o evento de transmissão criando um Ativo, uma Saída ao Vivo e um Localizador de Streaming. Isso vai arquivar o fluxo e torná-lo disponível para usuários por meio do ponto de extremidade de Streaming.

Ao aprender esses conceitos, é melhor considerar o objeto "Ativo" como a fita que você colocava em um vídeo cassete antigamente. A "Saída ao Vivo" é a máquina do gravador de fita cassete. O "Evento ao Vivo" é apenas o sinal de vídeo que entra na parte traseira da máquina.

Primeiro, você cria o sinal criando o "Evento ao Vivo".  O sinal não flui até que você inicie o Evento ao Vivo e conecte o codificador à entrada.

A fita pode ser criada a qualquer momento. Ela é apenas um "Ativo" vazio que você distribuirá para o objeto de Saída ao Vivo que, nesta analogia, é o gravador de fita cassete.

O gravador de fita cassete pode ser criado a qualquer momento. Ou seja, você pode criar uma Saída ao Vivo antes ou depois de iniciar o fluxo de sinal. Se você precisar acelerar as coisas, às vezes é útil criá-la antes de iniciar o fluxo de sinal.

Para interromper o gravador de fita cassete, chame delete na Saída ao Vivo. Isso não exclui o conteúdo do "Ativo" de fita.  O Ativo é sempre mantido com o conteúdo de vídeo arquivado até que você chame delete explicitamente no Ativo propriamente dito.

A próxima seção examinará a criação do Ativo ("fita") e da Saída ao Vivo ("gravador de fita cassete").

#### <a name="create-an-asset"></a>Criar um ativo

Criar um Ativo para a Saída ao Vivo usar. Na analogia acima, isso será a nossa fita, na qual registramos o sinal de vídeo ao vivo. Os visualizadores poderão ver o conteúdo ao vivo ou sob demanda por meio dessa fita virtual.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Criar uma Saída ao Vivo

As Saídas ao Vivo começam na criação e terminam quando são excluídas. Isso será o "gravador de fita cassete" para nosso evento. Ao excluir a Saída ao Vivo, você não está excluindo o Ativo subjacente nem o conteúdo do ativo. Pense nisso como a ação de ejetar a fita. O Ativo com a gravação durará o tempo que você desejar e, quando for ejetado (ou seja, quando a Saída ao Vivo for excluída), ele estará disponível para exibição sob demanda imediatamente. 

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Criar um Localizador de Streaming

> [!NOTE]
> Quando sua conta dos Serviços de Mídia é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. Para iniciar o streaming do conteúdo e aproveitar o [empacotamento dinâmico](encode-dynamic-packaging-concept.md) e a criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar no estado de **Execução**.

Quando você publica o Ativo usando um Localizador de Streaming, o Evento ao Vivo (até a duração da janela DVR) continuará visível até a expiração ou a exclusão do Localizador de Streaming, o que ocorrer primeiro. É assim que você disponibiliza a gravação de "fita" virtual para seu público-alvo de visualizadores para verem ao vivo e sob demanda. A mesma URL pode ser usada para assistir ao evento ao vivo, à janela DVR ou ao ativo sob demanda quando a gravação é concluída (quando a Saída ao Vivo é excluída.)

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateStreamingLocator)]

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Limpar recursos na conta dos Serviços de Mídia

Se você tiver terminado o fluxo de eventos e deseja limpar os recursos provisionados anteriormente, siga o procedimento a seguir:

* Pare de enviar o fluxo por push por meio do codificador.
* Interrompa o Evento ao Vivo. Depois que o Evento ao Vivo estiver parado, ele não incorrerá em nenhum encargo. Quando for necessário iniciá-lo novamente ele terá a mesma URL de ingestão, portanto, você não precisará reconfigurar seu codificador.
* Você pode parar seu ponto de extremidade de Streaming, a menos que você deseje continuar a fornecer o arquivo morto do evento ao vivo como um fluxo sob demanda. Se o Evento ao Vivo estiver parado, ele não incorrerá em nenhum encargo.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Assistir ao evento

Para assistir ao evento, copie a URL de streaming obtida quando você executou o código descrito em Criar um Localizador de Streaming. Você pode usar um player de mídia de sua escolha. O [Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) está disponível para testar sua transmissão em https://ampdemo.azureedge.net.

O Evento ao Vivo é convertido automaticamente em conteúdo sob demanda quando é interrompido. Mesmo depois de você parar e excluir o evento, os usuários poderão transmitir seu conteúdo arquivado como vídeo por demanda enquanto você não excluir o ativo. Não será possível excluir um ativo se este for usado por um evento; o evento deve ser excluído primeiro.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo as contas dos Serviços de Mídia e de armazenamento que você criou neste tutorial, exclua o grupo de recursos criados anteriormente.

Execute este comando da CLI:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Deixar o Evento ao Vivo em execução gera custos de cobrança. Esteja ciente de que, se o projeto/programa falhar ou for fechado por qualquer motivo, isso poderá deixar o Evento ao Vivo em execução em um estado de cobrança.

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

[Transmissão de arquivos](stream-files-tutorial-with-api.md)
 
