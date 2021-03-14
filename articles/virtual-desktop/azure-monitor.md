---
title: Use monitorar o monitor de área de trabalho virtual do Windows Preview-Azure
description: Como usar Azure Monitor para área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e9da1071686dafa003a5a49d0864b77644493344
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594468"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment-preview"></a>Usar Azure Monitor para área de trabalho virtual do Windows para monitorar sua implantação (versão prévia)

>[!IMPORTANT]
>O Azure Monitor para área de trabalho virtual do Windows está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendamos usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Monitor para área de trabalho virtual do Windows (versão prévia) é um Dashboard criado em pastas de trabalho do Azure Monitor que ajuda os profissionais de ti a compreender seus ambientes de área de trabalho virtual do Windows. Este tópico explicará como configurar Azure Monitor para área de trabalho virtual do Windows para monitorar seus ambientes de área de trabalho virtual do Windows.

## <a name="requirements"></a>Requisitos

Antes de começar a usar o Azure Monitor para área de trabalho virtual do Windows, você precisará configurar os seguintes itens:

- Todos os ambientes de área de trabalho virtual do Windows monitorados devem ser baseados na versão mais recente da área de trabalho virtual do Windows que seja compatível com o Azure Resource Manager.

- Pelo menos um espaço de trabalho de Log Analytics configurado.

- Habilite a coleta de dados para os seguintes itens em seu espaço de trabalho Log Analytics:
    - Todos os contadores de desempenho necessários
    - Quaisquer contadores de desempenho ou eventos usados em Azure Monitor para área de trabalho virtual do Windows
    - Dados da ferramenta de diagnóstico para todos os objetos no ambiente que você monitorará.
    - VMs (máquinas virtuais) no ambiente que você monitorará.

Qualquer pessoa que monitorar o Azure Monitor para área de trabalho virtual do Windows para seu ambiente também precisará das seguintes permissões de acesso de leitura:

- Acesso de leitura ao grupo de recursos em que os recursos do ambiente estão localizados.

- Acesso de leitura aos grupos de recursos nos quais os hosts de sessão do ambiente estão localizados

>[!NOTE]
> Somente acesso de leitura permite que os administradores exibam dados. Eles precisarão de permissões diferentes para gerenciar recursos no portal de área de trabalho virtual do Windows.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Abrir Azure Monitor para área de trabalho virtual do Windows

Você pode abrir Azure Monitor para área de trabalho virtual do Windows com um dos seguintes métodos:

- Vá para [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks).

- Procure e selecione **área de trabalho virtual do Windows** na portal do Azure e, em seguida, selecione **insights**.

- Procure e selecione **Azure monitor** na portal do Azure. Selecione **Hub de informações** em **insights** e, em **outras** , selecione **área de trabalho virtual do Windows** para abrir o painel na página de Azure monitor.

Quando você tiver Azure Monitor para a área de trabalho virtual do Windows aberta, marque uma ou mais das caixas de seleção rotuladas **assinatura**, **grupo de recursos**, **pool de hosts** e **intervalo de tempo** com base no ambiente que você deseja monitorar.

>[!NOTE]
>A área de trabalho virtual do Windows atualmente dá suporte apenas ao monitoramento de uma assinatura, grupo de recursos e pool de hosts por vez. Se você não encontrar o ambiente que deseja monitorar, consulte [nossa documentação de solução de problemas](troubleshoot-azure-monitor.md) para obter solicitações e problemas de recursos conhecidos.

## <a name="set-up-with-the-configuration-workbook"></a>Configurar com a pasta de trabalho de configuração

Se esta for a primeira vez que você abrir Azure Monitor para área de trabalho virtual do Windows, você precisará configurar Azure Monitor para os recursos da área de trabalho virtual do Windows. Para configurar seus recursos:

1. Abra sua pasta de trabalho no portal do Azure.
2. Selecione **abrir a pasta de trabalho de configuração**.

A pasta de trabalho configuração configura seu ambiente de monitoramento e permite que você verifique a configuração depois de concluir o processo de instalação. É importante verificar sua configuração se os itens no painel não estiverem sendo exibidos corretamente ou quando o grupo de produtos publicar atualizações que exigem pontos de dados adicionais.

## <a name="host-pool-diagnostic-settings"></a>Configurações de diagnóstico do pool de host

Você precisará habilitar Azure Monitor configurações de diagnóstico em todos os objetos no ambiente de área de trabalho virtual do Windows que dão suporte a esse recurso.

1. Abra Azure Monitor para área de trabalho virtual do Windows em [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)e, em seguida, selecione **pasta de trabalho de configuração**.

