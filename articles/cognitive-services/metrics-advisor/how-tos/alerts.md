---
title: Configurar alertas do assistente de métricas
titleSuffix: Azure Cognitive Services
description: Como configurar seus alertas do supervisor de métricas usando ganchos para email, Web e Azure DevOps.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 30d8fdf99da7a4854db0985bed6256ecd6f7a366
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420913"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>Como: configurar alertas e obter notificações usando um gancho

Depois que uma anomalia é detectada pelo assistente de métricas, uma notificação de alerta será disparada com base nas configurações de alerta, usando um gancho. Uma configuração de alerta pode ser usada com várias configurações de detecção, vários parâmetros estão disponíveis para personalizar sua regra de alerta.

## <a name="create-a-hook"></a>Criar um gancho

O revisor de métricas dá suporte a três tipos diferentes de ganchos: gancho de email, gancho da Web e DevOps do Azure. Você pode escolher aquele que funciona para seu cenário específico.       

### <a name="email-hook"></a>Gancho de email

> [!Note]
> Os administradores de recursos do revisor de métricas precisam definir as configurações de email e inserir informações relacionadas ao SMTP no assistente de métricas antes que os alertas de anomalias possam ser enviados. O administrador do grupo de recursos ou de assinatura precisa atribuir pelo menos uma função de *administrador do Orientador de métricas de serviços cognitivas* na guia controle de acesso do recurso do assistente de métricas. [Saiba mais sobre a configuração de configurações de email](../faq.md#how-to-set-up-email-settings-and-enable-alerting-by-email).

Para criar um gancho de email, os seguintes parâmetros estão disponíveis: 

Um cabo de email é o canal para que os alertas de anomalias sejam enviados aos endereços de email especificados na seção **email para** . Dois tipos de emails de alerta serão enviados: *feed de dados não disponível* alertas e *relatórios de incidentes* que contêm uma ou várias anomalias. 

|Parâmetro |Descrição  |
|---------|---------|
| Name | Nome do gancho de email |
| Enviar email para| Endereços de email que enviarão alerta para|
| Link externo | Campo opcional que habilita um redirecionamento personalizado, como para notas de solução de problemas. |
| Título de alerta de anomalias personalizado | O modelo de título dá suporte a,,,,,,, `${severity}` `${alertSettingName}` `${datafeedName}` `${metricName}` `${detectConfigName}` `${timestamp}` `${topDimension}` `${incidentCount}` , `${anomalyCount}`

Depois de clicar em **OK** , um gancho de email será criado. Você pode usá-lo em qualquer configuração de alerta para receber alertas de anomalias. 

### <a name="web-hook"></a>Gancho da Web

> [!Note]
> * Use o método **post** Request.
> * O corpo da solicitação colocará ser semelhante a:  
    `{"timestamp":"2019-09-11T00:00:00Z","alertSettingGuid":"49635104-1234-4c1c-b94a-744fc920a9eb"}`
> * Quando um gancho da Web é criado ou modificado, a API será chamada como um teste com um corpo de solicitação vazio. Sua API precisa retornar um código HTTP 200.

Um gancho da Web é o ponto de entrada para todas as informações disponíveis no serviço do revisor de métricas e chama uma API fornecida pelo usuário quando um alerta é disparado. Todos os alertas podem ser enviados por meio de um gancho da Web.

Para criar um gancho da Web, será necessário adicionar as seguintes informações:

|Parâmetro |Descrição  |
|---------|---------|
|Ponto de extremidade     | O endereço da API a ser chamado quando um alerta é disparado.        |
|Nome de usuário + senha | Para autenticação no endereço de API. Deixe este preto se a autenticação não for necessária.         |
|Cabeçalho     | Cabeçalhos personalizados na chamada à API.        |

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="janela de criação de gancho da Web.":::

Quando uma notificação é enviada por um gancho da Web, você pode usar as seguintes APIs para obter detalhes do alerta. Defina o *carimbo de data e hora* e *alertSettingGuid* em seu serviço de API, que está sendo enviado para o, em seguida, use as seguintes consultas: 
- `query_alert_result_anomalies`
- `query_alert_result_incidents`

### <a name="azure-devops"></a>Azure DevOps

O assistente de métricas também dá suporte à criação automática de um item de trabalho no Azure DevOps para acompanhar problemas/bugs quando qualquer anomalia detectada. Todos os alertas podem ser enviados por meio de ganchos do DevOps do Azure.

Para criar um gancho de DevOps do Azure, você precisará adicionar as informações a seguir

|Parâmetro |Descrição  |
|---------|---------|
| Name | Um nome para o gancho |
| Organização | A organização à qual seu DevOps pertence |
| Project | O projeto específico no DevOps. |
| Token de acesso |  Um token para autenticação no DevOps. | 

> [!Note]
> Você precisa conceder permissões de gravação se desejar que o assistente de métricas crie itens de trabalho com base em alertas de anomalias. Depois de criar ganchos, você pode usá-las em qualquer uma das suas configurações de alerta. Gerencie seus ganchos na página **configurações do gancho** .

## <a name="add-or-edit-alert-settings"></a>Adicionar ou editar configurações de alerta

Vá para a página de detalhes de métricas para localizar a seção **configurações de alerta** , no canto inferior esquerdo da página de detalhes de métricas. Ele lista todas as configurações de alerta que se aplicam à configuração de detecção selecionada. Quando uma nova configuração de detecção é criada, não há nenhuma configuração de alerta e nenhum alerta será enviado.  
Você pode usar os ícones **Adicionar** , **Editar** e **excluir** para modificar as configurações de alerta.

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="Item de menu configurações de alerta.":::

Clique nos botões **Adicionar** ou **Editar** para obter uma janela para adicionar ou editar suas configurações de alerta.

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="Adicionar ou editar configurações de alerta":::

**Nome da configuração de alerta** : o nome dessa configuração de alerta. Ele será exibido no título do email de alerta.

**Ganchos** : a lista de ganchos para os quais enviar alertas.

A seção marcada na captura de tela acima são as configurações para uma detecção de configuração. Você pode definir configurações de alerta diferentes para configurações de detecção diferentes. Escolha a configuração de destino usando a terceira lista suspensa nesta janela. 

### <a name="filter-settings"></a>Configurações de filtro 

Veja a seguir as configurações de filtro para uma configuração de detecção.

O **alerta do** tem 4 opções para a filtragem de anomalias:

* **Anomalias em todas as séries** : todas as anomalias serão incluídas no alerta.         
* **Anomalias no grupo de séries** : filtre a série por valores de dimensão. Defina valores específicos para algumas dimensões. As anomalias só serão incluídas no alerta quando a série corresponder ao valor especificado.       
* **Anomalias na série favorita** : somente as séries marcadas como favoritas serão incluídas no alerta.        |
* **Anomalias na parte superior N de todas as séries** : esse filtro é para o caso em que você só se preocupa com a série cujo valor está nos N mais importantes. Veremos alguns carimbos de data/hora e verificamos se o valor da série nesse carimbo de data/hora estão nos N maiores. Se a contagem "em Top n" for maior que o número especificado, a anomalia será incluída em um alerta.        |

**Opções de anomalias de filtro** é um filtro adicional com as seguintes opções:

- **severidade** : a anomalia só será incluída quando a severidade de anomalia estiver dentro do intervalo especificado.
- **Adiar** : parar alertas temporariamente para anomalias nos próximos N pontos (ponto), quando disparado em um alerta.
    - **tipo de adiamento** : quando definido como **série** , uma anomalia disparada só Adiará sua série. Para **métrica** , uma anomalia disparada irá adiar todas as séries nessa métrica.
    - **número de adiamento** : o número de pontos (ponto final) para adiar.
    - **Redefinir para não sucessivo** : quando selecionado, uma anomalia disparada só adiará as próximas n anomalias sucessivas. Se um dos seguintes pontos de dados não for uma anomalia, o adiamento será redefinido desse ponto; Quando não selecionado, uma anomalia disparada irá adiar os próximos n pontos (ponto), mesmo que os pontos de dados sucessivos não sejam anomalias.
- **valor** (opcional): filtrar por valor. Somente os valores de ponto que atendem à condição, anomalias serão incluídos. Se você usar o valor correspondente de outra métrica, os nomes de dimensão das duas métricas devem ser consistentes.

Anomalias não filtradas serão enviadas em um alerta.

### <a name="add-cross-metric-settings"></a>Adicionar configurações de métrica cruzada

Clique em **+ Adicionar configurações de métrica cruzada** na página Configurações de alerta para adicionar outra seção.

O seletor de **operador** é a relação lógica de cada seção, para determinar se eles enviam um alerta.


|Operador  |Descrição  |
|---------|---------|
|AND     | Somente envie um alerta se uma série corresponder a cada seção de alerta e todos os pontos de dados forem anomalias. Se as métricas tiverem nomes de dimensão diferentes, um alerta nunca será disparado.         |
|OU     | Envie o alerta se pelo menos uma seção contiver anomalias.         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="Operador para a seção de várias configurações de alerta":::

## <a name="next-steps"></a>Próximas etapas

- [Ajustar a detecção de anomalias usando os comentários](anomaly-feedback.md)
- [Diagnosticar um incidente](diagnose-incident.md).
- [Configurar métricas e ajustar a configuração de detecção](configure-metrics.md)