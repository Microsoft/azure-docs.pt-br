---
title: Usar monitor de área de trabalho virtual do Windows monitor – Azure
description: Como usar Azure Monitor para área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1b93e0c0d61eaa390eda66da2a583a4f429ddd79
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709541"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment"></a>Usar Azure Monitor para área de trabalho virtual do Windows para monitorar sua implantação

O Azure Monitor para área de trabalho virtual do Windows é um Dashboard criado em pastas de trabalho do Azure Monitor que ajuda os profissionais de ti a compreender seus ambientes de área de trabalho virtual do Windows Este tópico explicará como configurar Azure Monitor para área de trabalho virtual do Windows para monitorar seus ambientes de área de trabalho virtual do Windows.

## <a name="requirements"></a>Requisitos

Antes de começar a usar o Azure Monitor para área de trabalho virtual do Windows, você precisará configurar os seguintes itens:

- Todos os ambientes de área de trabalho virtual do Windows monitorados devem ser baseados na versão mais recente da área de trabalho virtual do Windows que seja compatível com o Azure Resource Manager.
- Pelo menos um espaço de trabalho de Log Analytics configurado. Use um espaço de trabalho Log Analytics designado para seus hosts de sessão de área de trabalho virtual do Windows para garantir que os contadores de desempenho e eventos sejam coletados apenas de hosts de sessão em sua implantação de área de trabalho virtual
- Habilite a coleta de dados para os seguintes itens em seu espaço de trabalho Log Analytics:
    - Diagnóstico do seu ambiente de área de trabalho virtual do Windows
    - Contadores de desempenho recomendados de seus hosts de sessão de área de trabalho virtual do Windows
    - Logs de eventos do Windows recomendados de seus hosts de sessão de área de trabalho virtual do Windows

 O processo de configuração de dados descrito neste artigo é o único que você precisará para monitorar a área de trabalho virtual do Windows. Você pode desabilitar todos os outros itens enviando dados para seu espaço de trabalho do Log Analytics para economizar custos.

Qualquer pessoa que estiver monitorando Azure Monitor para área de trabalho virtual do Windows para seu ambiente também precisará das seguintes permissões de acesso de leitura:

- Acesso de leitura às assinaturas do Azure que mantêm seus recursos de área de trabalho virtual do Windows
- Acesso de leitura aos grupos de recursos da assinatura que contêm os hosts de sessão da área de trabalho virtual do Windows
- Acesso de leitura ao espaço de trabalho do Log Analytics ou espaços de trabalho

>[!NOTE]
> Somente acesso de leitura permite que os administradores exibam dados. Eles precisarão de permissões diferentes para gerenciar recursos no portal de área de trabalho virtual do Windows.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Abrir Azure Monitor para área de trabalho virtual do Windows

Você pode abrir Azure Monitor para área de trabalho virtual do Windows com um dos seguintes métodos:

- Vá para [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks).
- Procure e selecione **área de trabalho virtual do Windows** na portal do Azure e, em seguida, selecione **insights**.
- Procure e selecione **Azure monitor** na portal do Azure. Selecione **Hub de informações** em **insights** e, em seguida, selecione **área de trabalho virtual do Windows**.
Quando a página estiver aberta, insira a **assinatura**, o **grupo de recursos**, o **pool de hosts** e o **intervalo de tempo** do ambiente que você deseja monitorar.

>[!NOTE]
>A área de trabalho virtual do Windows atualmente dá suporte apenas ao monitoramento de uma assinatura, grupo de recursos e pool de hosts por vez. Se você não encontrar o ambiente que deseja monitorar, consulte [nossa documentação de solução de problemas](troubleshoot-azure-monitor.md) para solicitações e problemas de recursos conhecidos.

## <a name="log-analytics-settings"></a>Configurações de Log Analytics

