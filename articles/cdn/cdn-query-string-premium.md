---
title: Controlar o comportamento de cache da CDN do Azure com cadeias de caracteres de consulta - camada Premium | Microsoft Docs
description: O cache da cadeia de caracteres de consulta da CDN do Azure controla como os arquivos são armazenados em cache quando uma solicitação da Web contém uma cadeia de caracteres de consulta. Este artigo descreve o cache da cadeia de caracteres de consulta na CDN Premium do Azure do produto Verizon.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 2bea8aa06daef5d119b4cbfc4853a2d6ab07ddb7
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593511"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Controlar o comportamento de cache da CDN do Azure com cadeias de caracteres de consulta - camada Premium
> [!div class="op_single_selector"]
> * [Camada Standard](cdn-query-string.md)
> * [Camada Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Visão geral
Com a Rede de Distribuição de Conteúdo do Azure (CDN), você pode controlar como os arquivos são armazenados em cache para uma solicitação na Web que contenha uma cadeia de caracteres de consulta. Em uma solicitação da Web com uma cadeia de caracteres de consulta, a cadeia de caracteres de consulta é aquela parte da solicitação que ocorre após um ponto de interrogação (?). Uma cadeia de caracteres de consulta pode conter um ou mais pares de chave-valor, no qual o nome do campo e seu valor são separados por um sinal de igual (=). Cada par chave-valor é separado por um e comercial (&). Por exemplo, http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Se houver mais de um par chave-valor em uma cadeia de caracteres de consulta de uma solicitação, a ordem não importa. 

> [!IMPORTANT]
> Os produtos da CDN premium e padrão oferecem a mesma funcionalidade de cache de cadeia de caracteres de consulta, mas a interface do usuário é diferente. Este artigo descreve a interface da **CDN Premium do Azure da Verizon**. Para cache da cadeia de caracteres de consulta com produtos Standard do CDN do Azure, consulte [Controlar o comportamento de cache da CDN do Azure com cadeias de caracteres de consulta - camada Standard](cdn-query-string.md).
>


Estão disponíveis três modos de cadeia de caracteres de consulta:

- **standard-cache**: Modo padrão. Nesse modo, o nó POP (ponto de presença) da CDN transmite as cadeias de caracteres de consulta do solicitante para o servidor de origem na primeira solicitação e armazena em cache o ativo. Todas as solicitações subsequentes para esse ativo que forem atendidas a partir do servidor POP ignoram a cadeia de caracteres de consulta até que o ativo em cache expire.

    >[!IMPORTANT] 
    > Se a autorização de token estiver ativada para qualquer caminho nesta conta, o modo de cache padrão é o único modo que poderá ser usado. 

- **no-cache**: Nesse modo, as solicitações com cadeias de caracteres de consulta não estão em cache no nó POP da CDN. O nó POP recupera o ativo diretamente da origem e passa-o para o solicitante com cada solicitação.

- **unique-cache**: Nesse modo, cada solicitação com um URL exclusiva, incluindo a cadeia de caracteres de consulta, é tratada como um ativo exclusivo com seu próprio cache. Por exemplo, a resposta do servidor de origem para uma solicitação para example.ashx?q=test1 é armazenada em cache no nó POP e retornada para caches subsequentes com a mesma cadeia de caracteres de consulta. Uma solicitação para example.ashx?q=test2 é armazenada em cache como um ativo separado com sua própria configuração de vida útil.
   
    >[!IMPORTANT] 
    > Não use este modo quando a cadeia de caracteres de consulta contiver parâmetros que serão alterados em cada solicitação, como uma ID da sessão ou um nome de usuário, porque isso resultará em baixo índice de ocorrências no cache.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Alterando as configurações de cache da cadeia de consulta para perfis de CDN Premium
1. Abra o perfil CDN, clique em **Gerenciar**.
   
    ![Botão Gerenciar perfil da CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    O portal de gerenciamento da CDN é aberto.
2. Passe o mouse sobre a guia **HTTP Grande**, em seguida, sobre o submenu **Configurações do Cache**. Clique em **Cache de Cadeia de Caracteres de Consulta**.
   
    As opções de cache de cadeia de caracteres de consulta são exibidas.
   
    ![Opções de cache de cadeia de caracteres de consulta CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Selecione um modo de cadeia de caracteres de consulta e clique em **Atualização**.

> [!IMPORTANT]
> Como é necessário um tempo para que o registro se propague através da CDN, as alterações nas configurações da cadeia de caracteres de cache poderão não estar visíveis imediatamente. A propagação geralmente é concluída em até 10 minutos.
 

