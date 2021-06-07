---
title: Gerenciar um recurso Datadog – soluções de parceiros do Azure
description: Este artigo descreve o gerenciamento de um recurso Datadog no portal do Azure. Como configurar o logon único, excluir uma organização confluente e obter suporte.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 04aef540bc134e5ec307be6a232ce47f0923e528
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046344"
---
# <a name="manage-the-datadog-resource"></a>Gerenciar o recurso do Datadog

Este artigo mostra como gerenciar as configurações para sua integração do Azure com o Datadog.

## <a name="resource-overview"></a>Visão Geral de Recursos

Para ver os detalhes do recurso Datadog, selecione **visão geral** no painel esquerdo.

:::image type="content" source="media/manage/resource-overview.png" alt-text="Visão geral do recurso Datadog" border="true" lightbox="media/manage/resource-overview.png":::

Os detalhes incluem:

- Nome do grupo de recursos
- Localização/região
- Subscription
- Marcações
- Link de logon único para a organização do Datadog
- Oferta/plano do Datadog
- Termo de cobrança

Ele também fornece links para painéis, logs e mapas de host do Datadog.

A tela de visão geral fornece um resumo dos recursos que enviam logs e métricas para Datadog.

- Tipo de recurso – tipo de recurso do Azure.
- Total de recursos – contagem de todos os recursos para o tipo de recurso.
- Recursos que enviam logs – contagem de recursos que enviam logs para Datadog por meio da integração.
- Métricas de envio de recursos – contagem de recursos que enviam métricas para Datadog por meio da integração.

## <a name="reconfigure-rules-for-metrics-and-logs"></a>Reconfigurar regras para métricas e logs

Para alterar as regras de configuração para métricas e logs, selecione **métricas e logs** no painel esquerdo.

:::image type="content" source="media/manage/reconfigure-metrics-and-logs.png" alt-text="Modifique a configuração de logs e métricas para o recurso Datadog." border="true":::

