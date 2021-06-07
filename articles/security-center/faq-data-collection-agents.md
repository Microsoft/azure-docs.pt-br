---
title: Perguntas frequentes sobre a Central de Segurança do Azure - coleta de dados e agentes
description: Perguntas frequentes sobre a coleta de dados, agentes e workspaces da Central de Segurança do Azure, um produto que ajuda a evitar, detectar e responder a ameaças
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2020
ms.author: memildin
ms.openlocfilehash: 4ef01045d1ca16d0101cdd9ccfcd118231cd28de
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102456106"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Perguntas frequentes - perguntas sobre coleta de dados, agentes e workspaces

A Central de Segurança coleta dados das VMs (máquinas virtuais) do Azure, dos Conjuntos de Dimensionamento de Máquinas Virtuais, dos contêineres de IaaS e de computadores não Azure (inclusive nos locais) para monitorar as vulnerabilidades e ameaças de segurança. Os dados são coletados usando o agente do Log Analytics, que lê uma variedade de configurações e logs de eventos relacionados à segurança do computador e copia os dados para o seu workspace visando a análise.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Sou cobrado pelos logs do Azure Monitor nos workspaces criados pela Central de Segurança?

Não. Os workspaces criados pela Central de Segurança, embora sejam configurados para os logs do Azure Monitor por cobrança de nó, não incorrerão em encargos de logs do Azure Monitor. A cobrança da Central de Segurança sempre tem base em sua política de segurança da Central de Segurança e nas soluções instaladas em um workspace:

- **Azure defender desativado** – a central de segurança habilita a solução "SecurityCenterFree" no espaço de trabalho padrão. Você não será cobrado se o Azure defender estiver desativado.

- **Azure defender on** – a central de segurança habilita a solução de "segurança" no espaço de trabalho padrão.

