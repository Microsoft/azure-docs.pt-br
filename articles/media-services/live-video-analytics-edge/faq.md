---
title: Análise de vídeo ao vivo em IoT Edge perguntas frequentes – Azure
description: Este artigo responde às perguntas mais frequentes sobre a análise de vídeo ao vivo em IoT Edge.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 661b6155ce2d95e2111a1fa338fd5df438e61e7d
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105032780"
---
# <a name="live-video-analytics-on-iot-edge-faq"></a>Perguntas frequentes sobre a análise de vídeo ao vivo IoT Edge

Este artigo responde às perguntas mais frequentes sobre a análise de vídeo ao vivo em Azure IoT Edge.

## <a name="general"></a>Geral

**Quais variáveis de sistema posso usar na definição de topologia do grafo?**

| Variável   |  Descrição  | 
| --- | --- | 
| [System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods) | Representa um instante em hora UTC, normalmente expresso como uma data e hora do dia no seguinte formato:<br>*yyyyMMddTHHmmssZ* | 
| System. PreciseDateTime | Representa uma instância de data e hora UTC (tempo Universal Coordenado) em um formato compatível com arquivo ISO8601 com milissegundos, no seguinte formato:<br>*AAAAMMDDThhmmss. fffZ* | 
| System. GraphTopologyName   | Representa uma topologia de gráfico de mídia e contém o plano gráfico de um grafo. | 
| System. GraphInstanceName |    Representa uma instância de grafo de mídia, mantém os valores de parâmetro e faz referência à topologia. | 

## <a name="configuration-and-deployment"></a>Configuração e implantação

**Posso implantar o módulo de borda de mídia em um dispositivo Windows 10?**

