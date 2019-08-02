---
title: Aceleração de site dinâmico via CDN do Azure
description: A CDN do Azure dá suporte à DSA (Aceleração de Site Dinâmico) para arquivos com conteúdo dinâmico.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: magattus
ms.openlocfilehash: 08e705d3c3623d4d02ccaea609eb0555aa1c8e33
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593917"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Aceleração de site dinâmico via CDN do Azure

Com a explosão de mídia social, comércio eletrônico e a Web extremamente personalizada, há um percentual em rápido crescimento do conteúdo fornecido para usuários finais que é gerado em tempo real. Os usuários esperam uma experiência na Web rápida, confiável e personalizada, independentemente do navegador, da localização, dispositivo ou rede. No entanto, as inovações que tornam essas experiências tão envolventes também deixam os downloads de página mais lentos e colocam em risco a qualidade da experiência do consumidor. 

A funcionalidade da CDN (rede de distribuição de conteúdo) padrão inclui a capacidade de armazenar arquivos em cache mais próximos dos usuários finais para acelerar a entrega de arquivos estáticos. No entanto, com aplicativos Web dinâmicos, armazenar em cache esse conteúdo em localizações de borda não é possível porque o servidor gera o conteúdo em resposta ao comportamento do usuário. Acelerar a entrega desse tipo de conteúdo é mais complexo do que o cache de borda tradicional e exige uma solução de ponta a ponta que ajuste bem cada elemento ao longo de todo o caminho de dados, do início à entrega. Com a otimização de DSA (aceleração de site dinâmico) da CDN do Azure, o desempenho de páginas da Web com conteúdo dinâmico é melhorado de maneira significativa.