Para obter detalhes de preço na sua moeda preferencial e de acordo com a sua região, confira [Preço da Central de Segurança](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> O tipo de preço do Log Analytics para workspaces criados pela Central de Segurança não afeta a cobrança da Central de Segurança.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-is-the-log-analytics-agent"></a>O que é o agente de Log Analytics?

Para monitorar vulnerabilidades de segurança e ameaças, a central de segurança do Azure depende do [agente de log Analytics](../azure-monitor/agents/log-analytics-agent.md) – esse é o mesmo agente usado pelo serviço de Azure monitor. 

O agente, às vezes, é chamado de Microsoft Monitoring Agent (ou "MMA"). 

O agente coleta vários detalhes de configuração relacionados à segurança e logs de eventos de computadores conectados e, em seguida, copia os dados para o espaço de trabalho do Log Analytics para análise posterior. Exemplos desses dados são: tipo e versão do sistema operacional, logs do sistema operacional (logs de eventos do Windows), processos em execução, nome do computador, endereços IP e usuário conectado.

Verifique se os computadores estão executando um dos sistemas operacionais com suporte para o agente, conforme descrito nas páginas a seguir:

* [Agente do Log Analytics para sistemas operacionais compatíveis com o Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

* [Agente do Log Analytics para sistemas operacionais compatíveis com o Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

Saiba mais sobre os [dados coletados pelo agente de log Analytics](security-center-enable-data-collection.md).




## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>O que qualifica uma VM para o provisionamento automático da instalação do agente do Log Analytics?

As VMs Windows ou Linux IaaS se qualificam se:

- Atualmente, a extensão do agente do Log Analytics não está instalada na máquina virtual.
- A VM está em estado de execução.
- O [Agente de Máquina Virtual do Azure](../virtual-machines/extensions/agent-windows.md) do Windows ou Linux está instalado.
- A máquina virtual não é usada como um dispositivo, como o firewall de aplicativo Web ou o firewall mais recente.


## <a name="where-is-the-default-log-analytics-workspace-created"></a>Onde o workspace do Log Analytics padrão é criado?

O local do workspace padrão depende da região do Azure:

- Para VMs nos Estados Unidos e no Brasil, o local do workspace é Estados Unidos
- Para VMs no Canadá, o local do workspace é Canadá
- Para VMs na Europa, o local do workspace é Europa
- Para VMs no Reino Unido, o local do workspace é Reino Unido
- Para VMs no Leste e no Sudeste da Ásia, o local do workspace é Ásia
- Para VMs na Coreia, o local do workspace é Coreia
- Para VMs na Índia, o local do workspace é Índia
- Para VMs no Japão, o local do workspace é Japão
- Para VMs na China, o local do workspace é China
- Para VMs na Austrália, o local do workspace é Austrália


## <a name="what-security-events-are-collected-by-the-log-analytics-agent"></a>Quais eventos de segurança são coletados pelo agente de Log Analytics?

Para obter uma lista completa dos eventos de segurança coletados pelo agente, consulte [quais tipos de eventos são armazenados para as configurações de eventos de segurança "comum" e "mínimo"?](security-center-enable-data-collection.md#what-event-types-are-stored-for-common-and-minimal).

> [!IMPORTANT]
> Observe que, para alguns serviços, como o Firewall do Azure, se você tiver habilitado o registro em log e escolhido um recurso informativo para registrar (por exemplo, definir o log como *detalhado*), poderá encontrar impactos significativos em suas necessidades de armazenamento log Analytics espaço de trabalho. 


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Posso excluir os workspaces padrão criados pela Central de Segurança?

**Não recomendamos a exclusão do workspace padrão.** A Central de Segurança usa os workspaces padrão para armazenar dados de segurança de suas VMs. Se você excluir um workspace, a Central de Segurança não poderá coletar esses dados, e algumas recomendações de segurança e alertas não estarão disponíveis.

Para recuperar, remova o agente do Log Analytics nas VMs conectadas ao workspace excluído. A Central de Segurança reinstala o agente e cria novos workspaces padrão.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Como posso usar meu espaço de trabalho do Log Analytics existente?

Você pode selecionar um espaço de trabalho do Log Analytics existente para armazenar dados coletados pela Central de Segurança. Para usar o espaço de trabalho do Log Analytics existente:

- O workspace deve ser associado à sua assinatura do Azure selecionada.
- No mínimo, você deve ter permissões de leitura para acessar o workspace.

Para selecionar um espaço de trabalho do Log Analytics existente:

1. No menu da Central de Segurança, selecione **Preço e configurações**.
1. Selecione a assinatura relevante.
1. Abra a página **provisionamento automático** .
1. Para o agente de Log Analytics, selecione **Editar configuração**. 

    :::image type="content" source="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png" alt-text="A configuração do agente de Log Analytics para usar ao usar a implantação automática" lightbox="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png":::

1. Selecione **conectar VMs do Azure a um espaço de trabalho diferente** e escolha seu espaço de trabalho existente.

    :::image type="content" source="./media/security-center-enable-data-collection/choose-workspace.png" alt-text="Selecionando um espaço de trabalho não padrão para o agente de Log Analytics reportar para" lightbox="./media/security-center-enable-data-collection/choose-workspace.png":::

    > [!TIP]
    > A lista inclui apenas espaços de trabalho aos quais você tem acesso e que estão em sua assinatura do Azure.

1. Clique em **Salvar**. Você será questionado se deseja reconfigurar as VMs monitoradas.

    - Selecione **Não** se quiser que as novas configurações de workspace sejam **aplicadas somente às novas VMs**. As novas configurações de workspace se aplicam somente a novas instalações de agente: VMs recém-descobertas que não têm o agente do Log Analytics instalado.
    - Selecione **Sim** se quiser que as novas configurações de workspace sejam **aplicadas a todas as VMs**. Além disso, todas as VMs conectadas a um workspace criado da Central de Segurança serão reconectadas ao novo workspace de destino.

    > [!NOTE]
    > Se você selecionar **Sim**, não exclua os workspaces criados pela Central de Segurança até que todas as VMs sejam reconectadas ao novo workspace de destino. Essa operação falhará se um workspace for excluído muito cedo.


## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>E se o agente do Log Analytics já foi instalado como extensão na VM?<a name="mmaextensioninstalled"></a>

Quando o Agente de Monitoramento foi instalado como extensão, a configuração de extensão permite gerar relatórios somente para um workspace. A Central de Segurança não substitui as conexões existentes nos workspaces do usuário. A Central de Segurança armazenará os dados de segurança de uma VM no workspace já conectado, desde que a solução "Security" ou "SecurityCenterFree" já tenha sido instalada. A Central de Segurança pode atualizar a versão da extensão para a mais recente nesse processo.

Para obter mais informações, confira [Provisionamento automático em casos de uma instalação de agente preexistente](security-center-enable-data-collection.md#preexisting).



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>E se o agente do Log Analytics foi instalado diretamente no computador, mas não como extensão (Agente Direto)?<a name="directagentinstalled"></a>

Se o agente do Log Analytics foi instalado diretamente na VM (mas não como extensão do Azure), a Central de Segurança instalará a extensão do agente do Log Analytics e poderá atualizá-lo para a versão mais recente.

O agente instalado continuará a gerar relatórios para os workspaces já configurados e também para o workspace configurado na Central de Segurança (há compatibilidade com a hospedagem múltipla em computadores Windows).

Se o espaço de trabalho configurado for um espaço de trabalho do usuário (não o espaço de trabalho padrão da central de segurança), você precisará instalar a solução "segurança" ou "SecurityCenterFree" nela para a central de segurança iniciar o processamento de eventos de VMs e computadores que se reportam a esse espaço de trabalho.

Em computadores Linux, a hospedagem múltipla do Agente não é compatível. Portanto, se uma instalação de agente existente for detectada, o provisionamento automático não ocorrerá e a configuração do computador não será alterada.

Em computadores existentes nas assinaturas integradas à Central de Segurança antes de 17 de março de 2019, quando um agente existente for detectado, a extensão do agente do Log Analytics não será instalada e o computador não será afetado. No caso desses computadores, confira a recomendação "Resolver problemas de integridade do agente de monitoramento nos seus computadores" para solucionar problemas de instalação de agente

Para saber mais, confira a próxima seção [O que acontece se um System Center Operations Manager ou agente direto do OMS já está instalado em minha VM?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>E se um agente do System Center Operations Manager já está instalado na minha VM?<a name="scomomsinstalled"></a>

A Central de Segurança instalará a extensão do agente do Log Analytics lado a lado com o agente do System Center Operations Manager existente. O agente existente continuará a gerar relatórios para o servidor do System Center Operations Manager normalmente. Observe que o agente do Operations Manager e o agente do Log Analytics compartilham bibliotecas comuns em tempo de execução, que serão atualizadas para a versão mais recente durante esse processo. Observação: se a versão 2012 do agente do Operations Manager foi instalada, não ative o provisionamento automático (os recursos de gerenciamento podem ser perdidos quando o servidor do Operations Manager também está na versão 2012).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Qual é o impacto da remoção dessas extensões?

Se você remover a Extensão de Monitoramento da Microsoft, a Central de Segurança não será capaz de coletar dados de segurança da VM e algumas recomendações de segurança e alertas não estarão disponíveis. Dentro de 24 horas, a Central de Segurança determinará que a VM não possui a extensão e reinstalará a extensão.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Como fazer para impedir a instalação automática do agente e a criação do workspace?

Você pode desligar o provisionamento automático para suas assinaturas na política de segurança, mas isso não é recomendado. Desativar o provisionamento automático limita os alertas e recomendações da central de segurança. Para desabilitar o provisionamento automático:

1. No menu da Central de Segurança, selecione **Preço e configurações**.
1. Selecione a assinatura relevante.
1. Se sua assinatura tiver o Azure defender habilitado, abra os **planos do Azure defender** e selecione **Azure defender desativado**.

    :::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Habilitar ou desabilitar o Azure defender":::

1. Na página **provisionamento automático** , selecione caneta e desative provisionamento automático na página  **política de segurança – coleta de dados** .

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Habilitar a implantação automática para o agente de Log Analytics":::

1. Clique em **Salvar**.


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Devo recusar a instalação do agente e a criação do workspace automáticas?

> [!NOTE]
> Reveja as seções [Quais são as implicações da recusa?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) e [etapas recomendadas para a recusa](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) se você optar por recusar o provisionamento automático.

Talvez seja ideal recusar o provisionamento automático nos seguintes casos:

- A instalação automática do agente pela Central de Segurança se aplica a toda a assinatura. Você não pode aplicar a instalação automática em um subconjunto de VMs. Se houver VMs essenciais que não podem ser instaladas com o agente do Log Analytics, você deverá recusar o provisionamento automático.
- A instalação da extensão do agente do Log Analytics atualiza a versão do agente. Isso se aplica a um agente direto e a um agente do System Center Operations Manager (neste último, o Operations Manager e o agente de Log Analytics compartilham bibliotecas de tempo de execução comuns, que serão atualizadas no processo). Se a versão do agente do Operations Manager instalada for 2012 e estiver atualizada, os recursos de gerenciamento poderão ser perdidos quando o servidor do Operations Manager também estiver na versão 2012. Pense em recusar o provisionamento automático se a versão do agente do Operations Manager instalada for 2012.
- Se você tiver um workspace personalizado fora da assinatura (um workspace centralizado), recuse o provisionamento automático. Você pode instalar a extensão do agente do Log Analytics manualmente e conectá-la ao workspace sem que a Central de Segurança substitua a conexão.
- Se você quiser evitar a criação de vários workspaces por assinatura e tiver seu próprio workspace personalizado na assinatura, terá duas opções:

   1. Você pode recusar o provisionamento automático. Após a migração, defina as configurações de espaço de trabalho padrão conforme descrito em [Como fazer para usar meu espaço de trabalho do Log Analytics existente?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. Ou você pode permitir que a migração seja concluída, que o agente do Log Analytics seja instalado nas VMs e que as VMs sejam conectadas ao workspace criado. Em seguida, selecione o seu próprio workspace personalizado definindo a configuração do workspace padrão com a opção de aceitar para reconfigurar os agentes já instalados. Para saber mais, confira [Como fazer para usar meu espaço de trabalho do Log Analytics existente?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Quais são as implicações de se recusar o provisionamento automático?

Quando a migração for concluída, a Central de Segurança não poderá coletar dados de segurança da VM e algumas recomendações de segurança e alertas não estarão disponíveis. Se você recusar, instale o agente do Log Analytics manualmente. Confira as [etapas recomendadas para a recusa](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Quais são as etapas recomendadas para a recusa do provisionamento automático?

Instale manualmente a extensão do agente do Log Analytics para que a Central de Segurança possa coletar dados de segurança das suas VMs e fazer recomendações e alertas. Consulte [Instalação do agente para VM do Windows](../virtual-machines/extensions/oms-windows.md) ou [Instalação do agente para VM do Linux](../virtual-machines/extensions/oms-linux.md) para obter orientações sobre a instalação.

Você pode conectar o agente a qualquer workspace personalizado existente ou a um workspace criado pela Central de Segurança. Se um espaço de trabalho personalizado não tiver as soluções "segurança" ou "SecurityCenterFree" habilitadas, será necessário aplicar uma solução. Para aplicar, selecione o workspace ou a assinatura personalizada e aplique uma camada de preços na página **Política de segurança – tipo de preço**.

:::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Habilitar ou desabilitar o Azure defender":::

A Central de Segurança habilitará a solução correta no workspace com base no tipo de preço selecionado.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Como fazer para remover extensões do OMS instaladas pela Central de Segurança?<a name="remove-oms"></a>

Você pode remover manualmente o agente do Log Analytics. Isso não é recomendado, pois limita as recomendações e os alertas da Central de Segurança.

> [!NOTE]
> Se a coleta de dados estiver habilitada, a Central de Segurança reinstalará o agente após a remoção.  Você precisa desabilitar a coleta de dados antes de remover manualmente o agente. Confira Como fazer para impedir a instalação automática do agente e a criação do workspace? para obter instruções sobre como desabilitar a coleta de dados.

Para remover manualmente o agente:

1.    No portal, abra o **Log Analytics**.

1.    Na página Log Analytics, selecione um workspace:

1.    Selecione as VMs que você não quer monitorar e selecione **Desconectar**.

   ![Remova o agente][3]

> [!NOTE]
> Se uma VM do Linux já tiver um agente do OMS, mas não como extensão, a remoção da extensão também removerá o agente, e você precisará reinstalá-lo.


## <a name="how-do-i-disable-data-collection"></a>Como desabilitar a coleta de dados?

O provisionamento automático é altamente recomendável a fim de obter alertas de segurança e recomendações sobre atualizações do sistema, vulnerabilidades do sistema operacional e proteção do ponto de extremidade. Por padrão, o provisionamento automático está desabilitado.

Se você o habilitou, mas agora deseja desabilitá-lo:

1. No [portal do Azure](https://portal.azure.com), abra **central de segurança** e selecione **preços e configurações**.

1. Selecione a assinatura na qual você deseja desabilitar o provisionamento automático.

1. Em **provisionamento automático**, desative a alternância para o agente de log Analytics.


## <a name="how-do-i-enable-data-collection"></a>Como habilitar a coleta de dados?

É possível habilitar a coleta de dados para suas assinaturas do Azure na Política de segurança. Para habilitar a coleta de dados. [Entre no portal do Azure](https://portal.azure.com), selecione **Procurar**, **Central de Segurança** e **Política de segurança**. Selecione a assinatura em que você deseja habilitar o provisionamento automático. Quando você seleciona uma assinatura **Política de segurança - coleta de dados** é exibido. Em **Provisionamento automático**, selecione **Habilitado**.


## <a name="what-happens-when-data-collection-is-enabled"></a>O que acontece quando a coleta de dados é habilitada?

Quando o provisionamento automático estiver habilitado, a Central de Segurança provisionará o agente do Log Analytics em todas as VMs do Azure compatíveis, bem como naquelas que forem criadas. O provisionamento automático é recomendável, mas a instalação manual do agente também está disponível. [Saiba como instalar a extensão do agente do Log Analytics](../azure-monitor/vm/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

O agente habilita o evento de criação de processo 4688 e o campo *CommandLine* dentro do evento 4688. Novos processos criados na VM são registrados pelo log de eventos e monitorados pelos serviços de detecção da Central de Segurança. Para obter mais informações sobre os detalhes registrados para cada novo processo, consulte [Campos de descrição no 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). O agente também coleta os eventos 4688 criados na VM e os armazena na pesquisa.

O agente também habilita a coleta de dados para [Controles de Aplicativos Adaptáveis](security-center-adaptive-application.md), a Central de Segurança configura uma política do AppLocker local no modo de Auditoria para permitir todos os aplicativos. Essa política fará com que o AppLocker gere eventos que são coletados e aproveitados pela Central de Segurança. É importante observar que essa política não será configurada em computadores nos quais já houver uma política do AppLocker configurada. 

Quando a Central de Segurança detecta atividade suspeita na VM, o cliente é notificado por email caso [informações de contato de segurança](security-center-provide-security-contact-details.md) tenham sido fornecidas. Um alerta também fica visível no painel de alertas de segurança da Central de Segurança.


## <a name="will-security-center-work-using-an-oms-gateway"></a>A Central de Segurança funcionará usando um gateway do OMS?

Sim. A Central de Segurança do Azure utiliza o Azure Monitor para coletar dados das VMs e servidores do Azure, usando o agente do Log Analytics.
Para coletar os dados, cada VM e servidor deve ser conectado à Internet usando HTTPS. A conexão pode ser direta, usando um proxy, ou por meio do [Gateway OMS](../azure-monitor/agents/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>O Monitoring Agent afeta o desempenho dos meus servidores?

O agente consome uma quantidade nominal de recursos do sistema e devem causar pouco impacto sobre o desempenho. Para obter mais informações sobre o impacto no desempenho, bem como o agente e a extensão, consulte o [guia de planejamento e operações](security-center-planning-and-operations-guide.md#data-collection-and-storage).




<!--Image references-->
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png