---
title: Perguntas Frequentes do Log Analytics | Microsoft Docs
description: "Respostas para perguntas frequentes sobre o serviço Azure Log Analytics."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: magoedte
ms.openlocfilehash: 0b27386cd0f9f3ae50314b8c5d7708aea3e3d028
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-faq"></a>Perguntas frequentes do Log Analytics
Essas Perguntas frequentes da Microsoft são uma lista de perguntas frequentes sobre o Log Analytics no Microsoft Azure. Se você tiver alguma pergunta adicional sobre o Log Analytics, vá para o [fórum de discussão](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e poste suas perguntas. Quando uma pergunta for frequente, ela será adicionada a este artigo para que possa ser encontrada com rapidez e facilidade.

## <a name="general"></a>Geral

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>P. O Log Analytics usa o mesmo agente da Central de Segurança do Azure?

R. No início de junho de 2017, a Central de Segurança do Azure começou a usar o Microsoft Monitoring Agent para coletar e armazenar dados. Para saber mais, veja [Perguntas frequentes sobre a migração da Plataforma Central de Segurança do Azure](../security-center/security-center-platform-migration-faq.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>P. Quais verificações são executadas pelas soluções AD e Avaliação do SQL?

R. A consulta a seguir mostra uma descrição de todas as verificações executadas no momento:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Os resultados podem então ser exportados para o Excel para análise adicional.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>P: Por que vejo algo diferente do OMS no console do System Center Operations Manager?

R: Dependendo do Pacote Cumulativo de Atualizações do Operations Manager em que você estiver, poderá ver um nó para *System Center Advisor*, *Insights Operacionais* ou *Log Analytics*.

A atualização de cadeia de caracteres de texto para *OMS* está incluída em um pacote de gerenciamento, que deve ser importado manualmente. Para ver o texto e a funcionalidade atuais, siga as instruções no artigo mais recente do KB sobre o Pacote Cumulativo de Atualizações do System Center Operations Manager e atualize o console.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>P: Há uma versão local do Log Analytics?

R: não. O Log Analytics é um serviço de nuvem escalonável que processa e armazena grandes quantidades de dados. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>P. Como fazer para solucionar problemas se o Log Analytics não está mais coletando dados?

R: Se você estiver no tipo de preço gratuito e tiver enviado mais de 500 MB de dados em um dia, a coleta de dados é interrompida para o restante do dia. Alcançar o limite diário é um motivo comum para o Log Analytics parar de coletar dados ou para dados parecerem estar ausentes.

O Log Analytics cria um evento de tipo *Operação* quando a coleta de dados inicia e para. 

Execute a seguinte consulta na pesquisa para verificar se você está alcançando o limite diário e perdendo dados: `Type=Operation OperationCategory="Data Collection Status"`

Quando a coleta de dados é interrompida, o *OperationStatus* torna-se **Aviso**. Quando a coleta de dados inicia, o *OperationStatus* é **Bem-sucedido**. 

A tabela a seguir descreve os motivos pelos quais a coleta de dados é interrompida e uma ação é sugerida para retomar a coleta de dados:

| Motivo para a coleta de dados ser interrompida                       | Para retomar a coleta de dados |
| -------------------------------------------------- | ----------------  |
| Limite diário de dados gratuito atingido<sup>1</sup>       | Aguarde até o dia seguinte para a coleta reiniciar automaticamente ou<br> Mude para um tipo de preço pago |
| Assinatura do Azure está em um estado suspenso devido a: <br> A avaliação gratuita terminou <br> O Azure Pass expirou <br> Limite de gastos mensal atingido (por exemplo, em uma assinatura do MSDN ou do Visual Studio)                          | Converter para uma assinatura paga <br> Converter para uma assinatura paga <br> Remova o limite ou espere o limite ser redefinido |

<sup>1</sup> Se o seu espaço de trabalho estiver no tipo de preço gratuito, você estará limitado a enviar 500 MB de dados por dia para o serviço. Ao alcançar o limite diário, a coleta de dados será interrompida até o próximo dia. Dados enviados enquanto a coleta de dados está interrompida não são indexados e não ficam disponíveis para pesquisa. Quando a coleta de dados é retomada, o processamento ocorre apenas para os novos dados enviados. 

O Log Analytics usa o horário UTC e cada dia inicia à meia-noite UTC. Se o espaço de trabalho atingir o limite diário, o processamento será retomado durante a primeira hora do dia seguinte UTC.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>P. Como posso ser notificado quando a coleta de dados for interrompida?

R: Use as etapas descritas em [criar uma regra de alerta](log-analytics-alerts-creating.md#create-an-alert-rule) para ser notificado quando a coleta de dados for interrompida.

Ao criar o alerta para quando a coleta de dados for interrompida, defina:
- **Nome** como *Coleta de dados interrompida*
- **Gravidade** como *Aviso*
- **Consulta de pesquisa** como `Type=Operation OperationCategory="Data Collection Status" OperationStatus=Warning`
- **Janela de tempo** como *2 Horas*.
- **Frequência de alerta** como uma hora, pois os dados de uso só são atualizados uma vez por hora.
- **Gerar alerta com base em** como *número de resultados*
- **Número de resultados** como *Maior que 0*

Use as etapas descritas em [Adicionar ações a regras de alerta](log-analytics-alerts-actions.md) para configurar uma ação de runbook, webhook ou email para a regra de alerta.


## <a name="configuration"></a>Configuração
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>P. Posso alterar o nome do contêiner de blob/tabela usado para ler do WAD (Diagnóstico do Azure)?

R. Não, não é possível ler de contêineres nem de tabelas arbitrárias no armazenamento do Azure no momento.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>P. Quais endereços IP é o serviço Log Analytics usa? Como fazer para garantir que meu firewall permita apenas tráfego para o serviços Log Analytics?

R. O serviço Log Analytics é baseado no Azure. Os endereços IP do Log Analytics estão nos [Intervalos IP do Datacenter do Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=41653).

Conforme as implantações de serviço são feitas, os endereços IP reais do serviço de Log Analytics mudam. Os nomes DNS a permitir através do firewall estão documentados em [Definir as configurações de proxy e firewall no Log Analytics](log-analytics-proxy-firewall.md).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>P. Eu uso o ExpressRoute para me conectar ao Azure. O meu tráfego do Log Analytics usará minha conexão ExpressRoute?

R. Os diferentes tipos de tráfego de ExpressRoute estão descritos na [Documentação de ExpressRoute](../expressroute/expressroute-faqs.md#supported-services).

O tráfego para o Log Analytics usa o circuito de ExpressRoute de emparelhamento público.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>P. Há alguma maneira simples e fácil de mover um espaço de trabalho existente do Log Analytics para outra assinatura do Azure/espaço de trabalho do Log Analytics?

R. O cmdlet `Move-AzureRmResource` permite que você mova um espaço de trabalho do Log Analytics, além de uma conta de automação de uma assinatura do Azure para outra. Para obter mais informações, consulte [Move-AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

Essa alteração também pode ser feita no portal do Azure.

Você não pode mover dados de um espaço de trabalho do Log Analytics para outro, ou alterar a região na qual os dados do Log Analytics estão armazenados.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>P: Como fazer para adicionar o Log Analytics para o System Center Operations Manager?

R: Atualizar para o pacote cumulativo de atualizações mais recente e importar pacotes de gerenciamento permitem conectar o Operations Manager ao Log Analytics.

>[!NOTE]
>A conexão do Operations Manager ao Log Analytics só está disponível para o System Center Operations Manager 2012 SP1 e posterior.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>P: como posso confirmar que um agente consiga se comunicar com o Log Analytics?

R: Para garantir que o agente possa se comunicar com o OMS, vá para: Painel de Controle, Segurança e Configurações, **Microsoft Monitoring Agent**.

Sob a guia **Azure Log Analytics (OMS)** , procure por uma marca de seleção verde. Um ícone de marca de seleção verde confirma que o agente é capaz de se comunicar com o serviço do Azure.

Um ícone de aviso amarelo significa que o agente está tendo problemas de comunicação com o Log Analytics. Uma motivo comum é que o serviço Microsoft Monitoring Agent foi interrompido. Use o gerenciador de controle de serviço para reiniciar o serviço.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>P: Como fazer para impedir que um agente de se comunicar com o Log Analytics?

R: No System Center Operations Manager, remova o computador da lista de computadores gerenciados do OMS. O Operations Manager atualiza a configuração do agente para não relatar mais para o Log Analytics. Para agentes conectados diretamente ao Log Analytics, você pode impedi-los de comunicar-se por meio de: Painel de Controle, Segurança e Configurações, **Microsoft Monitoring Agent**.
Em **Azure Log Analytics (OMS)**, remova todos os espaços de trabalho listados.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>P: Por que estou recebendo um erro ao tentar mover meu espaço de trabalho de uma assinatura do Azure para outra?

R: Se você estiver usando o portal do Azure, certifique-se de que apenas o espaço de trabalho seja selecionado para ser movido. Não selecione as soluções – elas serão movidas automaticamente depois do espaço de trabalho. 

Verifique se você tem permissão em ambas as assinaturas do Azure.

## <a name="agent-data"></a>Dados do agente
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>P. Que quantidade de dados posso enviar por meio do agente para o Log Analytics? Existe uma quantidade máxima de dados por cliente?
R. R. O plano gratuito define um limite diário de 500 MB por espaço de trabalho. Os planos standard e premium não têm limite para a quantidade de dados carregados. Como um serviço de nuvem, o Log Analytics é projetado para ser escalado verticalmente de modo automático para lidar com o volume recebido de um cliente, mesmo que sejam terabytes por dia.

O agente do Log Analytics foi projetado para garantir que ele tenha uma superfície pequena. Um de nossos clientes escreveu um blog sobre os testes que ele executou em nosso agente e quão impressionado ele ficou. O volume de dados varia de acordo com as soluções que você habilita. Você pode encontrar informações detalhadas sobre o volume de dados e ver a divisão por solução na página [Uso](log-analytics-usage.md).

Para obter mais informações, você pode ler um [blog do cliente](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) sobre as dimensões reduzidas do agente OMS.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>P. Quanta largura de banda de rede é usada pelo MMA (Agente de Gerenciamento da Microsoft) ao enviar dados para o Log Analytics?

R. A largura de banda é uma função na quantidade de dados enviados. Dados são compactados conforme eles são enviados pela rede.

### <a name="q-how-much-data-is-sent-per-agent"></a>P. Qual a quantidade de dados enviada por agente?

R. A quantidade de dados enviados por agente depende:

* Das soluções que você habilitou
* Do número de logs e contadores de desempenho sendo coletados
* Do volume de dados nos logs

O tipo de preço gratuito é uma boa maneira de integrar vários servidores e medir o volume de dados típico. O uso geral é mostrado na página [Uso](log-analytics-usage.md) .

Para computadores capazes de executar o agente WireData, use a seguinte consulta para ver quantos dados estão sendo enviados:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Log Analytics](log-analytics-get-started.md) para saber mais sobre o Log Analytics e colocá-lo em funcionamento em minutos.