Para obter mais informações, consulte [Configurar métricas e logs](create.md#configure-metrics-and-logs).

## <a name="view-monitored-resources"></a>Exibir recursos monitorados

Para ver a lista de recursos que estão emitindo logs para Datadog, selecione **recursos monitorados** no painel esquerdo.

:::image type="content" source="media/manage/view-monitored-resources.png" alt-text="Exibir recursos monitorados pelo Datadog" border="true":::

Você pode filtrar a lista de recursos por tipo de recurso, nome do grupo de recursos, local e se o recurso está enviando logs e métricas.

Os logs de coluna **para Datadog** indicam se o recurso está enviando logs para Datadog. Se o recurso não estiver enviando logs, esse campo indicará por que os logs não estão sendo enviados ao Datadog. Os motivos podem ser:

- O recurso não dá suporte ao envio de logs. Somente os tipos de recursos com categorias de log de monitoramento podem ser configurados para enviar logs para Datadog.
- O limite de cinco configurações de diagnóstico foi atingido. Cada recurso do Azure pode ter no máximo cinco configurações de diagnóstico. Para obter mais informações, consulte [configurações de diagnóstico](../../azure-monitor/essentials/diagnostic-settings.md).
- Erro. O recurso está configurado para enviar logs para Datadog, mas é bloqueado por um erro.
- Logs não configurados. Somente os recursos do Azure que têm as marcas de recurso apropriadas são configuradas para enviar logs para Datadog.
- Região sem suporte. O recurso do Azure está em uma região que atualmente não dá suporte ao envio de logs para Datadog.
- Agente Datadog não configurado. As máquinas virtuais sem o agente Datadog instalado não emitem logs para Datadog.

## <a name="api-keys"></a>Chaves de API

Para exibir a lista de chaves de API para o recurso Datadog, selecione as **chaves** no painel esquerdo. Você vê informações sobre as chaves.

:::image type="content" source="media/manage/keys.png" alt-text="Chaves de API para a organização Datadog." border="true":::

O portal do Azure fornece uma exibição somente leitura das chaves de API. Para gerenciar as chaves, selecione o link do portal do Datadog. Depois de fazer alterações no portal do Datadog, atualize a exibição portal do Azure.

A integração do Azure Datadog fornece a capacidade de instalar o agente do Datadog em uma máquina virtual ou serviço de aplicativo. Se uma chave padrão não estiver selecionada, a instalação do agente Datadog falhará.

## <a name="monitor-virtual-machines-using-the-datadog-agent"></a>Monitorar máquinas virtuais usando o agente Datadog

Você pode instalar agentes do Datadog em máquinas virtuais como uma extensão. Vá para **agente de máquina virtual** nas configurações da org Datadog no painel esquerdo. Esta tela mostra a lista de todas as máquinas virtuais na assinatura.

Para cada máquina virtual, os seguintes dados são exibidos:

- Nome do recurso – nome da máquina virtual
- Status do recurso – se a máquina virtual está parada ou em execução. O agente Datadog só pode ser instalado em máquinas virtuais que estejam em execução. Se a máquina virtual for interrompida, a instalação do agente Datadog será desabilitada.
- Versão do agente – o número de versão do agente Datadog.
- Status do agente – se o agente Datadog está em execução na máquina virtual.
- Integrações habilitadas – as principais métricas que estão sendo coletadas pelo agente Datadog.
- Método de instalação – a ferramenta específica usada para instalar o agente Datadog. Por exemplo, chefe ou script.
- Enviando logs – se o agente Datadog está enviando logs para Datadog.

Selecione a máquina virtual na qual instalar o agente Datadog. Selecione **instalar agente**.

O portal solicita a confirmação de que você deseja instalar o agente com a chave padrão. Selecione **OK** para iniciar a instalação. O Azure mostra o status como **instalando** até que o agente seja instalado e provisionado.

Depois que o agente do Datadog for instalado, o status será alterado para instalado.

Para ver se o agente do Datadog foi instalado, selecione a máquina virtual e navegue até a janela extensões.

Você pode desinstalar os agentes do Datadog em uma máquina virtual acessando o **agente da máquina virtual**. Selecione a máquina virtual e **desinstale o agente**.

## <a name="monitor-app-services-using-the-datadog-agent-as-an-extension"></a>Monitorar serviços de aplicativos usando o agente Datadog como uma extensão

Você pode instalar agentes do Datadog em serviços de aplicativos como uma extensão. Vá para **extensão do serviço de aplicativo** no painel esquerdo. Esta tela mostra a lista de todos os serviços de aplicativos na assinatura.

Para cada serviço de aplicativo, os seguintes elementos de dados são exibidos:

- Nome do recurso – nome da máquina virtual.
- Status do recurso – se o serviço de aplicativo está parado ou em execução. O agente Datadog só pode ser instalado em serviços de aplicativos que estejam em execução. Se o serviço de aplicativo for interrompido, a instalação do agente do Datadog será desabilitada.
- Plano do serviço de aplicativo – o plano específico configurado para o serviço de aplicativo.
- Versão do agente – o número de versão do agente Datadog.

Para instalar o agente do Datadog, selecione o serviço de aplicativo e a **extensão de instalação**. O agente Datadog mais recente é instalado no serviço de aplicativo como uma extensão.

O portal confirma que você deseja instalar o agente do Datadog. Além disso, as configurações do aplicativo para o serviço de aplicativo específico são atualizadas com a chave padrão. O serviço de aplicativo é reiniciado após a conclusão da instalação do agente Datadog.

Selecione **OK** para iniciar o processo de instalação para o agente Datadog. O portal mostra o status como **instalando** até que o agente seja instalado. Depois que o agente do Datadog for instalado, o status será alterado para instalado.

Para desinstalar os agentes do Datadog no serviço de aplicativo, vá para **extensão do serviço de aplicativo**. Selecione o serviço de aplicativo e a **extensão de desinstalação**

## <a name="reconfigure-single-sign-on"></a>Reconfigurar o logon único

Se você quiser reconfigurar o logon único, selecione **logon único** no painel esquerdo.

Para estabelecer o logon único por meio do Azure Active Directory, selecione **habilitar logon único por meio de Azure Active Directory**.

O portal recupera o aplicativo Datadog apropriado do Azure Active Directory. O aplicativo é proveniente do nome do aplicativo empresarial que você selecionou ao configurar a integração. Selecione o nome do aplicativo Datadog, conforme mostrado abaixo:
 
:::image type="content" source="media/manage/reconfigure-single-sign-on.png" alt-text="Reconfigurar o aplicativo de logon único." border="true":::
 
## <a name="disable-or-enable-integration"></a>Desabilitar ou habilitar a integração

Você pode parar de enviar logs e métricas do Azure para o Datadog. Você continuará sendo cobrado por outros serviços Datadogs que não estão relacionados ao monitoramento de métricas e logs.

Para desabilitar a integração do Azure com o Datadog, acesse **visão geral**. Selecione **desabilitar** e **OK**.
 
:::image type="content" source="media/manage/disable.png" alt-text="Desabilite o recurso Datadog." border="true":::

Para habilitar a integração do Azure com o Datadog, acesse **visão geral**. Selecione **habilitar** e **OK**. Selecionar **habilitar** recupera qualquer configuração anterior para métricas e logs. A configuração determina quais recursos do Azure emitem métricas e logs para Datadog. Depois de concluir a etapa, as métricas e os logs são enviados para Datadog.
 
:::image type="content" source="media/manage/enable.png" alt-text="Habilite o recurso Datadog." border="true":::

## <a name="delete-datadog-resource"></a>Excluir recurso Datadog

Vá para **visão geral** no painel esquerdo e selecione **excluir**. Confirme que você deseja excluir o recurso Datadog. Selecione **Excluir**.

:::image type="content" source="media/manage/delete.png" alt-text="Excluir recurso Datadog" border="true":::

Se apenas um recurso Datadog for mapeado para uma organização Datadog, os logs e as métricas não serão mais enviados ao Datadog. Todas as interrupções de cobrança para Datadog por meio do Azure Marketplace.

Se mais de um recurso Datadog for mapeado para a organização Datadog, excluir o recurso Datadog somente interromperá o envio de logs e métricas para esse recurso Datadog. Como a organização Datadog está vinculada a outros recursos do Azure, a cobrança continua por meio do Azure Marketplace.

## <a name="getting-support"></a>Obtendo suporte

Para contatar o suporte sobre a integração do Datadog do Azure, selecione **novo solicitação de suporte** no painel esquerdo. Selecione o link para o portal do Datadog.

:::image type="content" source="media/manage/support-request.png" alt-text="Criar uma nova solicitação de suporte do Datadog" border="true":::

## <a name="next-steps"></a>Próximas etapas

Para obter ajuda com a solução de problemas, consulte [solução de problemas de soluções de Datadog](troubleshoot.md).