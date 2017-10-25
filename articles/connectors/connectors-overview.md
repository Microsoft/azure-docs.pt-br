---
title: "Visão geral dos Conectores dos Aplicativos Lógicos | Microsoft Docs"
description: "Visão geral de conectores que podem ser usados em um aplicativo lógico"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: ca8dab2e-9b69-4b1e-865d-1facd9f0cdac
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan
ms.openlocfilehash: 9cbb258ae9e32549669623e6824dd9b18fa1f68f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-connectors-in-a-logic-app"></a>Usando conectores em um aplicativo lógico
Os conectores fornecem acesso rápido a eventos, dados e ações entre serviços, protocolos e plataformas.  A lista completa de conectores compatíveis com Aplicativos Lógicos pode [ser encontrada aqui](apis-list.md).  Os conectores podem ser usados como um gatilho ou uma ação em um aplicativo lógico, podendo exigir uma *conexão* configurada a ser usada (por exemplo: autorizar uma conta do Twitter a acessar ou publicar em seu nome).

## <a name="basics"></a>Noções básicas
Os conectores são serviços hospedados que você pode acessar como parte de um aplicativo lógico para integração a outros serviços, como Dynamics, Azure, Salesforce [e mais](apis-list.md).  Eles são implantados e gerenciados pela Microsoft para que você possa criar fluxos de trabalho de integração prestando atenção à escala, produtividade e segurança.  Você pode adicionar um conector a um aplicativo lógico pesquisando e escolhendo um gatilho ou uma ação de conector em **Mostrar APIs gerenciadas pela Microsoft**.

![Menu de ação para a seleção de gatilho][1]

Cada gatilho ou ação de conector terá seu conjunto de propriedades a ser configurado.  É possível clicar nos botões de informação para saber mais sobre a ação ou consultar a respectiva documentação [para saber mais](apis-list.md).

Se desejar fazer a integração a um serviço ou uma API que ainda não seja um conector, você também poderá estender aplicativos lógicos por meio de um [conector personalizado](../logic-apps/logic-apps-create-api-app.md) ou apenas chamar diretamente o serviço por um protocolo como HTTP.

## <a name="triggers"></a>Gatilhos
Alguns conectores têm um gatilho, o que significa que um evento nesse conector acionará um aplicativo lógico e transmitirá todos os dados como parte do gatilho.  Um gatilho é sempre a primeira etapa em um aplicativo lógico.  Gatilhos populares incluem operações como:

* Recorrência — execução de hora em hora
* Quando uma solicitação HTTP é recebida
* Quando um item é adicionado a uma fila
* Quando um email é recebido

Alguns gatilhos serão acionados no instante em que um evento ocorre por meio de uma notificação ao aplicativo lógico, já outros precisarão de um intervalo de recorrência configurado com a frequência que o aplicativo lógico verificará o serviço para um avento (no máximo a cada 15 segundos).  

Depois que um evento for recebido, a execução do aplicativo lógico será disparada e as ações no fluxo de trabalho serão iniciadas.  Você também poderá acessar todos os dados do gatilho em todo o fluxo de trabalho (por exemplo, o gatilho 'Em um novo tweet' passará o tweet para a execução).

## <a name="actions"></a>Ações
A maioria dos conectores tem uma ou várias ações que podem ser executadas como parte do fluxo de trabalho.  As ações são as etapas que ocorrem após a execução ter sido disparada de um gatilho.  Para adicionar uma ação, clique no botão **Nova Etapa** e procure o conector que deseja usar.  Uma vez selecionado (e após configuração das [conexões](#connections) que podem ser necessárias), você verá o cartão de ação que pode configurar.  É possível escolher dados de etapas anteriores clicando em qualquer um dos tokens de saídas ou inserir qualquer outra configuração, conforme a necessidade.

![Configurando uma ação de conector][2]

## <a name="connections"></a>conexões
A maioria dos conectores exige a configuração de uma *conexão* para que você possa usar o conector.  Uma *conexão* é qualquer configuração de logon ou conexão necessária para acessar o conector.  Para os conectores que usam OAuth, criar uma conexão significa entrar no serviço (como o Office 365, Salesforce ou GitHub) em que seu token de acesso pode ser criptografado e armazenado com segurança em um repositório secreto do Azure.  Outros conectores (como FTP e SQL) exigem uma conexão que contenha configuração, como endereço do servidor, nome de usuário e senha.  Esses detalhes de configuração de conexão também são criptografados e armazenados com segurança.  As conexões poderão acessar o serviço pelo tempo que o serviço permitir.  Para conexões OAuth do Azure Active Directory (como o Office 365 e Dynamics), podemos continuar atualizando o token de acesso indefinidamente.  Outros serviços podem estabelecer limites quanto ao tempo pelo qual podemos usar um token sem que ele seja atualizado.  De modo geral, determinadas ações, como mudar uma senha, invalidarão todos os tokens de acesso.  

As conexões podem ser exibidas e gerenciadas no Azure clicando em **Procurar** e selecionando **Conexões de API**.  No recurso Conexões de API, você pode exibir, editar, atualizar ou autorizar novamente todas as conexões que criou.

## <a name="next-steps"></a>Próximas etapas
* [Criar seu primeiro aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md)
* [Veja os usos comuns e exemplos de aplicativos lógicos](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Introdução aos gatilhos e ações de integração corporativa](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png
