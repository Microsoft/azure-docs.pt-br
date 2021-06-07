---
title: Monitorar seus backups com o Backup Explorer
description: Este artigo descreve como usar o Backup Explorer para executar o monitoramento em tempo real de backups em cofres, assinaturas, regiões e locatários.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: a50b9ee05be48113221f2a12f968540bd3a00b3b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88824405"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Monitorar seus backups com o Backup Explorer

À medida que as organizações fazem backup de cada vez mais computadores para a nuvem, é mais importante monitorar esses backups com eficiência. A melhor maneira de começar é usar um local central para exibir informações operacionais em um grande acervo.

O Backup Explorer é uma pasta de trabalho do Azure Monitor interna que fornece aos clientes do backup do Azure esse único local central. O Backup Explorer ajuda a monitorar atividades operacionais em todo o acervo de backup no Azure, abrangendo locatários, locais, assinaturas, grupos de recursos e cofres. Em termos gerais, o Backup Explorer fornece as seguintes funcionalidades:

* **Perspectiva em escala**: obtenha uma exibição agregada dos itens de backup, trabalhos, alertas, políticas e recursos que ainda não foram configurados para backup em todo o acervo.
* **Análise detalhada**: exiba informações detalhadas sobre cada um de seus trabalhos, alertas, políticas e itens de backup, tudo em apenas um lugar.
* **Interfaces acionáveis**: depois de identificar um problema, você pode resolvê-lo indo diretamente para o item de backup ou recurso do Azure relevante.

Essas funcionalidades são fornecidas de maneira integrada pela integração nativa com o Azure Resource Graph e as pastas de trabalho do Azure Monitor.

> [!NOTE]
>
> * O Backup Explorer está disponível no momento apenas para dados de VMs (máquinas virtuais) do Azure.
> * O Backup Explorer é destinado a ser um painel operacional para exibição de informações sobre seus backups nos últimos sete dias (máximo).
> * O Backup Explorer atualmente não é compatível com as nuvens nacionais.
> * Atualmente, não há suporte para a personalização do modelo do Gerenciador de backup.
> * Não recomendamos a gravação de automaçãos personalizadas em dados do grafo de recursos do Azure.
> * Atualmente, o Backup Explorer permite monitorar backups em um máximo de 1.000 assinaturas (entre locatários).

## <a name="get-started"></a>Introdução

Você pode acessar o Backup Explorer acessando qualquer um dos seus cofres dos Serviços de Recuperação e selecionando o link **Backup Explorer** no painel **Visão geral**.

![Link rápido do cofre](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Selecionar esse link abre o Backup Explorer, que fornece uma exibição agregada em todos os cofres e assinaturas aos quais você tem acesso. Se você estiver usando uma conta do Azure Lighthouse, poderá exibir dados em todos os locatários aos quais você tem acesso. Para obter mais informações, confira a seção "Exibições entre locatários" no final deste tutorial.

![Página de aterrissagem do Backup Explorer](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Casos de uso do Backup Explorer

O Backup Explorer exibe várias guias, cada uma fornecendo informações detalhadas sobre um artefato de backup específico (por exemplo, um item de backup, um trabalho ou uma política). Esta seção fornece uma visão geral breve de cada uma das guias. Os vídeos fornecem exemplos de casos de uso para cada artefato de backup, juntamente com descrições dos controles disponíveis.

### <a name="the-summary-tab"></a>A guia Resumo

A guia **Resumo** fornece uma visão rápida da condição geral do seu acervo de backup. Por exemplo, você pode exibir o número de itens que estão sendo protegidos, o número de itens para os quais a proteção não foi habilitada ou quantos trabalhos foram bem-sucedidos nas últimas 24 horas.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>A guia Itens de Backup

Você pode filtrar e exibir cada um de seus itens de backup por assinatura, por cofre e por outras características. Ao selecionar o nome de um item de backup, você pode abrir o painel do Azure para esse item. Por exemplo, na tabela, você pode observar que o último backup falhou para o item *X*. Ao selecionar *X*, você pode abrir o painel **Backup** do item, em que é possível disparar uma operação de backup sob demanda.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>A guia Trabalhos

Selecione a guia **Trabalhos** para exibir os detalhes de todos os trabalhos que foram disparados nos últimos sete dias. Aqui, você pode filtrar por *Operação de Trabalho*, *Status de Trabalho* e *Código de Erro* (para trabalhos com falha).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>A guia Alertas

Selecione a guia **Alertas** para exibir detalhes de todos os alertas que foram gerados em seus cofres nos últimos sete dias. Você pode filtrar os alertas por tipo (*Falha de Backup* ou *Falha de Restauração*), o status atual (*Ativo* ou *Resolvido*) e a severidade (*Crítico*, *Aviso* ou *Informações*). Você também pode selecionar um link para acessar a VM do Azure e executar qualquer ação necessária.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>A guia Políticas

Você pode selecionar a guia **Políticas** para exibir informações importantes sobre todas as políticas de backup que foram criadas em todo o seu acervo de backup. Você pode exibir o número de itens associados a cada política, juntamente com o período de retenção e a frequência de backup especificados pela política.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>A guia Backup Não Habilitado

O backup deve ser habilitado para todos os computadores que exigem proteção. Com o Gerenciador de backup, os administradores de backup podem identificar rapidamente quais computadores de uma organização ainda não estão protegidos pelo backup. Para exibir essas informações, selecione a guia **Backup Não Habilitado**.

O painel **Backup Não Habilitado** exibe uma tabela com uma lista de computadores desprotegidos. Sua organização pode atribuir marcas diferentes a computadores de produção e computadores de teste ou a computadores que atendem a uma variedade de funções. Como cada classe de computadores precisa de uma política de backup separada, filtrar por marcas ajuda a exibir informações específicas a cada uma delas. Selecionar o nome de qualquer computador o redireciona para o painel **Configurar Backup** desse computador, em que você pode optar por aplicar uma política de backup apropriada.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Exportar para o Excel

Você pode exportar o conteúdo de qualquer tabela ou gráfico como uma planilha do Excel. O conteúdo é exportado como está, com os filtros existentes aplicados. Para exportar linhas de tabela adicionais, você pode aumentar o número de linhas a serem exibidas na página usando a lista suspensa **Linhas por Página** na parte superior de cada guia.

## <a name="pin-to-the-dashboard"></a>Fixar ao painel

Você pode selecionar o ícone "fixar" na parte superior de cada tabela ou gráfico para fixá-lo ao seu painel do portal do Azure. Fixar essas informações ajuda a criar um painel personalizado ajustado para exibir as informações mais importantes para você.

## <a name="cross-tenant-views"></a>Exibições entre locatários

Se você for um usuário do Azure Lighthouse com acesso delegado a assinaturas em vários ambientes de locatário, poderá usar o filtro de assinatura padrão. Você exibe as assinaturas para as quais deseja ver os dados selecionando o ícone "filtrar" no canto superior direito do portal do Azure. Quando você usa esse recurso, o Backup Explorer agrega informações sobre todos os cofres em suas assinaturas selecionadas. Para saber mais, confira [O que é o Azure Lighthouse?](../lighthouse/overview.md).

## <a name="next-steps"></a>Próximas etapas

[Saiba como usar Azure Monitor para obter informações sobre seus dados de backup](./backup-azure-monitoring-use-azuremonitor.md)