A **CDN do Azure da Akamai** e **CDN do Azure CDN do Verizon** ambos oferecem otimização de DSA através do menu **Otimizado para** durante a criação do ponto de extremidade. Aceleração de site dinâmico da Microsoft é oferecida por meio [o serviço do Azure da frente](https://docs.microsoft.com/azure/frontdoor/front-door-overview).

> [!Important]
> Para perfis **CDN do Azure da Akamai**, é possível alterar a otimização de um ponto de extremidade CDN após sua criação.
>   
> Para perfis **CDN do Azure do Verizon**, não é possível alterar a otimização de um ponto de extremidade CDN depois que ele foi criado.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>Configuração do ponto de extremidade CDN para acelerar a entrega de arquivos dinâmicos

Para configurar um ponto de extremidade CDN para otimizar a entrega de arquivos dinâmicos, você pode usar o Portal do Azure, as APIs REST ou qualquer um dos SDK clientes para fazer o mesmo programaticamente. 

**Configurar um ponto de extremidade da CDN para otimização de DSA usando o Portal do Azure:**

1. Na página**Perfil CDN**, selecione **Ponto de Extremidade**.

   ![Adicionar um novo ponto de extremidade CDN](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   O painel **Adicionar um ponto de extremidade** é exibido.

2. Em **Otimizado para**, selecione **Aceleração de site dinâmico**.

    ![Criar um novo ponto de extremidade CDN com DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. Para **Caminho de investigação**, insira um caminho válido para um arquivo.

    Caminho de investigação é um recurso específico para DSA e um caminho válido é necessário para a criação. A DSA usa um arquivo de *caminho de investigação* pequeno colocado no servidor de origem para otimizar configurações de roteamento de rede para a CDN. Para o arquivo do caminho de investigação, você pode baixar e fazer upload do arquivo de exemplo para o seu site ou usar um ativo existente em sua origem com cerca de 10 KB de tamanho.

4. Insira as outras opções de ponto de extremidade necessárias (para obter mais informações, consulte [Criar um novo ponto de extremidade CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)) e, em seguida, selecione **Adicionar**.

   Depois que o ponto de extremidade CDN é criado, ele aplica as otimizações DSA para todos os arquivos que correspondem a determinados critérios. 


**Configurar um ponto de extremidade existente para DSA (CDN do Azure somente nos perfis Akamai):**

1. Na página**Perfil CDN**, selecione o ponto de extremidade que deseja modificar.

2. No painel esquerdo, selecione **Otimização**. 

   A página **Otimização** é exibida.

3. Um **Otimizado para**, selecione **Aceleração de site dinâmico** e, em seguida, selecione **Salvar**.

> [!Note]
> A DSA incorre em encargos adicionais. Para obter mais informações, consulte [Preços de Rede de Distribuição de Conteúdo](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>Otimização de DSA usando a CDN do Azure

Aceleração de site dinâmico na CDN do Azure acelera a entrega de ativos dinâmicos usando as técnicas a seguir:

-   [Otimização de rota](#route-optimization)
-   [Otimizações de TCP](#tcp-optimizations)
-   [Pré-busca de objeto(CDN do Azure da Akamai, somente)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Compactação da imagem adaptável (CDN do Azure da Akamai, somente)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Otimização de Rota

A otimização de rota é importante porque a Internet é um lugar dinâmico, em que o tráfego e interrupções temporárias alteram constantemente a topologia de rede. O BGP (Border Gateway Protocol) é o protocolo de roteamento da Internet, mas pode haver rotas mais rápidas por meio de servidores PoP (ponto de presença) intermediários. 

A otimização da rota escolhe o melhor caminho para a origem de modo que um site esteja continuamente acessível e o conteúdo dinâmico seja entregue aos usuários finais por meio da rota mais rápida e confiável possível. 

A rede Akamai usa técnicas para coletar dados em tempo real e comparar vários caminhos através de nós diferentes no servidor da Akamai, bem como a rota BGP padrão através Internet aberta, para determinar a rota mais rápida entre a origem e a borda da CDN. Essas técnicas evitam pontos de congestionamento da Internet e rotas longas. 

De forma similar, a rede do Verizon utiliza uma combinação de DNS Anycast, PoPs de suporte de alta capacidade e verificações de integridade para determinar os melhores gateways para melhor rotear dados do cliente para a origem.
 
Como resultado, conteúdo totalmente dinâmico e transacional é entregue de modo mais rápido e confiável para os usuários finais, mesmo quando ele é não armazenável em cache. 

### <a name="tcp-optimizations"></a>Otimizações de TCP

O protocolo TCP é o padrão do pacote de protocolos IP usado para fornecer informações entre os aplicativos em uma rede IP.  Por padrão, várias solicitações alternadas são necessárias para configurar uma conexão TCP, bem como limites para evitar congestionamentos de rede, o que resulta em ineficiências em escala. **CDN do Azure da Akamai** lida com esse problema ao otimizar em três áreas: 

 - [Eliminar o início lento de TCP](#eliminating-tcp-slow-start)
 - [Aproveitar as conexões persistentes](#leveraging-persistent-connections)
 - [Ajustar parâmetros de pacote TCP](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Eliminar o início lento de TCP

O *início lento* de TCP é um algoritmo do protocolo TCP que evita o congestionamento da rede, limitando a quantidade de dados enviados pela rede. Ele começa com tamanhos de janela de congestionamento pequenos entre remetente e receptor até que o máximo seja atingido ou que perda de pacotes seja detectada.

 Ambos os perfis **CDN do Azure da Akamai** e **CDN do Azure do Verizon** eliminam o início lento de TCP com as três etapas a seguir:

1. O monitoramento de largura de banda e integridade é usado para medir a largura de banda das conexões entre servidores PoP de borda.
    
2. Métricas são compartilhadas entre os servidores PoP de borda para que cada servidor esteja ciente das condições da rede e integridade do servidor de outros PoPs ao redor deles.  
    
3. Os servidores de borda da CDN fazem suposições sobre alguns parâmetros de transmissão, tais como qual deve ser o tamanho da janela ideal ao comunicar-se com outros servidores de borda da CDN nas proximidades. Essa etapa significa que o tamanho da janela de congestionamento inicial pode ser aumentado se a integridade de conexão entre os servidores de borda da CDN é capaz de realizar transferências de dados de pacotes maiores.  

#### <a name="leveraging-persistent-connections"></a>Aproveitar as conexões persistentes

Usando uma CDN, menos computadores exclusivos se conectam diretamente ao seu servidor de origem se comparado ao cenário em que os usuários se conectam diretamente à sua origem. O CDN do Azure também efetua pools de solicitações dos usuários em conjunto para estabelecer menos conexões com a origem.

Como mencionado anteriormente, várias solicitações de handshake são necessárias para estabelecer uma conexão TCP. As conexões persistentes, que são implementadas pelo cabeçalho HTTP `Keep-Alive` reutilizam as conexões TCP existentes para várias solicitações HTTP para economizar tempo de viagem de ida e volta e acelerar a entrega. 

A **CDN do Azure do Verizon** também envia pacotes periódicos de Keep Alive sobre a conexão TCP para impedir que uma conexão aberta seja fechada.

#### <a name="tuning-tcp-packet-parameters"></a>Ajustar parâmetros de pacote TCP

A **CDN do Azure da Akamai** ajusta os parâmetros que controlam as conexões de servidor para servidor e reduz a quantidade de viagens de ida e volta de longa distância necessárias para recuperar o conteúdo inserido no site, usando as técnicas a seguir:

- Aumentando a janela de congestionamento inicial para que mais pacotes possam ser enviados sem esperar por uma confirmação.
- Diminuindo o tempo limite de retransmissão inicial para que uma perda seja detectada e a retransmissão ocorra mais rapidamente.
- Diminuindo o tempo limite de retransmissão mínimo e máximo para reduzir o tempo de espera antes de assumir que pacotes foram perdidos durante a transmissão.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Pré-busca de objeto(CDN do Azure da Akamai, somente)

A maioria dos sites consistem em uma página HTML que faz referência a vários outros recursos, tais como imagens e scripts. Normalmente, quando um cliente solicita uma página da Web, o navegador primeiro baixa e analisa o objeto HTML e então faz solicitações adicionais a ativos vinculados que são necessários para carregar totalmente a página. 

*Pré-busca* é uma técnica para recuperar imagens e scripts inseridos na página HTML enquanto o HTML é fornecido para o navegador, antes mesmo que o navegador faça essas solicitações de objeto. 

Com a opção de pré-busca ativada no momento em que a CDN serve a página base HTML para o navegador do cliente, a CDN analisa o arquivo HTML e faz solicitações adicionais para quaisquer recursos vinculados e os armazenará em seu cache. Quando o cliente faz as solicitações para os ativos vinculados, o servidor de borda da CDN já tem os objetos solicitados e pode servi-los imediatamente sem uma viagem de ida e volta à origem. Essa otimização beneficiará tanto o conteúdo armazenável em cache quanto o não armazenável em cache.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Compactação da imagem adaptável (CDN do Azure da Akamai, somente)

Alguns dispositivos, especialmente os móveis, têm velocidades de rede mais lentas de vez em quando. Nesses cenários, é mais útil para o usuário receber imagens menores em sua página da Web mais rapidamente, em vez de esperar muito tempo para imagens de alta resolução.

Esse recurso monitora automaticamente a qualidade da rede e utiliza métodos padrão de compactação de JPEG quando velocidades de rede são mais lentas, para melhorar o tempo de entrega.

Compactação de imagem adaptável | Extensões de arquivo  
--- | ---  
Compactação JPEG | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Cache

Com o DSA, o cache é desativado por padrão na CDN, mesmo quando a origem incluir cabeçalhos `Cache-Control` ou `Expires` na resposta. A DSA normalmente é utilizada para ativos dinâmicos que não devem ser armazenados em cache porque são exclusivos para cada cliente. O cache pode interromper esse comportamento.

Se você tiver um site com uma mistura de ativos estáticos e dinâmicos, é melhor usar uma abordagem híbrida para obter o melhor desempenho. 

Para perfis **CDN Standard do Azure do Verizon** e **CDN Standard do Azure da Akamai**, é possível ativar o cache para pontos de extremidade da DSA específicos usando as [regras de cache](cdn-caching-rules.md).

Para acessar as regras de cache:

1. Na página **Perfil CDN** em configurações, selecione **Regras de cache**.  
    
    ![Botão de regras de cache da CDN](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    A página **Regras de cache** abre.

2. Crie uma regra de cache global ou customizada para ativar o cache do ponto de extremidade da DSA. 

Para os perfis **CDN Premium do Azure do Verizon** somente, você ativa o cache para pontos de extremidade da DSA específicos usando o [mecanismo de regras](cdn-rules-engine.md). Quaisquer regras criadas afetam apenas os pontos de extremidade do perfil otimizado para DSA. 

Para acessar o mecanismo de regras:
    
1. Na página **Perfil CDN**, selecione **Gerenciar**.  
    
    ![Botão Gerenciar perfil da CDN](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    O portal de gerenciamento da CDN é aberto.

2. No portal de gerenciamento de CDN, selecione **ADN** e, em seguida, selecione **Mecanismo de Regras**. 

    ![Mecanismo de regras para DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Como alternativa, é possível utilizar dois pontos de extremidade CDN: um ponto de extremidade otimizado com DSA para fornecer ativos dinâmicos e outro ponto de extremidade otimizado com um tipo de otimização estática, como entrega Web geral, para entrega de ativos armazenáveis em cache. Modifique as URLs da página da Web para conectar-se diretamente ao ativo no ponto de extremidade CDN que você planeja usar. 

Por exemplo: `mydynamic.azureedge.net/index.html` é uma página dinâmica e é carregado do ponto de extremidade DSA.  A página HTML faz referência a vários ativos estáticos como bibliotecas JavaScript ou imagens que são carregadas do ponto de extremidade CDN estático, tais como `mystatic.azureedge.net/banner.jpg` e `mystatic.azureedge.net/scripts.js`. 