2. Selecione um ambiente para monitorar sob **assinatura**, **grupo de recursos** e **pool de hosts**.

3. Em **configurações de diagnóstico do pool de hosts**, verifique se o diagnóstico de área de trabalho virtual do Windows está habilitado para o pool de hosts. Se não forem, será exibida uma mensagem de erro dizendo "nenhuma configuração de diagnóstico existente foi encontrada para o pool de hosts selecionado." 
   
   As tabelas a seguir devem ser habilitadas:

    - Ponto de verificação
    - Erro
    - Gerenciamento
    - Conexão
    - HostRegistration

    >[!NOTE]
    > Se você não vir a mensagem de erro, não precisará executar a etapa 4.

4. Selecione **Configurar pool de hosts**.

5. Selecione **Implantar**.

6. Atualizar a pasta de trabalho.

Você pode aprender mais sobre como habilitar o diagnóstico em todos os objetos no ambiente de área de trabalho virtual do Windows ou acessar o espaço de trabalho Log Analytics em [Enviar diagnóstico de área de trabalho virtual do Windows para log Analytics](diagnostics-log-analytics.md).

## <a name="configure-log-analytics"></a>Configurar a análise de logs

Para começar a usar o Azure Monitor para área de trabalho virtual do Windows, você também precisará de pelo menos um espaço de trabalho Log Analytics para coletar dados do ambiente que planeja monitorar e fornecê-los à pasta de trabalho. Se você já tiver uma configuração, pule para configurar os [contadores de desempenho](#set-up-performance-counters). Para configurar um novo espaço de trabalho Log Analytics para a assinatura do Azure que contém o ambiente de área de trabalho virtual do Windows, consulte [criar um espaço de trabalho log Analytics no portal do Azure](../azure-monitor/logs/quick-create-workspace.md).

>[!NOTE]
>Os encargos de armazenamento de dados padrão para Log Analytics serão aplicados. Para começar, recomendamos que você escolha o modelo pago conforme o uso e ajuste à medida que dimensionar sua implantação e obter mais dados. Para saber mais, consulte [preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="set-up-performance-counters"></a>Configurar contadores de desempenho

Você precisa habilitar contadores de desempenho específicos para coleta no intervalo de exemplo correspondente no espaço de trabalho Log Analytics. Esses contadores de desempenho são os únicos contadores que você precisará para monitorar a área de trabalho virtual do Windows. Você pode desabilitar todos os outros para economizar custos.

Se você já tiver os contadores de desempenho habilitados e quiser removê-los, siga as instruções em [Configurando contadores de desempenho](../azure-monitor/agents/data-sources-performance-counters.md) para reconfigurar seus contadores de desempenho. Embora o artigo descreva como adicionar contadores, você também pode removê-los no mesmo local.

Se você ainda não configurou os contadores de desempenho, veja como configurá-los para Azure Monitor para área de trabalho virtual do Windows:

1. Vá para [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)e, em seguida, selecione a **pasta de trabalho de configuração** na parte inferior da janela.

2. Em **configuração do log Analytics**, selecione o espaço de trabalho que você configurou para sua assinatura.

3. Em **contadores de desempenho do espaço de trabalho**, você verá a lista de contadores necessários para o monitoramento. No lado direito dessa lista, verifique os itens na lista de **contadores ausentes** para habilitar os contadores que você precisará para começar a monitorar seu espaço de trabalho.

4. Selecione **Configurar contadores de desempenho**.

5. Selecione **aplicar configuração**.

6. Atualize a pasta de trabalho de configuração e continue a configurar seu ambiente.

Você também pode adicionar novos contadores de desempenho após a configuração inicial sempre que o serviço for atualizado e exigir novas ferramentas de monitoramento. Você pode verificar se todos os contadores necessários estão habilitados selecionando-os na lista de **contadores ausentes** .

>[!NOTE]
>Os contadores de desempenho de atraso de entrada são compatíveis apenas com o Windows 10 RS5 e posterior ou o Windows Server 2019 e posterior.

Para saber mais sobre como adicionar manualmente contadores de desempenho que ainda não estão habilitados para coleta, consulte [Configurando contadores de desempenho](../azure-monitor/agents/data-sources-performance-counters.md).

### <a name="set-up-windows-events"></a>Configurar eventos do Windows

Em seguida, você precisará habilitar eventos específicos do Windows para coleta no espaço de trabalho Log Analytics. Os eventos descritos nesta seção são os únicos Azure Monitor para as necessidades da área de trabalho virtual do Windows. Você pode desabilitar todos os outros para economizar custos.

Para configurar eventos do Windows:

1. Se você já tiver os eventos do Windows habilitados e quiser removê-los, remova os eventos que não deseja antes de usar a pasta de trabalho de configuração para habilitar o conjunto necessário para o monitoramento.

2. Acesse Azure Monitor para área de trabalho virtual do Windows em [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)e, em seguida, selecione **pasta de trabalho de configuração** na parte inferior da janela.

3. Na **configuração de eventos do Windows**, há uma lista de eventos do Windows necessários para o monitoramento. No lado direito dessa lista está a lista de **eventos ausentes** , em que você encontrará os nomes de eventos e tipos de eventos necessários que não estão atualmente habilitados para seu espaço de trabalho. Registre cada um desses nomes para mais tarde.

4. Selecione **abrir configuração de espaços de trabalho**.

5. Selecione **dados**.

6. Selecione **logs de eventos do Windows**.

7. Adicione os nomes de evento ausentes da etapa 3 e o tipo de evento necessário para cada um.

8. Atualize a pasta de trabalho de configuração e continue a configurar seu ambiente.

Você pode adicionar novos eventos do Windows após a configuração inicial se as atualizações da ferramenta de monitoramento exigirem a habilitação de novos eventos. Para verificar se você tem todos os eventos necessários habilitados, volte para a lista de **eventos ausentes** e habilite os eventos ausentes que você vê lá.

## <a name="install-the-log-analytics-agent-on-all-hosts"></a>Instalar o agente de Log Analytics em todos os hosts

Por fim, você precisará instalar o agente de Log Analytics em todos os hosts no pool de hosts para enviar dados dos hosts para o espaço de trabalho selecionado.

Para instalar o agente de Log Analytics:

1. Acesse Azure Monitor para área de trabalho virtual do Windows em [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)e, em seguida, selecione **pasta de trabalho de configuração** na parte inferior da janela.

2. Se Log Analytics não estiver configurado para todos os hosts no pool de hosts, você verá um erro na parte inferior da seção de configuração de Log Analytics com a mensagem "alguns hosts no pool de hosts não estão enviando dados para o espaço de trabalho de Log Analytics selecionado". Selecione **adicionar hosts ao espaço de trabalho** para adicionar os hosts selecionados. Se você não vir a mensagem de erro, pare aqui.

3. Atualize a pasta de trabalho de configuração.

>[!NOTE]
>O computador host precisa estar em execução para instalar a extensão de Log Analytics. Se a implantação automática falhar em um host, você sempre poderá instalar a extensão em um host manualmente. Para saber como instalar a extensão manualmente, consulte [log Analytics extensão da máquina virtual para Windows](../virtual-machines/extensions/oms-windows.md).

## <a name="optional-configure-alerts"></a>Opcional: configurar alertas

Você pode configurar Azure Monitor para a área de trabalho virtual do Windows para notificá-lo se qualquer alerta de Azure Monitor grave ocorrer dentro de sua assinatura selecionada. Para fazer isso, siga as instruções em [responder a eventos com Azure monitor alertas](../azure-monitor/alerts/tutorial-response.md).

## <a name="diagnostic-and-usage-data"></a>Dados de uso e de diagnóstico

A Microsoft coleta automaticamente dados de uso e de desempenho por meio do uso do serviço Azure Monitor. A Microsoft usa esses dados para melhorar a qualidade, a segurança e a integridade do serviço.

Para fornecer recursos de solução de problemas precisos e eficientes, os dados coletados incluem a ID da sessão do portal, Azure Active Directory ID de usuário e o nome da guia do portal em que o evento ocorreu. A Microsoft não coleta nomes, endereços nem outras informações de contato.

Para obter mais informações sobre a coleta e uso de dados, consulte a [Política de privacidade do Microsoft Online Services](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>Para saber mais sobre como exibir ou excluir seus dados pessoais coletados pelo serviço, consulte [solicitações de entidade de dados do Azure para o GDPR](/microsoft-365/compliance/gdpr-dsr-azure). Para obter mais informações sobre GDPR, consulte [a seção GDPR do portal de confiança do serviço](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou seu portal do Azure de área de trabalho virtual do Windows, aqui estão alguns recursos que podem ajudá-lo:

- Confira nosso [Glossário](azure-monitor-glossary.md) para saber mais sobre os termos e conceitos relacionados ao Azure monitor para área de trabalho virtual do Windows.
- Se você encontrar um problema, confira nosso [Guia de solução de problemas](troubleshoot-azure-monitor.md) para obter ajuda.