---
title: Monitorar Key Vault com Azure Monitor para Key Vault | Microsoft Docs
description: Este artigo descreve o Azure Monitor para Key Vaults.
services: azure-monitor
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/10/2020
ms.openlocfilehash: 91aed191e3bb165d6690759426a596df39f8c10f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582289"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault"></a>Monitorando o serviço de cofre de chaves com Azure Monitor para Key Vault
Azure Monitor para Key Vault fornece monitoramento abrangente de seus cofres de chaves, fornecendo uma exibição unificada de suas solicitações de Key Vault, desempenho, falhas e latência.
Este artigo o ajudará a entender como integrar e personalizar a experiência de Azure Monitor para Key Vault.

## <a name="introduction-to-azure-monitor-for-key-vault"></a>Introdução ao Azure Monitor para Key Vault

Antes de saltar para a experiência, você deve entender como ela apresenta e visualiza informações.
-    **Na perspectiva de escala**, mostrando uma exibição de instantâneo do desempenho com base nas solicitações, no detalhamento de falhas e em uma visão geral das operações e da latência.
-   **Análise de busca detalhada** de um key vault específico para realizar uma análise detalhada.
-    **Personalizável**, onde você pode alterar quais métricas deseja ver, modificar ou definir limites que se alinham aos limites e salvar sua própria pasta de trabalho. Os gráficos na pasta de trabalho podem ser fixados a painéis do Azure.

O Azure Monitor para Key Vault combina logs e métricas para fornecer uma solução de monitoramento global. Todos os usuários podem acessar os dados de monitoramento baseados em métricas, no entanto, a inclusão de visualizações baseadas em logs pode exigir que os usuários [habilitem o registro do Azure Key Vault](../../key-vault/general/logging.md).

## <a name="view-from-azure-monitor"></a>Exibir no Azure Monitor

No Azure Monitor, é possível exibir os detalhes de solicitação, latência e falha de vários key vaults da sua assinatura e ajudar a identificar problemas de desempenho e cenários de limitação.

Para exibir a utilização e as operações de seus cofres de chaves em todas as suas assinaturas, execute as seguintes etapas:

