---
title: "Otimização de download de arquivos grandes por meio da Rede de Distribuição de Conteúdo do Azure"
description: "Otimização de downloads de arquivos grandes explicada em detalhes"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: v-semcev
ms.openlocfilehash: 7a5d5d1d0de24ebb0a5115ede1e572f38454bd78
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="large-file-download-optimization-via-the-azure-content-delivery-network"></a>Otimização de download de arquivos grandes por meio da Rede de Distribuição de Conteúdo do Azure

Os tamanhos de arquivos de conteúdo entregue pela Internet continuam crescendo devido a funcionalidades avançadas, elementos gráficos aprimorados e conteúdo de rich media. Esse crescimento é causado por vários fatores: penetração de banda larga, dispositivos de armazenamento maiores de baixo custo, aumento generalizado de vídeos de alta definição, dispositivos conectados à Internet (IoT), etc. Um mecanismo de distribuição rápido e eficiente para arquivos grandes é crítico para garantir uma experiência do consumidor agradável e sem problemas.

A distribuição de arquivos grandes apresenta vários desafios. Primeiro, o tempo médio para baixar um arquivo grande pode ser significativo, pois muitos aplicativos podem não baixar todos os dados em sequência. Em alguns casos, os aplicativos podem baixar a última parte de um arquivo antes da primeira. Quando for solicitada apenas uma pequena quantidade de um arquivo ou um usuário pausar um download, o download poderá falhar. O download também poderá ser atrasado até que a CDN (rede de distribuição de conteúdo) recupere o arquivo inteiro do servidor de origem. 

Em seguida, a latência entre o computador de um usuário e o arquivo determina a velocidade na qual ele pode exibir o conteúdo. Além disso, problemas de capacidade e congestionamento de rede também afetam a taxa de transferência. Maiores distâncias entre servidores e usuários criam mais oportunidades de perda de pacotes, o que reduz a qualidade. A redução na qualidade causada por uma taxa de transferência limitada e uma maior perda de pacotes pode aumentar o tempo de espera até a conclusão do download de um arquivo. 

Em terceiro lugar, muitos arquivos grandes não são entregues em sua totalidade. Os usuários podem cancelar um download no meio ou assistir somente aos primeiros minutos de um vídeo MP4 longo. Portanto, as empresas de distribuição de software e mídia desejam distribuir somente a parte de um arquivo que é solicitada. A distribuição eficiente das partes solicitadas reduz o tráfego de saída do servidor de origem. A distribuição eficiente também reduz a demanda de memória e E/S no servidor de origem. 

A Rede de Distribuição de Conteúdo do Azure da Akamai agora oferece um recurso que distribui arquivos grandes com eficiência aos usuários no mundo todo em escala. O recurso reduz as latências porque reduz a carga nos servidores de origem. Esse recurso está disponível no tipo de preço Standard da Akamai.

## <a name="configure-a-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Configurar um ponto de extremidade CDN para otimizar a distribuição de arquivos grandes

Configure o ponto de extremidade CDN para otimizar a distribuição de arquivos grandes pelo portal do Azure. Você também pode usar nossas APIs REST ou qualquer um dos SDKs de cliente para fazer isso. As seguintes etapas mostram o processo por meio do portal do Azure.

1. Para adicionar um novo ponto de extremidade, na página **Perfil CDN**, selecione **Ponto de extremidade**.

    ![Novo ponto de extremidade](./media/cdn-large-file-optimization/01_Adding.png)  
 
2. Na lista suspensa **Otimizado para**, selecione **Download de arquivos grandes**.

    ![Otimização de arquivos grandes selecionada](./media/cdn-large-file-optimization/02_Creating.png)


Depois de criar o ponto de extremidade CDN, ele aplica as otimizações de arquivo grande a todos os arquivos que correspondem a determinados critérios. A seção a seguir descreve esse processo.

## <a name="optimize-for-delivery-of-large-files-with-the-azure-content-delivery-network-from-akamai"></a>Otimizar a distribuição de arquivos grandes com a Rede de Distribuição de Conteúdo do Azure da Akamai

