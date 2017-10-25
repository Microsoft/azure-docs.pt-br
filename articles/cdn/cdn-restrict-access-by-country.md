---
title: "Restringir o conteúdo da CDN do Azure por país | Microsoft Docs"
description: "Aprenda a restringir o acesso ao seu conteúdo da CDN do Azure usando o recurso Filtragem geográfica."
services: cdn
documentationcenter: 
author: lichard
manager: akucer
editor: 
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 30160088d9c770400f342e67527e1cf1cabc4f6b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="restrict-azure-cdn-content-by-country"></a>Restringir conteúdo da CDN do Azure por país

## <a name="overview"></a>Visão geral
Quando um usuário solicita o conteúdo, por padrão, o conteúdo é apresentado, independentemente de onde o usuário fez essa solicitação. Em alguns casos, você talvez queira restringir o acesso ao seu conteúdo por país. Este tópico explica como usar o recurso **Filtragem geográfica** para configurar o serviço para permitir ou bloquear o acesso por país.

> [!IMPORTANT]
> Os produtos Verizon e Akamai fornecem a mesma funcionalidade de filtragem geográfica mas possuem uma pequena diferença nos códigos do país que suportam. Confira a Etapa 3 para obter um link para as diferenças.


Para obter informações sobre as considerações que se aplicam à configuração desse tipo de restrições, consulte a seção [Considerações](cdn-restrict-access-by-country.md#considerations) no final do tópico.  

![Filtragem de País](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>Etapa 1: Definir o caminho de diretório
Selecione o ponto de extremidade no portal e localize a guia Filtragem geográfica na barra de navegação à esquerda para localizar esse recurso.

Ao configurar um filtro de país, você deve especificar o caminho relativo para o local o acesso dos usuários será permitido ou negado. Você pode aplicar a filtragem geográfica para todos os arquivos com "/" ou pastas selecionadas, especificando os caminhos de diretório "/pictures/". Você também pode aplicar a filtragem geográfica a um único arquivo, especificando o arquivo e omitindo a barra à direita "/pictures/city.png".

Filtro de caminho do diretório de exemplo:

    /                                 
    /Photos/
    /Photos/Strasbourg/
      /Photos/Strasbourg/city.png

## <a name="step-2-define-the-action-block-or-allow"></a>Etapa 2: Definir a ação: bloquear ou permitir
**Bloqueio** : os usuários dos países especificados terão o acesso negado a ativos solicitados nesse caminho recursivo. Se nenhuma outra opção de filtragem de país tiver sido configurada para esse local, então, todos os outros usuários terão acesso permitido.

**Permissão** : somente os usuários dos países especificados terão o acesso permitido aos ativos solicitados desse caminho recursivo.

## <a name="step-3-define-the-countries"></a>Etapa 3: Definir os países
Selecione os países que você deseja bloquear ou permitir para o caminho. 

Por exemplo, a regra de bloqueio /Photos/Strasbourg/ filtrará arquivos, incluindo:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


### <a name="country-codes"></a>Códigos de país
O recurso **Filtragem geográfica** usa códigos de país para definir os países nos quais uma solicitação será permitida ou bloqueada para um diretório protegido. Você encontrará os códigos do país na [Códigos do País da CDN do Azure](https://msdn.microsoft.com/library/mt761717.aspx). 

## <a id="considerations"></a>Considerações
* Pode levar até 90 minutos para a Verizon ou alguns minutos com Akamai, para que as alterações em sua configuração de filtragem do país entrem em vigor.
* Esse recurso não oferece suporte a caracteres curinga (por exemplo, ‘*’).
* A configuração de filtragem geográfica associada com o caminho relativo será aplicada recursivamente para esse caminho.
* Apenas uma regra pode ser aplicada no mesmo caminho relativo (você não pode criar vários filtros de país que apontam para o mesmo caminho relativo). No entanto, uma pasta pode ter vários filtros de país. Isso é devido à natureza recursiva de filtros de país. Em outras palavras, uma subpasta de uma pasta configurada anteriormente pode ter um filtro de país diferente atribuído.

