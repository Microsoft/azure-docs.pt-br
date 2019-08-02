---
title: Tutorial - Configurar regras de cache da CDN do Azure | Microsoft Docs
description: Neste tutorial, você configura uma regra de cache global e uma regra de cache personalizada da CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: c6da3270de94fd0d5525f28cdd31039f5bd85dbd
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594068"
---
# <a name="tutorial-set-azure-cdn-caching-rules"></a>Tutorial: Definir regras de cache da CDN do Azure

> [!NOTE] 
> As regras de cache da CDN do Azure estão disponíveis apenas para **CDN Standard do Azure do Verizon** e **CDN Standard do Azure da Akamai**. Para **CDN Premium do Azure da Verizon**, use o [mecanismo de regras da CDN do Azure](cdn-rules-engine.md) no portal **Gerenciar** para uma funcionalidade semelhante.
 

Esse artigo descreve como é possível utilizar as regras de cache da CDN (rede de distribuição de conteúdo) do Azure para definir ou modificar o comportamento de expiração do cache padrão globalmente e com condições personalizadas, como um caminho de URL e extensão de arquivo. CDN do Azure fornece dois tipos de regras de cache:
- Regras de cache globais: Você pode definir uma regra de cache global para cada ponto de extremidade em seu perfil, o que afeta todas as solicitações para o ponto de extremidade. A regra de cache global substitui todos os cabeçalhos de diretiva de cache HTTP, se configurado.

- Regras de cache personalizadas: Você pode definir uma ou mais regras de cache personalizadas para cada ponto de extremidade em seu perfil. As regras de cache personalizadas combinam caminhos específicos e extensões de arquivo, são processadas em ordem e substituem a regra de cache global, se definido. 

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Abrir a página Regras de cache.
> - Criar uma regra de cache global.
> - Criar uma regra de cache personalizada.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de concluir as etapas deste tutorial, crie primeiro um perfil CDN e pelo menos um ponto de extremidade da CDN. Para saber mais, confira [Início Rápido: Criar um ponto de extremidade e um perfil de CDN do Azure](cdn-create-new-endpoint.md).

## <a name="open-the-azure-cdn-caching-rules-page"></a>Abrir a página Regras de cache da CDN do Azure

1. No [Portal do Azure](https://portal.azure.com), selecione um perfil da CDN e selecione um ponto de extremidade.

2. No painel esquerdo em Configurações, selecione **Regras de cache**.

   ![Botão de regras de cache da CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   A página **Regras de cache** é exibida.

   ![Página Regras de cache da CDN](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="set-global-caching-rules"></a>Configurar regras de cache globais

Crie uma regra de cache global da seguinte maneira:

1. Em **Regras de cache globais**, defina **Comportamento de cache de cadeia de caracteres de consulta** para **Ignorar cadeias de caracteres**.

2. Definir **Comportamento do cache** para **Definir se ausente**.
       
3. Para **Duração da expiração do cache**, digite 10 no campo **Dias**.

    A regra de cache global afeta todos as solicitações para o ponto de extremidade. Essa regra aceita os cabeçalhos de diretivas de cache de origem, se existirem (`Cache-Control` ou `Expires`); caso contrário, se eles não forem especificados, ele define o cache para 10 dias. 

    ![Regras de cache globais](./media/cdn-caching-rules/cdn-global-caching-rules.png)

## <a name="set-custom-caching-rules"></a>Configurar regras de cache personalizadas

Crie uma regra de cache personalizada da seguinte maneira:

1. Em **Regras de cache personalizadas**, defina **Condição de correspondência** para **Caminho** e **Valor de correspondência** para `/images/*.jpg`.
    
2. Defina o **Comportamento do cache** para **Substituir** e digite 30 no campo **Dias**.
       
    Essa regra de cache personalizada define uma duração de cache de 30 dias em qualquer `.jpg` arquivo de imagem na pasta `/images` do seu ponto de extremidade. Ele substitui quaisquer cabeçalhos HTTP `Cache-Control` ou `Expires` enviados pelo servidor de origem.

    ![Personalizar regras de cache](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

    
## <a name="clean-up-resources"></a>Limpar recursos

Nas etapas anteriores, você criou regras de cache. Se não deseja mais usar essas regras de cache, você poderá removê-las seguindo estas etapas:
 
1. Selecione um perfil CDN, em seguida, o ponto de extremidade com as regras de cache que você deseja remover.

2. No painel esquerdo em Configurações, selecione **Regras de cache**.

3. Em **Regras de cache globais**, defina **Comportamento do cache** para **Não definido**.
 
4. Em **Regras de cache personalizadas**, marque a caixa de seleção ao lado da regra que deseja excluir.

5. Selecione **Excluir**.

6. Na parte superior da página, selecione **Salvar**.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> - Abrir a página Regras de cache.
> - Criar uma regra de cache global.
> - Criar uma regra de cache personalizada.

Avance para o próximo artigo para saber como definir configurações de regra de cache adicionais.

> [!div class="nextstepaction"]
> [Controlar o comportamento de cache da CDN do Azure com regras de cache](cdn-caching-rules.md)



