---
title: Conceito de grafo de mídia-Azure
description: Um grafo de mídia permite definir onde a mídia deve ser capturada, como ela deve ser processada e onde os resultados devem ser entregues. Este artigo fornece uma descrição detalhada do conceito de grafo de mídia.
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: ad23acbbbdd0c15e92e471ee22a229470a8a3a75
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557659"
---
# <a name="media-graph"></a>Grafo de mídia

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida

* [Visão geral da Análise de Vídeo ao vivo no IoT Edge](overview.md)
* [Análise de Vídeo ao vivo na terminologia do IoT Edge](terminology.md)

## <a name="overview"></a>Visão geral

Um grafo de mídia permite definir onde a mídia deve ser capturada, como ela deve ser processada e onde os resultados devem ser entregues. Isso é feito por meio da conexão de componentes, ou nós, da maneira desejada. O diagrama a seguir fornece uma representação gráfica de um grafo de mídia.  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/media-graph.svg" alt-text="Grafo de mídia":::

A análise de vídeo ao vivo em IoT Edge dá suporte a diferentes tipos de fontes, processadores e coletores.

* Os **nós de origem** habilitam a captura de mídia no grafo de mídia. A mídia nesse contexto, conceitualmente, pode ser um fluxo de áudio, um fluxo de vídeo, um fluxo de dados ou um fluxo com áudio, vídeo e/ou dados combinados em um único fluxo.
* **Nós de processador** habilitam o processamento de mídia no grafo de mídia.
* Os **nós do coletor** habilitam o fornecimento dos resultados de processamento para serviços e aplicativos fora do grafo de mídia.

## <a name="media-graph-topologies-and-instances"></a>Topologias e instâncias do grafo de mídia 

A análise de vídeo ao vivo no IoT Edge permite que você gerencie gráficos de mídia por meio de dois conceitos – "topologia de grafo" e "instância de grafo". Uma topologia de grafo permite que você defina o plano gráfico de um grafo, com parâmetros como espaços reservados para valores. A topologia define quais nós são usados no grafo de mídia e como eles são conectados dentro do grafo de mídia. Por exemplo, se você quisesse gravar o feed de uma câmera, precisaria de um grafo com um nó de origem que recebe vídeo e um nó de coletor que grava o vídeo.

Os valores para os parâmetros na topologia são especificados quando você cria instâncias de grafo que fazem referência à topologia. Isso permite que você crie várias instâncias referenciando a mesma topologia, mas com valores diferentes para os parâmetros especificados na topologia. No exemplo acima, você poderia ter usado parâmetros para representar o endereço IP da câmera e o nome do vídeo gravado. Você pode criar várias instâncias de grafo com essa topologia – uma instância para cada câmera em uma compilação, talvez, cada uma com o endereço IP específico e o nome específico.

## <a name="media-graph-states"></a>Estados de gráficos de mídia  

O ciclo de vida de topologias de grafo e instâncias de grafo é mostrado no diagrama de estado a seguir.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/graph-topology-lifecycle.svg" alt-text="Topologia do grafo e ciclo de vida da instância do grafo":::

