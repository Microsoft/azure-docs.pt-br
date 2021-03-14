---
title: Reprovação de métricas de disco no portal do Azure | Microsoft Docs
description: Saiba quais métricas de disco foram preteridas e como atualizar seus alertas de métrica para usar novas métricas.
services: azure-monitor
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: 607ccacbfff86decaecaad0a869c8c0fe7500680
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033394"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Reprovação de métricas de disco no portal do Azure

As métricas preteridas relacionadas ao disco serão removidas em breve do portal do Azure. Uma nova versão de cada métrica preterida está disponível para uso. Este artigo mostra quais métricas são novas e como atualizar seus alertas de métrica para usá-las.

## <a name="list-of-new-metrics"></a>Lista de novas métricas

Esta tabela mapeia cada métrica preterida para a nova métrica correspondente. 

|Métrica preterida|Nova métrica (substituição)|
|----|----|
|QD do Disco de Dados (Preterido)|Profundidade da Fila de Disco de Dados (Versão prévia)|
|Bytes de Leitura do Disco de Dados/s (Preterido)|Bytes de Leitura do Disco de Dados/s (Visualização)|
|Operações de Leitura do Disco de Dados/s (Preterido)|Operações de Leitura do Disco de Dados/s (Visualização)|
|Bytes de Gravação do Disco de Dados/s (Preterido)|Bytes de Gravação do Disco de Dados/s (Visualização)|
|Operações de Gravação do Disco de Dados/s (Preterido)|Operações de Gravação do Disco de Dados/s (Visualização)|
|Sistema operacional QD (preterido)|Profundidade da fila do so (visualização)|
|Bytes de leitura do sistema operacional/s (preterido)|Bytes de leitura do sistema operacional/s (visualização)|
|Operações de leitura do sistema operacional/s (preterido)|Operações de leitura do sistema operacional/s (visualização)|
|Bytes de gravação do sistema operacional/s (preterido)|Bytes de gravação do sistema operacional/s (visualização)|
|Operações de gravação do sistema operacional/s (preterido)|Operações de gravação do sistema operacional/s (visualização)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>Migrar métricas em seus alertas de métricas

Atualize seus alertas de métrica para usar novas métricas.

1. Na portal do Azure, procure **alertas**. Em seguida, na seção **Serviços** , escolha **alertas**.

   > [!div class="mx-imgBorder"]
   > ![Serviço de alerta](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. Na página **alertas** , escolha o botão **gerenciar regras de alerta** . 

   > [!div class="mx-imgBorder"]
   > ![Gerenciar regras de alerta](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. Na lista suspensa **grupo de recursos** , marque a caixa de seleção **máquinas virtuais** e, na lista suspensa tipo de **sinal** , marque a caixa de seleção **métricas** . 

   > [!div class="mx-imgBorder"]
   > ![Filtrar alertas](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. Na lista de métricas, identifique as condições relacionadas aos discos. Clique no nome da regra. 

   O nome aparece como um hiperlink na coluna **nome** da tabela.

   > [!div class="mx-imgBorder"]
   > ![Localizar condições de disco](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. Na seção **condições** da página de **Gerenciamento de regras** , clique na condição do alerta. 

   A condição aparece como um hiperlink.  

   > [!div class="mx-imgBorder"]
   > ![Ajustar condições](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   A página **Configurar lógica de sinal** é exibida e as configurações da condição aparecem na seção **lógica de alerta** dessa página.

6. Faça um registro dessas configurações, pois elas desaparecerão quando você remover a métrica preterida.

   > [!div class="mx-imgBorder"]
   > ![Regras de condição](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > Considere capturar essas configurações em uma captura de tela ou em um arquivo de texto. 

7. Clique no link **voltar à seleção de sinal** .

   > [!div class="mx-imgBorder"]
   > ![Voltar à seleção de sinal](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. Na página **Configurar lógica de sinal** , escolha a métrica de substituição apropriada (nova métrica). Use a [tabela](#update-metrics) que aparece anteriormente neste artigo para identificar o nome da nova métrica.

   > [!TIP] 
   > Comece digitando na barra de pesquisa para restringir a lista de nomes de métrica. 

   > [!div class="mx-imgBorder"]
   > ![Escolher nova métrica](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. Escolha o botão **concluído** . 

   > [!div class="mx-imgBorder"]
   > ![Definir nova métrica](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. Confirme suas alterações escolhendo o botão **salvar** . 

    > [!div class="mx-imgBorder"]
    > ![Salvar nova métrica](./media/portal-disk-metrics-deprecation/save-new-metric.png)