Para começar a usar o Azure Monitor para área de trabalho virtual do Windows, você precisará de pelo menos um espaço de trabalho Log Analytics. Use um espaço de trabalho Log Analytics designado para seus hosts de sessão de área de trabalho virtual do Windows para garantir que os contadores de desempenho e os eventos sejam coletados apenas em hosts de sessão de formulário na sua implantação de área Se você já tiver um espaço de trabalho configurado, pule para [Configurar usando a pasta de trabalho de configuração](#set-up-using-the-configuration-workbook). Para configurar um, consulte [criar um log Analytics espaço de trabalho no portal do Azure](../azure-monitor/logs/quick-create-workspace.md).

>[!NOTE]
>Os encargos de armazenamento de dados padrão para Log Analytics serão aplicados. Para começar, recomendamos que você escolha o modelo pago conforme o uso e ajuste à medida que dimensionar sua implantação e obter mais dados. Para saber mais, consulte [preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="set-up-using-the-configuration-workbook"></a>Configurar usando a pasta de trabalho de configuração

Se for a primeira vez que abrir Azure Monitor para área de trabalho virtual do Windows, você precisará configurar o Azure Monitor para seu ambiente de área de trabalho virtual do Windows. Para configurar seus recursos:

1. Abra Azure Monitor para área de trabalho virtual do Windows no portal do Azure em [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), em seguida, selecione **pasta de trabalho de configuração**.
2. Selecione um ambiente para configurar em **assinatura**, **grupo de recursos** e **pool de hosts**.

A pasta de trabalho configuração configura seu ambiente de monitoramento e permite que você verifique a configuração depois de concluir o processo de instalação. É importante verificar sua configuração se os itens no painel não estiverem sendo exibidos corretamente ou quando o grupo de produtos publicar atualizações que exigem novas configurações.

### <a name="resource-diagnostic-settings"></a>Configurações do Diagnóstico do recurso

Para coletar informações sobre sua infraestrutura de área de trabalho virtual do Windows, você precisará habilitar várias configurações de diagnóstico em seus pools de hosts e espaços de trabalho da área de trabalho virtual do Windows (esse é o seu espaço de trabalho de área de trabalho virtual do Windows, não o seu Log Analytics Para saber mais sobre pools de hosts, espaços de trabalho e outros objetos de recursos de área de trabalho virtual do Windows, consulte nosso [Guia de ambiente](environment-setup.md).

Você pode saber mais sobre o diagnóstico de área de trabalho virtual do Windows e as tabelas de diagnóstico com suporte em [Enviar diagnóstico de área de trabalho virtual do Windows para log Analytics](diagnostics-log-analytics.md).

Para definir as configurações de diagnóstico de recurso na pasta de trabalho de configuração: 

1. Selecione a guia **configurações de diagnóstico de recurso** na pasta de trabalho configuração. 
2. Selecione **log Analytics espaço de trabalho** para enviar o diagnóstico de área de trabalho virtual do Windows. 

#### <a name="host-pool-diagnostic-settings"></a>Configurações de diagnóstico do pool de host

Para configurar o diagnóstico do pool de hosts usando a seção Configurações de diagnóstico de recurso na pasta de trabalho configuração:

1. Em **pool de hosts**, verifique se o diagnóstico de área de trabalho virtual do Windows está habilitado. Se não forem, será exibida uma mensagem de erro dizendo "nenhuma configuração de diagnóstico existente foi encontrada para o pool de hosts selecionado." Você precisará habilitar as seguintes tabelas de diagnóstico com suporte:

    - Ponto de verificação
    - Erro
    - Gerenciamento
    - Conexão
    - HostRegistration
    - AgentHealthStatus
    
    >[!NOTE]
    > Se você não vir a mensagem de erro, não precisará executar as etapas 2 a 4.

2. Selecione **Configurar pool de hosts**.
3. Selecione **Implantar**.
4. Atualize a pasta de trabalho de configuração.

#### <a name="workspace-diagnostic-settings"></a>Configurações de diagnóstico do espaço de trabalho 

Para configurar o diagnóstico de espaço de trabalho usando a seção Configurações de diagnóstico de recurso na pasta de trabalho configuração:

 1. Em **espaço de trabalho**, verifique se o diagnóstico de área de trabalho virtual do Windows está habilitado para o espaço de trabalho área de trabalho virtual do Windows. Se não forem, será exibida uma mensagem de erro dizendo "nenhuma configuração de diagnóstico existente foi encontrada para o espaço de trabalho selecionado". Você precisará habilitar as seguintes tabelas de diagnóstico com suporte:
 
    - Ponto de verificação
    - Erro
    - Gerenciamento
    - Feed
    
    >[!NOTE]
    > Se você não vir a mensagem de erro, não precisará executar as etapas de 2-4.

2. Selecione **Configurar espaço de trabalho**.
3. Selecione **Implantar**.
4. Atualize a pasta de trabalho de configuração.

### <a name="session-host-data-settings"></a>Configurações de dados do host da sessão

Para coletar informações sobre seus hosts de sessão de área de trabalho virtual do Windows, você precisará instalar o agente de Log Analytics em todos os hosts de sessão no pool de hosts, verificar se os hosts de sessão estão enviando para um espaço de trabalho Log Analytics e definir as configurações do Log Analytics Agent para coletar dados de desempenho e logs de eventos do Windows.

O espaço de trabalho Log Analytics para o qual você envia dados de host de sessão não precisa ser o mesmo que você envia dados de diagnóstico. Se você tiver hosts de sessão do Azure fora do seu ambiente de área de trabalho virtual do Windows, é recomendável ter um espaço de trabalho Log Analytics designado para os hosts de sessão de área de trabalho virtual 

Para definir o espaço de trabalho Log Analytics onde você deseja coletar dados de host de sessão: 

1. Selecione a guia **configurações de dados do host da sessão** na pasta de trabalho de configuração. 
2. Selecione o **log Analytics espaço de trabalho** para o qual você deseja enviar dados de host de sessão. 

#### <a name="session-hosts"></a>Hosts de sessão

Você precisará instalar o agente de Log Analytics em todos os hosts de sessão no pool de hosts e enviar dados desses hosts para o espaço de trabalho de Log Analytics selecionado. Se Log Analytics não estiver configurado para todos os hosts de sessão no pool de hosts, você verá uma seção **hosts de sessão** na parte superior das **configurações de dados do host de sessão** com a mensagem "alguns hosts no pool de hosts não estão enviando dados para o espaço de trabalho de log Analytics selecionado".

>[!NOTE]
> Se você não vir a seção **hosts de sessão** ou a mensagem de erro, todos os hosts de sessão serão configurados corretamente. Pule para configurar as instruções para [contadores de desempenho do espaço de trabalho](#workspace-performance-counters).

Para configurar seus hosts de sessão restantes usando a pasta de trabalho de configuração:

1. Selecione **adicionar hosts ao espaço de trabalho**. 
2. Atualize a pasta de trabalho de configuração.

>[!NOTE]
>O computador host precisa estar em execução para instalar a extensão de Log Analytics. Se a implantação automática não funcionar, você poderá instalar a extensão em um host manualmente. Para saber como instalar a extensão manualmente, consulte [log Analytics extensão da máquina virtual para Windows](../virtual-machines/extensions/oms-windows.md).

#### <a name="workspace-performance-counters"></a>Contadores de desempenho de espaço de trabalho

Você precisará habilitar contadores de desempenho específicos para coletar informações de desempenho de seus hosts de sessão e enviá-los para o espaço de trabalho Log Analytics.

Se você já tiver os contadores de desempenho habilitados e quiser removê-los, siga as instruções em [Configurando contadores de desempenho](../azure-monitor/agents/data-sources-performance-counters.md). Você pode adicionar e remover contadores de desempenho no mesmo local.

Para configurar contadores de desempenho usando a pasta de trabalho de configuração: 

1. Em **contadores de desempenho do espaço** de trabalho na pasta de trabalho configuração, verifique os **contadores configurados** para ver os contadores que você já habilitou para enviar ao espaço de trabalho do log Analytics. Verifique os **contadores ausentes** para certificar-se de que você habilitou todos os contadores necessários.
2. Se você tiver contadores ausentes, selecione **Configurar contadores de desempenho**.
3. Selecione **aplicar configuração**.
4. Atualize a pasta de trabalho de configuração.
5. Verifique se todos os contadores necessários estão habilitados verificando a lista de **contadores ausentes** . 

#### <a name="configure-windows-event-logs"></a>Configurar logs de eventos do Windows

Você também precisará habilitar logs de eventos específicos do Windows para coletar erros, avisos e informações dos hosts de sessão e enviá-los para o espaço de trabalho Log Analytics.

Se você já tiver habilitado os logs de eventos do Windows e quiser removê-los, siga as instruções em [Configurando logs de eventos do Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).  Você pode adicionar e remover logs de eventos do Windows no mesmo local.

Para configurar logs de eventos do Windows usando a pasta de trabalho de configuração:

1. Em **configuração de logs de eventos do Windows**, verifique os **logs de eventos configurados** para ver os logs de eventos que você já habilitou para enviar ao espaço de trabalho do log Analytics. Verifique os **logs de eventos ausentes** para certificar-se de que você habilitou todos os logs de eventos do Windows.
2. Se você tiver logs de eventos do Windows ausentes, selecione **Configurar eventos**.
3. Selecione **Implantar**.
4. Atualize a pasta de trabalho de configuração.
5. Verifique se todos os logs de eventos do Windows necessários estão habilitados verificando a lista de **logs de eventos ausentes** . 

>[!NOTE]
>Se a implantação automática de eventos falhar, selecione **abrir configuração do agente** na pasta de trabalho de configuração para adicionar manualmente quaisquer logs de eventos do Windows ausentes. 

## <a name="optional-configure-alerts"></a>Opcional: configurar alertas

Azure Monitor para área de trabalho virtual do Windows permite monitorar Azure Monitor alertas que ocorrem dentro de sua assinatura selecionada no contexto dos dados da área de trabalho virtual do Windows. Azure Monitor alertas são um recurso opcional em suas assinaturas do Azure e você precisa configurá-los separadamente do Azure Monitor para a área de trabalho virtual do Windows. Você pode usar a estrutura de alertas de Azure Monitor para definir alertas personalizados em eventos de área de trabalho virtual do Windows, diagnósticos e recursos. Para saber mais sobre alertas de Azure Monitor, confira [responder a eventos com Azure monitor alertas](../azure-monitor/alerts/tutorial-response.md).

## <a name="diagnostic-and-usage-data"></a>Dados de uso e de diagnóstico

A Microsoft coleta automaticamente dados de uso e de desempenho por meio do uso do serviço Azure Monitor. A Microsoft usa esses dados para melhorar a qualidade, a segurança e a integridade do serviço.

Para fornecer recursos de solução de problemas precisos e eficientes, os dados coletados incluem a ID da sessão do portal, Azure Active Directory ID de usuário e o nome da guia do portal em que o evento ocorreu. A Microsoft não coleta nomes, endereços nem outras informações de contato.

Para obter mais informações sobre a coleta e uso de dados, consulte a [Política de privacidade do Microsoft Online Services](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>Para saber mais sobre como exibir ou excluir seus dados pessoais coletados pelo serviço, consulte [solicitações de entidade de dados do Azure para o GDPR](/microsoft-365/compliance/gdpr-dsr-azure). Para obter mais informações sobre GDPR, consulte [a seção GDPR do portal de confiança do serviço](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou Azure Monitor para seu ambiente de área de trabalho virtual do Windows, aqui estão alguns recursos que podem ajudá-lo a começar a monitorar seu ambiente:

- Confira nosso [Glossário](azure-monitor-glossary.md) para saber mais sobre os termos e conceitos relacionados ao Azure monitor para área de trabalho virtual do Windows.
- Se você encontrar um problema, confira nosso [Guia de solução de problemas](troubleshoot-azure-monitor.md) para obter ajuda e problemas conhecidos.
