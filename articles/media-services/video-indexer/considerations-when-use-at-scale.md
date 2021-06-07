---
title: Coisas a considerar ao usar Video Indexer em escala – Azure
titleSuffix: Azure Media Services
description: Este tópico explica o que você deve considerar ao usar Video Indexer em escala.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: b955c0f494b757fd29c400194ef8b11314a89a03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96483603"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>Coisas a serem consideradas ao usar Video Indexer em escala

Ao usar o indexador de vídeo dos serviços de mídia do Azure para indexar vídeos e seu arquivo morto de vídeos está crescendo, considere o dimensionamento. 

Este artigo responde a perguntas como:

* Há restrições tecnológicas que preciso levar em conta?
* Há uma maneira inteligente e eficiente de fazê-lo?
* Posso evitar gastar dinheiro em excesso no processo?

O artigo fornece seis práticas recomendadas de como usar Video Indexer em escala.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>Ao carregar vídeos, considere usar uma URL em uma matriz de bytes

Video Indexer oferece a opção de carregar vídeos da URL ou diretamente enviando o arquivo como uma matriz de bytes, o último é fornecido com algumas restrições. Para obter mais informações, consulte [carregando considerações e limitações)](upload-index-videos.md#uploading-considerations-and-limitations)

Primeiro, ele tem limitações de tamanho de arquivo. O tamanho do arquivo da matriz de bytes é limitado a 2 GB em comparação com a limitação de tamanho de carregamento de 30 GB ao usar a URL.

Em segundo lugar, considere apenas alguns dos problemas que podem afetar o desempenho e, portanto, sua capacidade de dimensionar:

* O envio de arquivos usando várias partes significa alta dependência em sua rede, 
* confiabilidade do serviço, 
* conectividade 
* velocidade de carregamento, 
* perda de pacotes em algum lugar na World Wide Web.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="Primeira consideração para usar Video Indexer em escala":::

Ao carregar vídeos usando a URL, você só precisa fornecer um caminho para o local de um arquivo de mídia e Video Indexer cuida do resto (consulte o `videoUrl` campo na API [carregar vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload) ).

> [!TIP]
> Use o `videoUrl` parâmetro opcional da API carregar vídeo.

Para ver um exemplo de como carregar vídeos usando a URL, confira [Este exemplo](upload-index-videos.md#code-sample). Ou, você pode usar o [AzCopy](../../storage/common/storage-use-azcopy-v10.md) para uma maneira rápida e confiável de obter seu conteúdo para uma conta de armazenamento da qual você pode enviá-lo para video indexer usando a [URL SAS](../../storage/common/storage-sas-overview.md).

## <a name="increase-media-reserved-units-if-needed"></a>Aumentar as unidades reservadas de mídia, se necessário

Geralmente, no estágio de prova de conceito, quando você começa a usar Video Indexer, não precisa de muita capacidade de computação. Quando você começa a ter um arquivo maior de vídeos que precisa indexar e deseja que o processo esteja em um ritmo que se ajuste ao seu caso de uso, é necessário escalar verticalmente o uso de Video Indexer. Portanto, você deve pensar em aumentar o número de recursos de computação que você usa se a quantidade atual de potência de computação não for suficiente.

Nos serviços de mídia do Azure, quando você deseja aumentar a capacidade de computação e a paralelização, precisa prestar atenção às [unidades reservadas](../latest/concept-media-reserved-units.md)de mídia (RUs). RUs são as unidades de computação que determinam os parâmetros para suas tarefas de processamento de mídia. O número de RUs afeta o número de tarefas de mídia que podem ser processadas simultaneamente em cada conta e seu tipo determina a velocidade de processamento e um vídeo pode exigir mais de um RU se sua indexação for complexa. Quando seu RUs estiver ocupado, novas tarefas serão mantidas em uma fila até que outro recurso esteja disponível.

Para operar com eficiência e evitar ter recursos que ficam ociosos a partir do tempo, o Video Indexer oferece um sistema de dimensionamento automático que gira o RUs quando menos processamento é necessário e gira o RUs quando você está em horas de urgência (até usar totalmente todo o RUs). Você pode habilitar essa funcionalidade [ativando o dimensionamento automático](manage-account-connected-to-azure.md#autoscale-reserved-units) nas configurações da conta ou usando a [API Update-pagou-Account-Azure-Media-Services](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&pattern=update).

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="Segunda consideração para usar Video Indexer em escala":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="Unidades reservadas do AMS":::

Para minimizar a duração da indexação e baixa taxa de transferência que recomendamos, você começa com 10 RUs do tipo S3. Posteriormente, se você escalar verticalmente para dar suporte a mais conteúdo ou simultaneidade mais alta, e precisar de mais recursos para fazer isso, [entre em contato conosco usando o sistema de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) (somente em contas pagas) para solicitar mais alocação de RUs.

## <a name="respect-throttling"></a>Respeitar limitação

Video Indexer foi criado para lidar com a indexação em escala e, quando você quiser obter o máximo deles, você também deve estar ciente dos recursos do sistema e projetar sua integração de acordo. Você não quer enviar uma solicitação de upload para um lote de vídeos apenas para descobrir que alguns dos filmes não foram carregados e você está recebendo um código de resposta HTTP 429 (muitas solicitações). Isso pode ocorrer devido ao fato de que você enviou mais solicitações do que o [limite de filmes por minuto ao qual damos suporte](upload-index-videos.md#uploading-considerations-and-limitations). Video Indexer adiciona um `retry-after` cabeçalho na resposta http, o cabeçalho especifica quando você deve tentar a próxima tentativa. Verifique se você o respeita antes de tentar sua próxima solicitação.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="Projete seu bom de integração, respeite a limitação":::

## <a name="use-callback-url"></a>Usar URL de retorno de chamada

É recomendável que, em vez de sondar o status de sua solicitação constantemente no segundo que você enviou a solicitação de carregamento, você possa adicionar uma [URL de retorno de chamada](upload-index-videos.md#callbackurl)e aguardar Video indexer atualizá-lo. Assim que houver qualquer alteração de status na solicitação de upload, você receberá uma notificação POST para a URL especificada.

Você pode adicionar uma URL de retorno de chamada como um dos parâmetros da [API carregar vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload). Confira os exemplos de código no [repositório GitHub](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/). 

Para URL de retorno de chamada, você também pode usar Azure Functions, uma plataforma baseada em eventos sem servidor que pode ser disparada por HTTP e implementar um fluxo a seguir.

### <a name="callback-url-definition"></a>Definição da URL de retorno de chamada

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>Use os parâmetros de indexação corretos para você

Ao tomar decisões relacionadas ao uso de Video Indexer em escala, veja como obter o máximo deles com os parâmetros certos para suas necessidades. Pense no caso de uso, definindo parâmetros diferentes, você pode economizar dinheiro e tornar o processo de indexação para seus vídeos mais rapidamente.

Antes de carregar e indexar seu vídeo, leia esta breve [documentação](upload-index-videos.md), verifique o [IndexingPreset](upload-index-videos.md#indexingpreset) e o [streamingPreset](upload-index-videos.md#streamingpreset) para obter uma ideia melhor das suas opções.

Por exemplo, não defina a predefinição como streaming se você não planeja assistir ao vídeo, não indexe informações de vídeo se precisar apenas de informações de áudio.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>Índice na resolução ideal, não na resolução mais alta

Você pode estar se perguntando, que qualidade de vídeo você precisa para indexar seus vídeos? 

Em muitos casos, o desempenho da indexação não tem quase nenhuma diferença entre vídeos de HD (720P) e vídeos de 4K. Eventualmente, você obterá quase as mesmas informações com a mesma confiança. Quanto maior a qualidade do filme que você carrega, significa maior o tamanho do arquivo, e isso leva a um maior poder de computação e tempo necessário para carregar o vídeo.

Por exemplo, para o recurso de detecção facial, uma resolução mais alta pode ajudar no cenário em que há muitas faces pequenas, mas contextualmente importantes. No entanto, isso será fornecido com um aumento de quadrática no tempo de execução e um maior risco de falsos positivos.

Portanto, recomendamos que você verifique se você obtém os resultados certos para seu caso de uso e para primeiro testá-lo localmente. Carregue o mesmo vídeo em 720P e em 4K e compare as informações obtidas.

## <a name="next-steps"></a>Próximas etapas

[Examinar a saída do Video Indexer do Azure produzida pela API](video-indexer-output-json-v2.md)