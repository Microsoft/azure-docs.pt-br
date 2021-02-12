---
title: Monitore os serviços de Armazenamento do Microsoft Azure com o Azure Monitor for Storage | Microsoft Docs
description: Este artigo descreve o recurso Azure Monitor for Storage, que permite aos administradores entenderem rapidamente questões de desempenho e utilização de suas contas do Armazenamento do Azure.
ms.subservice: ''
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 05/11/2020
ms.openlocfilehash: 47799898251923b327954c764dcccb8157ba5119
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208634"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage"></a>Monitorando seu serviço de armazenamento com o Azure Monitor for Storage

O Azure Monitor for Storage fornece monitoramento abrangente das contas do Armazenamento do Azure proporcionando uma exibição unificada de desempenho, capacidade e disponibilidade dos serviços do Armazenamento. Você pode observar a capacidade de armazenamento e o desempenho de duas formas: fazendo a exibição diretamente a partir de uma conta de armazenamento ou visualizando a partir do Azure Monitor para ver grupos de contas de armazenamento. 

Este artigo o ajudará a entender a experiência que o Azure Monitor for Storage distribui para gerar conhecimento acionável sobre a integridade e o desempenho das contas do Armazenamento em escala, nos permitindo nos concentrar em hotspots e diagnosticar problemas de latência, limitação e disponibilidade.

## <a name="introduction-to-azure-monitor-for-storage"></a>Introdução ao Azure Monitor for Storage

Antes de mergulhar na experiência, você deve entender como ela apresenta e visualiza informações. Se você selecionar o recurso de Armazenamento diretamente a partir de uma conta de armazenamento ou a partir do Azure Monitor, o Azure Monitor for Storage apresentará uma experiência consistente. 

Ele distribui:

* **Perspectiva em escala** mostrando a exibição de um instantâneo da disponibilidade com base na integridade do serviço de armazenamento ou na operação de API, a utilização mostrando o número total de solicitações que o serviço de armazenamento recebe, e a latência mostrando o tempo médio que o serviço de armazenamento ou o tipo de operação de API está levando para processar solicitações. Você também pode exibir a capacidade por blob, arquivo, tabela e fila.

* **Análise detalhada** de uma conta de armazenamento específica para ajudar a diagnosticar problemas ou executar uma verificação detalhada por categoria – disponibilidade, desempenho, falhas e capacidade. A seleção de qualquer uma dessas opções fornece uma visão detalhada das métricas.  

* **Personalizável**, logo, você pode alterar que métricas deseja ver e modificar, pode definir restrições alinhadas com os seus limites e salvar sua própria pasta de trabalho. Os gráficos da pasta de trabalho podem ser fixados ao painel do Azure.  

Esse recurso não exige que você habilite nem configure nada, as métricas das contas de armazenamento são coletadas por padrão. Se não estiver familiarizado com as métricas disponíveis no Armazenamento do Azure, exiba sua descrição e definição examinando [Métricas de armazenamento do Azure](../../storage/blobs/monitor-blob-storage.md).