O recurso do tipo de otimização de arquivos grandes ativa as otimizações e as configurações de rede para distribuir arquivos grandes, de forma mais rápida e com maior capacidade de resposta. A distribuição na Web geral com a Akamai armazena em cache somente arquivos abaixo de 1,8 GB e pode fornecer túnel para arquivos (sem cache) de até 150 GB. A otimização de arquivos grandes armazena em cache arquivos de até 150 GB.

A otimização de arquivos grandes é eficaz quando determinadas condições são atendidas. As condições incluem o funcionamento do servidor de origem e os tamanhos e tipos de arquivos solicitados. Antes de entrarmos em detalhes sobre essas questões, você deve entender como funciona a otimização. 

### <a name="object-chunking"></a>Agrupamento de objeto 

A Rede de Distribuição de Conteúdo do Azure da Akamai usa uma técnica chamada agrupamento de objeto. Quando um arquivo grande é solicitado, a CDN recupera partes menores do arquivo da origem. Depois que o servidor de borda da CDN/POP recebe uma solicitação de arquivo completa ou de intervalo de bytes, ele verifica se há suporte para o tipo de arquivo nessa otimização. Ele também verifica se o tipo de arquivo atende aos requisitos de tamanho do arquivo. Se o tamanho do arquivo for maior que 10 MB, o servidor de borda CDN solicitará o arquivo da origem em partes de 2 MB. 

Depois que a parte chega na borda da CDN, ela é armazenada em cache e imediatamente disponibilizada para o usuário. Em seguida, a CDN faz uma pré-busca na próxima parte em paralelo. Essa pré-busca garante que o conteúdo permaneça uma parte à frente do usuário, o que reduz a latência. Esse processo continua até que todo o arquivo é baixado (se solicitado), todos os intervalos de bytes estão disponíveis (se solicitado) ou o cliente encerra a conexão. 