Sim. Para obter mais informações, consulte [contêineres do Linux no Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Capturar da câmera IP e das configurações RTSP

**É necessário usar um SDK especial em meu dispositivo para enviar em um fluxo de vídeo?**

Não, a análise de vídeo ao vivo em IoT Edge dá suporte à captura de mídia usando RTSP (protocolo de streaming em tempo real) para streaming de vídeo, que tem suporte na maioria das câmeras de IP.

**Posso enviar mídia para a análise de vídeo ao vivo em IoT Edge usando o protocolo RTMP (Real-Time Messaging Protocol) ou Smooth Streaming (como um evento de serviços de mídia ao vivo)?**

Não, a análise de vídeo ao vivo dá suporte apenas a RTSP para capturar vídeo de câmeras IP. Qualquer câmera que dê suporte ao streaming RTSP sobre TCP/HTTP deve funcionar. 

**Posso redefinir ou atualizar a URL de origem RTSP em uma instância de grafo?**

Sim, quando a instância do grafo está no estado *inativo* .  

**Um simulador RTSP está disponível para uso durante o teste e o desenvolvimento?**

Sim, um módulo de borda de [simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) está disponível para uso nos guias de início rápido e tutoriais para dar suporte ao processo de aprendizado. Esse módulo é fornecido como o melhor esforço e pode não estar sempre disponível. É recomendável que você *não* use o simulador por mais de algumas horas. Você deve investir em testes com sua fonte RTSP real antes de planejar uma implantação de produção.

**Há suporte para a descoberta de ONVIF de câmeras IP na borda?**

Não, não há suporte para a descoberta de dispositivos abertos do ONVIF (Fórum de interface de vídeo de rede) na borda.

## <a name="streaming-and-playback"></a>Streaming e reprodução

**Posso reproduzir ativos registrados nos serviços de mídia do Azure a partir do Edge usando tecnologias de streaming como HLS ou DASH?**

Sim. Você pode transmitir ativos gravados como qualquer outro ativo nos serviços de mídia do Azure. Para transmitir o conteúdo, você deve ter um ponto de extremidade de streaming criado e no estado executando. Usar o processo de criação do localizador de streaming padrão fornecerá acesso a um manifesto Apple HTTP Live Streaming (HLS) ou streaming adaptável dinâmico sobre HTTP (DASH, também conhecido como MPEG-DASH) para streaming para qualquer estrutura de Player com capacidade. Para obter mais informações sobre como criar e publicar manifestos HLS ou DASH, consulte [empacotamento dinâmico](../latest/dynamic-packaging-overview.md).

**Posso usar os recursos de proteção de conteúdo padrão e DRM dos serviços de mídia em um ativo Arquivado?**

Sim. Todos os recursos padrão de proteção de conteúdo de criptografia dinâmica e DRM (gerenciamento de direitos digitais) estão disponíveis para uso em ativos que são registrados de um grafo de mídia.

**Quais jogadores posso usar para exibir o conteúdo dos ativos gravados?**

Há suporte para todos os players padrão compatíveis com HLS versão 3 ou versão 4. Além disso, também há suporte para qualquer jogador com capacidade de reprodução de MPEG-DASH compatível.

Os players recomendados para teste incluem:

* [Player de Mídia do Azure](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Shaka Player](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [HTTP Live Streaming nativo da Apple](https://developer.apple.com/streaming/)
* Player de vídeo HTML5 interno do Edge, Chrome ou Safari
* Jogadores comerciais que dão suporte à reprodução de HLS ou DASH

**Quais são os limites de streaming de um ativo de grafo de mídia?**

O streaming de um ativo dinâmico ou gravado de um grafo de mídia usa a mesma infraestrutura de alta escala e ponto de extremidade de streaming aos quais os serviços de mídia dão suporte para streaming sob demanda e ao vivo para mídia & entretenimento, além dos clientes da parte superior (OTT) e de difusão. Isso significa que você pode habilitar com rapidez e facilidade a rede de distribuição de conteúdo do Azure, Verizon ou Akamai para entregar seu conteúdo a um público tão pequeno quanto alguns visualizadores ou até milhões, dependendo do seu cenário.

Você pode fornecer conteúdo usando o Apple HLS ou o MPEG-DASH.

## <a name="design-your-ai-model"></a>Projete seu modelo de ia 

**Tenho vários modelos de ia encapsulados em um contêiner do Docker. Como devo usá-los com a análise de vídeo ao vivo?** 

As soluções variam dependendo do protocolo de comunicação usado pelo servidor inferência para se comunicar com a análise de vídeo ao vivo. As seções a seguir descrevem como cada protocolo funciona.

*Use o protocolo http*:

* Contêiner único (lvaExtension único):  

   No servidor do inferência, você pode usar uma única porta, mas diferentes pontos de extremidade para modelos de ia diferentes. Por exemplo, para um exemplo de Python, você pode usar diferentes `route` s por modelo, conforme mostrado aqui: 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   Em seguida, em sua implantação de análise de vídeo ao vivo, ao criar uma instância de grafos, defina a URL do servidor de inferência para cada instância, como mostrado aqui: 

   1ª instância: URL do servidor de inferência =`http://lvaExtension:44000/score/face_detection`<br/>
   2ª instância: URL do servidor de inferência =`http://lvaExtension:44000/score/vehicle_detection`  
   
    > [!NOTE]
    > Como alternativa, você pode expor seus modelos de ia em portas diferentes e chamá-los ao instanciar grafos.  

* Vários contêineres: 

   Cada contêiner é implantado com um nome diferente. Anteriormente, no conjunto de documentação de análise de vídeo ao vivo, mostramos como implantar uma extensão chamada *lvaExtension*. Agora você pode desenvolver dois contêineres diferentes, cada um com a mesma interface HTTP, o que significa que eles têm o mesmo `/score` ponto de extremidade. Implante esses dois contêineres com nomes diferentes e verifique se ambos estão escutando em *portas diferentes*. 

   Por exemplo, um contêiner chamado `lvaExtension1` está ouvindo a porta `44000` e um segundo contêiner chamado `lvaExtension2` está ouvindo a porta `44001` . 

   Em sua topologia de análise de vídeo ao vivo, você instancia dois grafos com diferentes URLs de inferência, como mostrado aqui: 

   Primeira instância: URL do servidor de inferência = `http://lvaExtension1:44001/score`    
   Segunda instância: URL do servidor de inferência = `http://lvaExtension2:44001/score`
   
*Use o protocolo gRPC*: 

* Com o módulo de análise de vídeo ao vivo 1,0, quando você usa um protocolo gRPC (chamada de procedimento remoto de uso geral), a única maneira de fazer isso é se o servidor gRPC expõe modelos de ia diferentes por meio de portas diferentes. Neste [exemplo de código](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json), uma única porta, 44000, expõe todos os modelos Yolo. Teoricamente, o servidor Yolo gRPC poderia ser reescrito para expor alguns modelos na porta 44000 e outros na porta 45000. 

* Com o módulo de análise de vídeo ao vivo 2,0, uma nova propriedade é adicionada ao nó de extensão gRPC. Essa propriedade, **extensionConfiguration**, é uma cadeia de caracteres opcional que pode ser usada como parte do contrato gRPC. Quando você tem vários modelos de ia empacotados em um único servidor de inferência, não é necessário expor um nó para todos os modelos de ia. Em vez disso, para uma instância de grafo, você, como o provedor de extensão, pode definir como selecionar os diferentes modelos de ia usando a propriedade **extensionConfiguration** . Durante a execução, a análise de vídeo ao vivo passa essa cadeia de caracteres para o servidor inferência, que pode usá-la para invocar o modelo de ia desejado. 

**Estou criando um servidor gRPC em um modelo de ia e quero ser capaz de dar suporte ao seu uso por várias câmeras ou instâncias de grafo. Como devo criar meu servidor?** 

 Primeiro, certifique-se de que o servidor pode lidar com mais de uma solicitação por vez ou trabalhar em threads paralelos. 

Por exemplo, um número padrão de canais paralelos foi definido no seguinte [exemplo de gRPC de análise de vídeo ao vivo](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py): 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

Na instanciação do servidor gRPC anterior, o servidor pode abrir apenas três canais de cada vez por câmera, ou por instância de topologia de grafo. Não tente conectar mais de três instâncias ao servidor. Se você tentar abrir mais de três canais, as solicitações estarão pendentes até que um canal existente seja Descartado.  

A implementação do servidor gRPC anterior é usada em nossos exemplos de Python. Como desenvolvedor, você pode implementar seu próprio servidor ou usar a implementação padrão anterior para aumentar o número do trabalhador, que você define para o número de câmeras a serem usadas para feeds de vídeo. 

Para configurar e usar várias câmeras, você pode instanciar várias instâncias de topologia de grafo, cada uma apontando para o mesmo servidor de inferência ou outro (por exemplo, o servidor mencionado no parágrafo anterior). 

**Quero ser capaz de receber vários quadros de upstream antes de tomar uma decisão de inferência. Como posso habilitar isso?** 

Nossos [exemplos padrão](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) atuais funcionam em um modo *sem estado* . Eles não mantêm o estado das chamadas anteriores nem mesmo quem chamou. Isso significa que várias instâncias de topologia podem chamar o mesmo servidor de inferência, mas o servidor não pode distinguir quem está chamando ou o estado por chamador. 

*Use o protocolo http*:

Para manter o estado, cada chamador ou instância de topologia do grafo, o chama o servidor inferência usando o parâmetro de consulta HTTP que é exclusivo do chamador. Por exemplo, os endereços de URL do servidor de inferência para cada instância são mostrados aqui:  

primeira instância da topologia = `http://lvaExtension:44000/score?id=1`<br/>
2ª instância de topologia = `http://lvaExtension:44000/score?id=2`

… 

No lado do servidor, a rota de Pontuação sabe quem está chamando. Se ID = 1, ele poderá manter o estado separadamente para esse chamador ou instância de topologia do grafo. Em seguida, você pode manter os quadros de vídeo recebidos em um buffer. Por exemplo, use uma matriz, ou um dicionário com uma chave DateTime, e o valor é o quadro. Em seguida, você pode definir o servidor para processar (inferir) após o número *x* de quadros ser recebido. 

*Use o protocolo gRPC*: 

Com uma extensão gRPC, cada sessão é para um único feed de câmera, portanto, não é necessário fornecer uma ID. Agora, com a propriedade extensionConfiguration, você pode armazenar os quadros de vídeo em um buffer e definir o servidor para processar (inferir) após o número *x* de quadros ser recebido. 

**Todos os ProcessMediaStreams em um contêiner específico executam o mesmo modelo de ia?** 

Não. Iniciar ou parar chamadas do usuário final em uma instância de grafo constituem uma sessão, ou talvez haja uma câmera desconectada ou reconectar. O objetivo é persistir uma sessão se a câmera estiver transmitindo vídeo. 

* Duas câmeras que enviam vídeo para processamento cria duas sessões. 
* Uma câmera que vai para um grafo que tem dois nós de extensão gRPC cria duas sessões. 

Cada sessão é uma conexão Full duplex entre a análise de vídeo ao vivo e o servidor gRPC, e cada sessão pode ter um modelo ou pipeline diferente. 

> [!NOTE]
> No caso de uma câmera desconectar ou reconectar, com a câmera ficando offline por um período além dos limites de tolerância, a análise de vídeo ao vivo abrirá uma nova sessão com o servidor gRPC. Não há nenhum requisito para o servidor rastrear o estado entre essas sessões. 

A análise de vídeo ao vivo também adiciona suporte a várias extensões de gRPC para uma única câmera em uma instância de grafo. Você pode usar essas extensões gRPC para executar o processamento de ia em sequência, em paralelo, ou como uma combinação de ambos. 

> [!NOTE]
> Ter várias extensões executadas em paralelo afetará os recursos de hardware. Tenha isso em mente, pois você está escolhendo o hardware que atende às suas necessidades computacionais. 

**Qual é o número máximo de ProcessMediaStreams simultâneas?** 

A análise de vídeo ao vivo não aplica limites a esse número.  

**Como posso decidir se meu servidor inferência deve usar CPU ou GPU ou qualquer outro acelerador de hardware?** 

Sua decisão depende da complexidade do modelo de ia desenvolvido e de como você deseja usar os aceleradores de CPU e de hardware. Ao desenvolver o modelo de ia, você pode especificar quais recursos o modelo deve usar e quais ações ele deve executar. 

**Como fazer armazenar imagens com caixas delimitadas pós-processamento?** 

Hoje, estamos fornecendo coordenadas de caixa delimitadora apenas como mensagens de inferência. Você pode criar um fluxo de MJPEG personalizado que pode usar essas mensagens e sobrepor as caixas delimitadoras nos quadros de vídeo.  

## <a name="grpc-compatibility"></a>compatibilidade do gRPC 

**Como saber quais são os campos obrigatórios para o descritor de fluxo de mídia?** 

Qualquer campo ao qual você não forneça um valor recebe um [valor padrão, conforme especificado por gRPC](https://developers.google.com/protocol-buffers/docs/proto3#default).  

A análise de vídeo ao vivo usa a versão *Proto3* do idioma do buffer de protocolo. Todos os dados de buffer de protocolo usados por contratos de análise de vídeo ao vivo estão disponíveis nos [arquivos de buffer de protocolo](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). 

**Como posso garantir que estou usando os arquivos de buffer de protocolo mais recentes?** 

Você pode obter os arquivos de buffer de protocolo mais recentes no [site de arquivos de contrato](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). Sempre que atualizarmos os arquivos de contrato, eles estarão nesse local. Não há nenhum plano imediato para atualizar os arquivos de protocolo, portanto, procure o nome do pacote na parte superior dos arquivos para saber a versão. E deve ser: 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

Todas as atualizações nesses arquivos incrementarão o "valor de v" no final do nome. 

> [!NOTE]
> Como a análise de vídeo ao vivo usa a versão Proto3 do idioma, os campos são opcionais e a versão é compatível com versões anteriores e posteriores. 

**Quais recursos do gRPC estão disponíveis para uso com a análise de vídeo ao vivo? Quais recursos são obrigatórios e que são opcionais?** 

Você pode usar qualquer recurso de gRPC do servidor, desde que o contrato buffers de protocolo (Protobuf) seja atendido. 

## <a name="monitoring-and-metrics"></a>Monitoramento e métricas

**Posso monitorar o grafo de mídia na borda usando a grade de eventos do Azure?**

Sim. Você pode consumir métricas Prometheus e publicá-las na sua grade de eventos. 

**Posso usar Azure Monitor para exibir a integridade, as métricas e o desempenho de meus gráficos de mídia na nuvem ou na borda?**

Sim, damos suporte a essa abordagem. Para saber mais, consulte [visão geral de métricas de Azure monitor](../../azure-monitor/essentials/data-platform-metrics.md).

**Há alguma ferramenta para facilitar o monitoramento do módulo IoT Edge de serviços de mídia?**

O Visual Studio Code dá suporte à extensão de ferramentas de IoT do Azure, com a qual você pode monitorar facilmente os pontos de extremidade do módulo LVAEdge. Você pode usar essa ferramenta para iniciar rapidamente o monitoramento do ponto de extremidade interno do Hub IoT para "eventos" e exibir as mensagens de inferência que são roteadas do dispositivo de borda para a nuvem. 

Além disso, você pode usar essa extensão para editar o módulo "myLVAEdge" para modificar as configurações de grafo de mídia.

Para obter mais informações, consulte o artigo [monitoramento e registro em log](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Disponibilidade e cobrança

**Como a análise de vídeo ao vivo em IoT Edge cobrada?**

Para obter detalhes de cobrança, consulte [preços dos serviços de mídia](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="next-steps"></a>Próximas etapas

[Início rápido: introdução à análise de vídeo ao vivo no IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
