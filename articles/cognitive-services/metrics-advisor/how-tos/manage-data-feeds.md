---
title: Gerenciar feeds de dados no assistente de métricas
titleSuffix: Azure Cognitive Services
description: Saiba como gerenciar feeds de dados que você adicionou ao assistente de métricas.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: mbullwin
ms.openlocfilehash: fb6eaf44967732d3a41ea92b0896540a40f694e3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184715"
---
# <a name="how-to-manage-your-data-feeds"></a>Como gerenciar seus feeds de dados

Saiba como gerenciar seus feeds de dados integrados no assistente de métricas. Este artigo orienta você pelo gerenciamento de feeds de dados no assistente de métricas.

## <a name="edit-a-data-feed"></a>Editar um feed de dados

> [!NOTE]
> Os detalhes a seguir não podem ser alterados após a criação de um feed de dados. 
> * ID do feed de dados
> * Hora de criação
> * Dimensão
> * Tipo de Fonte
> * Granularidade

Somente o administrador de um feed de dados tem permissão para fazer alterações nele. 

Para pausar ou reativar um feed de dados:

1. Na página lista de feeds de dados, clique na operação que você deseja executar no feed de dados.

2. Na página detalhes do feed de dados, clique no botão de opção **status** .

Para excluir um feed de dados: 

1. Na página lista de feeds de dados, clique em **excluir** no feed de dados.

2. Na página detalhes do feed de dados, clique em **excluir**.

Ao alterar a hora de início, você precisa verificar o esquema novamente. Você pode alterá-lo usando **Editar parâmetros**.

##  <a name="backfill-your-data-feed"></a>Aterrar seu feed de dados

Selecione o botão de  **aterramento** para disparar uma ingestão imediata em um carimbo de data/hora, para corrigir uma ingestão com falha ou substituir os dados existentes.
- A hora de início é inclusiva.
- A hora de término é exclusiva.
- A detecção de anomalias é disparada novamente no intervalo selecionado.

:::image type="content" source="../media/datafeeds/backfill-datafeed.png" alt-text="Feed de dados de aterramento":::

## <a name="manage-permission-of-a-data-feed"></a>Gerenciar a permissão de um feed de dados

O acesso ao espaço de trabalho é controlado pelo recurso do supervisor de métricas, que usa Azure Active Directory para autenticação. Outra camada de controle de permissão é aplicada aos dados de métrica.

O assistente de métricas permite que você conceda permissões a diferentes grupos de pessoas em diferentes feeds de dados. Existem dois tipos de funções: 

- Administrador: quem tem permissões completas para gerenciar um feed de dados, incluindo modificar e excluir.
- Visualizador: quem tem acesso a uma exibição somente leitura do feed de dados.
 

## <a name="advanced-settings"></a>Configurações avançadas

Há várias configurações avançadas opcionais ao criar um novo feed de dados, elas podem ser modificadas na página de detalhes do feed de dados.

### <a name="ingestion-options"></a>Opções de ingestão

* **Deslocamento de tempo de ingestão**: por padrão, os dados são ingeridos de acordo com a granularidade especificada. Por exemplo, uma métrica com um carimbo de data/hora *diário* será ingerida um dia após seu carimbo de data/hora. Você pode usar o deslocamento para atrasar o tempo de ingestão com um número *positivo* ou avançar com um número *negativo* .

* **Simultaneidade máxima**: defina esse parâmetro se sua fonte de dados der suporte à simultaneidade limitada. Caso contrário, deixe na configuração padrão.

* **Parar repetição após**: se a ingestão de dados falhar, ele tentará automaticamente dentro de um período. O início do período é a hora em que ocorreu a primeira ingestão de dados. O comprimento do período é definido de acordo com a granularidade. Se você deixar o valor padrão (-1), o valor será determinado de acordo com a granularidade como abaixo.
    
    | Granularidade       | Parar repetição após           |
    | :------------ | :--------------- |
    | Diário, personalizado (>= 1 dia), semanal, mensal, anual     | 7 dias          |
    | Por hora, personalizado (< 1 dia)       | 72 horas |

* **Intervalo mín**. de repetição: você pode especificar o intervalo mínimo ao tentar extrair dados da origem. Se você deixar o valor padrão (-1), o intervalo de repetição será determinado de acordo com a granularidade como abaixo.
    
    | Granularidade       | Intervalo de repetição mínimo           |
    | :------------ | :--------------- |
    | Diário, personalizado (>= 1 dia), semanalmente, mensalmente     | 30 minutos          |
    | Por hora, personalizado (< 1 dia)      | 10 minutos |
    | Anual | 1 dia          |
 
### <a name="fill-gap-when-detecting"></a>Lacuna de preenchimento ao detectar: 

> [!NOTE]
> Essa configuração não afetará sua fonte de dados e não afetará os gráficos de dados exibidos no Portal. O preenchimento automático ocorre apenas durante a detecção de anomalias.

