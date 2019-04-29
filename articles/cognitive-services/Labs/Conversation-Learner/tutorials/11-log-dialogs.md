---
title: Como registrar diálogos em um modelo de Aprendiz de Conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como registrar diálogos com um modelo de Aprendizado de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e3a84bfa643ebe74983bcef0d0ea72c701ffa589
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707506"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Como registrar diálogos com um modelo de Aprendizado de Conversa

Este tutorial demonstra como Registro de Diálogos são empregados para treinar melhor modelos de aprendiz de conversa de interações gravadas com usuários do mundo real.

## <a name="video"></a>Vídeo

[![Versão prévia de tutorial de Registro de Diálogos](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

e o modelo de previsão do tempo criado nos tutoriais anteriores.

## <a name="details"></a>Detalhes
Registro de Diálogos são logs gravados de interação do seu bot com usuários finais. Aproveitando esses Registros de Diálogos você pode corrigir os rótulos de entidade e as seleções de ação para melhorar o desempenho do modelo e o desempenho do sistema geral.

## <a name="steps"></a>Etapas

Na interface do usuário da Web clique em "Importar Tutoriais" e selecione o modelo nomeado "Tutorial-11-LogDialogs".

Este modelo contém uma entidade chamada "cidade" e ações projetadas para responder a consultas sobre o clima naquela cidade. Dois diálogos treinados foram usados para treinar o modelo, portanto, as expectativas de desempenho são um pouco baixas. O modelo melhoraria com treinamento adicional e a exposição de interações de usuários do mundo real.

### <a name="create-a-new-conversation"></a>Cria uma nova conversa

1. No painel esquerdo, clique em "Registrar Diálogos" e, em seguida, no botão "Novo Registro de Diálogo".
2. No painel de chat, onde está escrito "Digite sua mensagem...", digite "Previsão do tempo em Austin"
3. Clique no botão "Teste Concluído".
4. Clique na caixa de diálogo de log "Previsão do tempo de Austin" na lista.
5. Clique na expressão "Previsão do tempo de Austin" no painel de chat.
6. Clique em "Austin", em seguida, clique em "cidade" na lista de entidades.
7. Clique no botão “Enviar alterações”.
    - Essa alteração no valor da entidade causa alterações downstream para a conversa, já que temos novos valores de entidade na memória. As ações posteriores provavelmente se tornarão inválidas especialmente aquelas envolvendo a entidade "cidade".
8. Clique em "Qual cidade?" expressão no painel de chat.
9. Selecione a resposta, "O clima em Austin está provavelmente ensolarado".
10. Clique no botão "Salvar como Diálogo de Treinamento".
    - O treinamento é iniciado imediatamente

![](../media/T11_logdialog.png)

Uma última observação. Dependendo das necessidades de negócios, o recurso de registro em log de conversa pode ser desativado indo para as configurações e desmarcando "Registrar conversas".

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Retorno de chamada de detecção de entidade](./12-entity-detection-callback.md)
