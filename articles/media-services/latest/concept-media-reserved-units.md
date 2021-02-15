---
title: Unidades reservadas de mídia-Azure
description: As unidades reservadas de mídia permitem que você dimensione o processo de mídia e determine a velocidade das tarefas de processamento de mídia.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: e0cfc47b546c31e6b93d224e64de20b79baacfd9
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98891288"
---
# <a name="media-reserved-units"></a>Unidades reservadas de mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Os serviços de mídia do Azure permitem dimensionar o processamento de mídia Gerenciando MRUs (unidades reservadas de mídia). Um MRU fornece capacidade adicional de computação necessária para a codificação de mídia. O número de MRUs determina a velocidade com a qual as tarefas de mídia são processadas e quantas tarefas de mídia podem ser processadas simultaneamente em uma conta. Por exemplo, se sua conta tiver cinco MRUs e houver tarefas a serem processadas, cinco tarefas de mídia poderão ser executadas simultaneamente. Todas as tarefas restantes serão enfileiradas e poderão ser selecionadas para processamento sequencialmente quando uma tarefa em execução for concluída. Cada MRU que você provisiona resulta em uma reserva de capacidade, mas não fornece recursos dedicados. Durante os horários de uma demanda extremamente alta, todas as suas MRUs podem não iniciar o processamento imediatamente.

Uma tarefa é uma operação individual de trabalho em um ativo, por exemplo, codificação de streaming adaptável. Quando você envia um trabalho, os serviços de mídia cuidam da interrupção do trabalho em operações individuais (ou seja, tarefas) que serão associadas a MRUs separadas.

## <a name="choosing-between-different-reserved-unit-types"></a>Escolha entre tipos diferentes de unidade reservada

A tabela a seguir o ajudará a tomar uma decisão ao escolher entre diferentes velocidades de codificação.  Ele mostra a duração da codificação para um vídeo de 8 minutos, 1080p, dependendo do MRU usado.

|Tipo de RU|Cenário|Exemplo de resultados para o 7 min 1080p vídeo |
|---|---|---|
| **S1**|Codificação de taxa de bits única. <br/>Arquivos com resoluções SD ou inferiores, não sensível ao tempo, de baixo custo.|A codificação para o arquivo MP4 de resolução SD de taxa de bits única usando "H264 taxa de bits única SD 16x9" leva cerca de 7 minutos.|
| **S2**|Codificação de taxa de bits única e de taxa de bits múltipla.<br/>Uso normal para codificação SD e HD.|A codificação com a predefinição "H264 taxa de bits única 720p" leva cerca de 6 minutos.<br/><br/>A codificação com a predefinição "H264 taxas de bits múltiplas 720p" leva cerca de 12 minutos.|
| **S3**|Codificação de taxa de bits única e de taxa de bits múltipla.<br/>Vídeos com resolução Full HD e 4K. Codificação urgente com retorno mais rápido.|A codificação com a predefinição "H264 taxa de bits única 1080p" leva aproximadamente 3 minutos.<br/><br/>A codificação com a predefinição "H264 Taxas de Bits Múltiplas 1080p" leva aproximadamente oito minutos.|

> [!NOTE]
> Se você não provisionar MRU para sua conta, suas tarefas de mídia serão processadas com o desempenho de um MRU S1 e as tarefas serão selecionadas sequencialmente. Nenhuma capacidade de processamento é reservada para que o tempo de espera entre a conclusão de uma tarefa e a próxima iniciando dependerá da disponibilidade dos recursos no sistema.

## <a name="considerations"></a>Considerações

* Para trabalhos de análise de áudio e análise de vídeo disparados pelos serviços de mídia V3 ou Video Indexer, é altamente recomendável provisionar a conta com dez unidades S3. Se você precisar de mais de 10 MRUs do S3, abra um ticket de suporte usando o [Portal do Microsoft Azure](https://portal.azure.com/).
* Para tarefas de codificação que não têm MRUs, não há nenhum limite superior ao tempo que suas tarefas podem gastar no estado enfileirado e, no máximo, apenas uma tarefa será executada de cada vez.

## <a name="billing"></a>Cobrança

Você é cobrado com base em quantos minutos as unidades reservadas de mídia são provisionadas em sua conta, independentemente de haver algum trabalho em execução ou não. Para obter uma explicação detalhada, consulte a seção Perguntas frequentes da página [Preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="next-step"></a>Próxima etapa
[Dimensionar unidades reservadas de mídia com a CLI](media-reserved-units-cli-how-to.md) 
 [Analisar vídeos](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Confira também

* [Cotas e limites](limits-quotas-constraints.md)
