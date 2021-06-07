---
title: Funcionalidades da estrutura da IU dos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services conceptual document
description: Saiba mais sobre as funcionalidades da estrutura da IU
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5b1aab8b38614249d6b502044b5c4c8170f46b3c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492210"
---
# <a name="ui-framework-capabilities"></a>Funcionalidades da Estrutura da IU

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

A estrutura da IU dos Serviços de Comunicação do Azure permite criar experiências de comunicação usando um conjunto de componentes reutilizáveis. Esses componentes são fornecidos em dois tipos: Os componentes **base** são os blocos de construção mais básicos da experiência de interface do usuário, enquanto as combinações desses componentes básicos são chamadas de componentes de **composição**.

## <a name="ui-framework-composite-components"></a>Componentes de composição da estrutura da IU

| Composição               | Descrição                                               | Web   | Android | iOS   |
|-------------------------|-----------------------------------------------------------|-------|---------|-------|
| Composição de Chamada em Grupo | Experiência de chamada de saída de voz e vídeo leve para a chamada dos Serviços de Comunicação do Azure usando ativos de design da interface do usuário do Fluent. Dá suporte a chamadas em grupo usando a ID do Grupo dos Serviços de Comunicação do Azure. A composição permite que uma chamada individual seja usada pela referência de uma identidade dos Serviços de Comunicação do Azure ou um número de telefone para a PSTN com um número de telefone adquirido por meio do Azure.                                    | React |  |  |
| Composição de Chat em Grupo    | Experiência de chat leve para os Serviços de Comunicação do Azure usando ativos de design da interface do usuário do Fluent. Essa experiência se concentra em fornecer um cliente de chat simples que pode se conectar às conversas dos Serviços de Comunicação do Azure. Ela permite que os usuários enviem mensagens e vejam as mensagens recebidas com indicadores de digitação e confirmações de leitura. Ela é escalada de cenários 1:1 para chat em grupo. Dá suporte a um só thread de chat.                         | React |  |  |

## <a name="ui-framework-base-components"></a>Componentes base da estrutura da IU

| Componente             | Descrição                                                                                                                                                                                                                                                                        | Web   | Android | iOS |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------|---------|-----|
| Provedor de Chamada    | Componente de inicialização básico para chamadas. Componente necessário para inicializar outros componentes baseados nele. Processa a lógica principal para inicializar o cliente de chamada usando tokens de acesso dos Serviços de Comunicação do Azure. Dá suporte à junção de grupo. | React | N/D     | N/D |
| Controles de Mídia   | Permite que os usuários gerenciem a chamada atual alternando o mudo, ativando/desativando o vídeo e encerrando a chamada.                                                                                                                                                              | React | N/D     | N/D |
| Galeria de Mídia   | Mostra todos os participantes da chamada em uma só galeria. A Galeria dá suporte a participantes estáticos e habilitados para vídeo. Para os participantes habilitados para vídeo, o vídeo é renderizado.                                                                                                                | React | N/D     | N/D |
| Configurações do Microfone | Selecione o microfone a ser usado para a chamada. Esse controle pode ser usado antes e durante uma chamada para a escolha do dispositivo de microfone.                                                                                                                                               | React | N/D     | N/D |
| Camera Settings     | Escolha a câmera a ser usada para a chamada de vídeo. Esse controle pode ser usado antes e durante uma chamada para a escolha do dispositivo de vídeo.                                                                                                                                             | React | N/D     | N/D |
| Configurações de Dispositivo     | Combina as configurações de microfone e câmera em um só componente                                                                                                 | React | N/D     | N/D |
| Provedor de Chat       | Componente de inicialização básico para chats. Componente necessário para inicializar outros componentes baseados nele. Processa a lógica principal para inicializar o cliente de chat com um token de acesso dos Serviços de Comunicação do Azure e o thread no qual ele ingressará.                                     | React | N/D     | N/D |
| Caixa de Envio          | Componente de entrada que permite aos usuários enviar mensagens para o thread de chat. A entrada dá suporte a texto, hiperlinks, emojis e outros caracteres Unicode, incluindo outros alfabetos.                                                                                                                         | React | N/D     | N/D |
| Thread de Chat           | Componente de thread que mostra que o usuário recebeu e enviou mensagens com as informações de remetente dele. O thread dá suporte a indicadores de digitação e confirmações de leitura. Role essas conversas para examinar o histórico de chat.
| Lista de Participantes      | Mostra todos os participantes da chamada ou do thread de chat como uma lista.  | React | N/D     | N/D |

## <a name="ui-framework-capabilities"></a>Funcionalidades da Estrutura da IU

| Recurso                                                             | Composição de Chamada em Grupo | Composição de Chat em Grupo | Componentes básicos |
|---------------------------------------------------------------------|-------------------------|----------------------|-----------------|
| Participar da Reunião do Teams                                                  |                         |                      |           
| Participar do Evento ao Vivo do Teams                                               |                         |                      | 
| Iniciar chamada VoIP para usuário do Teams                                       |                         |                      | 
| Participar de um Chat da Reunião do Teams                                           |                         |                      |            
| Ingressar na chamada dos Serviços de Comunicação do Azure com a ID do Grupo                | ✔                      |                      | ✔
| Iniciar uma chamada VoIP para um ou mais usuários dos Serviços de Comunicação do Azure |                         |                      |           
| Participar de um thread de chat dos Serviços de Comunicação do Azure                    |                         | ✔                   | ✔
| Ativar/desativar mudo na chamada                                                    | ✔                       |                      | ✔
| Ativar/desativar vídeo na chamada                                                | ✔                       |                      | ✔
| Compartilhamento de tela                                                      | ✔                       |                      | ✔
| Galeria de participantes                                                 | ✔                       |                      | ✔
| Gerenciamento de microfone                                               | ✔                       |                      | ✔
| Gerenciamento de câmera                                                   | ✔                       |                      | ✔
| Lobby de Chamada                                                          |                         |                      | ✔
| Enviar mensagem de chat                                                   |                         | ✔                   |            
| Receber mensagem de chat                                                |                         | ✔                   | ✔
| Indicadores de Digitação                                                   |                         | ✔                   | ✔
| Confirmação de Leitura                                                        |                         | ✔                   | ✔
| Lista de Participantes                                                    |                         |                      | ✔


## <a name="customization-support"></a>Suporte à personalização

| Tipo de componente            | Temas     | Layout                                                              | Modelos de Dados |
|---------------------------|------------|---------------------------------------------------------------------|-------------|
| Componente de Composição       |     N/D    | N/D                                                                 |     N/D     |
| Componente Base            |     N/D    | O layout dos componentes pode ser modificado usando a definição de estilo externa         |     N/D     |


## <a name="platform-support"></a>Suporte a plataforma

| .    | Windows            | macOS                | Ubuntu   | Linux    | Android  | iOS        |
|--------|--------------------|----------------------|----------|----------|----------|------------|
| SDK da interface do usuário | Chrome\*, novo Microsoft Edge | Chrome\*, Safari\*\* | Chrome\* | Chrome\* | Chrome\* | Safari\*\* |

\*Observe há suporte para a última versão do Chrome, além das duas versões anteriores.

\*\*Observe que há suporte para as versões posteriores à 13.1 do Safari. Ainda não há suporte para vídeo de saída para o Safari no macOS, mas há suporte no iOS. Só há suporte para o compartilhamento de tela de saída no iOS desktop.