>[!NOTE]
>Não há nenhum encargo para acessar esse recurso e você será cobrado somente pelos recursos essenciais do Azure Monitor que você configurar ou habilitar, conforme descrito na página [Detalhes de preços de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="view-from-azure-monitor"></a>Exibir no Azure Monitor

No Azure Monitor, você pode exibir os detalhes de transação, latência e capacidade de várias contas de armazenamento de sua assinatura e ajudar a identificar problemas de desempenho e capacidade, além de falhas.

Para exibir a utilização e a disponibilidade de suas contas de armazenamento em todas as suas assinaturas, execute as etapas a seguir.

1. Entre no [portal do Azure](https://portal.azure.com).

2. Selecione **Monitor** no painel esquerdo do portal do Azure e, na seção **Insights**, selecione **Contas de Armazenamento**.

    ![Exibição de várias contas de armazenamento](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Pasta de trabalho Visão geral

Na pasta de trabalho **visão geral** da assinatura selecionada, a tabela exibe as métricas de armazenamento interativo e o estado de disponibilidade do serviço para até 5 contas de armazenamento agrupadas na assinatura. Você pode filtrar os resultados com base nas opções selecionadas nas seguintes listas suspensas:

* **Assinaturas** - somente as assinaturas que têm contas de armazenamento são listadas.  

* **Contas de armazenamento** -por padrão, 5 contas de armazenamento são previamente selecionadas. Se você selecionar várias ou todas as contas de armazenamento no seletor de escopo, até 200 contas de armazenamento serão retornadas. Por exemplo, se você tivesse um total de 573 contas de armazenamento nas três assinaturas que selecionou, somente 200 seriam exibidas. 

* **Intervalo de tempo** – por padrão, exibe as últimas quatro horas de informações com base nas seleções correspondentes feitas.

O bloco contador sob as listas suspensas acumula o número total de contas de armazenamento da assinatura e reflete quantas desse total foram selecionadas. Há codificação de cor condicional ou mapas de calor para colunas na pasta de trabalho que relatam métricas de transação ou erros. A cor mais intensa tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos. Para as colunas baseadas em erros, o valor aparece em vermelho, e para as colunas baseadas em métricas, ele aparece em azul.

A seleção de um valor nas colunas **Disponibilidade**, **Latência E2E**, **Latência do Servidor** e **Erros/tipo de erro de transação** o levará a um relatório personalizado para o tipo específico de métricas de armazenamento que corresponda à coluna selecionada para essa conta de armazenamento. Para obter mais informações sobre as pastas de trabalho para cada categoria, consulte a seção a seguir chamada [Pastas de trabalho de armazenamento detalhadas](#detailed-storage-workbooks). 

>[!NOTE]
>Para obter detalhes sobre que erros podem ser mostrados no relatório, consulte o [esquema de Tipo de Resposta](../../storage/blobs/monitor-blob-storage-reference.md#metrics-dimensions) e procure tipos de resposta como **ServerOtherError**, **ClientOtherError**, **ClientThrottlingError**. Dependendo das contas de armazenamento selecionadas, se houver mais do que três tipos de erros relatados, todos os demais erros serão representados sob a categoria **Outros**.

O limite de **Disponibilidade** padrão é:

* Aviso - 99%
* Crítico - 90%

Para definir um limite de disponibilidade com base no que você observou ou nos requisitos, examine [modificar o limite de disponibilidade](#modify-the-availability-threshold). 

### <a name="capacity-workbook"></a>Pasta de trabalho de capacidade

Selecione **Capacidade** na parte superior da página e a pasta de trabalho **Capacidade** será aberta. Ela mostra a quantidade de armazenamento total usada e a capacidade empregada por cada serviço de dados na conta para ajudar a identificar o uso excessivo ou a subutilização do armazenamento.

![Pasta de trabalho Capacidade de várias contas de armazenamento](./media/storage-insights-overview/storage-account-capacity-02.png) 

Há codificação de cor condicional ou mapas de calor para colunas na pasta de trabalho que relatam as métricas de capacidade com um valor azul. A cor mais intensa tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos.

Ao selecionar um valor em qualquer uma das colunas da pasta de trabalho, você fará a análise detalhada da pasta de trabalho **Capacidade** da conta de armazenamento. Mais informações sobre o relatório de análise detalhada são descritas na seção a seguir chamada [Pastas de trabalho de armazenamento detalhadas](#detailed-storage-workbooks). 

## <a name="view-from-a-storage-account"></a>Exibir a partir de uma conta de armazenamento

Para acessar o Azure Monitor para VMs diretamente a partir de uma conta de armazenamento:

1. No portal do Azure, selecione Contas de armazenamento.

2. Selecione uma conta de armazenamento na lista. Na seção Monitoramento, selecione Insights.

    ![Captura de tela que mostra a página da pasta de trabalho de visão geral da conta de armazenamento.](./media/storage-insights-overview/storage-account-direct-overview-01.png)

Na pasta de trabalho **Visão geral** da conta de armazenamento, são mostradas várias métricas de desempenho do armazenamento que ajudam a avaliar rapidamente o seguinte:

* Integridade do serviço de armazenamento para você ver imediatamente se um problema fora do seu controle está afetando o serviço na região em que ele foi implantado, o que é indicado na coluna **Resumo**.

* Gráficos de desempenho interativos que mostram os detalhes mais fundamentais relacionados a capacidade, disponibilidade, transações e latência do armazenamento.  

* Blocos de métricas e status que realçam a disponibilidade do serviço, a contagem total de transações para o serviço de armazenamento, a latência E2E e a latência do servidor.

A seleção do botão **Falhas**, **Desempenho** , **Disponibilidade** ou **Capacidade** abrirá a respectiva pasta de trabalho. 

![Página Visão geral da conta de armazenamento selecionada](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Pastas de trabalho de armazenamento detalhadas

Se você selecionar um valor nas colunas **Disponibilidade**, **Latência E2E**, **Latência do Servidor** e **Erros/tipos de erro de transação** da pasta de trabalho **Visão geral** das diversas contas de armazenamento ou pressionar um dos botões **Falhas**,  **Desempenho**, **Disponibilidade** ou **Capacidade** da pasta de trabalho **Visão geral** de uma conta de armazenamento específica, cada uma dessas opções distribuirá um conjunto de informações interativas relacionadas ao armazenamento personalizadas para essa categoria.  

* **Disponibilidade** abre a pasta de trabalho **Disponibilidade**. Ela mostra o estado de integridade atual do serviço de Armazenamento do Azure, uma tabela exibindo o estado de integridade disponível de cada objeto categorizado pelo serviço de dados definido na conta de armazenamento com uma linha de tendência representando o intervalo de tempo selecionado, e um gráfico de tendência de disponibilidade para cada serviço de dados da conta.  

    ![Exemplo de relatório de disponibilidade](./media/storage-insights-overview/storage-account-availability-01.png)

* **Latência E2E** e **Latência do Servidor** abrem a pasta de trabalho  **Desempenho**. Ela inclui um bloco de status de valor acumulado que mostra a latência E2E e a latência do servidor, um gráfico de desempenho da latência E2E versus a latência do servidor e uma tabela que lista a latência de chamadas bem-sucedidas por API categorizadas pelo serviço de dados definido na conta de armazenamento.

    ![Exemplo de relatório de desempenho](./media/storage-insights-overview/storage-account-performance-01.png)

* A seleção de qualquer uma das categorias de erro listadas na grade abre a pasta de trabalho **Falha**. O relatório exibe blocos de métricas de todos os outros erros do lado do cliente exceto os descritos e solicitações bem-sucedidas, erros de limitação do cliente, um gráfico de desempenho da métrica de dimensão de **Tipo de Resposta** da transação específica do atributo ClientOtherError, e duas tabelas - **Transações por nome da API** e **Transações por tipo de resposta**.

   ![Exemplo de relatório de falha](./media/storage-insights-overview/storage-account-failures-01.png)

* **Capacidade** abre a pasta de trabalho **Capacidade**. Ela mostra nos blocos e no gráfico a quantidade total de armazenamento usada para cada objeto de dados de armazenamento da conta e quantos objetos de dados estão armazenados nela.  

    ![Página Capacidade da conta de armazenamento selecionada](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Fixar e exportar

Você pode fixar qualquer uma das seções de métrica em um Painel do Azure selecionando o ícone de alfinete na parte superior direita da seção.

![Exemplo de fixação no painel na seção de métrica](./media/storage-insights-overview/workbook-pin-example.png)

As pastas de trabalho **Visão geral** ou **Capacidade** de várias assinaturas e contas de armazenamento dão suporte à exportação dos resultados no formato do Excel pela seleção do ícone de seta para baixo à direita do ícone de alfinete.

![Exemplo de exportação dos resultados em grade da pasta de trabalho](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage"></a>Personalizar o Azure Monitor for Storage

Esta seção destaca os cenários comuns de edição da pasta de trabalho para a personalização conforme as necessidades de análise de dados:

* Criar um escopo da pasta de trabalho para sempre selecionar uma assinatura ou conta de armazenamento específica
* Alterar as métricas na grade
* Alterar o limite de disponibilidade
* Alterar a renderização de cores

As personalizações são salvas em uma pasta de trabalho personalizada para evitar a substituição da configuração padrão na pasta de trabalho publicada. As pastas de trabalho são salvas em um grupo de recursos, seja na seção **Meus Relatórios**, que é particular a você, ou na seção **Relatórios Compartilhados**, que é acessível a todos com acesso ao grupo de recursos. Depois de salvar a pasta de trabalho personalizada, você precisa ir para a galeria de pastas de trabalho para iniciá-la.

![Iniciar galeria de pastas de trabalho na barra de comandos](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Especificação de uma assinatura ou conta de armazenamento

Você pode configurar as pastas de trabalho **Visão geral** ou **Capacidade** de várias assinaturas e contas de armazenamento para definir o escopo com uma assinatura ou conta de armazenamento específica a cada execução seguindo as etapas a seguir.

1. Selecione **Monitor** no portal e, em seguida, selecione **Contas de Armazenamento** no painel esquerdo.

2. Na pasta de trabalho **Visão geral**, na barra de comandos, selecione **Editar**.

3. Selecione na lista suspensa **Assinaturas** uma ou mais assinaturas que você deseja usar como padrão. Lembre-se de que a pasta de trabalho permite selecionar um total de até 10 assinaturas.  

4. Selecione na lista suspensa **Contas de Armazenamento** uma ou mais contas que você queira usar como padrão. Lembre-se de que a pasta de trabalho permite selecionar um total de até 200 contas de armazenamento. 

5. Selecione **Salvar como** na barra de comandos para salvar uma cópia da pasta de trabalho com as personalizações e, em seguida, clique em **Edição concluída** para retornar ao modo de leitura.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Modificar métricas e cores na pasta de trabalho

As pastas de trabalho predefinidas contêm dados de métrica e você pode modificar ou remover qualquer uma das visualizações e personalizá-las conforme as necessidades específicas de sua equipe.

Em nosso exemplo, estamos trabalhando com a pasta de trabalho de capacidade de várias assinaturas e contas de armazenamento para demonstrar como:

* Remover uma métrica
* Alterar a renderização de cores

Você pode executar as mesmas alterações em qualquer uma das seguintes pastas de trabalho predefinidas: **Falhas**, **Desempenho**, **Disponibilidade** e **Capacidade** .

1. Selecione **Monitor** no portal e, em seguida, selecione **Contas de Armazenamento** no painel esquerdo.

2. Selecione **Capacidade** para passar para a pasta de trabalho de capacidade e, na barra de comandos, selecione **Editar**.

    ![Selecione Editar para modificar uma pasta de trabalho](./media/storage-insights-overview/workbook-edit-workbook.png)

3. Ao lado da seção de métricas, selecione **Editar**.

    ![Selecione Editar para modificar as métricas da pasta de trabalho de capacidade](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. Vamos remover a coluna **Linha de tempo da capacidade usada pela conta**, portanto, selecione **Configurações de Coluna** na grade de métricas.

    ![Editar configurações de coluna](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. No painel **Editar configurações de coluna**, selecione na seção **Colunas** **microsoft.storage/storageaccounts-Capacity-UsedCapacity Timeline$|Account used capacity Timeline$** e, na lista suspensa **Renderizador de coluna**, selecione **Oculto**.

6. Selecione **Salvar e fechar** para confirmar sua alteração.

Agora, vamos alterar o tema de cores para que as métricas de capacidade do relatório usem verde em vez de azul.

1. Selecione **Configurações de Coluna** na grade de métricas.

2. No painel **Editar configurações de coluna** , selecione na seção **colunas** **Microsoft. Storage/storageaccounts-Capacity-UsedCapacity $ `|` Microsoft. Storage/storageaccounts/blobservices-Capacity-capacidade $ `|` Microsoft. Storage/storageaccounts/fileservices-Capacity-filecapacity $ `|` Microsoft. Storage/storageaccounts/queueservices-Capacity-QueueCapacity $ `|` Microsoft. Storage/storageaccounts/tableservices-Capacity-TableCapacity $**. Na lista suspensa **Paleta de cores**, selecione **Verde**.

3. Selecione **Salvar e fechar** para confirmar sua alteração.

4. Selecione **Salvar como** na barra de comandos para salvar uma cópia da pasta de trabalho com as personalizações e, em seguida, clique em **Edição concluída** para retornar ao modo de leitura.  

### <a name="modify-the-availability-threshold"></a>Alterar o limite de disponibilidade

Neste exemplo, trabalharemos com a pasta de trabalho de capacidade da conta de armazenamento e demonstraremos como modificar o limite de disponibilidade. Por padrão, o bloco e a grade que demonstram a porcentagem de disponibilidade são configurados com um limite mínimo de 90 e o limite máximo de 99. Vamos alterar o valor do limite mínimo da **porcentagem de disponibilidade** na grade **Disponibilidade por nome de API** para 85%, o que significa que o estado de integridade mudará para crítico se o limite for menor que 85%. 

1. Selecione **Contas de armazenamento** no portal e, em seguida, selecione uma conta de armazenamento na lista.

2. Selecione **Insights** no painel esquerdo.

3. Na pasta de trabalho, selecione **Disponibilidade** para passar para a pasta de trabalho de disponibilidade e, em seguida, selecione **Editar** na barra de comandos. 

4. Role para baixo até a parte inferior da página e, no lado esquerdo ao lado da grade **Disponibilidade por API**, selecione **Editar**.

    ![Editar configurações da grade Disponibilidade por Nome de API](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Selecione **Configurações de coluna** e, em seguida, no painel **Editar configurações de coluna**, na seção **Colunas**, selecione **Disponibilidade (%) (Limites + Formatadas)** .

6. Altere o valor do estado de integridade **Crítico** de **90** para **85** e clique em **Salvar e Fechar**.

    ![Modificar o valor do limite de disponibilidade do estado crítico](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. Selecione **Salvar como** na barra de comandos para salvar uma cópia da pasta de trabalho com as personalizações e, em seguida, clique em **Edição concluída** para retornar ao modo de leitura.

## <a name="troubleshooting"></a>Solução de problemas

Para obter diretrizes gerais de solução de problemas, consulte o [artigo de solução de problemas](troubleshoot-workbooks.md)de informações baseadas na pasta de trabalho dedicada.

Esta seção o ajudará no diagnóstico e na solução de alguns dos problemas mais comuns que você pode encontrar ao usar o Azure Monitor for Storage. Use a lista abaixo para localizar as informações relevantes para o seu problema específico.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Resolvendo problemas de desempenho, capacidade ou disponibilidade

Para ajudar na solução de problemas relacionados ao armazenamento que você identificará com o Azure Monitor for Storage, consulte o [guia de solução de problemas](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance) do Armazenamento do Azure.  

### <a name="why-can-i-only-see-200-storage-accounts"></a>Por que só posso ver 200 contas de armazenamento?

O número de contas de armazenamento selecionadas terá como limite 200, independentemente do número de assinaturas que forem selecionadas.

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>Como alterar as cores e o limite da disponibilidade?

Consulte a seção [Modificar o limite de disponibilidade](#modify-the-availability-threshold) para ver as etapas detalhadas de como alterar a cor e os limites de disponibilidade.

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Como analisar e solucionar problemas dos dados mostrados no Azure Monitor for Storage?

 Consulte o artigo [Monitorar, diagnosticar e solucionar problemas do Armazenamento do Microsoft Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md) para ver detalhes de como analisar e solucionar problemas dos dados do Armazenamento do Azure mostrados no Azure Monitor for Storage.

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>Por que não vejo todos os tipos de erros nas métricas?

Atualmente, até três tipos diferentes de erros são mostrados e o restante dos erros é agrupado em um único bucket. Ele é controlado com o uso de splitByLimit e pode ser modificado. Para alterar essa propriedade:

1. Clique em editar pasta de trabalho.
2. Vá até métricas, clique em editar e, em seguida, selecione **Transações, Somar** ou quaisquer métricas que você queira editar.

    ![Vá para métricas e clique em editar e em "transações, Somas"](./media/storage-insights-overview/fqa7.png)

3. Em seguida, altere o Número de Divisões.

    ![Selecione parâmetros de métrica"](./media/storage-insights-overview/fqa7-2.png)

Se quiser ver n tipos de erros diferentes, especifique splitByLimit como n + 1, com o 1 extra para o restante dos erros.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>Salvei minha pasta de trabalho enquanto estava em alguma Conta de Armazenamento. Por que não consigo encontrá-la agora?

Cada pasta de trabalho será salva na conta de armazenamento na qual você a salvou. Tente localizar a Conta de Armazenamento específica na qual o usuário salvou a pasta de trabalho. Caso contrário, não há como localizar uma pasta de trabalho específica sem conhecer o recurso (conta de armazenamento).

## <a name="next-steps"></a>Próximas etapas

* Configure [alertas de métrica](../platform/alerts-metric.md) e [notificações de integridade do serviço](../../service-health/alerts-activity-log-service-notifications-portal.md) para configurar alertas automatizados a fim de auxiliar na detecção de problemas.

* Conheça os cenários aos quais as pastas de trabalho foram projetadas para dar suporte, como criar relatórios, personalizar relatórios existentes e muito mais, examinando [Criar relatórios interativos com pastas de trabalho do Azure Monitor](../platform/workbooks-overview.md).

* Para um guia aprofundado sobre como usar a Análise de Armazenamento e outras ferramentas para identificar, diagnosticar e solucionar problemas relacionados ao Armazenamento do Azure, consulte [Monitorar, diagnosticar e solucionar problemas do Armazenamento do Microsoft Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md).
