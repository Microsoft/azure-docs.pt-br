---
title: 'Início Rápido: Portal da Web do Assistente de Métricas'
titleSuffix: Azure Cognitive Services
description: Saiba como começar a usar o portal da Web do Assistente de Métricas.
services: cognitive-services
author: mrbullwinkle
ms.author: mbullwin
manager: nitinme
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.custom:
- mode-portal
ms.openlocfilehash: aaeaf92df140019db32c326199c9614fd8f5ae37
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531805"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>Início Rápido: Monitore sua primeira métrica usando o portal da Web

Quando provisiona uma instância do Assistente de Métricas, você pode usar as APIs e o workspace baseado na Web para trabalhar com o serviço. O workspace baseado na Web é uma forma simples de começar a usar o serviço rapidamente. Ele também proporciona uma forma visual de definir configurações, personalizar seu modelo e executar a análise da causa raiz. 

* Integre os dados da métrica
* Veja suas métricas e visualizações
* Ajuste as configurações de detecção
* Explore insights de diagnóstico
* Crie e assine alertas de anomalias

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Depois que tiver sua assinatura do Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Criar um recurso do Assistente de Métricas"  target="_blank"> crie um recurso do Assistente de Métricas </a> no portal do Azure para implantar sua instância do Assistente de Métricas.  

    
> [!TIP]
> * Poderá levar de 10 a 30 minutos para que o recurso do Assistente de Métricas seja implantado. Clique em **Ir para o recurso** quando ele for implantado com êxito.
> * Se quiser usar a API REST para interagir com o serviço, você precisará da chave e do ponto de extremidade do recurso criado. É possível encontrá-los na guia **Chaves e pontos de extremidade** no recurso.

Este documento usa um Banco de Dados SQL como exemplo para criar seu primeiro monitor.

## <a name="sign-in-to-your-workspace"></a>Entrar no seu workspace

