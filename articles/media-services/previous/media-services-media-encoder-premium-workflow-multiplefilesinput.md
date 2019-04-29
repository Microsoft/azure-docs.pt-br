---
title: Vários arquivos de entrada e propriedades de componente com o Codificador Premium - Azure | Microsoft Docs
description: Este tópico explica como usar setRuntimeProperties para usar vários arquivos de entrada e transmitir dados personalizados para o processador de mídia do Fluxo de Trabalho Premium do Codificador de Mídia.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: xpouyat;anilmur;juliako
ms.openlocfilehash: 608ca4bc3b58dd3c718d6239f90260154d2f6c3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465246"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Usando vários arquivos de entrada e propriedades do componente com o Codificador Premium
## <a name="overview"></a>Visão geral
Há situações em que talvez você precise personalizar as propriedades do componente, especificar o conteúdo XML da Lista de Clipes ou enviar vários arquivos de entrada ao enviar uma tarefa com o processador de mídia **Fluxo de trabalho Premium de codificação de mídia** . Alguns exemplos incluem:

* Sobreposição de texto em vídeo e definição do valor do texto (por exemplo, a data atual) no tempo de execução de cada vídeo de entrada.
* Personalização do XML da Lista de Clipes (para especificar um ou vários arquivos de origem, com ou sem corte etc.).
* Sobreposição de uma imagem de logotipo no vídeo de entrada durante a codificação do vídeo.
* Vários idiomas de áudio codificação.

Para permitir que o **Fluxo de Trabalho Premium do Codificador de Mídia** reconheça que você está alterando algumas propriedades no fluxo de trabalho ao criar a tarefa ou enviar vários arquivos de entrada, é necessário usar uma cadeia de caracteres de configuração que contenha **setRuntimeProperties** e/ou **transcodeSource**. Este tópico explica como usá-los.

## <a name="configuration-string-syntax"></a>Sintaxe da cadeia de caracteres de configuração
A cadeia de caracteres de configuração a ser definida na tarefa de codificação usa um documento XML com esta aparência:

```xml
<?xml version="1.0" encoding="utf-8"?>
<transcodeRequest>
  <transcodeSource>
  </transcodeSource>
  <setRuntimeProperties>
    <property propertyPath="Media File Input/filename" value="VideoFileName.mp4" />
  </setRuntimeProperties>
</transcodeRequest>
```

Veja a seguir o código C# que lê a configuração XML em um arquivo, o atualiza com o nome de arquivo de vídeo correto e a transfere para a tarefa em um trabalho:

```csharp
string premiumConfiguration = ReadAllText(@"D:\home\site\wwwroot\Presets\SetRuntime.xml").Replace("VideoFileName", myVideoFileName);

// Declare a new job.
IJob job = _context.Jobs.Create("Premium Workflow encoding job");

// Get a media processor reference, and pass to it the name of the 
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

// Create a task with the encoding details, using a string preset.
ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                              processor,
                              premiumConfiguration,
                              TaskOptions.None);

// Specify the input assets
task.InputAssets.Add(workflow); // workflow asset
task.InputAssets.Add(video); // video asset with multiple files

// Add an output asset to contain the results of the job. 
// This output is specified as AssetCreationOptions.None, which 
// means the output asset is not encrypted. 
task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
```

## <a name="customizing-component-properties"></a>Personalizando as propriedades do componente
### <a name="property-with-a-simple-value"></a>Propriedade com um valor simples
Em alguns casos, é útil personalizar uma propriedade do componente junto com o arquivo de fluxo de trabalho que será executado pelo Fluxo de Trabalho Premium do Codificador de Mídia.

Suponha que você tenha criado um fluxo de trabalho que sobrepõe texto em vídeos, e que o texto (por exemplo, a data atual) deva ser definido em tempo de execução. É possível fazer isso enviando o texto a ser definido como o novo valor da propriedade de texto do componente de sobreposição por meio da tarefa de codificação. Você pode usar esse mecanismo para alterar outras propriedades de um componente no fluxo de trabalho (por exemplo, a posição ou a cor da sobreposição, a taxa de bits do codificador AVC etc.).

