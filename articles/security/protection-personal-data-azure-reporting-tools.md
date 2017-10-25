---
title: "Documentar a proteção de dados pessoais com ferramentas de relatório do Azure | Microsoft Docs"
description: "como usar as tecnologias e os serviços de relatório do Azure para ajudar a proteger a privacidade de dados pessoais."
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.openlocfilehash: 0ec9ceb63c3e1872e9815a7895b624276fc46123
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="document-protection-of-personal-data-with-azure-reporting-tools"></a>Documentar a proteção de dados pessoais com ferramentas de relatório do Azure

Este artigo abordará como usar as tecnologias e os serviços de relatório do Azure para ajudar a proteger a privacidade de dados pessoais.

## <a name="scenario"></a>Cenário

Uma empresa de cruzeiro de grande porte, com sede nos Estados Unidos, está expandindo suas operações para oferecer roteiros nos mares Mediterrâneo, Adriático e Báltico, bem como nas Ilhas Britânicas. Para ajudar nesses esforços, ela adquiriu várias linhas de cruzeiro menores localizadas na Itália, na Alemanha, na Dinamarca e no Reino Unido.

A empresa usa o Microsoft Azure para o processamento e armazenamento de dados corporativos. Eles incluem informações de identificação pessoal, como nomes, endereços, números de telefone e informações de cartão de crédito de sua base global de clientes. Também incluem informações tradicionais de Recursos Humanos, como endereços, números de telefone, números de identificação fiscal e outras informações sobre os funcionários da empresa em todas as localizações. A linha de cruzeiro também mantém um banco de dados grande de membros do programa de recompensa e fidelidade que inclui informações pessoais para acompanhamento das relações com os clientes atuais e anteriores.

Os funcionários corporativos acessam a rede de escritórios remotos da empresa e os agentes de viagem localizados no mundo todo têm acesso a alguns recursos da empresa.

## <a name="problem-statement"></a>Problema declarado

A empresa deve proteger a privacidade dos dados pessoais de clientes e funcionários por meio de uma estratégia de segurança de várias camadas que usa os recursos de gerenciamento e segurança do Azure para impor controles estritos no acesso e processamento de dados pessoais, bem como conseguir demonstrar suas medidas de proteção para auditores internos e externos.

## <a name="company-goal"></a>Meta da empresa

Como parte de sua estratégia de segurança de proteção avançada, uma das metas da empresa é acompanhar todo o acesso e processamento de dados pessoais e garantir que a documentação de proteções de privacidade adequadas para os dados pessoais estejam em vigor e funcionando.

## <a name="solutions"></a>Soluções

O Microsoft Azure fornece ferramentas abrangentes de monitoramento, log e diagnóstico para ajudar a acompanhar e registrar atividades e eventos associados ao acesso e processamento de dados pessoais, do fluxo geográfico de dados e do acesso de terceiros aos dados pessoais. Como a segurança dos dados pessoais na nuvem é uma responsabilidade compartilhada, a Microsoft também fornece aos clientes:

- Informações detalhadas sobre seu próprio processamento dos dados dos clientes

- Medidas de segurança administradas pela Microsoft

- O local para o qual envia os dados dos clientes e a forma como eles são enviados

- Detalhes do próprio processo de análises de privacidade da Microsoft

### <a name="azure-active-directory"></a>Azure Active Directory

