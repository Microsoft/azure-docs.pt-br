---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: scheduler
ms.service: scheduler
author: derek1ee
ms.topic: include
ms.date: 08/16/2016
ms.author: deli
ms.custom: include file
ms.openlocfilehash: b3788ede23a423bebf96661ea88b227bfb5fdf4c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172298"
---
A tabela a seguir descreve cada uma das principais cotas, limites, padrões e limitações no Agendador do Azure.

| Resource | Descrição de limite |
| -------- | ----------------- |
| **Tamanho do trabalho** | O tamanho máximo do trabalho é 16.000. Se uma operação PUT ou uma PATCH resultar em um tamanho de trabalho maior do que esse limite, um código de status 400 Solicitação incorreta será retornado. | 
| **Coleções de trabalho** | O número máximo de coleções de trabalho por assinatura do Azure é de 200.000. | 
| **Trabalhos por coleção** | Por padrão, o número máximo de trabalhos é de cinco em uma coleção de trabalhos gratuita e 50 trabalhos em uma coleção de trabalhos padrão. <p>É possível alterar o número máximo de trabalhos em uma coleção. Todos os trabalhos na coleção de trabalhos estão limitados ao valor definido na coleção de trabalhos. Se você tentar criar mais trabalhos do que a cota máxima de trabalhos, a solicitação falhará com um código de status 409 Conflito. | 
| **Hora de início da hora** | O "tempo para hora de início" é de 18 meses. |
| **Intervalo de recorrência** | O período máximo de recorrência é de 18 meses. | 
| **Frequência** | Por padrão, a cota máxima de frequência é de uma hora em uma coleção de trabalhos gratuita e de um minuto em uma coleção de trabalhos padrão. <p>É possível realizar a frequência máxima em uma coleção de trabalhos menor do que o máximo. Todos os trabalhos na coleção estão limitados ao valor definido na coleção de trabalhos. Se você tentar criar um trabalho com uma frequência maior do que a máxima na coleção de trabalhos, a solicitação falhará com um código de status 409 Conflito. | 
| **Tamanho do corpo** | O tamanho máximo do corpo de uma solicitação é 8.192 caracteres. |
| **Tamanho da solicitação de URL** | O tamanho máximo para uma URL de solicitação é de 2.048 caracteres. |
| **Contagem de cabeçalho** | A contagem máxima de cabeçalhos é de 50 cabeçalhos. | 
| **Tamanho do cabeçalho de agregação** | O tamanho máximo do cabeçalho agregação é 4.096 caracteres. |
| **Tempo limite** | O tempo limite da solicitação é estático, ou seja, não é configurável. e é de 60 segundos para ações de HTTP. Para operações mais demoradas, siga os protocolos assíncronos do HTTP. Por exemplo, retornar um 202 imediatamente, mas continuar funcionando em segundo plano. | 
| **Histórico de trabalho** | O corpo de resposta máximo armazenado no histórico de trabalho é de 2.048 bytes. |
| **Retenção de histórico de trabalho** | Histórico de trabalho é mantido por até dois meses ou até as últimas 1.000 execuções. | 
| **Retenção de trabalhos concluídos e com falha** | Os trabalhos concluídos e com falha são retidos por 60 dias. |
||| 

