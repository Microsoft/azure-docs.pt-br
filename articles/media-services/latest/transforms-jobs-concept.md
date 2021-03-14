---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Campos obrigatórios. Veja mais em aka.ms/skyeye/meta.
Título: transformações e trabalhos nos serviços de mídia: descrição dos serviços de mídia do Azure: saiba como criar transformações para descrever as regras para processar seus vídeos nos serviços de mídia do Azure.
services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: ''

MS. Service: Media-Services MS. Workload: MS. Topic: conceitual MS. Date: 08/19/2019 MS. Author: inhenkel
---

# <a name="transforms-and-jobs-in-media-services"></a>Transformações e trabalhos nos serviços de mídia

Este tópico fornece detalhes sobre [transformações](/rest/api/media/transforms) e [trabalhos](/rest/api/media/jobs) e explica a relação entre essas entidades.

## <a name="overview"></a>Visão geral

### <a name="transformsjobs-workflow"></a>Fluxo de trabalho de transformações/trabalhos

O diagrama a seguir mostra o fluxo de trabalho de transformações/trabalhos:

![Fluxo de trabalho de transformações e trabalhos nos serviços de mídia do Azure](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Fluxo de trabalho típico

1. Crie uma transformação.
2. Envie trabalhos sob essa transformação.
3. Transformações de lista.
4. Exclua uma transformação se você não estiver planejando usá-la no futuro.

#### <a name="example"></a>Exemplo

Suponha que você quisesse extrair o primeiro quadro de todos os seus vídeos como uma imagem em miniatura – as etapas que você executaria são:

1. Defina a receita ou a regra para processar seus vídeos: "usar o primeiro quadro do vídeo como a miniatura".
2. Para cada vídeo, você diria ao serviço:
    1. Onde encontrar esse vídeo.
    2. Onde gravar a saída a imagem em miniatura de saída.

Uma **Transformação** ajuda você a criar uma vez a receita (Etapa 1) e enviar trabalhos usando essa receita (Etapa 2).

> [!NOTE]
> As propriedades da **transformação** e do **trabalho** do tipo DateTime estão sempre no formato UTC.

## <a name="transforms"></a>Transformações

Use **Transformações** para configurar tarefas comuns para codificar ou analisar vídeos. Cada **transformação** descreve uma receita ou um fluxo de trabalho de tarefas para processar seus arquivos de vídeo ou áudio. Uma única transformação pode aplicar mais de uma regra. Por exemplo, uma transformação poderia especificar que cada vídeo seja codificado em um arquivo MP4 em uma determinada taxa de bits, e que uma imagem em miniatura gerada desde o primeiro quadro do vídeo. Você precisaria adicionar uma entrada de TransformOutput para cada regra que você deseja incluir em sua Transformação. Você usa predefinições para informar à transformação como os arquivos de mídia de entrada devem ser processados.

### <a name="viewing-schema"></a>Exibindo esquema

Nos serviços de mídia v3, as predefinições são entidades fortemente tipadas na própria API. Você pode encontrar a definição de "esquema" para esses objetos em [especificação de API aberta (ou Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Você também pode exibir as definições predefinidas (como **StandardEncoderPreset**) na [API REST](/rest/api/media/transforms/createorupdate#standardencoderpreset), no [SDK do .net](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset)ou em outra documentação de referência do SDK dos serviços de mídia v3.

### <a name="creating-transforms"></a>Criando transformações

Você pode criar transformações usando REST, CLI ou qualquer um dos SDKs publicados. A API é controlada pelo Azure Resource Manager v3, portanto, você também pode usar modelos do Azure Resource Manager para criar Transformações na sua conta dos Serviços de Mídia do Microsoft Azure. O controle de acesso baseado em função do Azure pode ser usado para bloquear o acesso a transformações.

### <a name="updating-transforms"></a>Atualizando transformações

Se você precisar atualizar sua [transformação](/rest/api/media/transforms), use a operação de **atualização** . Ele se destina a fazer alterações na descrição ou nas prioridades do TransformOutputs subjacente. É recomendável que essas atualizações sejam feitas quando todos os trabalhos em andamento forem concluídos. Se você pretende reescrever a receita, precisa criar uma nova transformação.

### <a name="transform-object-diagram"></a>Diagrama de objeto de transformação

O diagrama a seguir mostra o objeto de **transformação** e os objetos referenciados por ele, incluindo as relações de derivação. As setas cinzas mostram um tipo que o trabalho referencia e as setas verdes mostram relações de derivação de classe.

Selecione a imagem para exibi-la no tamanho total.  

[![Diagrama que mostra o objeto de transformação e os objetos referenciados por ele, incluindo as relações de derivação de classe entre os objetos.](./media/api-diagrams/transform-small.png)](./media/api-diagrams/transform-large.png#lightbox)

## <a name="jobs"></a>Trabalhos

Um **trabalho** é a solicitação real aos serviços de mídia para aplicar a **transformação** a um determinado conteúdo de áudio ou vídeo de entrada. Quando a Transformação for criada, você poderá enviar trabalhos usando as APIs dos Serviços de Mídia ou um dos SDKs publicados. O **Trabalho** especifica informações como a localização do vídeo de entrada e a localização da saída. Você pode especificar o local do vídeo de entrada usando: URLs HTTPS, URLs SAS ou [ativos](/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Entrada de trabalho do HTTPS

Use a [entrada de trabalho do HTTPS](job-input-from-http-how-to.md) se seu conteúdo já estiver acessível por meio de uma URL e você não precisar armazenar o arquivo de origem no Azure (por exemplo, importar do S3). Esse método também é adequado se você tiver o conteúdo no armazenamento de BLOBs do Azure, mas não precisar que o arquivo esteja em um ativo. Atualmente, esse método dá suporte apenas a um único arquivo para entrada.

### <a name="asset-as-job-input"></a>Ativo como entrada do trabalho

Use o [ativo como entrada de trabalho](job-input-from-local-file-how-to.md) se o conteúdo de entrada já estiver em um ativo ou se o conteúdo estiver armazenado no arquivo local. Também é uma boa opção se você planeja publicar o ativo de entrada para streaming ou download (digamos que você deseja publicar o MP4 para download, mas também deseja fazer fala para detecção de texto ou facial). Esse método dá suporte a ativos de vários arquivos (por exemplo, conjuntos de streaming MBR que foram codificados localmente).

### <a name="checking-job-progress"></a>Verificando o andamento do trabalho

O progresso e o estado de trabalhos podem ser obtidos pelo monitoramento de eventos com a Grade de Eventos do Azure. Para obter mais informações, consulte [Monitorar eventos usando EventGrid](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Atualizando trabalhos

A operação de atualização na entidade de [trabalho](/rest/api/media/jobs) pode ser usada para modificar a *Descrição* e as propriedades de *prioridade* depois que o trabalho tiver sido enviado. Uma alteração na propriedade *priority* só será eficaz se o trabalho ainda estiver na fila. Se o trabalho tiver iniciado o processamento ou tiver sido concluído, a alteração da prioridade não terá qualquer efeito.

### <a name="job-object-diagram"></a>Diagrama de objeto de trabalho

O diagrama a seguir mostra o objeto de **trabalho** e os objetos que ele faz referência, incluindo as relações de derivação.

Clique na imagem para exibi-la em tamanho normal.  

[![Diagrama que mostra o objeto de trabalho e os objetos referenciados, incluindo as relações de derivação de classe entre os objetos.](./media/api-diagrams/job-small.png)](./media/api-diagrams/job-large.png#lightbox)

## <a name="configure-media-reserved-units"></a>Configurar unidades reservadas de mídia

Para trabalhos de análise de vídeo e análise de áudio disparados pelos serviços de mídia V3 ou Video Indexer, é altamente recomendável provisionar sua conta com 10 MRUs (unidades reservadas de mídia) S3. Se você precisar de mais de 10 MRUs do S3, abra um ticket de suporte usando o [Portal do Microsoft Azure](https://portal.azure.com/).

Para obter detalhes, confira [Dimensionar o processamento de mídia com a CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="see-also"></a>Confira também

* [Códigos de erro](/rest/api/media/jobs/get#joberrorcode)
* [Filtragem, classificação, paginação de entidades dos Serviços de Mídia](entities-overview.md)

## <a name="next-steps"></a>Próximas etapas

- Antes de começar a desenvolver, examine o [desenvolvimento com as APIs dos serviços de mídia v3](media-services-apis-overview.md) (inclui informações sobre como acessar APIs, convenções de nomenclatura, etc.)
- Confira estes tutoriais:

    - [Tutorial: codificar um arquivo remoto com base na URL e transmitir o vídeo](stream-files-tutorial-with-rest.md)
    - [Tutorial: carregar, codificar e transmitir vídeos](stream-files-tutorial-with-api.md)
    - [Tutorial: Analisar vídeos com os Serviços de Mídia v3](analyze-videos-tutorial-with-api.md)
