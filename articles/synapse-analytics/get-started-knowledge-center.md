---
title: 'Tutorial: Comece a explorar o Centro de Conhecimento do Azure Synapse'
description: Neste tutorial, você aprenderá a usar o Centro de Conhecimento do Azure Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/04/2021
ms.openlocfilehash: a26f46da7b392bd3b4a49aacb360a4c6147f8d2c
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382633"
---
# <a name="explore-the-synapse-knowledge-center"></a>Explorar o Centro de Conhecimento do Azure Synapse

Neste tutorial, você aprenderá a usar o Centro de Conhecimento do Synapse Studio.

## <a name="getting-to-the-knowledge-center"></a>Introdução ao Centro de Conhecimento

Há duas maneiras de localizar o Centro de Conhecimento no Synapse Studio:

  1. No hub inicial, ao lado da parte superior da página, clique em **Learn**.
  2. Na barra de menus na parte superior, clique em **?** e em **Centro de conhecimento**.

Escolha um dos métodos e abra o **Centro de Conhecimento**.

## <a name="overview"></a>Visão geral

O **Centro de Conhecimento** permite que você faça três coisas:
* **Use amostras imediatamente**. Se você quiser um exemplo rápido de como o Azure Synapse funciona, escolha essa opção.
* **Procurar na galeria**. Essa opção permite vincular conjuntos de dados de exemplo e adicionar código de exemplo na forma de scripts SQL, notebooks e pipelines.
* **Conheça o Synapse Studio**. Essa opção o levará em um breve tour pelas partes básicas do Synapse Studio. Isso será útil se você nunca tiver usado o Synapse Studio.

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>Como explorar o armazenamento de blobs com o pool de SQL sem servidor

1. Acesse o **Centro de conhecimento** e clique em **Usar exemplos imediatamente**.
1. Selecione **Consultar dados com SQL**.
1. Clique em **Usar exemplo**.
1. Um novo script SQL de exemplo será aberto.
1. Role o conteúdo até a primeira consulta (linhas 28 a 32) e selecione o texto da consulta.
1. Clique em Executar. Ele executará apenas o código selecionado.

## <a name="loading-more-nyc-taxi-data"></a>Como carregar mais dados de táxi de NYC

1. Acesse o **Centro de conhecimento** e clique em **Procurar na galeria**.
1. Selecione a guia **Scripts SQL** na parte superior.
1. Escolha o exemplo de ingestão de dados **Carregar o conjunto de dados de Táxis de Nova York** e clique em **Continuar**.
1. Em **Pool de SQL**, escolha **Selecionar um pool existente**, escolha **SQLPOOL1** e o banco de dados **SQLPOOL1** criado anteriormente.
1. Clique em **Abrir Script**.
1. Um novo script SQL de exemplo será aberto.
1. Clique em **Executar**
1. Isso criará várias tabelas para todos os dados de táxi de NYC e as carregará usando o comando COPY do T-SQL. Se você tiver criado essas tabelas nas etapas anteriores do guia de início rápido, selecione e execute apenas o código para CRIAR e COPIAR para tabelas que não existem.

    > [!NOTE] 
    > Ao usar a galeria de exemplos para o script SQL com um pool de SQL dedicado (antigo SQL DW), você só poderá usar um pool de SQL dedicado (antigo SQL DW).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar um administrator](get-started-add-admin.md)