Algumas séries temporais não são contínuas. Quando houver pontos de dados ausentes, o Orientador de métricas usará o valor especificado para preenchê-los antes da detecção de anomalias para obter maior precisão.
As opções são: 

* Usando o valor do ponto de dados real anterior. Isso é usado por padrão.
* Usando um valor específico.

### <a name="action-link-template"></a>Modelo de link de ação: 

Os modelos de link de ação são usados para predefinir URLs http acionáveis, que consistem nos espaços reservados `%datafeed` , `%metric` ,, `%timestamp` `%detect_config` e `%tagset` . Você pode usar o modelo para redirecionar de uma anomalia ou de um incidente para uma URL específica para fazer uma busca detalhada.

:::image type="content" source="../media/action-link-template.png" alt-text="Modelo de link de ação" lightbox="../media/action-link-template.png":::

Depois de preencher o link de ação, clique em **ir para o link de ação** na opção ação da lista de incidentes e no menu do botão direito do mouse da árvore de incidentes. Substitua os espaços reservados no modelo de link de ação pelos valores correspondentes da anomalia ou do incidente.

| Espaço reservado | Exemplos | Comentário |
| ---------- | -------- | ------- |
| `%datafeed` | - | ID do feed de dados |
| `%metric` | - | ID da métrica |
| `%detect_config` | - | Detectar ID de configuração |
| `%timestamp` | - | Timestamp de uma hora de anomalia ou de término de um incidente persistente |
| `%tagset` | `%tagset`, <br> `[%tagset.get("Dim1")]`, <br> `[ %tagset.get("Dim1", "filterVal")]` | Valores de dimensão de uma anomalia ou de uma anomalia superior de um incidente.   <br> O `filterVal` é usado para filtrar valores correspondentes dentro dos colchetes.   |

Disso

* Se o modelo de link de ação for `https://action-link/metric/%metric?detectConfigId=%detect_config` ,
  * O link de ação irá `https://action-link/metric/1234?detectConfigId=2345` para anomalias ou incidentes em métrica `1234` e detectar configuração `2345` .

* Se o modelo de link de ação for `https://action-link?[Dim1=%tagset.get('Dim1','')&][Dim2=%tagset.get('Dim2','')]` , 
    * O link de ação seria `https://action-link?Dim1=Val1&Dim2=Val2` quando a anomalia é `{ "Dim1": "Val1", "Dim2": "Val2" }` . 
    * O link de ação seria `https://action-link?Dim2=Val2` quando a anomalia é `{ "Dim1": "", "Dim2": "Val2" } ` , uma vez que `[Dim1=***&]` é ignorado para a cadeia de caracteres vazia do valor da dimensão. 

* Se o modelo de link de ação for `https://action-link?filter=[Name/Dim1 eq '%tagset.get('Dim1','')' and ][Name/Dim2 eq '%tagset.get('Dim2','')']` , 
    * O link de ação seria `https://action-link?filter=Name/Dim1 eq 'Val1' and Name/Dim2 eq 'Val2'` quando a anomalia é `{ "Dim1": "Val1", "Dim2": "Val2" }` , 
    * O link da ação seria `https://action-link?filter=Name/Dim2 eq 'Val2'` quando a anomalia é `{ "Dim1": "", "Dim2": "Val2" }` porque `[Name/Dim1 eq '***' and ]` é ignorada para a cadeia de caracteres vazia do valor da dimensão. 
   
### <a name="data-feed-not-available-alert-settings"></a>Configurações de alerta de "feed de dados não disponível"

Um feed de dados será considerado como não disponível se nenhum dado for ingerido da origem dentro do período de carência especificado desde o momento em que o feed de dados iniciar a ingestão. Um alerta é disparado nesse caso.

Para configurar um alerta, você precisa [criar um gancho](alerts.md#create-a-hook) primeiro. Os alertas serão enviados por meio do gancho configurado.

* **Período de carência**: a configuração do período de carência é usada para determinar quando enviar um alerta se nenhum ponto de dados for ingerido. O ponto de referência é o tempo da primeira ingestão. Se uma ingestão falhar, o Orientador de métricas continuará tentando em um intervalo regular especificado pela granularidade. Se ele continuar a falhar após o período de carência, um alerta será enviado.

* **Adiar automaticamente**: quando essa opção é definida como zero, cada carimbo de data/hora com *não disponível* dispara um alerta. Quando uma configuração diferente de zero é especificada, carimbos de data/hora contínuos após o primeiro carimbo de data/hora com *não disponível* não são disparados de acordo com a configuração especificada.

## <a name="next-steps"></a>Próximas etapas
- [Configurar métricas e ajustar a configuração de detecção](configure-metrics.md)
- [Ajustar a detecção de anomalias usando os comentários](anomaly-feedback.md)
- [Diagnosticar um incidente](diagnose-incident.md).