Você começa com [a criação de uma topologia de grafo](direct-methods.md#graphtopologyset). Em seguida, para cada feed de vídeo ao vivo que você deseja processar com essa topologia, você [cria uma instância de grafo](direct-methods.md#graphinstanceset). 

A instância do grafo estará no `Inactive` estado (ocioso).

Quando você estiver pronto para enviar o feed de vídeo ao vivo para a instância do Graph, [ative](direct-methods.md#graphinstanceactivate) -o. A instância do Graph passará brevemente por um estado de transição `Activating` e, se for bem-sucedida, entrará em um `Active` estado. No `Active` estado, a mídia será processada (se a instância do grafo receber dados de entrada).

> [!NOTE]
>  Uma instância de grafo pode estar ativa sem que os dados fluam através dela (por exemplo, a câmera fica offline).
> Sua assinatura do Azure será cobrada quando a instância do grafo estiver no estado ativo.

Você pode repetir o processo de criação e ativação de outras instâncias de grafo para a mesma topologia, se você tiver outros feeds de vídeo ao vivo para processar.

Quando terminar de processar o feed de vídeo ao vivo, você poderá [desativar](direct-methods.md#graphinstancedeactivate) a instância do grafo. A instância de grafo passará brevemente por um estado de transição `Deactivating` , liberará todos os dados que ele tiver e, em seguida, retornará para o `Inactive` estado.

Você só pode [excluir](direct-methods.md#graphinstancedelete) uma instância de grafo quando ela está no `Inactive` estado.

Depois que todas as instâncias de grafo que se referem a uma topologia de grafo específica tiverem sido excluídas, você poderá [excluir a topologia do grafo](direct-methods.md#graphtopologydelete).


## <a name="sources-processors-and-sinks"></a>Fontes, processadores e coletores  

A análise de vídeo ao vivo em IoT Edge dá suporte aos seguintes tipos de nós em um grafo de mídia:

### <a name="sources"></a>Origens 

#### <a name="rtsp-source"></a>Fonte RTSP 

Um nó de origem RTSP permite que você ingerir a mídia de um servidor [RTSP](https://tools.ietf.org/html/rfc2326) . As câmeras baseadas em IP e de vigilância transmitem seus dados em um protocolo chamado RTSP (protocolo de streaming em tempo real) que é diferente de outros tipos de dispositivos, como telefones e câmeras de vídeo. Esse protocolo é usado para estabelecer e controlar as sessões de mídia entre um servidor (a câmera) e um cliente. O nó de origem RTSP em um grafo de mídia atua como um cliente e pode estabelecer uma sessão com um servidor RTSP. Muitos dispositivos, como a maioria das [câmeras de IP](https://en.wikipedia.org/wiki/IP_camera) , têm um servidor RTSP interno. [ONVIF](https://www.onvif.org/) exige que o RTSP tenha suporte em sua definição de [perfis G, S &](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) dispositivos em conformidade com T. O nó de origem RTSP exige que você especifique uma URL RTSP, juntamente com as credenciais para habilitar uma conexão autenticada.

#### <a name="iot-hub-message-source"></a>Origem da mensagem do Hub IoT 

Assim como outros [módulos de IOT Edge](../../iot-fundamentals/iot-glossary.md#iot-edge), a análise de vídeo ao vivo no módulo IOT Edge pode receber mensagens por meio do hub de [IOT Edge](../../iot-fundamentals/iot-glossary.md#iot-edge-hub). Essas mensagens podem ser enviadas de outros módulos ou de aplicativos em execução no dispositivo de borda ou da nuvem. Essas mensagens são entregues (roteadas) a uma [entrada nomeada](../../iot-edge/module-composition.md#sink) no módulo. Um nó de origem de mensagem do Hub IoT permite que essas mensagens alcancem um grafo de mídia. Essas mensagens ou sinais podem ser usados internamente no grafo de mídia, normalmente para ativar as Gates de sinais (consulte [sinais de sinal](#signal-gate-processor) abaixo). 

Por exemplo, você pode ter um módulo IoT Edge que gera uma mensagem quando uma porta é aberta. A mensagem desse módulo pode ser roteada para IoT Edge Hub, de onde ele pode ser roteado para a origem da mensagem do Hub IoT de um grafo de mídia. Dentro do grafo de mídia, a origem da mensagem do Hub IoT pode passar o evento para um processador de porta de sinal, que pode então ativar a gravação do vídeo de uma fonte RTSP em um arquivo. 

### <a name="processors"></a>Processadores  

#### <a name="motion-detection-processor"></a>Processador de detecção de movimento 

O nó processador de detecção de movimento permite detectar o movimento no vídeo ao vivo. Ele examina os quadros de vídeo de entrada e determina se há movimento no vídeo. Se o movimento for detectado, ele passará os quadros de vídeo para o componente downstream e emitirá um evento. O nó processador de detecção de movimento (em conjunto com outros nós) pode ser usado para disparar a gravação do vídeo de entrada quando o movimento é detectado.

#### <a name="frame-rate-filter-processor"></a>Processador de filtro de taxa de quadros  

O nó do processador de filtro de taxa de quadros permite que você exemplo de quadros do fluxo de vídeo de entrada em uma taxa especificada. Isso permite que você reduza o número de quadros enviados para componentes de fluxo baixo (como nó de processador de extensão HTTP) para processamento adicional.
>[!WARNING]
> Esse processador foi **preterido** na versão mais recente da análise de vídeo ao vivo no módulo IOT Edge. O gerenciamento de taxa de quadros agora tem suporte dentro dos próprios processadores de extensão do grafo.

#### <a name="http-extension-processor"></a>Processador de extensão HTTP

O nó processador de extensão HTTP permite que você conecte seu próprio módulo IoT Edge a um grafo de mídia. Esse nó usa quadros de vídeo decodificados como entrada e transmite esses quadros para um ponto de extremidade HTTP REST exposto pelo seu módulo. Esse nó tem a capacidade de se autenticar com o ponto de extremidade REST, se necessário. Além disso, o nó tem um formatador de imagem interno para dimensionamento e codificação de quadros de vídeo antes que eles sejam retransmitidos para o ponto de extremidade REST. O escalonador tem opções para que a taxa de proporção da imagem seja preservada, preenchida ou alongada. O codificador de imagem dá suporte aos formatos JPEG, PNG ou BMP. Saiba mais sobre o processador [aqui](media-graph-extension-concept.md#http-extension-processor).

#### <a name="grpc-extension-processor"></a>Processador de extensão gRPC

O nó do processador de extensão gRPC usa quadros de vídeo decodificados como entrada e transmite esses quadros para um ponto de extremidade [gRPC](terminology.md#grpc) exposto pelo seu módulo. O nó dá suporte à transferência de dados usando [memória compartilhada](https://en.wikipedia.org/wiki/Shared_memory) ou inserindo diretamente o conteúdo no corpo de mensagens gRPC. Além disso, o nó tem um formatador de imagem interno para dimensionar e codificar os quadros de vídeo antes que eles sejam retransmitidos para o ponto de extremidade gRPC. O escalonador tem opções para que a taxa de proporção da imagem seja preservada, preenchida ou alongada. O codificador de imagem dá suporte aos formatos JPEG, png e bmp. Saiba mais sobre o processador [aqui](media-graph-extension-concept.md#grpc-extension-processor).

#### <a name="signal-gate-processor"></a>Processador da porta do sinal  

O nó processador da porta do sinal permite que você encaminhe a mídia condicionalmente de um nó para outro. Ele também atua como um buffer, permitindo a sincronização de mídia e eventos. Um caso de uso típico é inserir um nó do processador da porta de sinal entre o nó de origem RTSP e o nó do coletor de ativos e usar a saída de um nó do processador do detector de movimento para disparar o portão. Com um grafo de mídia como esse, você estaria gravando vídeo somente quando o movimento é detectado.

### <a name="sinks"></a>Coletores  

#### <a name="asset-sink"></a>Coletor de ativos  

Um nó do coletor de ativos permite que você grave dados de mídia (vídeo e/ou áudio) em um ativo dos serviços de mídia do Azure. Só pode haver um nó de coletor de ativos em um grafo de mídia. Consulte a seção [ativo](terminology.md#asset) para obter mais informações sobre ativos e sua função na gravação e reprodução de mídia. Você também pode ver o artigo [gravação de vídeo contínua](continuous-video-recording-concept.md) para obter detalhes sobre como as propriedades desse nó são usadas.

#### <a name="file-sink"></a>Coletor de arquivos  

O nó do coletor de arquivos permite que você grave dados de mídia (vídeo e/ou áudio) em um local no sistema de arquivos local do dispositivo de IoT Edge. Só pode haver um nó de coletor de arquivo em um grafo de mídia e deve ser downstream de um nó de processador de porta de sinal. Isso limita a duração dos arquivos de saída para os valores especificados nas propriedades do nó do processador da porta do sinal. Para garantir que o dispositivo de borda não fique sem espaço em disco, você também pode definir o tamanho máximo que a análise de vídeo ao vivo no módulo IoT Edge pode usar para armazenar dados.  
> [!NOTE]
Se o coletor de arquivos ficar cheio, a análise de vídeo ao vivo no módulo IoT Edge iniciará a exclusão dos dados mais antigos e o substituirá pelo novo.
#### <a name="iot-hub-message-sink"></a>Coletor de mensagens do Hub IoT  

Um nó de coletor de mensagens do Hub IoT permite que você publique eventos no Hub IoT Edge. O Hub de IoT Edge pode rotear os dados para outros módulos ou aplicativos no dispositivo de borda ou para o Hub IoT na nuvem (por rotas especificadas no manifesto de implantação). O nó do coletor de mensagens do Hub IoT pode aceitar eventos de processadores upstream, como um nó de processador de detecção de movimento, ou de um serviço de inferência externo por meio de um nó de processador de extensão HTTP.

## <a name="rules-on-the-use-of-nodes"></a>Regras sobre o uso de nós

Consulte [limitações em topologias de grafo](quotas-limitations.md#limitations-on-graph-topologies-at-preview) para obter regras adicionais sobre como nós diferentes podem ser usados em um grafo de mídia.

## <a name="scenarios"></a>Cenários

Usando uma combinação de fontes, processadores e coletores definidos acima, você pode criar grafos de mídia para uma variedade de cenários envolvendo a análise de vídeo ao vivo. Os cenários de exemplo são:

* [Gravação de vídeo contínua](continuous-video-recording-concept.md)
* [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)
* [Análise de Vídeo ao vivo sem gravação de vídeo](analyze-live-video-concept.md)

## <a name="next-steps"></a>Próximas etapas

Para ver como você pode executar a detecção de movimento em um feed de vídeo ao vivo, consulte [início rápido: executar análise de vídeo ao vivo com seu próprio modelo](use-your-model-quickstart.md).
