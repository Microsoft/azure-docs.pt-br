---
title: "Aceleração de site dinâmica por meio da Azure CDN"
description: "Aprofundamento em aceleração de site dinâmico"
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
ms.date: 08/02/2017
ms.author: v-semcev
ms.openlocfilehash: be2719e0e02c8bc69800ef4a3e7da3c3164cb9dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Aceleração de site dinâmica por meio da Azure CDN

Com a explosão de mídia social, comércio eletrônico e a Web extremamente personalizada, há um percentual em rápido crescimento do conteúdo fornecido para usuários finais que é gerado em tempo real. Os usuários esperam experiências na Web rápidas, confiáveis e personalizadas, independentemente do seu navegador, localização, dispositivo ou rede. No entanto, as inovações que tornam essas experiências tão envolventes também deixam os downloads de página mais lentos e colocam em risco a qualidade da experiência do consumidor. 

A funcionalidade de CDN Standard inclui a capacidade de armazenar os arquivos em cache com mais proximidade dos usuários finais, para acelerar a entrega de arquivos estáticos. No entanto, com aplicativos Web dinâmicos, armazenar em cache esse conteúdo em localizações de borda não é possível porque o servidor gera o conteúdo em resposta ao comportamento do usuário. Acelerar a entrega desse tipo de conteúdo é mais complexo do que o cache de borda tradicional e exige uma solução de ponta a ponta que ajuste bem cada elemento ao longo de todo o caminho de dados, do início à entrega. Com a DSA (aceleração de site dinâmico) da Azure CDN, o desempenho das páginas da Web com conteúdo dinâmico melhorou consideravelmente.

A CDN do Azure da Akamai e da Verizon oferece otimização DSA por meio do menu **Otimizado para** durante a criação do ponto de extremidade.

## <a name="configuring-cdn-endpoint-to-accelerate-delivery-of-dynamic-files"></a>Configurar o ponto de extremidade CDN para acelerar a entrega de arquivos dinâmicos

Você pode configurar o ponto de extremidade CDN para otimizar a entrega de arquivos dinâmicos por meio do Portal do Azure selecionando a opção **Aceleração de Site Dinâmico** na seleção de propriedade **Otimizado por** durante a criação do ponto de extremidade. Você também pode usar nossas APIs REST ou qualquer um dos SDKs de cliente para fazer a mesma coisa programaticamente. 

### <a name="probe-path"></a>Caminho de investigação
O caminho de investigação é um recurso específico para a Aceleração de Site Dinâmico, cuja criação requer um caminho de investigação válido. A DSA usa um pequeno arquivo de *caminho de investigação* colocado sobre a origem para otimizar as configurações de roteamento de rede para a CDN. Você pode baixar e carregar nosso arquivo de exemplo em seu site ou então, em vez disso, usar um ativo existente em sua origem com aproximadamente 10 KB para o caminho de investigação, se o ativo existir.

