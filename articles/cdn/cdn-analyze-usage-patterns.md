---
title: Relatórios de núcleo da Verizon | Microsoft Docs
description: 'Você pode exibir os padrões de uso para o CDN usando os relatórios a seguir: Largura de banda, dados transferidos, acertos, status do Cache, taxa de IPV4/IPV6 dados transferidos de acertos do Cache.'
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d48ddafdc1ec30ae1533b3a3101582f33e7f4b5c
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594152"
---
# <a name="core-reports-from-verizon"></a>Relatórios de núcleo da Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Usando os Relatórios de núcleo da Verizon por meio do portal Gerenciar para perfis Verizon, é possível exibir os padrões de uso para sua CDN com os seguintes relatórios:

* Largura de banda
* Dados Transferidos
* Acertos
* Status do Cache
* Taxa de Acertos do Cache
* Dados IPv4/IPV6 Transferidos

## <a name="accessing-verizon-core-reports"></a>Acessando Relatórios de núcleo da Verizon
1. Na folha do perfil do CDN, clique no botão **Gerenciar** .
   
    ![Botão Gerenciar perfil da CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    O portal de gerenciamento da CDN é aberto.
2. Passe o mouse sobre a guia **Análise**, em seguida, sobre o submenu **Relatórios Principais**. Clique em um relatório no menu.
   
    ![Portal de gerenciamento da CDN - menu Relatórios Principais](./media/cdn-reports/cdn-core-reports.png)

3. Para cada relatório, selecione um intervalo de datas na lista **Intervalo de Datas**. Você pode selecionar um intervalo de datas predefinido, como **Hoje** ou **Esta semana**, ou selecionar **Personalizado** e inserir manualmente um intervalo de datas clicando nos ícones de calendário. 

4. Depois de selecionar um intervalo de datas, clique em **Ir** para gerar o relatório. 

4. Se você deseja exportar os dados em formato Excel, clique no ícone do Excel acima do botão **Ir**.

## <a name="bandwidth"></a>Largura de banda
O relatório de largura de banda consiste em um grafo e em uma tabela de dados que indicam o uso de largura de banda da CDN para HTTP e HTTPS em um período específico, em Mbps. Você pode exibir o uso de largura de banda entre todos os POPs ou em um POP específico. Esse relatório permite exibir os picos de tráfego e a distribuição para os POPs.

Na lista **Nós do Edge**, selecione **Todos os Nós do Edge** para ver o tráfego de todos os nós ou selecione uma região específica.

O relatório é atualizado a cada cinco minutos.

![Relatório Largura de banda](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Dados Transferidos
Esse relatório consiste em um grafo e em uma tabela de dados que indicavam o uso do tráfego da CDN para HTTP e HTTPS em um período específico, em GB. Você pode exibir o uso de tráfego em todos os POPs ou em um POP específico. Esse relatório permite exibir os picos de tráfego e a distribuição entre os POPs.

Na lista **Nós do Edge**, selecione **Todos os Nós do Edge** para ver o tráfego de todos os nós ou selecione uma região específica.

O relatório é atualizado a cada cinco minutos.

![Relatório Dados transferidos](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Acertos (códigos de status)
Este relatório descreve a distribuição dos códigos de status de solicitação para o seu conteúdo. Todas as solicitações de conteúdo geram um código de status HTTP. O código de status descreve como os POPs de borda lidaram com a solicitação. Por exemplo, um código de status 2xx indica que a solicitação foi servida com êxito a um cliente, enquanto um código de status 4xx indica que ocorreu um erro. Para saber mais sobre os códigos de status HTTP, confira a [Lista de códigos de status HTTP](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![Relatório Acertos](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Status do Cache
Este relatório descreve a distribuição de acertos do cache e de erros de cache para solicitações do cliente. Como o desempenho mais rápido provém de ocorrências no cache, é possível otimizar velocidades de entrega de dados minimizando perdas no cache e ocorrências no cache expiradas. 

Para reduzir os erros de cache, configure seu servidor de origem para minimizar o uso do seguinte: 
 * Cabeçalhos de resposta `no-cache`
 * Armazenamento em cache de cadeia de caracteres de consulta, a menos que estritamente necessária  
 * Códigos de resposta não armazenáveis em cache

Para reduzir correspondências de cache expiradas, defina um ativo `max-age` como um longo período para minimizar o número de solicitações para o servidor de origem.

![Relatório Status do cache](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Os status do cache principal incluem:
* TCP_HIT: Servido do servidor de borda. O objeto estava no cache e não excedeu sua idade máxima.
* TCP_MISS: Servido do servidor de origem. O objeto não estava no cache e a resposta voltou para a origem.
* TCP_EXPIRED _ MISS: Servido do servidor de origem após revalidação com a origem. O objeto estava no cache, mas tinha excedido sua idade máxima. A revalidação com a origem resultou na substituição do objeto por uma nova resposta da origem.
* TCP_EXPIRED _ HIT: Servido do Edge Após revalidação com a origem. O objeto estava no cache mas excedeu sua idade máxima. Uma revalidação com o servidor de origem resultou na não modificação do objeto do cache.

### <a name="full-list-of-cache-statuses"></a>Lista completa de status do cache
* TCP_HIT - esse status é relatado quando uma solicitação é servida diretamente do POP para o cliente. Um ativo é servido imediatamente de um POP quando é armazenado em cache no POP mais próximo ao cliente e tem uma TTL (vida útil) válida. O TTL é determinado pelos seguintes cabeçalhos de resposta:
  
  * Cache-Control: período máximo s
  * Cache-Control: período máximo
  * Expira
* TCP_MISS: Este status indica que uma versão em cache do ativo solicitado não foi encontrada no POP mais próximo ao cliente. O ativo é solicitado de um servidor de origem ou de um servidor de escudo de origem. Se o servidor de origem ou o servidor de escudo de origem retornar um ativo, ele será servido ao cliente e armazenado em cache no cliente e no servidor de borda. Caso contrário, será retornado um código de status diferente de 200 (por exemplo, 403 Proibido ou 404 Não encontrado).
* TCP_EXPIRED_HIT: Esse status é relatado quando uma solicitação que direciona um ativo com uma TTL expirada foi servida diretamente do POP ao cliente. Por exemplo, quando a idade máxima do ativo tiver expirado. 
  
   Normalmente, uma solicitação expirada resulta em uma solicitação de revalidação para o servidor de origem. Para que um status TCP_EXPIRED _HIT ocorra, o servidor de origem deverá indicar que não há uma versão mais recente do ativo. Normalmente, essa situação resulta em uma atualização dos cabeçalhos Cache-Control e Expires do ativo.
* TCP_EXPIRED MISS: Esse status é relatado quando uma versão mais recente de um ativo em cache expirado é servida do POP ao cliente. Esse status ocorrerá quando a TTL de um ativo armazenado em cache tiver expirado (por exemplo, idade máxima expirada) e o servidor de origem retornar uma versão mais recente desse ativo. Essa nova versão do ativo é servida para o cliente em vez da versão armazenada em cache. Além disso, ela é armazenada em cache no servidor de borda e no cliente.
* CONFIG_NOCACHE: Este status indica que uma configuração específica do cliente POP do edge impediu que o ativo fosse armazenado em cache.
* NONE - esse status indica que uma verificação de atualização de conteúdo do cache não foi executada.
* TCP_CLIENT_REFRESH_MISS: Esse status é relatado quando um cliente HTTP, como um navegador, força um POP de borda a recuperar uma nova versão de um ativo obsoleto do servidor de origem. Por padrão, os servidores impedem que um cliente HTTP force os servidores do edge a recuperar uma nova versão do ativo do servidor de origem.
* TCP_PARTIAL_HIT: Esse status é relatado quando uma solicitação de intervalo de bytes resulta em um acerto de um ativo parcialmente armazenado em cache. O intervalo de bytes solicitado é servido imediatamente do POP ao cliente.
* UNCACHEABLE: Esse status é relatado quando um ativo `Cache-Control` e `Expires` cabeçalhos indicam que ele deve não ser armazenado em cache em um POP ou pelo cliente HTTP. Esses tipos de solicitações são servidos do servidor de origem.

## <a name="cache-hit-ratio"></a>Taxa de Acertos do Cache
Este relatório indica a porcentagem de solicitações em cache que foram servidas diretamente do cache.

O relatório fornece os seguintes detalhes:

* O conteúdo solicitado foi armazenado em cache no POP mais próximo ao solicitante.
* A solicitação foi servida diretamente da borda da rede.
* A solicitação não exigia revalidação no servidor de origem.

O relatório não inclui:

* Solicitações negadas devido às opções de filtragem de país/região.
* Solicitações de ativos cujos cabeçalhos indicam que eles não devem ser armazenado em cache. Por exemplo, cabeçalhos `Cache-Control: private`, `Cache-Control: no-cache` ou `Pragma: no-cache` impedem o armazenamento de um ativo em cache.
* Solicitações de intervalo de bytes para conteúdo parcialmente armazenado em cache.

A fórmula é: (TCP_ HIT/(TCP_ HIT+TCP_MISS))*100

![Relatório Taxa de acertos do cache](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>Dados IPv4/IPV6 Transferidos
Esse relatório mostra a distribuição de uso de tráfego em IPV4 versus IPV6.

![Dados IPv4/IPV6 Transferidos](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Considerações
Os relatórios só podem ser gerados com informações dos últimos 18 meses.

