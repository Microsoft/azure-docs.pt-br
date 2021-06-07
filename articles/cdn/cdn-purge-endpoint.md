---
title: Limpar um ponto de extremidade da CDN do Azure | Microsoft Docs
description: Saiba como limpar todo o conteúdo armazenado em cache de um ponto de extremidade da Rede de Distribuição de Conteúdo do Microsoft Azure. Os nós de borda armazenam ativos em cache até o fim da vida útil dos ativos.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/17/2019
ms.author: allensu
ms.openlocfilehash: 4fe4b99f9635ff254f1a75e03f13d7e6ffcb3c49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100366510"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Limpar um ponto de extremidade da CDN do Azure
## <a name="overview"></a>Visão geral
Os nós de borda da CDN do Azure armazenarão ativos em cache até a TTL (vida útil) do ativo expirar.  Depois que a TTL do ativo expira, quando um cliente solicita o ativo do nó de borda, o nó recupera uma nova cópia atualizada do ativo para atender à solicitação do cliente e atualizar o armazenamento do cache.

A prática recomendada para garantir que os usuários sempre obtenham a cópia mais recente de seus ativos é verter os ativos para cada atualização e publicá-los como novas URLs.  A CDN recuperará imediatamente os novos ativos para as solicitações do próximo cliente.  Às vezes, convém limpar o conteúdo em cache de todos os nós de borda e forçá-los a recuperar novos ativos atualizados.  Isso pode ocorrer devido a atualizações do aplicativo Web ou para atualizar rapidamente ativos que contenham informações incorretas.

> [!TIP]
> Observe que a limpeza só limpa o conteúdo em cache nos servidores de borda da CDN.  Qualquer cache downstream, como servidores proxy e caches do navegador local, ainda podem manter uma cópia em cache do arquivo.  É importante lembrar disso quando você definir a vida útil de um arquivo.  Você pode forçar um cliente downstream a solicitar a versão mais recente do arquivo, dando a ela um nome exclusivo sempre que atualizá-la ou aproveitando o [cache de cadeia de caracteres de consulta](cdn-query-string.md).  
> 
> 

Este tutorial o orienta durante a limpeza de ativos de todos os nós de borda de um ponto de extremidade.

## <a name="walkthrough"></a>Passo a passo
1. No [Portal do Azure](https://portal.azure.com), navegue até o perfil CDN que contém o ponto de extremidade que você deseja limpar.
2. Na folha do perfil da CDN, clique no botão Limpar.
   
    ![Folha Perfil CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    A folha Limpeza é aberta.
   
    ![Folha Limpeza da CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. Na folha Limpeza, selecione o endereço do serviço que deseja limpar na lista suspensa de URLs.
   
    ![Formulário de limpeza](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Você também pode acessar a folha Limpeza clicando no botão **Limpar** na folha de ponto de extremidade da CDN.  Nesse caso, o campo **URL** será pré-populado com o endereço do serviço do ponto de extremidade específico.
   > 
   > 
4. Selecione os ativos que você deseja limpar dos nós de borda.  Para limpar todos os ativos, clique na caixa de seleção **Limpar todos** .  Caso contrário, digite o caminho de cada ativo que você quer limpar na caixa de texto **Caminho**. Veja abaixo os formatos com suporte no caminho.
    1. **Limpeza de URL única**: limpe o ativo individual especificando a URL completa, com ou sem a extensão de arquivo, por exemplo, `/pictures/strasbourg.png`; `/pictures/strasbourg`
    2. **Limpeza de caractere curinga**: o asterisco (\*) pode ser usado como um caractere curinga. Limpe todos os arquivos, pastas e subpastas em um ponto de extremidade com `/*` no caminho ou limpe todos os arquivos e subpastas em uma determinada pasta especificando a pasta seguida por `/*`, por exemplo, `/pictures/*`.  Observe que, no momento, a limpeza do caractere curinga não é compatível com a CDN do Azure do Akamai. 
    3. **Limpeza do domínio raiz**: limpe a raiz do ponto de extremidade com "/" no caminho.
   
   > [!TIP]
   > Os caminhos devem ser especificados para limpeza e devem ser uma URL relativa que atenda à [expressão regular](/dotnet/standard/base-types/regular-expression-language-quick-reference) a seguir. Atualmente, **Limpar tudo** e **Limpeza de caractere curinga** não têm suporte na **CDN do Azure do Akamai**.
   > > Limpeza de URL única `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Cadeia de consulta `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Limpeza de caractere curinga `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`. 
   > 
   > Outras caixas de texto **Caminho** serão mostradas depois que você digitar um texto, para permitir que crie uma lista com vários ativos.  Para excluir ativos da lista, clique no botão de reticências (...).
   > 
5. Clique no botão **Limpar** .
   
    ![Botão Limpar](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> O processamento das solicitações de limpeza demora cerca de 10 minutos com a **CDN do Azure da Microsoft**, cerca de 2 minutos com a **CDN do Azure da Verizon** (Standard e Premium) e cerca de 10 segundos com a **CDN do Azure da Akamai**.  A CDN do Azure tem um limite de 100 solicitações de limpeza simultâneas por vez no nível do perfil. 
> 
> 

## <a name="see-also"></a>Confira também
* [Pré-carregar ativos em um ponto de extremidade da CDN do Azure](cdn-preload-endpoint.md)
* [Referência da API REST da CDN do Azure – limpar ou pré-carregar um ponto de extremidade](/rest/api/cdn/cdn/endpoints)

