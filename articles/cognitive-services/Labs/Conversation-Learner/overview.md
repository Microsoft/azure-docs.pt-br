---
title: O que é o Conversation Learner? - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba mais sobre o Conversation Learner e como ele funciona.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c46ffe2076d4b1491a3b27958dfbf5ed09115eaa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708304"
---
# <a name="what-is-conversation-learner"></a>O que é o Conversation Learner?

O Conversation Learner permite que você compile e ensine interfaces de conversa que aprendem de interações de exemplo. 

Diferentemente das abordagens tradicionais, o Conversation Learner considera o contexto de ponta a ponta de um diálogo para melhorar as respostas e fornecer experiências de usuário mais atraentes. Abrangendo um amplo conjunto de casos de uso orientados a tarefas, o Conversation Learner aplica o aprendizado de máquina em segundo plano para fazer com que bots e agentes inteligentes tenham menor probabilidade de frustrar os usuários, incorrer em custos adicionais de atendimento ao cliente e estimular mais interações intuitivas.

Os desenvolvedores começam, inserindo diálogos prototípicos que desejam imitar. O Modelo aprende conforme mais diálogos são inseridos. Quando o modelo estiver funcionando bem, o Bot poderá ser implantado para usuários finais. O Conversation Learner registra conversas com usuários, e o desenvolvedor pode examiná-las. Se erros forem identificados, o desenvolvedor pode fazer uma correção no local, e o modelo é treinado novamente e está disponível para uso imediatamente.

Essa abordagem reduz a codificação manual da lógica de controle de diálogo e permite que os proprietários de empresas ou especialistas de domínio contribuam para uma interface de conversação sem conhecimento de aprendizado de máquina anterior. Se for implantado como parte de um bot, de um dispositivo inteligente ou de um agente inteligente, o Conversation Learner poderá iterar rapidamente novas habilidades, comportamentos ou competências e rapidamente melhorará a qualidade. 

O Conversation Learner capacita os desenvolvedores para aumentar a velocidade de entrada no mercado e orientar diálogos bem-sucedidos em vários canais conversacionais por meio do Microsoft Bot Framework, ou autônomos usando sua própria infraestrutura.

Resumo e destaques:

- O Conversation Learner é uma primeira forma de inteligência artificial de criação de bots orientados a tarefa.

- Ele se baseia em uma rede neural recorrente (LSTM) de ponta a ponta e aprende diretamente de exemplos multivalores de conversas. 

- Permite que os designers, desenvolvedores, usuários de negócios e funcionários de call center criem e mantenham os bots. 

- Fornece a capacidade de expressar as regras de negócios e o senso comum no código.

- Durante as sessões de ensino, o modelo de rede neural é usado para pontuar o próximo conjunto de ações esperadas na conversa. O desenvolvedor do bot, então, pode selecionar a ação correta e treinar a rede para fornecer a resposta correta.
 
- Após a conclusão do treinamento, o desenvolvedor pode usar as caixas de diálogo de log das interações do usuário para fazer correções em respostas de bot e treinar novamente o modelo. 

- Pode chamar APIs específicas de domínio e de terceiros para concluir tarefas.