**setRuntimeProperties** é usado para substituir uma propriedade nos componentes do fluxo de trabalho.

Exemplo:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
      <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
  </setRuntimeProperties>
</transcodeRequest>
```

### <a name="property-with-an-xml-value"></a>Propriedade com um valor XML
Para definir uma propriedade que espere um valor XML, encapsule-a usando `<![CDATA[ and ]]>`.

Exemplo:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

> [!NOTE]
> Lembre-se de não colocar um retorno de carro logo após `<![CDATA[`.

### <a name="propertypath-value"></a>Valor de propertyPath
Nos exemplos anteriores, propertyPath era “/Entrada de Arquivo de Mídia/nomearquivo”, “/inactiveTimeout” ou “clipListXml”.
Em geral, esse é o nome do componente que, por sua vez, é o nome da propriedade. O caminho pode ter mais ou menos níveis, como "/primarySourceFile" (pois a propriedade está na raiz do fluxo de trabalho) ou "/Processamento de Vídeo/Sobreposição Gráfica/Opacidade" (pois a Sobreposição está em um grupo).    

Para verificar o caminho e o nome da propriedade, use o botão de ação ao lado de cada propriedade. Você pode clicar nesse botão de ação e escolher **Editar**. Isso mostrará o nome da propriedade e, logo acima dele, o namespace.

![Ação/Editar](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Propriedade](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Vários arquivos de entrada
Cada tarefa enviada para o **Fluxo de Trabalho Premium do Codificador de Mídia** exige dois ativos:

* O primeiro é um *Ativo de Fluxo de Trabalho* que contém um arquivo de fluxo de trabalho. Você pode criar arquivos de fluxo de trabalho usando o [Designer de Fluxo de Trabalho](media-services-workflow-designer.md).
* O segundo é um *Ativo de Mídia* que contém o(s) arquivo(s) de mídia que você deseja codificar.

Ao enviar vários arquivos de mídia para o codificador **Fluxo de Trabalho Premium do Codificador de Mídia** , as seguintes restrições se aplicam:

* Todos os arquivos de mídia devem estar no mesmo *Ativo de Mídia*. Não há suporte para o uso de vários Ativos de Mídia.
* Você deve definir o arquivo primário neste Ativo de Mídia (de preferência, esse é o arquivo de vídeo principal que o codificador deverá processar).
* É necessário transmitir os dados de configuração que incluem o elemento **setRuntimeProperties** e/ou **transcodeSource** para o processador.
  * **setRuntimeProperties** é usado para substituir a propriedade de nome do arquivo ou outra propriedade nos componentes do fluxo de trabalho.
  * **transcodeSource** é usado para especificar o conteúdo XML da Lista de Clipes.

Conexões no fluxo de trabalho:

* Se você usar um ou vários componentes de Entrada do Arquivo de Mídia e pretende usar **setRuntimeProperties** para especificar o nome do arquivo, não conecte o pino do componente do arquivo primário a eles. Verifique se não há nenhuma conexão entre o objeto do arquivo primário e a(s) Entrada(s) do Arquivo de Mídia.
* Se você preferir usar o XML da Lista de Clipes e um componente da Fonte de Mídia, poderá conectar os dois juntos.

![Nenhuma conexão do Arquivo de Fonte Primária à Entrada do Arquivo de Mídia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Não haverá conexão do arquivo primário com o(s) componente(s) de Entrada do Arquivo de Mídia se você usar setRuntimeProperties para definir a propriedade de nome do arquivo.*

![Conexão do XML da Lista de Clipes à Fonte da Lista de Clipes](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*É possível conectar o XML da Lista de Clipes à Fonte de Mídia e usar transcodeSource.*

### <a name="clip-list-xml-customization"></a>Personalização do XML da Lista de Clipes
É possível especificar o XML da Lista de Clipes no fluxo de trabalho, em tempo de execução, usando **transcodeSource** no XML da cadeia de caracteres de configuração. Isso exige que o marcador do XML da Lista de Clipes esteja conectado ao componente de Fonte de Mídia no fluxo de trabalho.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <transcodeSource>
      <clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
      </clipList>
    </transcodeSource>
    <setRuntimeProperties>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Se você deseja especificar /primarySourceFile para usar essa propriedade, para nomear os arquivos de saída usando 'Expressões', é recomendável transmitir o XML da Lista de Clipes como uma propriedade, *após* a propriedade /primarySourceFile, para evitar que a Lista de Clipes seja substituída pela configuração de /primarySourceFile.

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Com outro corte preciso de quadro:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Exemplo 1 : Sobrepor uma imagem no vídeo

### <a name="presentation"></a>Apresentação
Considere um exemplo no qual você deseja sobrepor uma imagem de logotipo no vídeo de entrada durante a codificação do vídeo. Neste exemplo, o vídeo de entrada chamado "Microsoft_HoloLens_Possibilities_816p24.mp4" e o logotipo é chamado "PNG". Você deve executar as seguintes etapas:

* Criar um Ativo de Fluxo de Trabalho com o arquivo de fluxo de trabalho (veja o exemplo a seguir).
* Crie um ativo de mídia que contém dois arquivos: MyInputVideo.mp4 como o arquivo primário e MYLOGO.
* Enviar uma tarefa para o processador de mídia Fluxo de Trabalho Premium do Codificador de Mídia com os ativos de entrada indicados acima e especificar a cadeia de caracteres de configuração a seguir.

Configuração:

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

No exemplo acima, o nome do arquivo de vídeo é enviado ao componente de Entrada do Arquivo de Mídia e à propriedade primarySourceFile. O nome do arquivo de logotipo é enviado para outra Entrada do Arquivo de Mídia que está conectada ao componente de sobreposição de gráfico.

> [!NOTE]
> O nome do arquivo de vídeo é enviado à propriedade primarySourceFile. A razão disso é para que essa propriedade seja usada no fluxo de trabalho na criação do nome do arquivo de saída correto usando Expressões, por exemplo.

### <a name="step-by-step-workflow-creation"></a>Criação de fluxo de trabalho passo a passo
Estas são as etapas para criar um fluxo de trabalho que usa dois arquivos como entrada: um vídeo e uma imagem. Isto fará a sobreposição da imagem na parte superior do vídeo.

Abra o **Designer de Fluxo de Trabalho** e selecione **Arquivo** > **Novo Workspace** > **Transcodificar Esquema**.

O novo fluxo de trabalho mostra três elementos:

* Arquivo de Origem Principal
* XML da Lista de Clipes
* Arquivo/Ativo de Saída  

![Novo fluxo de trabalho de codificação](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Novo fluxo de trabalho de codificação*

Para aceitar o arquivo de mídia de entrada, comece adicionando um componente de Entrada do Arquivo de Mídia. Para adicionar um componente ao fluxo de trabalho, procure-o na caixa de pesquisa do Repositório e arraste a entrada desejada até o painel do designer.

Em seguida, adicione o arquivo de vídeo que será usado para criar o fluxo de trabalho. Para fazer isso, clique no painel da tela de fundo do Designer de Fluxo de Trabalho e procure a propriedade do Arquivo de Origem Primário no painel de propriedades do lado direito. Clique no ícone de pasta e selecione o arquivo de vídeo apropriado.

![Fonte de Arquivo Primário](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Fonte de Arquivo Primário*

Em seguida, especifique o arquivo de vídeo no componente de Entrada do Arquivo de Mídia.   

![Fonte de Entrada do Arquivo de Mídia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Fonte de Entrada do Arquivo de Mídia*

Assim que isso for feito, o componente Entrada do Arquivo de Mídia inspecionará o arquivo e preencherá seus pinos de saída para refletir o arquivo inspecionado por ele.

A próxima etapa é adicionar um “Atualizador de Tipo de Dados de Vídeo” para especificar o espaço de cores de Rec.709. Adicione um "Conversor de Formato de Vídeo" definido como tipo de Layout de Dados/Layout = Planar Configurável. Isso converterá a transmissão de vídeo em um formato que pode ser usado como uma fonte do componente de sobreposição.

![Atualizador de tipo de dados e conversor de formato de vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Atualizador de tipo de dados e conversor de formato de vídeo*

![Tipo de layout = Planar Configurável](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*O Tipo de layout é Planar Configurável*

Em seguida, adicione um componente de Sobreposição de Vídeo e conecte o marcador de vídeo (descompactado) ao marcador de vídeo (descompactado) da entrada do arquivo de mídia.

Adicione outra Entrada do Arquivo de Mídia (para carregar o arquivo de logotipo), clique nesse componente e renomeie-o para "Logotipo de Entrada do Arquivo de Mídia" e escolha uma imagem (um arquivo .png, por exemplo) na propriedade do arquivo. Conecte o marcador da imagem Descompactada ao marcador da imagem Descompactada da sobreposição.

![Fonte de arquivo de imagem e componente de sobreposição](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Fonte de arquivo de imagem e componente de sobreposição*

Se quiser modificar a posição do logotipo no vídeo (por exemplo, talvez você queira posicioná-lo a 10% do canto superior esquerdo do vídeo), desmarque a caixa de seleção "Entrada Manual". É possível fazer isso porque você está usando uma Entrada do Arquivo de Mídia para fornecer o arquivo de logotipo ao componente de sobreposição.

![Posição da sobreposição](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Posição da sobreposição*

Para codificar a transmissão de vídeo para H.264, adicione os componentes do Codificador de Vídeo AVC e do codificador AAC à superfície do designer. Conecte os marcadores.
Configure o codificador AAC e escolha Conversão de Formato de Áudio/Predefinição: 2.0 (L, R).

![Codificadores de Áudio e Vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Codificadores de Áudio e Vídeo*

Agora, adicione os componentes **Multiplexador ISO Mpeg-4** e **Saída de Arquivo** e conecte os pinos, conforme mostrado.

![Multiplexador MP4 e saída de arquivo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*Multiplexador MP4 e saída de arquivo*

É necessário definir o nome do arquivo de saída. Clique no componente **Saída de Arquivo** e edite a expressão do arquivo:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nome de saída do arquivo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nome de saída do arquivo*

Você pode executar o fluxo de trabalho localmente para verificar se ele é executado corretamente.

Após a conclusão, você poderá executá-lo nos Serviços de Mídia do Azure.

Primeiro, prepare um ativo nos Serviços de Mídia do Azure com dois arquivos: o arquivo de vídeo e o logotipo. Você pode fazer isso usando a API REST ou o .NET. Também é possível fazer isso usando o portal do Azure ou o AMSE ( [Gerenciador dos Serviços de Mídia do Azure](https://github.com/Azure/Azure-Media-Services-Explorer) ).

Este tutorial mostra como gerenciar ativos com o AMSE. Há duas maneiras de adicionar arquivos a um ativo:

* Crie uma pasta local, copie os dois arquivos nela e arraste e solte a pasta na guia **Ativo** .
* Carregue o arquivo de vídeo como um ativo, exiba as informações do ativo, vá para a guia Arquivos e carregue um arquivo adicional (logotipo).

> [!NOTE]
> Lembre-se de definir um arquivo primário no ativo (o arquivo de vídeo principal).

![Arquivos de ativo no AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Arquivos de ativo no AMSE*

Selecione o ativo e escolha codificá-lo com o Codificador Premium. Carregue o fluxo de trabalho e selecione-o.

Clique no botão para transmitir dados ao processador e adicione o seguinte XML para definir as propriedades de tempo de execução:

![Codificador Premium no AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Codificador Premium no AMSE*

Em seguida, cole os dados de XML a seguir. Você precisa especificar o nome do arquivo de vídeo para a Entrada do Arquivo de Mídia e para primarySourceFile. Especifique o nome do arquivo para o logotipo também.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*

Se você usar o SDK do .NET para criar e executar a tarefa, esses dados XML deverão ser transmitidos como a cadeia de caracteres de configuração.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Após a conclusão do trabalho, o arquivo MP4 no ativo de saída exibirá a sobreposição!

![Sobreposição no vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Sobreposição no vídeo*

Você pode baixar o fluxo de trabalho de exemplo no [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Exemplo 2: Várias codificações de idioma de áudio

Um exemplo de fluxo de trabalho de codificação de vários idiomas de áudio está disponível em [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Esta pasta contém um fluxo de trabalho de exemplo que pode ser usado para codificar um arquivo MXF para um ativo de arquivos MP4 múltiplas com várias faixas de áudio.

Esse fluxo de trabalho assume que o arquivo MXF contém uma faixa de áudio; as faixas de áudio adicionais devem ser passadas como arquivos separados de áudios (WAV ou... MP4).

Para codificar, siga estas etapas:

* Crie um ativo dos serviços de mídia com o arquivo MXF e os arquivos de áudio (arquivos de áudio de 0 a 18).
* Certifique-se de que o arquivo MXF é definido como um arquivo primário.
* Crie um trabalho e uma tarefa usando o processador do codificador de fluxo de trabalho Premium. Use o fluxo de trabalho fornecido (MultiMP4-1080p-19audio-v1.workflow).
* Passar os dados setruntime.xml para a tarefa (se você usar o Gerenciador de serviços de mídia do Azure, use o botão "passar dados xml para o fluxo de trabalho").
  * Atualize os dados XML para especificar as marcas de idiomas e nomes de arquivo correto.
  * O fluxo de trabalho tem componentes de áudio denominado 1 áudio para áudio 18.
  * RFC5646 há suporte para a marca de idioma.

```xml
<?xml version="1.0" encoding="utf-16"?>
<transcodeRequest>
  <setRuntimeProperties>
    <property propertyPath="Media File Input Video/filename" value="MainVideo.mxf" />
    <property propertyPath="Language/language_code" value="en" />
    <property propertyPath="/primarySourceFile" value="MainVideo.mxf" />
    <property propertyPath="Audio 1/Media File Input/filename" value="french-audio.wav" />
    <property propertyPath="Audio 1/Language/language_code" value="fr" />
    <property propertyPath="Audio 2/Media File Input/filename" value="german-audio.wav" />
    <property propertyPath="Audio 2/Language/language_code" value="de" />
    <property propertyPath="Audio 3/Media File Input/filename" value="japanese-audio.wav" />
    <property propertyPath="Audio 3/Language/language_code" value="ja" />
  </setRuntimeProperties>
</transcodeRequest>
```

* O ativo codificado conterá várias faixas de áudio de idiomas e essas faixas devem ser selecionáveis no Player de Mídia do Azure.

## <a name="see-also"></a>Consulte também
* [Apresentando a codificação Premium nos Serviços de Mídia do Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [How to use Premium Encoding in Azure Media Services (Como usar a codificação Premium nos Serviços de Mídia do Azure)](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Encoding on-demand content with Azure Media Services (Codificação do conteúdo sob demanda com os Serviços de Mídia do Azure)](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Codecs e formatos de fluxo de trabalho do Media Encoder Premium](media-services-premium-workflow-encoder-formats.md)
* [Exemplos de arquivos de fluxo de trabalho](https://github.com/Azure/azure-media-services-samples)
* [Ferramenta do Explorador dos Serviços de Mídia do Azure](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