> [!Note]
> A DSA incorre em encargos adicionais. Para obter mais informações, consulte a [página de preço](https://azure.microsoft.com/pricing/details/cdn/).

As capturas de tela a seguir ilustram o processo por meio do Portal do Azure.
 
![Adicionar um novo ponto de extremidade CDN](./media/cdn-dynamic-site-acceleration/01_Endpoint_Profile.png) 

*Figura 1: adicionando um novo ponto de extremidade CDN do Perfil CDN*
 
![Criar um novo ponto de extremidade CDN com DSA](./media/cdn-dynamic-site-acceleration/02_Optimized_DSA.png)  

*Figura 2: criar um ponto de extremidade CDN com a otimização de aceleração de site dinâmico selecionada*

Depois de criar o ponto de extremidade CDN, ele aplica as otimizações de DSA a todos os arquivos que correspondem a certos critérios. A seção a seguir descreve a otimização de DSA em detalhes.

## <a name="dsa-optimization-using-azure-cdn"></a>Otimização de DSA usando a CDN do Azure

A Aceleração de Site Dinâmico na CDN do Azure acelera a entrega de ativos dinâmicos usando as seguintes técnicas:

-   Otimização de Rota
-   Otimizações de TCP
-   Pré-busca do objeto (somente Akamai)
-   Compactação de Imagem Móvel (somente Akamai)

### <a name="route-optimization"></a>Otimização de Rota

A otimização de rota é importante porque a Internet é um lugar dinâmico, em que o tráfego e interrupções temporárias alteram constantemente a topologia de rede. O BGP (Border Gateway Protocol) é o protocolo de roteamento da Internet, mas pode haver rotas mais rápidas por meio de servidores PoP (ponto de presença) intermediários. 

A otimização da rota escolhe o melhor caminho para a origem de modo que um site esteja continuamente acessível e o conteúdo dinâmico seja entregue aos usuários finais por meio da rota mais rápida e confiável possível. 

A rede Akamai usa técnicas para coletar dados em tempo real e comparar vários caminhos através de nós diferentes no servidor da Akamai, bem como a rota BGP padrão através Internet aberta, para determinar a rota mais rápida entre a origem e a borda da CDN. Essas técnicas evitam pontos de congestionamento da Internet e rotas longas. 

De forma similar, a rede do Verizon utiliza uma combinação de DNS Anycast, PoPs de suporte de alta capacidade e verificações de integridade para determinar os melhores gateways para melhor rotear dados do cliente para a origem.
 
Como resultado, conteúdo totalmente dinâmico e transacional é entregue de modo mais rápido e confiável para os usuários finais, mesmo quando ele é não armazenável em cache. 

### <a name="tcp-optimizations"></a>Otimizações de TCP

O protocolo TCP é o padrão do pacote de protocolos IP usado para fornecer informações entre os aplicativos em uma rede IP.  Por padrão, há várias solicitações alternadas necessárias para configurar uma conexão TCP, bem como limites para evitar congestionamentos de rede que resultam em ineficiências em escala. A CDN do Azure da Akamai lida com esse problema otimizando em três áreas: 

 - eliminar o início lento
 - aproveitar as conexões persistentes
 - ajustar parâmetros de pacote TCP (somente Akamai)

#### <a name="eliminating-slow-start"></a>Eliminar o início lento

O *início lento* faz parte do protocolo TCP, o que impede o congestionamento da rede limitando a quantidade de dados enviados pela rede. Ele começa com tamanhos de janela de congestionamento pequenos entre remetente e receptor até que o máximo seja atingido ou que perda de pacotes seja detectada.

A CDN do Azure da Akamai e da Verizon elimina o início lento em três etapas:

1.  Ambas a rede da Akamai e a da Verizon usam monitoramento de integridade e de largura de banda para medir a largura de banda de conexões entre os servidores PoP de borda.
2. As métricas são compartilhadas entre os servidores PoP de borda para que cada servidor esteja ciente das condições da rede e integridade do servidor de outros PoPs ao redor deles.  
3. Os servidores de borda da CDN agora são capazes de fazer suposições sobre alguns parâmetros de transmissão, tais como qual deve ser o tamanho da janela ideal ao se comunicar com outros servidores de borda da CDN nas proximidades. Essa etapa significa que o tamanho da janela de congestionamento inicial pode ser aumentado se a integridade de conexão entre os servidores de borda da CDN é capaz de realizar transferências de dados de pacotes maiores.  

#### <a name="leveraging-persistent-connections"></a>Aproveitar as conexões persistentes

Usando uma CDN, menos computadores exclusivos se conectam diretamente ao seu servidor de origem se comparado ao cenário em que os usuários se conectam diretamente à sua origem. A CDN do Azure da Akamai e da Verizon também agrupa solicitações do usuário em pools para estabelecer menos conexões com a origem.

Conforme mencionado anteriormente, conexões TCP fazem várias solicitações alternadas em um handshake para estabelecer uma nova conexão. Conexões persistentes, também conhecidas como "HTTP Keep Alive", reutilizarão conexões TCP existentes para várias solicitações HTTP de modo a salvar viagens de ida e volta e acelerar a entrega. 

A rede Verizon também envia pacotes keep-alive periódicos pela conexão TCP para impedir que uma conexão aberta seja fechada.

#### <a name="tuning-tcp-packet-parameters"></a>Ajustar parâmetros de pacote TCP

A CDN do Azure da Akamai também ajusta os parâmetros que controlam as conexões de servidor para servidor e reduz a quantidade de viagens de ida e volta de longa distância necessárias para recuperar o conteúdo inserido no site, usando as seguintes técnicas:

1.  Aumentando a janela de congestionamento inicial para que mais pacotes possam ser enviados sem esperar por uma confirmação.
2.  Diminuindo o tempo limite de retransmissão inicial para que uma perda seja detectada e a retransmissão ocorra mais rapidamente.
3.  Diminuindo o tempo limite de retransmissão mínimo e máximo para reduzir o tempo de espera antes de assumir que pacotes foram perdidos durante a transmissão.

### <a name="object-prefetch-akamai-only"></a>Pré-busca do objeto (somente Akamai)

A maioria dos sites consistem em uma página HTML que faz referência a vários outros recursos, tais como imagens e scripts. Normalmente, quando um cliente solicita uma página da Web, o navegador primeiro baixa e analisa o objeto HTML e então faz solicitações adicionais a ativos vinculados que são necessários para carregar totalmente a página. 

*Pré-busca* é uma técnica para recuperar imagens e scripts inseridos na página HTML enquanto o HTML é fornecido para o navegador, antes mesmo que o navegador faça essas solicitações de objeto. 

Com a opção **pré-busca** ativada no momento em que a CDN serve a página base HTML para o navegador do cliente, a CDN analisará o arquivo HTML e fará solicitações adicionais para quaisquer recursos vinculados e os armazenará em seu cache. Quando o cliente faz as solicitações para os ativos vinculados, o servidor de borda da CDN já tem os objetos solicitados e pode servi-los imediatamente sem uma viagem de ida e volta à origem. Essa otimização beneficiará tanto o conteúdo armazenável em cache quanto o não armazenável em cache.

### <a name="adaptive-image-compression-akamai-only"></a>Compactação de Imagem Adaptável (somente Akamai)

Alguns dispositivos, especialmente os móveis, têm velocidades de rede mais lentas de vez em quando. Nesses cenários, é mais útil para o usuário receber imagens menores em sua página da Web mais rapidamente, em vez de esperar muito tempo para imagens de alta resolução.

Esse recurso monitora automaticamente a qualidade da rede e utiliza métodos padrão de compactação de JPEG quando velocidades de rede são mais lentas, para melhorar o tempo de entrega.

Compactação de imagem adaptável | Extensões de arquivo  
--- | ---  
Compactação JPEG | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Cache

Com o DSA, o armazenamento em cache é desativado por padrão na CDN, mesmo quando a origem inclui controle de cache/expira cabeçalhos na resposta. Esse padrão é desativado porque DSA é normalmente usado para ativos dinâmicos que não devem ser armazenados em cache desde que eles sejam exclusivos para cada cliente e ativar o cache por padrão pode interromper esse comportamento.

Se você tiver um site com uma mistura de ativos estáticos e dinâmicos, é melhor usar uma abordagem híbrida para obter o melhor desempenho. 

Se você estiver usando ADN com Verizon Premium, você poderá ativar novamente o cache para casos específicos usando o mecanismo de regras.  

Uma alternativa é usar dois pontos de extremidade CDN. Um deles com DSA para entregar ativos dinâmicos e o outro ponto de extremidade com um tipo de otimização estática, assim como entrega Web em geral, para entregar ativos armazenáveis em cache. Para realizar essa alternativa, você modificará as URLs de página da Web para vincular diretamente para o ativo no ponto de extremidade CDN que você planeja usar. 

Por exemplo: `mydynamic.azureedge.net/index.html` é uma página dinâmica e é carregado do ponto de extremidade DSA.  A página HTML faz referência a vários ativos estáticos como bibliotecas JavaScript ou imagens que são carregadas do ponto de extremidade CDN estático, tais como `mystatic.azureedge.net/banner.jpg` e `mystatic.azureedge.net/scripts.js`. 

Você pode encontrar um exemplo [aqui](https://docs.microsoft.com/azure/cdn/cdn-cloud-service-with-cdn#controller) sobre como usar os controladores em um aplicativo Web ASP .NET para servir conteúdo por meio de uma URL específica da CDN.