Para obter mais informações sobre a solicitação de intervalo de bytes, consulte [RFC 7233](https://tools.ietf.org/html/rfc7233).

A CDN armazena em cache todas as partes, conforme são recebidas. O arquivo inteiro não precisa ser armazenado em cache no cache da CDN. As solicitações posteriores para os intervalos de bytes ou de arquivo são atendidas no cache da CDN. Se nem todas as partes forem armazenadas em cache na CDN, uma pré-busca será usada para solicitar as partes da origem. Essa otimização se baseia na capacidade do servidor de origem de dar suporte a solicitações de intervalo de bytes. _Se o servidor de origem não dá suporte a solicitações de intervalo de bytes, essa otimização não é eficaz._ 

### <a name="caching"></a>Cache
A otimização de arquivos grandes usa tempos de expiração de cache padrão diferentes da distribuição na Web geral. Ele faz distinção entre cache positivo e negativo cache com base em códigos de resposta HTTP. Se o servidor de origem especificar um tempo de expiração por meio de um controle de cache ou um cabeçalho de expirações na resposta, a CDN respeitará esse valor. Quando a origem não especifica e o arquivo corresponde às condições de tipo e tamanho para esse tipo de otimização, a CDN usa os valores padrão para a otimização de arquivo grande. Caso contrário, a CDN usará padrões para a distribuição na Web geral.


|    | Web geral | Otimização de arquivos grandes 
--- | --- | --- 
Cache: Positivo <br> HTTP 200, 203, 300, <br> 301, 302 e 410 | 7 dias |1 dia  
Cache: Negativo <br> HTTP 204, 305, 404, <br> e 405 | Nenhum | 1 segundo 

### <a name="deal-with-origin-failure"></a>Lidar com falhas de origem

O tamanho do tempo limite de leitura da origem aumenta de dois segundos na distribuição na Web geral para dois minutos no tipo de otimização de arquivos grandes. Esse aumento é responsável pelos tamanhos maiores de arquivo para evitar uma conexão de tempo limite prematura.

Quando uma conexão atinge o tempo limite, a CDN repete a operação várias vezes antes de enviar um erro “504 – Tempo Limite do Gateway” para o cliente. 

### <a name="conditions-for-large-file-optimization"></a>Condições para a otimização de arquivo grande

A tabela a seguir lista o conjunto de critérios a serem atendidos para a otimização de arquivo grande:

Condição | Valores 
--- | --- 
Tipos de arquivo com suporte | 3g2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> mpeg, mpg, mts, pkg, qt, rm, swf, tar, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Tamanho mínimo do arquivo | 10 MB 
Tamanho máximo do arquivo | 150 GB 
Características do servidor de origem | Deve dar suporte a solicitações de intervalo de bytes 

## <a name="optimize-for-delivery-of-large-files-with-the-azure-content-delivery-network-from-verizon"></a>Otimizar a distribuição de arquivos grandes com a Rede de Distribuição de Conteúdo do Azure da Verizon

A Rede de Distribuição de Conteúdo do Azure da Verizon distribui arquivos grandes sem um limite de tamanho do arquivo. Recursos adicionais são ativados por padrão para agilizar a distribuição de arquivos grandes.

### <a name="complete-cache-fill"></a>Concluir o preenchimento do cache

O recurso padrão de preenchimento de cache completo permite que a CDN efetue pull de um arquivo para o cache quando uma solicitação inicial é abandonada ou perdida. 

O preenchimento de cache completo é mais útil para ativos grandes. Normalmente, os usuários não os baixam do início ao fim. Eles usam o download progressivo. O comportamento padrão força o servidor de borda a iniciar uma busca em segundo plano do ativo do servidor de origem. Depois disso, o ativo estará no cache local do servidor de borda. Depois que o objeto completo está no cache, o servidor de borda pode atender as solicitações de intervalo de bytes para a CDN, para o objeto armazenado em cache.

O comportamento padrão de pode ser desabilitado por meio do Mecanismo de Regras na camada Premium da Verizon.

### <a name="peer-cache-fill-hot-filing"></a>Transmissão automática do preenchimento de cache par

O recurso de transmissão automática do preenchimento de cache par usa um algoritmo sofisticado exclusivo. Ele usa servidores de cache de borda adicionais baseados na largura de banda e métricas de solicitações agregadas para atender às solicitações de cliente por objetos grandes e altamente populares. Esse recurso impede uma situação em que grandes números de solicitações extras são enviados ao servidor de origem de um usuário. 

### <a name="conditions-for-large-file-optimization"></a>Condições para a otimização de arquivo grande

Os recursos de otimização da Verizon estão ativados por padrão. Não há nenhum limite no tamanho máximo do arquivo. 

## <a name="additional-considerations"></a>Considerações adicionais

Considere os aspectos adicionais a seguir para esse tipo de otimização.
 
### <a name="azure-content-delivery-network-from-akamai"></a>Rede de Distribuição de Conteúdo do Azure da Akamai

- O processo de agrupamento gera solicitações adicionais para o servidor de origem. No entanto, o volume total de dados entregues da origem é muito menor. O agrupamento resulta em melhores características de cache na CDN.

- A demanda de memória e E/S é reduzida na origem, porque partes menores do arquivo são entregues.

- Para partes armazenadas em cache na CDN, não há nenhuma solicitação adicional para a origem, até que o conteúdo expire ou seja removido do cache.

- Os usuários podem fazer solicitações de intervalo para a CDN e eles são tratados como qualquer arquivo normal. A otimização se aplica somente se ele for um tipo de arquivo válido e o intervalo de bytes estiver entre 10 MB e 150 GB. Se o tamanho médio do arquivo solicitado for menor que 10 MB, talvez você deseje usar a distribuição na Web geral.

### <a name="azure-content-delivery-network-from-verizon"></a>Rede de Distribuição de Conteúdo do Azure da Verizon

O tipo de otimização de distribuição na Web geral pode fornecer arquivos grandes.