Depois que o recurso for criado, entre no [portal do Assistente de Métricas](https://go.microsoft.com/fwlink/?linkid=2143774). Selecione seu workspace para começar a monitorar as métricas. 
 
No momento, é possível criar um recurso do Assistente de Métricas em cada região disponível. Você pode alternar os workspaces no portal do Assistente de Métricas a qualquer momento.


## <a name="onboard-time-series-data"></a>Integrar dados de série temporal

O Assistente de Métricas fornece conectores para diferentes fontes de dados, como o Banco de Dados SQL, o Azure Data Explorer e o Armazenamento de Tabelas do Azure. As etapas para conectar dados são semelhantes para conectores diferentes, embora alguns parâmetros de configuração possam variar. Confira em [conectar dados de fontes diferentes](../data-feeds-from-different-sources.md) os parâmetros necessários para fontes de dados específicas.

Este início rápido usa um Banco de Dados SQL como exemplo. Para ingerir seus próprios dados, siga as mesmas etapas.

Para começar, entre em seu workspace do Assistente de Métricas usando sua conta do Active Directory. Na página de aterrissagem, selecione o **Diretório**, a **Assinatura** e o **Workspace** recém-criado e clique em **Introdução**. Após a página principal da carga de trabalho ser carregada, selecione **Adicionar feed de dados** no menu à esquerda.

### <a name="data-schema-requirements-and-configuration"></a>Requisitos e configuração do esquema de dados

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings"></a>Definir configurações de conexão

> [!TIP]
> Confira em [como adicionar feeds de dados](../how-tos/onboard-your-data.md) os detalhes sobre os parâmetros disponíveis.

Adicione o feed de dados conectando-se à sua fonte de dados de série temporal. Comece selecionando os seguintes parâmetros:

* **Tipo de fonte**: o tipo de fonte de dados em que os dados de série temporal ficam armazenados.
* **Granularidade**: o intervalo entre pontos de dados consecutivos nos dados de série temporal, por exemplo, Anual, Mensal, Diário. O menor intervalo com suporte da personalização é 60 segundos.
* **Ingerir dados desde (UTC)** : a hora de início do primeiro carimbo de data/hora a ser ingerido. 


Depois, especifique a **Cadeia de conexão** com as credenciais de sua fonte de dados e uma **Consulta** personalizada. A consulta é usada para especificar os dados a serem ingeridos e convertidos no esquema necessário.

[!INCLUDE [query requirements](../includes/query-requirements.md)]

:::image type="content" source="../media/connection-settings.png" alt-text="Configurações de conexão" lightbox="../media/connection-settings.png":::


### <a name="verify-the-connection-and-load-the-data-schema"></a>Verificar a conexão e carregar o esquema de dados

Após a criação da cadeia de conexão e da cadeia de caracteres de consulta, selecione **Verificar e obter esquema** para verificar a conexão e executar a consulta para obter o esquema de dados da fonte de dados. Normalmente, são necessários alguns segundos dependendo da conexão com a fonte de dados. Se ocorrer um erro nesta etapa, confirme se:

1. A cadeia de conexão e a consulta estão corretas.
2. Sua instância do Assistente de Métricas poderá se conectar à fonte de dados caso haja configurações de firewall.

### <a name="schema-configuration"></a>Configuração de esquema

Quando o esquema de dados for carregado e mostrado conforme abaixo, selecione os campos apropriados.


|Seleção  |Descrição  |Observações  |
|---------|---------|---------|
|**Timestamp**     | O carimbo de data/hora de um ponto de dados. Se omitido, o Assistente de Métricas usará o carimbo de data/hora de quando o ponto de dados for ingerido. Para cada feed de dados, você pode especificar no máximo uma coluna como carimbo de data/hora.        | Opcional. Deve ser especificado com, no máximo, uma coluna.       |
|**Medida**     |  Os valores numéricos no feed de dados. Para cada feed de dados, você pode especificar várias medidas, mas pelo menos uma coluna deve ser selecionada como medida.        | Deve ser especificado com, pelo menos, uma coluna.        |
|**Dimensão**     | Valores categóricos. Uma combinação de diferentes valores identifica uma série temporal de dimensão única em particular, por exemplo: país/região, idioma, locatário. Você pode selecionar um número arbitrário de colunas como dimensões ou pode não selecionar nenhuma. Observação: se for selecionar como dimensão uma coluna que não seja de cadeia de caracteres, tenha cuidado com a explosão da dimensão. | Opcional.        |
|**Ignorar**     | Ignorar a coluna selecionada.        |         |


:::image type="content" source="../media/schema-configuration.png" alt-text="Configuração de esquema" lightbox="../media/schema-configuration.png":::

### <a name="automatic-roll-up-settings"></a>Configurações de acúmulo automático

> [!IMPORTANT]
> Se você quiser habilitar a **análise de causa raiz** e outras funcionalidades de diagnóstico, a “configuração acúmulo automático” precisará ser definida. Após habilitadas, as configurações de acúmulo automático não podem ser alteradas.

O Assistente de Métricas pode executar automaticamente a agregação (SUM/MAX/MIN...) de cada dimensão durante a ingestão e, em seguida, criar uma hierarquia que será usada na análise de causa raiz e em outros recursos de diagnóstico. Consulte [Configurações de acúmulo automático](../how-tos/onboard-your-data.md#automatic-roll-up-settings) para obter mais detalhes.

Dê um nome personalizado para o feed de dados, que será exibido no workspace. Clique em **Enviar**. 

## <a name="tune-detection-configuration"></a>Ajustar a configuração de detecção

Depois que o feed de dados for adicionado, o Assistente de Métricas tentará ingerir dados de métricas começando na data de início especificada. Levará algum tempo para que os dados sejam totalmente ingeridos, e você poderá exibir o status da ingestão clicando em **Progresso da ingestão** na parte superior da página do feed de dados. Se os dados forem ingeridos, o Assistente de Métricas aplicará a detecção e continuará monitorando a fonte em busca de novos dados.

Quando a detecção for aplicada, clique em uma das métricas listadas no feed de dados para localizar a **Página de detalhes da métrica** para: 
- Exibir visualizações de todas as fatias de série temporal nessa métrica
- Atualizar a configuração de detecção para chegar aos resultados esperados
- Configurar notificações de anomalias detectadas

:::image type="content" source="../media/metric-details.png" alt-text="Detalhes da métrica" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>Exibir os insights de diagnóstico

Após o ajuste da configuração de detecção, as anomalias encontradas devem refletir anomalias reais em seus dados. O Assistente de Métricas faz a análise de métricas multidimensionais, como clustering de anomalias, correlação de incidentes e análise de causa raiz. Use esses recursos para analisar e diagnosticar incidentes em seus dados.

Para exibir insights de diagnóstico, clique nos pontos vermelhos nas visualizações de série temporal, que representam as anomalias detectadas. Aparecerá uma janela com um link para a página de análise de incidentes. 

:::image type="content" source="../media/incident-link.png" alt-text="Link do incidente" lightbox="../media/incident-link.png":::

Após clicar no link, você será dinamizado para a página de análise de incidentes, que analisa a anomalia correspondente e apresenta diversos insights de diagnóstico. Na parte superior, haverá estatísticas sobre o incidente, como a **Gravidade**, as **Anomalias envolvidas** e a **Hora de início** e a **Hora de término** do impacto. 

Em seguida, você verá a anomalia ancestral do incidente, bem como um aviso automatizado sobre a causa raiz. Esse aviso automatizado sobre a causa raiz é gerado pela análise da árvore de incidentes de todas as anomalias relacionadas, incluindo: desvio, distribuição e contribuição para as anomalias pai. 

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Diagnóstico de incidente" lightbox="../media/incident-diagnostic.png":::

Com base nesses dados, você já pode ter uma visão clara do que está acontecendo e do impacto do incidente, bem como da causa raiz mais provável. Dessa forma, é possível agir imediatamente para resolver o incidente o quanto antes. 

No entanto, você também pode percorrer de maneira dinâmica mais insights de diagnóstico tirando proveito de recursos adicionais para analisar detalhadamente as anomalias por dimensão, exibir anomalias semelhantes e fazer comparações entre métricas. Saiba mais em [Como diagnosticar um incidente](../how-tos/diagnose-incident.md). 

## <a name="get-notified-when-new-anomalies-are-found"></a>Receber notificações quando novas anomalias forem encontradas

Se quiser receber um alerta quando for detectada uma anomalia em seus dados, você poderá criar uma assinatura de uma ou mais de suas métricas. O Assistente de Métricas usa ganchos para enviar alertas. Três tipos de ganchos têm suporte: gancho de email, web hook e Azure DevOps. Usaremos o web hook como exemplo. 

### <a name="create-a-web-hook"></a>Criar um web hook

Um web hook é o ponto de entrada para obter anomalias observadas de maneira programática pelo serviço do Assistente de Métricas, que chama uma API fornecida pelo usuário quando um alerta é disparado. Para obter detalhes sobre como criar um gancho, confira a seção **Criar um gancho** em [Como: Configurar alertas e obter notificações usando um gancho](../how-tos/alerts.md#create-a-hook). 

### <a name="configure-alert-settings"></a>Definição de configurações de alerta

Após a criação do gancho, uma configuração de alerta determina como e quais notificações de alerta devem ser enviadas. Você pode definir várias configurações de alerta para cada métrica. Duas configurações importantes são **Alerta para**, que especifica as anomalias a serem incluídas, e **Filtrar opções de anomalias**, que define quais anomalias incluir no alerta. Confira a seção **Adicionar ou editar configurações de alerta** em [Como: Configurar alertas e obter notificações usando um web hook](../how-tos/alerts.md#add-or-edit-alert-settings) para obter mais detalhes.


## <a name="next-steps"></a>Próximas etapas

- [Integrar seus feeds de dados](../how-tos/onboard-your-data.md)
    - [Gerenciar feeds de dados](../how-tos/manage-data-feeds.md)
    - [Configurações para diferentes fontes de dados](../data-feeds-from-different-sources.md)
- [Usar a API REST ou bibliotecas de clientes](./rest-api-and-client-library.md)
- [Configurar métricas e ajustar a configuração de detecção](../how-tos/configure-metrics.md)