1. Entre no [Portal do Azure](https://portal.azure.com/)

2. Selecione **Monitor** no painel esquerdo na portal do Azure e, na seção insights, selecione **cofres de chaves**.

![Captura de tela da experiência de visão geral com vários grafos](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>Pasta de trabalho Visão geral

Na pasta de trabalho Visão geral da assinatura selecionada, a tabela exibe as métricas interativas dos key vaults agrupados na assinatura. Você pode filtrar os resultados com base nas opções selecionadas nas seguintes listas suspensas:

* Assinaturas - somente as assinaturas que têm key vaults são listadas.

* Key Vaults - por padrão, somente até 5 key vaults são selecionados previamente. Se você selecionar todos ou vários key vaults no seletor de escopo, até 200 key vaults serão retornados. Por exemplo, se você tiver um total de 573 key vaults nas três assinaturas selecionadas, somente 200 vaults serão exibidos.

* Intervalo de tempo - por padrão, exibe as últimas 24 horas de informações com base nas seleções correspondentes.

O bloco contador, nas listas suspensas, acumula o número total de key vaults nas assinaturas selecionadas e reflete quantos foram selecionados. Existem mapas de calor condicionais com código de cores para as colunas da pasta de trabalho que relatam as métricas de solicitação, falhas e latência. A cor mais intensa tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos.

## <a name="failures-workbook"></a>Pasta de trabalho Falhas

Selecione **Falhas** na parte superior da página e a guia Falhas será aberta. Ela mostra as ocorrências da API, a frequência ao longo do tempo, juntamente com a quantidade de determinados códigos de resposta.

![Captura de tela da pasta de trabalho Falhas](./media/key-vaults-insights-overview/failures.png)

Há codificação de cor condicional ou mapas de calor para colunas na pasta de trabalho que relatam as métricas de ocorrências da API com um valor azul. A cor mais intensa tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos.

A pasta de trabalho exibe Sucessos (códigos de status 2xx), Erros de autenticação (códigos de status 401/403), Limitação (códigos de status 429) e Outras falhas (códigos de status 4xx).

Para entender melhor o que cada um dos códigos de status representa, recomendamos ler a documentação em [Status e códigos de resposta do Azure Key Vault](../../key-vault/general/authentication-requests-and-responses.md).

## <a name="view-from-a-key-vault-resource"></a>Exibição de um recurso do Key Vault

Para acessar o Azure Monitor para Key Vault diretamente em um key vault:

1. Na portal do Azure, selecione Key Vaults.

2. Na lista, escolha um key vault. Na seção monitoramento, escolha insights.

Essas exibições também podem ser acessadas selecionando o nome do recurso de um key vault na pasta de trabalho do Azure Monitor.

![Captura de tela da exibição de um recurso do key vault](./media/key-vaults-insights-overview/key-vault-resource-view.png)

Na pasta de trabalho **Visão geral** para o key vault, são mostradas várias métricas de desempenho que ajudam a avaliar rapidamente o seguinte:

- Gráficos de desempenho interativos que mostram os detalhes mais fundamentais relacionados a transações, latência e disponibilidade do key vault.

- Os blocos de métricas e status que realçam a disponibilidade do serviço, a contagem total de transações para o recurso do key vault e a latência geral.

Selecionar qualquer uma das outras guias para **Falhas** ou **Operações** abre as respectivas pastas de trabalho.

![Captura de tela da exibição de falhas](./media/key-vaults-insights-overview/resource-failures.png)

A pasta de trabalho Falhas detalha os resultados de todas as solicitações do key vault no intervalo de tempo selecionado e fornece a categorização sobre Sucessos (2xx), Erros de autenticação (401/403), Limitação (429) e outras falhas.

![Captura de tela da exibição de operações](./media/key-vaults-insights-overview/operations.png)

A pasta de trabalho Operações permite aprofundar-se nos detalhes completos de todas as transações, que podem ser filtradas pelo status do resultado usando os blocos de nível superior.

![Captura de tela que mostra a pasta de trabalho de operações que contém detalhes completos de todas as transações.](./media/key-vaults-insights-overview/info.png)

Os usuários também podem explorar as exibições com base em tipos de transação específicos na tabela superior, que atualiza dinamicamente a tabela inferior, na qual os usuários podem exibir detalhes completos da operação em um painel de contexto pop-up.

>[!NOTE]
> Observe que os usuários devem ter as configurações de diagnóstico habilitadas para exibir esta pasta de trabalho. Para saber mais sobre como habilitar a configuração de diagnóstico, leia mais sobre [Registro do Azure Key Vault](../../key-vault/general/logging.md).

## <a name="pin-and-export"></a>Fixar e exportar

Você pode fixar qualquer uma das seções de métrica em um Painel do Azure, selecionando o ícone de alfinete na parte superior direita da seção.

As pastas de trabalho Visão geral ou falhas de várias assinaturas e key vaults dão suporte à exportação dos resultados no formato do Excel, selecionando o ícone de download à esquerda do ícone de alfinete.

![Captura de tela do ícone de fixação selecionado](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>Personalizar Azure Monitor para Key Vault

Esta seção destaca os cenários comuns para editar a pasta de trabalho a fim de personalizar o suporte às necessidades de análise de dados:
*  Escopo da pasta de trabalho para sempre selecionar uma assinatura ou key vault específico
* Alterar as métricas na grade
* Alterar o limite de solicitações
* Alterar a renderização de cores

Você pode iniciar as personalizações habilitando o modo de edição e selecionando o botão **Personalizar** na barra de ferramentas superior.

![Captura de tela do botão Personalizar](./media/key-vaults-insights-overview/customize.png)

As personalizações são salvas em uma pasta de trabalho personalizada para evitar a substituição da configuração padrão em nossa pasta de trabalho publicada. As pastas de trabalho são salvas em um grupo de recursos, seja na seção Meus Relatórios, que é particular a você, ou na seção Relatórios Compartilhados, que é acessível a todos com acesso ao grupo de recursos. Depois de salvar a pasta de trabalho personalizada, você precisa ir para a galeria de pastas de trabalho para iniciá-la.

![Captura de tela da galeria da pasta de trabalho](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>Especificação de uma assinatura ou um key vault

Você pode configurar as pastas de trabalho Visão geral ou Falhas de várias assinaturas e key vaults para definir o escopo para uma assinatura ou key vault específico em cada execução, seguindo as etapas abaixo:

1. Selecione **Monitor** no portal e, em seguida, selecione **cofres de chaves** no painel esquerdo.
2. Na pasta de trabalho **Visão geral**, na barra de comandos, selecione **Editar**.
3. Selecione na lista suspensa **Assinaturas** uma ou mais assinaturas que você deseja usar como o padrão. Lembre-se de que a pasta de trabalho permite selecionar até 10 assinaturas no total.
4. Selecione na lista suspensa **Key Vaults** uma ou mais contas que você deseja usar como padrão. Lembre-se de que a pasta de trabalho permite selecionar até 200 contas de armazenamento no total.
5. Selecione **Salvar como** na barra de comandos para salvar uma cópia da pasta de trabalho com as personalizações e, em seguida, clique em **Edição concluída** para retornar ao modo de leitura.

## <a name="troubleshooting"></a>Solução de problemas

Para obter diretrizes gerais de solução de problemas, consulte o [artigo de solução de problemas](troubleshoot-workbooks.md)de informações baseadas na pasta de trabalho dedicada.

Esta seção o ajudará com o diagnóstico e a solução de problemas de alguns dos problemas comuns que você pode encontrar ao usar o Azure Monitor para Key Vault. Use a lista abaixo para localizar as informações relevantes para o seu problema específico.

### <a name="resolving-performance-issues-or-failures"></a>Resolução de problemas ou falhas de desempenho

Para ajudar a solucionar problemas relacionados ao cofre de chaves que você identifica com Azure Monitor para Key Vault, consulte a [documentação do Azure Key Vault](../../key-vault/index.yml).

### <a name="why-can-i-only-see-200-key-vaults"></a>Por que só posso ver os cofres de chaves 200

Há um limite de 200 key vaults que podem ser selecionados e exibidos. Independentemente do número de assinaturas selecionadas, o número de key vaults selecionados tem um limite de 200.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Por que não vejo todas as minhas assinaturas no seletor de assinatura

Mostramos apenas as assinaturas que contêm key vaults, escolhidas no filtro de assinatura selecionado, que são selecionadas no "Diretório + Assinatura" no cabeçalho do portal do Azure.

![Captura de tela do filtro de assinatura](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Quero fazer alterações ou adicionar visualizações adicionais a Key Vault insights, como fazer isso

Para fazer alterações, selecione o "Modo de edição" para modificar a pasta de trabalho e, em seguida, você pode salvar seu trabalho como uma nova pasta de trabalho vinculada a uma assinatura e um grupo de recursos designados.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Qual é o refinamento de tempo depois de fixarmos qualquer parte das pastas de trabalho

Utilizamos o intervalo de agregação "Automático" e, portanto, depende do intervalo de tempo selecionado.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Qual é o intervalo de tempo quando qualquer parte da pasta de trabalho é fixada

O intervalo de tempo dependerá das configurações do painel.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>E se eu quiser ver outros dados ou fazer minhas próprias visualizações? Como fazer alterações no Key Vault insights

Você pode editar a pasta de trabalho existente usando o modo de edição e, em seguida, salvar seu trabalho como uma nova pasta de trabalho que terá todas as novas alterações.

## <a name="next-steps"></a>Próximas etapas

Conheça os cenários aos quais as pastas de trabalho foram projetadas para dar suporte, como criar relatórios, personalizar relatórios existentes e muito mais, examinando [Criar relatórios interativos com pastas de trabalho do Azure Monitor](../visualize/workbooks-overview.md).
