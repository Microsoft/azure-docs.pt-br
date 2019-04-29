---
title: Tutoriais avançados do fluxo de trabalho do Codificador de Mídia Premium
description: Este documento contém instruções passo a passo que mostram como executar tarefas avançadas com o Fluxo de trabalho do Codificador de Mídia Premium e também como criar fluxos de trabalho complexos com o Designer de Fluxo de Trabalho.
services: media-services
documentationcenter: ''
author: xstof
manager: femila
editor: ''
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: christoc;xpouyat;juliako
ms.openlocfilehash: d227e3618c138e6661cc4be7caa2b9a3ba1af3f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61240396"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Tutoriais avançados do fluxo de trabalho do Codificador de Mídia Premium
## <a name="overview"></a>Visão geral
Este documento contém instruções passo a passo que mostram como personalizar fluxos de trabalho com o **Designer de Fluxo de Trabalho**. Encontre os arquivos de fluxo de trabalho [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>SUMÁRIO
Os tópicos a seguir serão abordados:

* [Codificando MXF em um MP4 de taxa de bits única](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Iniciando um novo fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Usando a Entrada do Arquivo de Mídia](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Inspecionando fluxos de mídia](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Adicionando um codificador de vídeo para geração de arquivo .MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Codificando o fluxo de áudio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multiplexando fluxos de áudio e de vídeo em um contêiner MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Gravando o arquivo MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Criando um Ativo dos Serviços de Mídia do arquivo de saída](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Testar o fluxo de trabalho concluído localmente](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Codificando MXF em MP4s com várias taxas bits - empacotamento dinâmico habilitado](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Adicionando uma ou mais saídas MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Configurando os nomes de saída do arquivo](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Adicionando uma Trilha de Áudio separada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Adicionando o arquivo SMIL “ISM”](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Codificando MXF em MP4 com várias taxas de bit - plano gráfico aprimorado](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * Visão geral do fluxo de trabalho para aprimoramento
  * [Convenções de nomenclatura do arquivo](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Publicando as propriedades do componente na raiz do fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Faça com que os nomes de arquivo de saída gerados dependam dos valores de propriedade publicados](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Adicionando miniaturas à saída MP4 com várias taxas de bit](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * Visão geral do fluxo de trabalho para adição de miniaturas
  * [Adicionando codificação JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Lidando com a conversão de espaço de cor](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Gravando as miniaturas](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Detectando erros em um fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Fluxo de trabalho concluído](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Corte baseado em tempo da saída MP4 com várias taxas de bits](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Visão geral do fluxo de trabalho para começar a adição do corte](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Usando o corte de fluxo](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Fluxo de trabalho concluído](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Introdução ao componente com script](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Criando scripts em um fluxo de trabalho: hello world](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Corte baseado em quadro da saída MP4 com várias taxas de bits](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Visão geral do plano gráfico para começar a adição do corte](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Como usar o XML da lista de clipes](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Modificando a lista de clipes de um componente com script](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Adicionando uma propriedade de conveniência ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>Codificando MXF em um MP4 de taxa de bits única
Esta seção demonstra como criar um arquivo MP4 de taxa de bits única com áudio codificado em AAC-HE a partir de um arquivo de entrada .MXF.

### <a id="MXF_to_MP4_start_new"></a>Iniciando um novo fluxo de trabalho
Abra o Designer de Fluxo de Trabalho e selecione Arquivo &gt; Novo Workspace &gt; Transcodificar Esquema

O novo fluxo de trabalho mostra três elementos:

* Arquivo de Origem Principal
* XML da Lista de Clipes
* Arquivo/Ativo de Saída  

![Novo fluxo de trabalho de codificação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Novo fluxo de trabalho de codificação*

### <a id="MXF_to_MP4_with_file_input"></a>Usando a Entrada do Arquivo de Mídia
Para aceitar o arquivo de mídia de entrada, é necessário começar com a adição de um componente de Entrada do Arquivo de Mídia. Para adicionar um componente ao fluxo de trabalho, procure-o na caixa de pesquisa do Repositório e arraste a entrada desejada até o painel do designer. Repita a ação com a Entrada do Arquivo de Mídia e conecte o componente de Arquivo de Origem Principal ao pino de entrada Nome de arquivo na Entrada do Arquivo de Mídia.

![Entrada do Arquivo de Mídia conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Entrada do Arquivo de Mídia conectado*

Inicialmente, identifique um arquivo de exemplo adequado para usar durante a criação de um fluxo de trabalho personalizado. Para fazer isso, clique no plano de fundo do painel do designer e procure pela propriedade do Arquivo de Origem Principal no painel de propriedades do lado direito. Clique no ícone de pasta e selecione o arquivo com o qual deseja testar o fluxo de trabalho. O componente de Entrada do Arquivo de Mídia inspeciona o arquivo e preenche seus pinos de saída para refletir os detalhes do arquivo de exemplo inspecionado por ele.

![Entrada do Arquivo de Mídia preenchida](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Entrada do Arquivo de Mídia preenchida*

Agora que a entrada está preenchida, a próxima etapa é configurar as configurações de codificação de saída. Assim como o Arquivo de Origem Principal foi configurado, configure a propriedade da Variável da pasta de saída, logo abaixo.

![Propriedades de Entrada e Saída configuradas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Propriedades de Entrada e Saída configuradas*

### <a id="MXF_to_MP4_streams"></a>Inspecionando fluxos de mídia
Com frequência, convém saber como é o fluxo à medida que ele percorre o fluxo de trabalho. Para inspecionar um fluxo em qualquer ponto do fluxo de trabalho, basta clicar em um pino de saída ou de entrada em qualquer um dos componentes. Nesse caso, tente clicar no pino de saída Vídeo Descompactado na Entrada do Arquivo de Mídia. Uma caixa de diálogo é aberta, permitindo a inspeção do vídeo de saída.

![Inspeção do pino de saída Vídeo Descompactado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Inspeção do pino de saída Vídeo Descompactado*

Neste caso, ele mostra que o vídeo contém uma entrada de 1920 x 1080 a 24 quadros por segundo em uma amostragem de 4:2:2 de um vídeo de quase 2 minutos.

### <a id="MXF_to_MP4_file_generation"></a>Adicionando um codificador de vídeo para geração de arquivo .MP4
Agora, um pino de saída Vídeo Descompactado e vários pinos de saída Áudio Descompactados estão disponíveis para uso na Entrada do Arquivo de Mídia. Para codificar o vídeo de entrada, um componente de codificação precisa ser adicionado ao fluxo de trabalho - nesse caso, para geração de arquivos .MP4.

Para codificar o fluxo de vídeo para H.264, adicione o componente do Codificador de Vídeo AVC à superfície do designer. Esse componente recebe um fluxo de vídeo descompactado como entrada e fornece um fluxo de vídeo compactado AVC em seu pino de saída.

![Codificador AVC desconectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Codificador AVC desconectado*

Suas propriedades determinam como a codificação ocorre exatamente. Vamos analisar algumas das configurações mais importantes:

* Largura da Saída e Altura da Saída: determinam a resolução do vídeo codificado. Nesse caso, 640x360 é uma configuração válida.
* Taxa de Quadros: quando definida como passagem, adotará a taxa de quadros de origem, mas é possível substituir isso. Essa conversão de taxa de quadros não tem compensação de movimento.
* Perfil e Nível: determinam o perfil e o nível do AVC. Para saber mais sobre os diferentes níveis e perfis de forma conveniente, clique no ícone de interrogação no componente do Codificador de vídeo AVC e a página de Ajuda mostrará mais detalhes sobre cada um dos níveis. Neste exemplo, use o Perfil Principal no nível 3.2 (padrão).
* Modo de Controle de Taxa e Taxa de bits (kbps): neste cenário, opte por uma saída de taxa de bits constante (CBR) de 1200 kbps
* Formato de Vídeo: fornece informações sobre a VUI (Informação de Uso do Vídeo) gravada no fluxo H.264 (informações que podem ser usadas por um decodificador para melhorar a exibição, mas que não são essenciais para decodificar corretamente):
* NTSC (normalmente para os Estados Unidos ou o Japão, usando 30 qps)
* PAL (normalmente para a Europa, usando 25 qps)
* Modo de Tamanho de GOP: configure o Tamanho de GOP Fixo para nossos objetivos com um Intervalo de Chave de dois segundos com GOPs Fechados. A configuração de 2 segundos garante a compatibilidade com o empacotamento dinâmico fornecido pelos Serviços de Mídia do Azure.

Para alimentar o codificador de AVC, conecte o pino de saída Vídeo Descompactado do componente de Entrada de arquivo de mídia ao pino de entrada Vídeo Descompactado do codificador de AVC.

![Codificador AVC conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Codificador Principal de AVC conectado*

### <a id="MXF_to_MP4_audio"></a>Codificando o fluxo de áudio
Neste ponto, o fluxo de áudio descompactado original ainda precisa ser compactado. Para compactação do fluxo de áudio, adicione um componente de codificador AAC (Dolby) ao fluxo de trabalho.

![Codificador AVC desconectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Codificador AAC desconectado*

Agora há uma incompatibilidade: há apenas um pino de entrada áudio descompactado no Codificador AAC, enquanto é bem provável que a Entrada do Arquivo de Mídia tenha fluxos de áudio descompactados diferentes disponíveis: um para o canal de áudio esquerdo e outro para o direito. (Se você estiver lidando com som surround, serão seis canais.) Portanto, não é possível conectar diretamente o áudio da origem da Entrada do arquivo de mídia no codificador de áudio AAC. O componente AAC espera um chamado fluxo de áudio chamado "intercalado": um único fluxo que possui os canais esquerdo e direito intercalados entre si. Quando soubermos de nosso arquivo de mídia de origem quais faixas de áudio estão em determinada posição na origem, poderemos gerar esse fluxo de áudio intercalado com as posições de alto-falante corretamente atribuídas para os lados esquerdo e direito.

Primeiro, convém gerar um fluxo intercalado dos canais de áudio de origem exigidos. O componente Intercalador do Fluxo de Áudio trata disso para nós. Adicione-o ao fluxo de trabalho e conecte as saídas de áudio da Entrada do Arquivo de Mídia nele.

![Intercalador do fluxo de áudio conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Intercalador do fluxo de áudio conectado*

Agora que temos um fluxo de áudio intercalado, ainda não especificamos a qual local devemos atribuir as posições esquerda ou direita do alto-falante. Para especificar isso, podemos usar o Atribuidor de posição de alto-falante.

![Adicionando um Atribuidor de Posição do Alto-falante](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Adicionando um Atribuidor de Posição do Alto-falante*

Configure o Atribuidor de Posição de Alto-falante para uso com um fluxo de entrada estéreo por meio de um Filtro de Predefinição do Codificador "Personalizado" e da Predefinição de Canal chamada "2.0 (L, R)". (Isso atribui a posição do alto-falante esquerdo ao canal 1, e a posição do alto-falante direito ao canal 2.)

Conecte a saída do Atribuidor de Posição do Alto-falante à entrada do Codificador AAC. Em seguida, instrua o Codificador AAC a trabalhar com uma Predefinição de Canal "2.0 (L, R)", para que ele saiba lidar com o áudio estéreo como uma entrada.

### <a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexando fluxos de áudio e de vídeo em um contêiner MP4
Com base em nosso fluxo de vídeo codificado em AVC e em nosso fluxo de áudio codificado em AAC, podemos capturar ambos em um contêiner .MP4. O processo de mistura de fluxos diferentes em um único fluxo é chamado de "multiplexação" (ou "muxing"). Nesse caso, estamos intercalando os fluxos de áudio e de vídeo em um único pacote .MP4 coerente. O componente que coordena isso para um contêiner .MP4 é chamado de Multiplexador ISO MPEG-4. Adicione esse componente à superfície do designer e conecte o Codificador de vídeo AVC e o Codificador AAC às suas entradas.

![Multiplexador MPEG4 conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Multiplexador MPEG4 conectado*

### <a id="MXF_to_MP4_writing_mp4"></a>Gravando o arquivo MP4
O componente Saída do Arquivo é usado durante a gravação de um arquivo de saída. Podemos conectá-lo à saída do Multiplexador ISO MPEG-4 para que sua saída seja gravada em disco. Para fazer isso, conecte o pino de saída Contêiner (MPEG-4) ao pino de entrada Gravação do Arquivo de Saída.

![Saída do arquivo conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Saída do arquivo conectado*

O nome do arquivo usado é determinado pela propriedade Arquivo. Embora essa propriedade possa ser codificada para um determinado valor, convém defini-la por meio de uma expressão.

Para que o fluxo de trabalho determine automaticamente a propriedade de nome do Arquivo de saída a partir de uma expressão, clique no botão ao lado do Nome do arquivo (ao lado do ícone de pasta). No menu suspenso, selecione "Expressão". Isso abre o editor de expressão. Primeiro, limpe o conteúdo do editor.

![Editor de expressão vazio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Editor de expressão vazio*

O editor de expressão permite a inserção de qualquer valor literal, combinado com uma ou mais variáveis. As variáveis começam com um sinal de cifrão. Quando você pressiona a tecla $, o editor mostra uma caixa suspensa com as variáveis disponíveis para sua escolha. Em nosso caso, usaremos uma combinação da variável do diretório de saída e da variável básica de nome do arquivo de entrada:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Editor de expressão preenchido](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Editor de expressão preenchido*

> [!NOTE]
> Para ver um arquivo de saída de seu trabalho de codificação no Azure, você deve fornecer um valor no editor de expressão.
>
>

Quando você confirma a expressão clicando em ok, a janela de propriedade mostra para que valor a propriedade Arquivo é resolvida neste momento.

![Diretório de saída de resolução da Expressão do Arquivo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Diretório de saída de resolução da Expressão do Arquivo*

### <a id="MXF_to_MP4_asset_from_output"></a>Criando um Ativo dos Serviços de Mídia do arquivo de saída
Embora tenhamos escrito um arquivo de saída MP4, ainda precisamos indicar que este arquivo pertence ao ativo de saída que os Serviços de Mídia geram como resultado da execução deste fluxo de trabalho. Para essa finalidade, usamos o nó Arquivo/Ativo de Saída na tela do fluxo de trabalho. Todos os arquivos recebidos nesse nó fazem parte do ativo resultante dos Serviços de Mídia do Azure.

Conecte o componente Saída do Arquivo ao componente Arquivo/Ativo de Saída para concluir o fluxo de trabalho.

![Fluxo de trabalho concluído](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Fluxo de trabalho concluído*

### <a id="MXF_to_MP4_test"></a>Testar o fluxo de trabalho concluído localmente
Para testar o fluxo de trabalho localmente, pressione o botão Executar na barra de ferramentas superior. Após a conclusão da execução do fluxo de trabalho, inspecione a saída gerada na pasta de saída configurada. Você verá o arquivo de saída MP4 concluído que foi codificado a partir do arquivo de origem de entrada MXF.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>Codificação do MXF em MP4 - empacotamento dinâmico com várias taxas bits habilitado
Este passo a passo cria um conjunto de arquivos MP4 com várias taxas de bits e áudio codificado em AAC a partir de um único arquivo de entrada .MXF.

Quando houver o desejo por uma saída de ativo com várias taxas de bit para uso junto com os recursos de Empacotamento dinâmico oferecidos pelos Serviços de Mídia do Azure, será necessário gerar vários arquivos MP4 alinhados com GOP de cada taxa de bits e resolução diferentes. Para fazer isso, o passo a passo [Codificação do MXF em um MP4 de taxa de bits única](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) fornece um bom ponto de partida.

![Como iniciar o fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Como iniciar o fluxo de trabalho*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Adicionando uma ou mais saídas MP4
Cada arquivo MP4 em nosso ativo resultante dos Serviços de Mídia do Azure oferece suporte a uma taxa de bits e resolução diferentes. Vamos adicionar um ou mais arquivos de saída MP4 ao fluxo de trabalho.

Para termos certeza de que todos os nossos codificadores de vídeo foram criados com as mesmas configurações, é mais conveniente duplicar o Codificador de vídeo AVC já existente e configurar outra combinação de resolução e taxa de bits (vamos adicionar uma de 960 x 540 a 25 quadros por segundo a 2.5 Mbps). Para duplicar o codificador existente, copie e cole-o na superfície do designer.

Conecte o pino de saída Vídeo Descompactado da Entrada do Arquivo de Mídia ao nosso novo componente AVC.

![Segundo codificador AVC conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Segundo codificador AVC conectado*

Agora, adapte a configuração de nosso novo codificador AVC para a saída 960 x 540 a 2.5 Mbps. (Use as propriedades "Largura de saída", "Altura de saída" e "Taxa de bits (kbps)" para isso).

Considerando que queremos usar o ativo resultante junto com o empacotamento dinâmico dos Serviços de Mídia do Azure, o ponto de extremidade de streaming precisa ser capaz de gerar a partir dos fragmentos MP4/DASH HLS/Fragmentados desses arquivos MP4, que são exatamente alinhados entre si de uma maneira que os clientes que alternam entre taxas de bits diferentes obtenham uma experiência de vídeo e áudio contínua e suave. Para fazer isso acontecer, precisamos garantir que o tamanho do GOP ("grupo de imagens") dos dois arquivos MP4 seja definido como dois segundos nas propriedades dos codificadores AVC, o que pode ser feito da seguinte forma:

* Definindo o Modo de Tamanho do GOP como o tamanho GOP Fixo e
* O Intervalo de Quadro Principal como dois segundos.
* Além disso, defina o Controle IDR do GOP como GOP Fechado a fim de garantir que todos os GOPs fiquem ativos sem dependências

Para facilitar a compreensão desse fluxo de trabalho, renomeie o primeiro codificador AVC como "Codificador de Vídeo AVC 640 x 360 1200 kbps" e o segundo codificador AVC como"Codificador de Vídeo AVC 960 x 540 2500 kbps".

Agora, adicione um segundo Multiplexador ISO MPEG-4 e uma segunda Saída de arquivo. Conecte o multiplexador ao novo codificador AVC e certifique-se de que sua saída seja direcionada para o Arquivo de saída. Conecte também a saída do codificador de áudio AAC à entrada do novo multiplexador. A Saída do arquivo, por sua vez, pode ser conectada ao nó Arquivo/Ativo de Saída a fim de adicioná-la ao Ativo dos Serviços de Mídia que será criado.

![Segundo Muxer e Saída do arquivo conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Segundo Muxer e Saída do arquivo conectado*

Para manter a compatibilidade com o empacotamento dinâmico dos Serviços de Mídia do Azure, configure Modo de Partes do multiplexador para contagem ou duração de GOP e defina os GOPs por parte como 1. (Esse deve ser o padrão).

![Modos de parte do muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Modos de parte do muxer*

Observação: convém repetir esse processo para qualquer outra combinação de resolução e taxa de bits que você deseja adicionar à saída do ativo.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Configurando os nomes de saída do arquivo
Mais de um arquivo foi adicionado ao ativo de saída. Com isso, é necessário se certificar de que os nomes de arquivo de cada um dos arquivos de saída sejam diferentes uns dos outros, e talvez até mesmo aplicar uma convenção de nomenclatura de arquivo para que fique claro, a partir do nome do arquivo, com o você está lidando.

A nomenclatura de saída do arquivo pode ser controlada por meio de expressões no designer. Abra o painel de propriedades de um dos componentes de Saída do arquivo, e abra o editor de expressão para a propriedade Arquivo. Nosso primeiro arquivo de saída foi configurado por meio da seguinte expressão (consulte o tutorial para ir de um [MXF para uma saída MP4 taxa de bits única](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Isso significa que nosso nome de arquivo é determinado por duas variáveis: o diretório de saída para gravação e o nome base do arquivo de origem. O primeiro é exposto como uma propriedade na raiz do fluxo de trabalho, e o último é determinado pelo arquivo de entrada. O diretório de saída é usado para teste local; essa propriedade será substituída pelo mecanismo de fluxo de trabalho, quando o fluxo de trabalho for executado pelo processador de mídia baseado em nuvem nos Serviços de Mídia do Azure.
Para atribuir aos nossos dois arquivos de saída uma nomenclatura de saída consistente, altere a primeira expressão de nomenclatura de arquivo para:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

e a segunda para:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Execute uma execução de teste intermediária para se certificar de que os dois arquivos de saída MP4 foram gerados corretamente.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Adicionando uma Trilha de Áudio separada
Como veremos posteriormente ao gerarmos um arquivo .ism para acompanhar nossos arquivos de saída MP4, também precisaremos de um arquivo MP4 somente de áudio como a trilha de áudio para nosso streaming adaptável. Para criar esse arquivo, adicione outro muxer ao fluxo de trabalho (Multiplexador ISO-MPEG-4) e conecte o pino de saída do codificador AAC ao seu pino de entrada para a Trilha 1.

![Muxer de áudio adicionado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Muxer de áudio adicionado*

Crie um terceiro componente de Saída do arquivo para mostrar o fluxo de saída do muxer e configurar a expressão de nomenclatura do arquivo como:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Criação de Saída do arquivo pelo muxer de áudio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Criação de Saída do arquivo pelo muxer de áudio*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Adicionando o arquivo SMIL .ISM
Para o empacotamento dinâmico funcionar junto com os dois arquivos MP4 (e com o MP4 somente de áudio) em nosso ativo dos Serviços de Mídia, também precisamos de um arquivo de manifesto (também chamado de arquivo “SMIL”: Linguagem de integração de multimídia sincronizada). Esse arquivo indica aos Serviços de Mídia do Azure quais são os arquivos MP4 que estão disponíveis para empacotamento dinâmico e quais deles devem ser considerados para o streaming de áudio. Um arquivo de manifesto típico para um conjunto de MP4s com um único fluxo de áudio tem a seguinte aparência:

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="https://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>
```

O arquivo .ism contém uma instrução switch, uma referência a cada um dos arquivos de vídeo MP4 individuais e também uma (ou mais) referências de arquivo de áudio para um MP4 que contém apenas o áudio.

A geração do arquivo de manifesto para nosso conjunto de MP4s pode ser realizada por meio de um componente chamado de "Gravador de Manifesto AMS". Para usá-lo, arraste-o para a superfície e conecte os pinos de saída "Gravação Concluída" dos três componentes de Saída de arquivo com a entrada do Gravador de Manifesto AMS. Em seguida, conecte a saída do Gravador de Manifesto AMS ao Arquivo/Ativo de Saída.

Assim como nossos outros componentes de saída do arquivo, configure o nome de saída do arquivo .ism com uma expressão:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Nosso fluxo de trabalho concluído é semelhante ao seguinte:

![Fluxo de trabalho concluído de MXF para MP4 com várias taxas de bit](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Fluxo de trabalho concluído de MXF para MP4 com várias taxas de bit*

## <a id="MXF_to__multibitrate_MP4"></a>Codificando MXF em MP4 com várias taxas de bit - plano gráfico aprimorado
No [passo a passo do fluxo de trabalho anterior](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) vimos como um único ativo de entrada MXF pode ser convertido em um ativo de saída com arquivos MP4 com várias taxas de bits, um arquivo MP4 somente áudio e um arquivo de manifesto para uso em conjunto com o empacotamento dinâmico dos Serviços de Mídia do Azure.

Este passo a passo mostra como alguns dos aspectos podem ser aprimorados e tornados mais convenientes.

### <a id="MXF_to_multibitrate_MP4_overview"></a>Visão geral do fluxo de trabalho para aprimoramento
![Fluxo de trabalho de MP4 com várias taxas de bit para aprimoramento](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Fluxo de trabalho de MP4 com várias taxas de bit para aprimoramento*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>Convenções de nomenclatura do arquivo
No fluxo de trabalho anterior, especificamos uma expressão simples como base para a geração de nomes de arquivo de saída. No entanto, temos algumas duplicações: todos os componentes individuais do arquivo de saída especificaram essa expressão.

Por exemplo, nosso componente de saída de arquivo para o primeiro arquivo de vídeo está configurado com esta expressão:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Enquanto no segundo vídeo de saída, temos uma expressão como esta:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Não seria mais claro, menos propenso a erros e mais conveniente se pudéssemos remover algumas essas duplicações e tornar as coisas mais configuráveis? Felizmente, podemos fazer isso: os recursos de expressão do designer em combinação com a capacidade de criar propriedades personalizadas na raiz de nosso fluxo de trabalho nos proporcionará um pouco mais de conveniência.

Vamos supor que obteremos a configuração de nome do arquivo com base nas taxas de bits dos arquivos MP4 individuais. Nosso objetivo é configurar essas taxas de bit em um local central (na raiz de nosso grafo), de onde poderão ser acessadas para configurar e realizar a geração de nome de arquivo. Para fazer isso, começamos publicando a propriedade de taxa de bits a partir dos dois codificadores AVC até a raiz de nosso fluxo de trabalho, de modo que fique acessível a partir da raiz e dos codificadores AVC. (Mesmo se for exibido em dois pontos diferentes, haverá apenas um valor subjacente).

### <a id="MXF_to__multibitrate_MP4_publishing"></a>Publicando as propriedades do componente na raiz do fluxo de trabalho
Abra o primeiro codificador AVC, vá até a propriedade Taxa de bits (kbps) e, no menu suspenso, selecione Publicar.

![Publicando a propriedade de taxa de bits](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publicando a propriedade de taxa de bits*

Configure a caixa de diálogo Publicar para publicar a raiz do nosso grafo de fluxo de trabalho, com o nome publicado de "video1bitrate" e um nome de exibição legível de "Taxa de Bits do Vídeo 1". Configure um nome do grupo personalizado chamado "Taxas de Bits de Streaming" e toque em Publicar.

![Publicando a propriedade de taxa de bits](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Caixa de diálogo de publicação para a propriedade de taxa de bits*

Repita o mesmo para a propriedade de taxa de bits do segundo codificador AVC e nomeie-o como "video2bitrate" com um nome de exibição de "Taxas de Bits do Vídeo 2", no mesmo grupo personalizado "Taxas de Bits de Streaming".

Se agora inspecionarmos as propriedades raiz do fluxo de trabalho, veremos nosso grupo personalizado com as duas propriedades publicados em exibição. Ambas refletem o valor de suas respectivas taxas de bits do codificador AVC.

![Propriedades de taxa de bits publicadas na raiz do fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Sempre que quisermos acessar essas propriedades a partir do código ou de uma expressão, poderemos fazer isso da seguinte maneira:

* no código embutido de um componente logo abaixo da raiz: node.getPropertyAsString('../video1bitrate', null)
* em uma expressão: ${ROOT_video1bitrate}

Vamos concluir o grupo "Taxas de Bits de Streaming" publicando também a taxa de bits de nossa trilha de áudio. Nas propriedades do Codificador AAC, procure pela configuração de Taxa de bits e selecione Publicar no menu suspenso ao lado dela. Publique na raiz do grafo com o nome "audio1bitrate" e exiba o nome "Taxa de Bits do Áudio 1" em nosso grupo personalizado "Taxas de Bits de Streaming".

![Caixa de diálogo de publicação para a taxa de bits de áudio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Caixa de diálogo de publicação para a taxa de bits de áudio*

![Propriedades de áudio e vídeos resultantes na raiz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Propriedades de áudio e vídeos resultantes na raiz*

A alteração de qualquer um desses três valores também reconfigura e altera os valores nos respectivos componentes aos quais estão vinculados (e dos quais foram publicados).

### <a id="MXF_to__multibitrate_MP4_output_files"></a>Faça com que os nomes de arquivo de saída gerados dependam dos valores de propriedade publicados
Em vez de codificar nossos nomes de arquivo gerados, podemos mudar nossa expressão de nome de arquivo em cada um dos componentes de Arquivo de saída, a fim de depender das propriedades de taxa de bits que publicamos na raiz do grafo. Começando com nossa primeira saída de arquivo, encontre a propriedade Arquivo e edite a expressão da seguinte forma:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Os parâmetros diferentes nessa expressão podem ser acessados e inseridos pressionando o sinal de cifrão no teclado enquanto estiver na janela de expressão. Um dos parâmetros disponíveis é nossa propriedade video1bitrate que publicamos anteriormente.

![Acessando parâmetros dentro de uma expressão](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Acessando parâmetros dentro de uma expressão*

Faça o mesmo para a saída de arquivo de nosso segundo vídeo:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

e para a saída do arquivo somente de áudio:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Se agora alterarmos a taxa de bits de qualquer um dos arquivos de áudio ou de vídeo, o respectivo codificador será reconfigurado e a convenção de nomenclatura de arquivos com base na taxa de bits será respeitada automaticamente.

## <a id="thumbnails_to__multibitrate_MP4"></a>Adicionando miniaturas à saída MP4 com várias taxas de bit
Começando em um fluxo de trabalho que gera [uma saída MP4 com várias taxas de bit a partir de uma entrada MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), agora vamos analisar como adicionar miniaturas à saída.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>Visão geral do fluxo de trabalho para adição de miniaturas
![Fluxo de trabalho de MP4 com várias taxas de bit para começar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Fluxo de trabalho de MP4 com várias taxas de bit para começar*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Adicionando codificação JPG
A essência de nossa geração de miniaturas será o componente Codificador de JPG, capaz de gerar arquivos JPG.

![Codificador de JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*Codificador de JPG*

No entanto, não podemos conectar diretamente nosso fluxo de Vídeo Descompactado da Entrada do Arquivo de Mídia para o codificador de JPG. Em vez disso, ele espera receber quadros individuais. Isso podemos fazer por meio do componente Portão de quadro do vídeo.

![Conectando um portão de quadro ao codificador de JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Conectando um portão de quadro ao codificador de JPG*

O portão de quadro permite, uma vez a cada vários segundos ou quadros, a passagem de um quadro de vídeo. O intervalo e o deslocamento de tempo com base no qual isso ocorre pode ser configurado nas propriedades.

![Propriedades do Portão de Quadro do Vídeo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Propriedades do Portão de Quadro do Vídeo*

Vamos criar uma miniatura a cada minuto definindo o modo como Tempo (segundos) e o Intervalo como 60.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>Lidando com a conversão de espaço de cor
Embora pareça lógico que os pinos de Vídeo Descompactado do portão de quadro e a Entrada do Arquivo de Mídia possam ser conectados agora, receberíamos um aviso se fizéssemos isso.

![Erro no espaço de cor de entrada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Erro no espaço de cor de entrada*

Isso ocorre por que a maneira com a qual as informações de cores são representadas em nosso fluxo original bruto de vídeo descompactado, provenientes de nosso MXF, é diferente do esperado pelo Codificador de JPG. Mais especificamente, algo chamado "espaço de cores" de "RGB" ou "Escala de cinza" é esperado. Isso significa que o fluxo de vídeo de entrada do Portão de Quadro do Vídeo precisa ter uma conversão aplicada com relação a seu espaço de cores.

Arraste o Conversor de Espaço de Cor - Intel até o fluxo de trabalho e conecte-o ao nosso portão de quadro.

![Conexão de um Conversor de Espaço de Cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Conexão de um Conversor de Espaço de Cor*

Na janela Propriedades, selecione a entrada BGR 24 na lista de Predefinições.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Gravando as miniaturas
Diferente do nossos vídeos MP4, o componente do Codificador de JPG produz mais de um arquivo. Para lidar com isso, é possível usar um componente de Gravador de arquivo JPG de Pesquisa de cena: ele usa as miniaturas JPG recebidas e as grava, com cada nome de arquivo recebendo como sufixo um número diferente. (O número normalmente indicando o número de segundos/unidades no fluxo do qual a miniatura foi extraída.)

![Apresentação do Gravador de arquivo JPG de Pesquisa de cena](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Apresentação do Gravador de arquivo JPG de Pesquisa de cena*

Configure a propriedade Caminho da pasta de saída com a expressão: ${ROOT_outputWriteDirectory}

e a propriedade Prefixo de nome de arquivo com:

    ${ROOT_sourceFileBaseName}_thumb_

O prefixo determina como os arquivos de miniatura estão sendo chamados. Eles recebem um sufixo com um número indicando a posição da miniatura no fluxo.

![Propriedades do Gravador de arquivo JPG de Pesquisa de cena](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Propriedades do Gravador de arquivo JPG de Pesquisa de cena*

Conecte o Gravador de arquivo JPG de Pesquisa de Cena ao nó Arquivo/Ativo de Saída.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>Detectando erros em um fluxo de trabalho
Conecte a entrada do conversor de espaço de cor à saída de vídeo descompactado bruto. Agora execute um teste local no fluxo de trabalho. Há uma boa chance de a execução do fluxo de trabalho parar de repente e indicar com um contorno vermelho no componente que encontrou um erro:

![Erro no Conversor de Espaço de Cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Erro no Conversor de Espaço de Cor*

Clique no pequeno ícone "E" vermelho no canto superior direito do componente Conversor de Espaço de Cor para ver o motivo pelo qual a tentativa de codificação falhou.

![Caixa de diálogo do erro no Conversor de Espaço de Cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Caixa de diálogo do erro no Conversor de Espaço de Cor*

Na verdade, como você pode ver, o padrão do espaço de cor recebido para o conversor de espaço de cor deve ser rec601 para nossa conversão solicitada de YUV para RGB. Aparentemente, nosso fluxo não indica seu rec601. (Rec 601 é um padrão de codificação de sinais de vídeo analógicos entrelaçados no formato de vídeo digital. Ele especifica uma região ativa cobrindo 720 amostras de luminosidade e 360 exemplos de crominância por linha. O sistema de codificação de cores é conhecido como YCbCr 4:2:2).

Para corrigir isso, indicaremos nos metadados de nosso fluxo que estamos lidando com conteúdo rec601. Para fazer isso, usaremos um componente Atualizador de tipo de dados de vídeo, que colocaremos entre nossa origem bruta e o componente de conversão de espaço de cor. Esse atualizador de tipo de dados permite a atualização manual de determinadas propriedades de tipo de dados de vídeo. Configure-o para indicar um Padrão de espaço de cor de "Rec 601". Isso faz com que o Atualizador de Tipo de Dados do Vídeo marque o fluxo com o espaço de cores "Rec 601", caso ainda não exista um espaço de cores definido. (Ele não substituirá os metadados existentes, a menos que a caixa de seleção Substituir tenha sido marcada).

![Atualizando o padrão de espaço de cores com o Atualizador de tipo de dados](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Atualizando o padrão de espaço de cores com o Atualizador de tipo de dados*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>Fluxo de trabalho concluído
Agora que o fluxo de trabalho foi concluído, realize outro teste de execução para vê-lo passar.

![Fluxo de trabalho concluído para várias saídas mp4 com miniaturas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Fluxo de trabalho concluído para várias saídas mp4 com miniaturas*

## <a id="time_based_trim"></a>Corte baseado em tempo da saída MP4 com várias taxas de bits
Começando em um fluxo de trabalho que gera [uma saída MP4 com várias taxas de bit a partir de uma entrada MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), agora vamos analisar como cortar o vídeo de origem com base em carimbos de data e hora.

### <a id="time_based_trim_start"></a>Visão geral do fluxo de trabalho para começar a adição do corte
![Iniciando o fluxo de trabalho para adição do corte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Iniciando o fluxo de trabalho para adição do corte*

### <a id="time_based_trim_use_stream_trimmer"></a>Usando o corte de fluxo
O componente Corte de Fluxo permite cortar o início e o final de um fluxo de entrada com base em informações de tempo (segundos, minutos...). O corte não oferece suporte ao corte baseado em quadros.

![Corte de fluxo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Corte de fluxo*

Em vez de vincular diretamente os codificadores AVC e o atribuidor de posição de alto-falante à Entrada do Arquivo de Mídia, colocaremos entre eles o corte de fluxo. (Um para o sinal de vídeo e outro para o sinal de áudio intercalado).

![Como colocar o Corte de fluxo entre eles](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Como colocar o Corte de fluxo entre eles*

Vamos configurar o corte para processarmos apenas o vídeo e o áudio entre 15 e 60 segundos do vídeo.

Acesse as propriedades do Corte de fluxo de vídeo e configure as propriedades de Hora de início (15 s) e Hora de término (60 s). Para termos certeza de que o corte de áudio e de vídeo estará sempre configurado com os mesmos valores de início e de término, os publicamos na raiz do fluxo de trabalho.

![Publicar a propriedade de hora de início do Corte de Fluxo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publicar a propriedade de hora de início do Corte de Fluxo*

![Publicar o diálogo da propriedade para a hora de início](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Publicar o diálogo da propriedade para a hora de início*

![Publicar o diálogo da propriedade para a hora de término](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Publicar o diálogo da propriedade para a hora de término*

Se agora inspecionarmos a raiz de nosso fluxo de trabalho, as duas propriedades estarão claramente em exibição e poderão ser configuradas a partir daí.

![Propriedades publicadas disponíveis na raiz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Propriedades publicadas disponíveis na raiz*

Agora, abra as propriedades de corte do corte de áudio e configure os horários de início e de término com uma expressão que faça referência às propriedades publicadas na raiz de nosso fluxo de trabalho.

Para a hora de início do corte de áudio:

    ${ROOT_TrimmingStartTime}

e para a hora de término:

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>Fluxo de trabalho concluído
![Fluxo de trabalho concluído](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Fluxo de trabalho concluído*

## <a id="scripting"></a>Introdução ao componente com script
Os componentes com script podem executar scripts aleatórios durante as fases de execução de nosso fluxo de trabalho. Há quatro scripts diferentes que podem ser executados, cada um com características específicas e seus próprios lugares no ciclo de vida do fluxo de trabalho:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

A documentação do Componente com Script mostra mais detalhes sobre cada um dos scripts acima. Na [seção a seguir](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), o componente com script **realizeScript** é usado para construir dinamicamente um xml de lista de clipes quando o fluxo de trabalho é iniciado. Esse script é chamado durante a configuração do componente, que ocorre apenas uma vez em seu ciclo de vida.

### <a id="scripting_hello_world"></a>Criando scripts em um fluxo de trabalho: hello world
Arraste um Componente com Script para a superfície do designer e renomeie-o (por exemplo, "DefinirXMLDeListaDeClipes").

![Adicionando um Componente com Script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Adicionando um Componente com Script*

Quando você inspeciona as propriedades do Componente com Script, os quatro tipos diferentes de script são exibidos, cada um configurável para outro script.

![Propriedades do Componente com Script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriedades do Componente com Script*

Limpe o processInputScript e abra o editor para o realizeScript. Agora, estamos configurados e prontos para começar a criar scripts.

Os scripts são escritos em Groovy, uma linguagem de script compilada dinamicamente para a plataforma Java que mantém a compatibilidade com Java. Na verdade, grande parte do código Java é um código Groovy válido.

Vamos escrever um script Groovy simples de olá, mundo no contexto de nosso realizeScript. Digite o seguinte no editor:

    node.log("hello world");

Agora, realize um teste local. Após essa execução, inspecione a propriedade Logs (por meio da guia Sistema no Componente com Script).

![Saída do log de Hello world](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Saída do log de Hello world*

O objeto do nó no qual chamamos o método faz referência ao nosso "nó" atual, ou ao componente no qual estivermos gerando o script. Assim, cada componente tem a capacidade de gerar dados de log, disponíveis na guia Sistema. Nesse caso, mostramos a cadeia de caracteres literal "hello world". É importante entender aqui que essa pode ser uma ferramenta de depuração valiosa, fornecendo informações sobre o que o script está realmente fazendo.

De dentro de nosso ambiente de script, também temos acesso às propriedades de outros componentes. Tente o seguinte:

```java
    //inspect current node:
    def nodepath = node.getNodePath();
    node.log("this node path: " + nodepath);

    //walking up to other nodes:
    def parentnode = node.getParentNode();
    def parentnodepath = parentnode.getNodePath();
    node.log("parent node path: " + parentnodepath);

    //read properties from a node:
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null );
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null);
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);
```

Nossa janela de log mostra o seguinte:

![Saída de log para acessar os caminhos do nó](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Saída de log para acessar os caminhos do nó*

## <a id="frame_based_trim"></a>Corte baseado em quadro da saída MP4 com várias taxas de bits
Começando em um fluxo de trabalho que gera [uma saída MP4 com várias taxas de bit a partir de uma entrada MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), agora vamos analisar como cortar o vídeo de origem com base em contagens de quadro.

### <a id="frame_based_trim_start"></a>Visão geral do plano gráfico para começar a adição do corte
![Fluxo de trabalho para começar a adição do corte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Fluxo de trabalho para começar a adição do corte*

### <a id="frame_based_trim_clip_list"></a>Como usar o XML da lista de clipes
Em todos os tutoriais de fluxo de trabalho anteriores, usamos o componente de Entrada do Arquivo de Mídia como nossa fonte de entrada de vídeo. No entanto, para este cenário específico, usaremos o componente de Origem da Lista de Clipes. Essa não é a melhor maneira de trabalhar; use a Origem da Lista de Clipes apenas quando houver um motivo para isso (como no caso a seguir, onde estamos usando os recursos de corte de lista de clipes).

Para alternar de nossa Entrada do Arquivo de Mídia para a Origem de lista de clipes, arraste o componente Origem de lista de clipes para a superfície de design e conecte o pino XML da lista de clipes ao nó XML da lista de clipe do designer de fluxo de trabalho. Isso preenche a Origem da Lista de Clipes com os pinos de saída, de acordo com o nosso vídeo de entrada. Agora conecte os pinos de Vídeo Descompactado e Áudio Descompactados da Origem da Lista de Clipes aos respectivos Codificadores AVC e Intercalador de fluxo de áudio. Agora remova a Entrada do Arquivo de Mídia.

![A Entrada do Arquivo de Mídia foi substituída pela Origem da Lista de Clipes](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*A Entrada do Arquivo de Mídia foi substituída pela Origem da Lista de Clipes*

O componente Origem da Lista de Clipes recebe como entrada um "XML de lista de clipes". Ao selecionar o arquivo de origem para testar localmente, esse xml de lista de clipes é preenchido automaticamente para você.

![Propriedade XML de lista de clipes preenchida automaticamente](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Propriedade XML de lista de clipes preenchida automaticamente*

Analisando um pouco mais de perto o xml, ele se parece com o seguinte:

![Diálogo Editar lista de clipes](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Diálogo Editar lista de clipes*

No entanto, isso não reflete os recursos do xml da lista de clipes. Uma opção que temos é adicionar um elemento "Corte" à origem do áudio e do vídeo, da seguinte forma:

![Adicionando um elemento de corte à lista de clipes](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Adicionando um elemento de corte à lista de clipes*

Se você modificar o xml da lista de clipes da forma indicada acima e executar um teste local, você verá o vídeo corretamente cortado entre 10 e 20 segundos do vídeo.

Ao contrário do que acontece quando você realiza uma execução local, esse mesmo xml de lista de clipes não teria o mesmo efeito quando aplicado em um fluxo de trabalho executado nos Serviços de Mídia do Azure. Quando o Codificador Premium do Azure é iniciado, o xml de lista de clipes é gerado periodicamente com base no arquivo de entrada recebido pelo trabalho de codificação. Isso significa que qualquer alteração feita no xml seria infelizmente substituída.

Para combater a limpeza do xml de lista de clipes quando um trabalho de codificação é iniciado, podemos gerar isso novamente de forma dinâmica logo após o início de nosso fluxo de trabalho. Essas ações personalizadas podem ser realizadas por meio do que é chamado de "Componente de script". Para saber mais, confira [Introdução ao Componente com Script](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Arraste um Componente com Script para a superfície do designer e renomeie-o como "DefinirXMLDeListaDeClipes".

![Adicionando um Componente com Script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Adicionando um Componente com Script*

Quando você inspeciona as propriedades do Componente com Script, os quatro tipos diferentes de script são exibidos, cada um configurável para outro script.

![Propriedades do Componente com Script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriedades do Componente com Script*

### <a id="frame_based_trim_modify_clip_list"></a>Modificando a lista de clipes de um componente com script
Antes de podermos reescrever o xml de lista de clipes gerado durante a inicialização do fluxo de trabalho, precisaremos de acesso à propriedade e ao conteúdo do xml de lista de clipes. Podemos fazer isso da seguinte forma:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Lista de clipes recebida registrada em log](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Lista de clipes recebida registrada em log*

Primeiro, precisamos de um meio para determinar de que ponto a que ponto queremos cortar o vídeo. Para tornar isso conveniente para o usuário menos técnico do fluxo de trabalho, publique duas propriedades na raiz do grafo. Para fazer isso, clique com o botão direito do mouse na superfície do designer e selecione "Adicionar Propriedade":

* Primeira propriedade: “ClippingTimeStart” do tipo: “TIMECODE”
* Segunda propriedade: “ClippingTimeEnd” do tipo: “TIMECODE”

![Adicionar o diálogo Propriedade à hora de início de corte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Adicionar o diálogo Propriedade à hora de início de corte*

![Propriedades da hora de corte publicadas na raiz do fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Propriedades da hora de corte publicadas na raiz do fluxo de trabalho*

Configure as duas propriedades com um valor adequado:

![Configurar as propriedades de início de término do corte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Configurar as propriedades de início de término do corte*

Agora, em nosso script, podemos acessar as duas propriedades, da seguinte maneira:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Janela do log mostrando o início e o término do corte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Janela do log mostrando o início e o término do corte*

Vamos analisar as cadeias de código de tempo com uma forma de uso mais conveniente, usando uma expressão regular simples:

```java
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);
    node.log("framerate end is: " + endframerate);
```

![Janela do log com a saúda do código de tempo analisado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Janela do log com a saúda do código de tempo analisado*

Com essas informações em mãos, podemos modificar o xml de lista de clipes para refletir as horas de início e de término para o corte preciso de quadro desejado do filme.

![Código de script para adicionar elementos de corte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Código de script para adicionar elementos de corte*

Isso foi feito por meio de operações de manipulação de cadeia de caracteres normais. O xml da lista de clipes modificado resultante é gravado novamente na propriedade clipListXML na raiz do fluxo de trabalho por meio do método "setProperty". A janela de log, após a execução de outro teste, nos mostraria o seguinte:

![Registrando em log a lista de clipes resultante](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Registrando em log a lista de clipes resultante*

Faça um teste de execução para ver como os fluxos de áudio e de vídeo foram cortados. Como você fará mais de um teste de execução com valores diferentes para os pontos de corte, perceberá que eles não serão levados em consideração! O motivo para isso é que o designer, ao contrário do tempo de execução do Azure, NÃO substitui o xml de lista de clipes em cada execução. Isso significa que apenas na primeira vez que você definir de entrada e saída pontos, fará com que o xml transformar, todas as outras vezes, nossa cláusula de proteção (se (`clipListXML.indexOf("<trim>") == -1`)) impedirá que o fluxo de trabalho adicione outro elemento de corte quando já houver um presente.

Para tornar o teste local do fluxo de trabalho mais conveniente, é melhor adicionarmos algum código de manutenção que verifica se um elemento de corte já está presente. Em caso positivo, podemos removê-lo antes de continuar por meio da modificação do xml com os novos valores. Em vez de usar manipulações de cadeia de caracteres simples, provavelmente será mais seguro fazer isso por meio da análise do modelo de objeto xml real.

Antes de podermos adicionar esse código, precisamos adicionar algumas instruções de importação ao início do nosso script:

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;
```

Depois disso, podemos adicionar o código de limpeza necessário:

```java
    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes:
    elementsToDelete.each{
        e -> e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();
```

Esse código fica logo acima do ponto no qual podemos adicionar os elementos de corte ao xml da lista de clipes.

Neste ponto, podemos executar e modificar nosso fluxo de trabalho o quanto quisermos enquanto as alterações são aplicadas sempre.    

### <a id="frame_based_trim_clippingenabled_prop"></a>Adicionando uma propriedade de conveniência ClippingEnabled
Como você não quer que o corte ocorra sempre, vamos finalizar nosso fluxo de trabalho adicionando um sinalizador booliano conveniente que indica se queremos ou não permitir o corte/recorte.

Como antes, publique uma nova propriedade na raiz de nosso fluxo de trabalho chamada "ClippingEnabled" do tipo "BOOLEAN".

![Propriedade publicada para habilitar o recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Propriedade publicada para habilitar o recorte*

Com a cláusula de proteção simples abaixo, podemos verificar se o corte é necessário e decidir se nossa lista de clipes precisa ser modificada ou não.

```java
    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }
```

### <a id="code"></a>Código completo

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);

    //for local testing: delete any pre-existing trim elements
    //from the clip list xml by parsing the xml into a DOM:

    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e ->
        e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }

    //add trim elements to cliplist xml
    if ( clipListXML.indexOf("<trim>") == -1 )
    {
        //trim video
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode +
            " </outPoint>\n </trim> \n");
        //trim audio
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" +
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML );
        node.setProperty("../clipListXml",clipListXML);
    }
```

## <a name="also-see"></a>Consulte também
[Apresentando a codificação Premium nos Serviços de Mídia do Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Como usar a codificação Premium nos Serviços de Mídia do Azure](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Codificando conteúdo sob demanda com os Serviços de Mídia do Azure](media-services-encode-asset.md#media-encoder-premium-workflow)

[Codecs e formatos de fluxo de trabalho do Media Encoder Premium](media-services-premium-workflow-encoder-formats.md)

[Exemplos de arquivos de fluxo de trabalho](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Ferramenta do Explorador dos Serviços de Mídia do Azure](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