O [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. As funcionalidades de entrada e relatórios de auditoria do serviço fornecem informações detalhadas das atividades de entrada e uso do aplicativo para ajudá-lo a monitorar e garantir o acesso apropriado aos dados pessoais dos clientes e funcionários.

Há dois tipos de relatórios de atividade:

- Os [logs/relatórios de atividades de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) fornecem um registro detalhado das tarefas/atividades do sistema

- O [log/relatório de atividades de entrada](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) mostra quem executou cada atividade listada no relatório de auditoria

Usando os dois juntos, você pode acompanhar o histórico de todas as tarefas executadas e quem executou cada uma. Ambos os tipos de relatórios são personalizáveis e podem ser filtrados.

#### <a name="how-do-i-access-the-audit-and-security-logs"></a>Como fazer para acessar os logs de auditoria e segurança?

Os logs de auditoria e segurança podem ser acessados no portal do Active Directory de três maneiras diferentes: por meio da seção **Atividade** (selecione **Logs de auditoria** ou **Entradas**) ou em **Usuários e grupos** ou **Aplicativos empresariais** em **Gerenciar** no Active Directory. Os relatórios também podem ser acessados por meio da API de relatório do Azure Active Directory. 

1. No portal do Azure, selecione **Azure Active Directory.**

2. Na seção **Atividade**, selecione **Logs de auditoria.**

    ![](media/protection-personal-data-azure-reporting-tools/image001.png)

3. Personalize a exibição de lista clicando em **Colunas** na barra de ferramentas.

4.  Selecione um item na exibição de lista para ver todos os detalhes disponíveis sobre ele.

    ![](media/protection-personal-data-azure-reporting-tools/image003.png)

Os relatórios do Azure Active Directory também incluem dois tipos de relatórios de segurança, **usuários sinalizados para risco** e **entradas de risco**, que podem ajudá-lo a monitorar riscos potenciais no ambiente do Azure.

Para obter mais informações sobre o serviço de relatório, consulte [Relatório do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)

Visite [Relatórios de atividades do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal#activity-reports) para informações mais específicas sobre os relatórios disponíveis no Azure Active Directory. Este site inclui mais detalhes sobre como acessar e usar os [relatórios de atividades de logs de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) e os [relatórios de atividades de entrada](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) no portal. Também inclui informações sobre os relatórios de segurança [usuários sinalizados para risco](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-user-at-risk) e [entradas de risco](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-risky-sign-ins).

Visite o site [Referência da API de auditoria do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference) para obter mais informações sobre como se conectar ao relatório do Azure Directory de forma programática.

### <a name="log-analytics"></a>Log Analytics

O [Log Analytics](https://azure.microsoft.com/services/log-analytics/) pode [coletar dados do Azure Monitor](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-storage) para correlacioná-los com outros dados e fornecer análise adicional. O Azure Monitor coleta e analisa os dados de monitoramento para o ambiente do Azure. 

Ferramentas de análise no Log Analytics, como pesquisas de logs, exibições e soluções, funcionam com todos os dados coletados, fornecendo uma análise centralizada de todo o ambiente. O Log Analytics pode agregar e analisar logs de Eventos do Windows, logs do IIS e Syslogs, que podem ajudar a detectar violações potenciais de dados pessoais que podem expor dados pessoais a usuários não autorizados.

#### <a name="how-do-i-use-log-analytics"></a>Como fazer para usar o Log Analytics?

Acesse o Log Analytics por meio do portal do OMS ou do portal do Azure, em qualquer navegador da Web. O Log Analytics inclui uma linguagem de consulta para recuperar e consolidar dados no repositório rapidamente. Crie e salve Pesquisas de Logs para analisar diretamente os dados no portal.

Para criar um espaço de trabalho do Log Analytics no portal do Azure, faça o seguinte:

1. Selecione **Log Analytics** na lista de serviços no Marketplace.

2. Selecione **Criar** e, em seguida, especifique o nome do espaço de trabalho do OMS, selecione sua assinatura, o grupo de recursos, o local e o tipo de preço.

3. Clique em **OK** para exibir uma lista dos espaços de trabalho.

4. Selecione um espaço de trabalho para ver seus detalhes.

    ![](media/protection-personal-data-azure-reporting-tools/image004.png)

Visite a [documentação do Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para saber mais sobre o serviço.

Visite o tutorial [Introdução a um espaço de trabalho do Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started) para criar um espaço de trabalho de avaliação e saber mais sobre os conceitos básicos de como usar o serviço.

Visite as seguintes páginas da Web para obter informações mais específicas sobre como se conectar para usar o Log Analytics com os logs descritos acima:

[Fontes de dados de logs de eventos do Windows no Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)

[Logs do IIS no Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs)

[Fontes de dados do Syslog no Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-syslog)

### <a name="azure-monitorazure-activity-log"></a>Azure Monitor/Log de Atividades do Azure 

O [Azure Monitor](https://azure.microsoft.com/services/monitor/) fornece logs e métricas de infraestrutura de nível básico para a maioria dos serviços do Microsoft Azure.
O monitoramento pode ajudá-lo a obter informações detalhadas sobre os aplicativos do Azure. O Azure Monitor se baseia na extensão de diagnóstico do Azure (Windows ou Linux) para coletar a maioria dos logs e métricas de nível do aplicativo. [O Log de Atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) é um dos recursos que podem ser exibidos com o Azure Monitor. Ele acompanha todas as chamadas à API e fornece uma grande quantidade de informações sobre as atividades que ocorrem no [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
Você pode pesquisar o Log de atividade (anteriormente chamado de Logs de auditoria ou operacionais) para obter informações sobre o recurso como visto pela infraestrutura do Azure. 

Embora muitas das informações registradas no Log de atividades sejam referentes ao desempenho e à integridade do serviço, há também informações relacionadas à proteção de dados. Usando o Log de Atividades, você pode determinar “o que, quem e quando” de qualquer operação de gravação (PUT, POST, DELETE) executada nos recursos em sua assinatura do Azure.

Por exemplo, ele fornece um registro quando um administrador exclui um grupo de segurança de rede, o que pode afetar a proteção de dados pessoais. As entradas do Log de atividades são armazenadas no Azure Monitor por 90 dias.

#### <a name="how-do-i-use-the-data-collected-by-azure-monitor"></a>Como fazer para usar os dados coletados pelo Azure Monitor?

Há várias maneiras de usar os dados no Log de atividades e outros recursos do Azure Monitor.

- Transmita os dados para outros locais em linha real.

- Armazene os dados por períodos mais longos do que os padrões, usando uma [conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction) e configurando uma política de retenção.

- Visualize os dados em gráficos, usando o [portal do Azure](https://azure.microsoft.com/features/azure-portal/), o [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), o [Microsoft PowerBI](https://powerbi.microsoft.com/) ou ferramentas de visualização de terceiros.

- Consulte os dados usando a API REST do Azure Monitor, comandos da CLI, cmdlets do [PowerShell](https://docs.microsoft.com/powershell/) ou o SDK do .NET.

Para começar a usar o Azure Monitor, selecione **Mais Serviços** no portal do Azure.

1. Role para baixo até **Monitor** na seção **Monitoramento e Gerenciamento**.

    ![](media/protection-personal-data-azure-reporting-tools/image005.png)

2.  O Monitor é aberto na exibição **Log de Atividades**.

    ![](media/protection-personal-data-azure-reporting-tools/image007.png)

Você pode criar e salvar consultas para os filtros comuns e fixar as consultas mais importantes em um painel do portal para que sempre saiba se ocorreram eventos que atendem aos seus critérios.

1. Filtre a exibição por grupo de recursos, intervalo de tempo e categoria de evento.

    ![](media/protection-personal-data-azure-reporting-tools/image008.png)

2. Em seguida, fixe consultas em um painel do portal clicando no botão **Fixar**. Isso ajuda a criar uma única fonte de informações para os dados operacionais em seus serviços. O nome da consulta e o número de resultados serão exibidos no painel.

Também use o Monitor para exibir métricas para todos os recursos do Azure, configurar configurações de diagnóstico e alertas e pesquisar o log. Para obter mais informações sobre como usar o Azure Monitor e o Log de Atividades, consulte [Introdução ao Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

### <a name="azure-diagnostics"></a>Diagnóstico do Azure 

A funcionalidade de diagnóstico do Azure permite a coleta de dados de várias fontes. Os logs de Eventos do Windows, que incluem o log de Segurança, podem ser especialmente úteis no acompanhamento e na documentação da proteção de dados pessoais. O log de segurança acompanha eventos de êxito e falha de logon, bem como alterações de permissões, detecção de padrões indicando determinados tipos de ataques, alterações nas políticas relacionadas à segurança, alterações na associação a um grupo de segurança e muito mais.

Por exemplo, a ID de Evento 4695 alerta a tentativa de remover a proteção de dados protegidos auditáveis. Isso se refere à DPAPI (API de Proteção de Dados), que ajuda a proteger dados como chaves privadas, credenciais armazenadas e outras informações confidenciais.

#### <a name="how-do-i-enable-the-diagnostics-extension-for-windows-vms"></a>Como fazer para habilitar a extensão de diagnóstico em VMs Windows?

Use o PowerShell para habilitar a extensão de diagnóstico em uma VM Windows, para coletar dados de log. As etapas para fazer isso dependem de qual modelo de implantação é usado (Resource Manager ou Clássico). Para habilitar a extensão de diagnóstico em uma VM existente criada por meio do modelo de implantação do Resource Manager, use o [cmdlet Set-AzureRMVMDiagnosticsExtension do PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension?view=azurermps-4.3.1).

   ![](media/protection-personal-data-azure-reporting-tools/image009.png)

*\$diagnosticsconfig_path* é o caminho para o arquivo que contém a configuração de diagnóstico em XML. Para obter instruções mais detalhadas sobre como habilitar o Diagnóstico do Azure em uma VM, consulte [Usar o PowerShell para habilitar o Diagnóstico do Azure em uma máquina virtual que executa o Windows.](https://docs.microsoft.com/azure/virtual-machines/windows/ps-extensions-diagnostics)

A extensão de diagnóstico do Azure pode transferir os dados coletados para uma conta de armazenamento do Azure ou enviá-los para serviços como o Application Insights. Em seguida, você pode usar os dados para auditoria.

#### <a name="how-do-i-store-and-view-diagnostic-data"></a>Como fazer para armazenar e exibir os dados de diagnóstico?

É importante lembrar de que os dados de diagnóstico não são armazenados permanentemente, a menos que sejam transferidos para o emulador de armazenamento do Microsoft Azure ou para o armazenamento do Azure. Para armazenar e exibir dados de diagnóstico no Armazenamento do Azure, siga estas etapas:

1. Especifique uma conta de armazenamento no arquivo ServiceConfiguration.cscfg. O Diagnóstico do Azure pode usar o serviço Blob ou o serviço Tabela, dependendo do tipo de dados. Os logs de Eventos do Windows são armazenados em formato de Tabela.

2. Transfira os dados. Solicite a transferência dos dados de diagnóstico por meio do arquivo de configuração. Para o SDK 2.4 e anterior, você também pode fazer a solicitação de forma programática.

3. Exiba os dados usando o [Gerenciador de Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer), o [Gerenciador de Servidores](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) no Visual Studio ou o [Gerenciador de Diagnóstico do Azure](https://www.cerebrata.com/products/azure-diagnostics-manager) no Azure Management Studio.

Para obter mais informações sobre como executar essas etapas, consulte [Armazenar e exibir dados de diagnóstico no Armazenamento do Azure.](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)

### <a name="azure-storage-analytics"></a>Análise do Armazenamento do Azure 

A análise de armazenamento registra informações detalhadas sobre solicitações bem-sucedidas e com falha para um serviço de armazenamento. Essas informações podem ser usadas para monitorar solicitações individuais, que podem ajudar na documentação do acesso aos dados pessoais armazenados no serviço. No entanto, o log da Análise de Armazenamento não está habilitado por padrão na conta de armazenamento. Habilite-o no portal do Azure.

#### <a name="how-do-i-configure-monitoring-for-a-storage-account"></a>Como fazer para configurar o monitoramento de uma conta de armazenamento?

Para configurar o monitoramento de uma conta de armazenamento, faça o seguinte:

1. Selecione **Contas de armazenamento** no portal do Azure e, em seguida, selecione o nome da conta que você deseja monitorar.

    ![](media/protection-personal-data-azure-reporting-tools/image011.png)

2. Na seção **Monitoramento**, selecione **Diagnóstico.**

3.  Selecione o **tipo** de dados de métricas que você deseja monitorar para cada serviço (Blob, Tabela, Arquivo). Para instruir o Armazenamento do Azure a salvar os logs de diagnóstico para ler, gravar e excluir solicitações para os serviços blob, tabela e fila, selecione **Logs de Blob, logs de Tabela** e **Logs de Fila.**

    ![](media/protection-personal-data-azure-reporting-tools/image013.png)

4. Usando o controle deslizante na parte inferior, defina a política de **retenção** em dias (valor de 1 a 365). O padrão é sete dias.

5. Selecione **Salvar** para aplicar as definições de configuração.

As entradas de log do Log de Armazenamento contêm as seguintes informações sobre solicitações individuais:

- Informações de tempo, como a hora de início, latência de ponta a ponta e latência de servidor.

- Detalhes da operação de armazenamento, como o tipo de operação, a chave do objeto de armazenamento acessada pelo cliente, êxito ou falha e o código de status HTTP retornado ao cliente.

- Detalhes de autenticação, como o tipo de autenticação usado pelo cliente.

- Informações de simultaneidade, como o valor de ETag e a última modificação do carimbo de data/hora.

- Os tamanhos das mensagens de solicitação e resposta.

Para obter instruções mais detalhadas sobre como habilitar o log da Análise de Armazenamento, consulte [Monitorar uma conta de armazenamento no portal do Azure.](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

### <a name="azure-security-center"></a>Central de Segurança do Azure 

A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) monitora o estado de segurança dos recursos do Azure para prevenir e detectar ameaças, além de fornecer recomendações de resposta. Ela fornece várias maneiras de ajudar a documentar medidas de segurança que protegem a privacidade de dados pessoais.

O monitoramento da integridade da segurança ajuda a garantir a conformidade com as políticas de segurança. O monitoramento de segurança é uma estratégia proativa que audita os recursos para identificar sistemas que não seguem os padrões ou as melhores práticas organizacionais. Monitore o estado de segurança dos seguintes recursos:

- Computação (máquinas virtuais e serviços de nuvem)

- Rede (redes virtuais)

- Armazenamento e dados (auditoria de servidor e banco de dados e detecção de ameaças, TDE, criptografia de armazenamento)

- Aplicativos (problemas potenciais de segurança)

Problemas de segurança em uma dessas categorias podem representar uma ameaça à privacidade de dados pessoais.

#### <a name="how-do-i-view-the-security-state-of-my-azure-resources"></a>Como fazer para exibir o estado de segurança de meus recursos do Azure?

A Central de Segurança analisa periodicamente o estado de segurança de seus recursos do Azure. Exiba as vulnerabilidades potenciais de segurança identificadas na seção **Prevenção** do painel.

   ![](media/protection-personal-data-azure-reporting-tools/image014.png)

1. Na seção **Prevenção**, selecione o bloco **Computação**. Nele, você verá uma **Visão geral**, juntamente com a listagem **Máquinas virtuais** de todas as VMs e seus estados de segurança, bem como a lista **Serviços de nuvem** de funções web e de trabalho monitoradas pela Central de Segurança.

2. Na guia **Visão geral**, destaque uma recomendação para exibir mais informações.

3. Na guia **Máquinas virtuais**, selecione uma VM para exibir detalhes adicionais.

Quando a coleta de dados é habilitada na Central de Segurança do Azure, o Microsoft Monitoring Agent é provisionado automaticamente em todas as máquinas virtuais existentes e novas com suporte implantadas. Os dados coletados por meio desse agente são armazenados em um espaço de trabalho existente do [Log Analytics](https://azure.microsoft.com/services/log-analytics/) associado à sua assinatura ou a um novo espaço de trabalho.

Os [Relatórios de Inteligência contra Ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) são fornecidos pela Central de Segurança. Eles oferecem informações úteis para ajudar a distinguir a identidade do invasor, os objetivos, as campanhas de ataque atuais e do histórico, bem como táticas, ferramentas e procedimentos usados. Informações de mitigação e correção também são incluídas.

A principal finalidade desses relatórios de ameaça é ajudá-lo a responder com eficácia à ameaça imediata e ajudar a tomar medidas para atenuar o problema. As informações dos relatórios também podem ser úteis ao documentar a resposta a incidentes para fins de relatório e auditoria.

Os Relatórios de Inteligência contra Ameaças são apresentados em formato .PDF, acessado por meio de um link no campo **Relatórios** da folha **Processo suspeito executado** de cada alerta de segurança na Central de Segurança do Azure.

Para obter mais informações sobre como exibir e usar o Relatório de Inteligência contra Ameaças, consulte [Relatório de Inteligência contra Ameaças da Central de Segurança do Azure.](https://docs.microsoft.com/azure/security-center/security-center-threat-report)

## <a name="next-steps"></a>Próximas etapas:

[Introdução à API de relatório do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

[O que é o Log Analytics?](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

[Visão geral do monitoramento no Microsoft Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

[Introdução ao Log de Atividades do Azure (vídeo)](https://azure.microsoft.com/resources/videos/intro-activity-log/)
