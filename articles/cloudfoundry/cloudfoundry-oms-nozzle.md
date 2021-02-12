---
title: Implantar o bocal do Azure Log Analytics para monitoramento de Cloud Foundry
description: Orientações passo a passo sobre a implantação do bocal do agregador de logs do Cloud Foundry para o Azure Log Analytics. Use o bocal para monitorar as métricas de desempenho e de integridade de sistema do Cloud Foundry.
services: virtual-machines-linux
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: fde0afcd37cd464b0b87e5ccd257d4a7a684eeb0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021581"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Implantar o Bocal do Azure Log Analytics para Monitoramento do Sistema do Cloud Foundry

[Azure monitor](https://azure.microsoft.com/services/log-analytics/) é um serviço no Azure. Ele ajuda a coletar e analisar dados gerados em ambientes locais e em nuvem.

O bocal de Log Analytics (o bocal) é um componente de Cloud Foundry (CF), que encaminha as métricas da [Cloud Foundry agregador](https://docs.cloudfoundry.org/loggregator/architecture.html) firehose para logs de Azure monitor. Com o Bocal, é possível coletar, exibir e analisar a integridade do sistema e as métricas de desempenho do CF em várias implantações.

Neste documento, você aprenderá a implantar o bocal no ambiente do CF e, em seguida, acessar os dados no console de logs de Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

As etapas a seguir são pré-requisitos para implantar o Bocal.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. implantar um ambiente de Cloud Foundry do CF ou Pivotal no Azure

É possível usar o Bocal com uma implantação de software livre do CF ou com uma implantação do PCF (Pivotal Cloud Foundry).

* [Implantar o Cloud Foundry no Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Implantar o Pivotal Cloud Foundry no Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. instalar as ferramentas de linha de comando do CF para implantar o bocal

O Bocal é executado como um aplicativo no ambiente CF. Você precisa da CLI do CF para implantar o aplicativo.

O Bocal também precisa de permissão de acesso para o firehose do agregador de logs e para o controlador da nuvem. Para criar e configurar o usuário, você precisa do serviço UAA (Conta e Autenticação do Usuário).

* [Instalar a CLI do Cloud Foundry](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Instalar o cliente da linha de comando do UAA do Cloud Foundry](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Antes de configurar o cliente de linha de comando UAA, verifique se o RubyGems está instalado.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. criar um espaço de trabalho Log Analytics no Azure

É possível criar o espaço de trabalho do Log Analytics manualmente ou usando um modelo. O modelo implantará uma configuração de exibições de KPI pré-configuradas e alertas para o console de logs de Azure Monitor. 

#### <a name="to-create-the-workspace-manually"></a>Para criar o workspace manualmente:

1. Na portal do Azure, pesquise a lista de serviços no Azure Marketplace e, em seguida, selecione Log Analytics espaços de trabalho.
2. Selecione **Criar** e, em seguida, selecione opções para os seguintes itens:

   * **Espaço de Trabalho do Log Analytics**: digite um nome para o espaço de trabalho.
   * **Assinatura**: se você tiver várias assinaturas, escolha a mesma que a da sua implantação do CF.
   * **Grupo de recursos**: é possível criar um novo grupo de recursos ou usar o mesmo com a implantação do CF.
   * **Local**: insira o local.
   * **Tipo de preço**: selecione **OK** para concluir.

Para obter mais informações, confira [Introdução aos logs do Azure Monitor](../azure-monitor/overview.md).

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Para criar o espaço de trabalho do Log Analytics por meio do modelo de monitoramento do Azure Marketplace:

1. Abra o portal do Azure.
1. Clique no sinal "+" ou em "Criar um recurso" no canto superior esquerdo.
1. Digite "Cloud Foundry" na janela de pesquisa, selecione "Solução de Monitoramento Cloud Foundry".
1. A página inicial do modelo da solução de monitoramento Cloud Foundry é carregada, clique em “Criar” para iniciar a folha do modelo.
1. Insira os parâmetros necessários:
    * **Assinatura**: selecione uma assinatura do Azure para o espaço de trabalho do Log Analytics, normalmente a mesmo com a implantação do Cloud Foundry.
    * **Grupo de recursos**: selecione um grupo de recursos existente ou crie um novo para o espaço de trabalho do Log Analytics.
    * **Localização do Grupo de Recursos**: selecione a localização do grupo de recursos.
    * **OMS_Workspace_Name**: insira um nome de workspace. Se o workspace não existir, o modelo criará um novo.
    * **OMS_Workspace_Region**: selecione a localização para o workspace.
    * **OMS_Workspace_Pricing_Tier**: selecione o SKU do espaço de trabalho do Log Analytics. Confira as [diretrizes de preços](https://azure.microsoft.com/pricing/details/log-analytics/) para referência.
    * **Termos legais**: clique em Termos legais e clique em “Criar” para aceitar o termo legal.
1. Depois que todos os parâmetros forem especificados, clique em "Criar" para implantar o modelo. Quando a implantação for concluída, o status será exibido na guia de notificação.


## <a name="deploy-the-nozzle"></a>Implantar o Bocal

Há algumas maneiras diferentes para implantar o Bocal: como um bloco PCF ou como um aplicativo CF.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Implantar o Bocal como um bloco Ops Manager do PCF

Siga as etapas para [instalar e configurar o Bocal do Azure Log Analytics para PCF](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Essa é a abordagem simplificada, o bloco PCF Ops manager configurará e efetuará o push do bocal automaticamente. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Implantar o Bocal manualmente como um aplicativo CF

Se você não estiver usando o Ops Manager do PCF, implante o Bocal como um aplicativo. As seções a seguir descrevem esse processo.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Entre na implantação do CF como administrador por meio da CLI do CF

Execute o comando a seguir:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" é o nome de domínio do CF. É possível recuperá-lo procurando por "SYSTEM_DOMAIN" no arquivo de manifesto de implantação no CF. 

"CF_User" é o nome do administrador do CF. É possível recuperar o nome e a senha procurando a seção "scim", o nome e o "cf_admin_password" no arquivo de manifesto de implantação do CF.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Criar um usuário do CF e conceder as permissões necessárias

Execute os seguintes comandos:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" é o nome de domínio do CF. É possível recuperá-lo procurando por "SYSTEM_DOMAIN" no arquivo de manifesto de implantação no CF.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Baixar a última versão do Bocal do Log Analytics

Execute o comando a seguir:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente

Agora é possível definir variáveis de ambiente no arquivo manifest.yml em seu diretório atual. O exemplo a seguir mostra o manifesto do aplicativo do Bocal. Substitua os valores por suas informações específicas de espaço de trabalho do Log Analytics.

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Azure Monitor logs. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Azure Monitor logs. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Azure Monitor logs. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Azure Monitor logs as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Azure Monitor logs. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Efetuar push do aplicativo no seu computador de desenvolvimento

Certifique-se de que você está na pasta oms-log-analytics-firehose-nozzle. Execute o comando a seguir:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Validar a instalação do Bocal

### <a name="from-apps-manager-for-pcf"></a>No Gerenciador de Aplicativos (para o PCF)

1. Entre no Ops Manager e certifique-se de que o bloco é exibido no painel de instalação.
2. Entre no Gerenciador de Aplicativos, certifique-se de que o espaço criado para o bocal aparece no relatório de uso e confirme que o status é normal.

### <a name="from-your-development-computer"></a>No seu computador de desenvolvimento

Na janela da CLI do CF, digite:
```
cf apps
```
Verifique se o aplicativo do Bocal do OMS está em execução.

## <a name="view-the-data-in-the-azure-portal"></a>Exibir os dados no Portal do Azure

Se você tiver implantado a solução de monitoramento por meio do modelo do Marketplace, acesse o Portal do Azure e encontre a solução. Você pode encontrar a solução no grupo de recursos especificado no modelo. Clique na solução, navegue até o "console do log Analytics", as exibições pré-configuradas são listadas, com os principais KPIs do sistema Cloud Foundry, dados do aplicativo, alertas e métricas de integridade da VM. 

Se você tiver criado o espaço de trabalho do Log Analytics manualmente, siga as etapas abaixo para criar exibições e alertas:

### <a name="1-import-the-oms-view"></a>1. importar a exibição do OMS

No portal do OMS, navegue até o **Designer de exibição**  >  **importar**  >  **procurar** e selecione um dos arquivos omsview. Por exemplo, selecione *Cloud Foundry.omsview* e salve a exibição. Agora um bloco é exibido na página **Visão Geral** do OMS. Selecione-a para ver as métricas visualizadas.

É possível personalizar essas exibições ou criar novas por meio do **Designer de exibição**.

O *"Cloud Foundry.omsview"* é uma versão prévia do modelo de exibição do OMS do Cloud Foundry. Este é um modelo padrão totalmente configurado. Se você tiver sugestões ou comentários sobre o modelo, envie-os para a [seção de problemas](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. criar regras de alerta

É possível [criar os alertas](../azure-monitor/platform/alerts-overview.md) e personalizar as consultas e os valores limite conforme necessário. A seguir estão os alertas recomendados:

| Consulta de pesquisa                                                                  | Gerar alerta com base em | Description                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Número de resultados < 1   | **bbs.Domain.cf-apps** indica se o domínio cf-aps está atualizado. Isso significa que as solicitações de aplicativo do CF enviadas pelo Cloud Controller estão sincronizadas com o bbs.LRPsDesired (AIs recomendadas para Diego) para execução. Nenhum dado recebido significa que o domínio cf-apps não está atualizado na janela de tempo especificada. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Número de resultados > 0   | Para células Diego, 0 significa íntegro e 1 significa não íntegro. Defina o alerta se várias células Diego não íntegras forem detectadas na janela de tempo especificada. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Número de resultados > 0 | 1 significa que o sistema é íntegro e 0 significa que o sistema é não íntegro. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Número de resultados > 0   | Consul emite o status de integridade periodicamente. 0 significa que o sistema é íntegro e 1 significa que emissor de rota detectou que o Consul está inoperante. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | Número de resultados > 0 | O número delta de mensagens intencionalmente removidas pelo Doppler em razão da pressão de retorno. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Número de resultados > 0   | O Agregador de Logs emite **LGR** para indicar problemas com o processo de registro. Um exemplo desse problema ocorre quando a saída de mensagem de log é muito alta. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Número de resultados > 0   | Quando o bocal recebe um alerta de consumidor lento do agregador, ele envia o **valuemetric slowconsumeralert** ao para logs de Azure monitor. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Número de resultados > 0   | Se o número delta de eventos perdidos atingir um limite, isso significará que o bocal terá problemas de execução. |

## <a name="scale"></a>Dimensionar

É possível dimensionar o Bocal e o agregador de logs.

### <a name="scale-the-nozzle"></a>Dimensionar o bocal

Você deve iniciar com pelo menos duas instâncias do Bocal. O firehose distribui a carga de trabalho em todas as instâncias do Bocal.
Para certificar-se de que o Bocal pode acompanhar o tráfego de dados do firehouse, configure o alerta **slowConsumerAlert** (listado na seção anterior, "Criar regras de alerta"). Após ter sido alertado, execute as [diretrizes para Bocal lento](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) para determinar se o dimensionamento é necessário.
Para escalar verticalmente o Bocal, use o Gerenciador de Aplicativos ou a CLI do CF para aumentar a quantidade de instâncias ou os recursos de disco ou de memória do Bocal.

### <a name="scale-the-loggregator"></a>Dimensionar o agregador de logs

O Agregador de Logs envia uma mensagem de log **LGR** para indicar problemas com o processo de registro. É possível monitorar o alerta para determinar se o agregador de logs precisa ser escalado verticalmente.
Para escalar verticalmente o agregador de logs, aumente o tamanho do buffer do Doppler ou adicione mais instâncias de servidor do Doppler no manifesto do CF. Para obter mais informações, consulte [as diretrizes para dimensionar o agregador de logs](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Atualizar

Para atualizar o Bocal com uma versão mais recente, baixe a versão do novo Bocal, siga as etapas na seção “Implantar o Bocal” anterior e efetue push do aplicativo novamente.

### <a name="remove-the-nozzle-from-ops-manager"></a>Remover o Bocal do Ops Manager

1. Entre no Ops Manager.
2. Localize o **Microsoft Azure log Analytics bocal para** o bloco PCF.
3. Selecione o ícone de lixeira e confirme a exclusão.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Remover o Bocal do seu computador de desenvolvimento

Na janela da CLI do CF, digite:
```
cf delete <App Name> -r
```

Se você remover o Bocal, os dados no Portal do OMS não serão removidos automaticamente. Ele expira com base na configuração de retenção de logs de Azure Monitor.

## <a name="support-and-feedback"></a>Suporte e comentários

O Bocal do Azure Log Analytics é de software livre. Envie suas dúvidas e comentários para a [seção GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Para abrir uma solicitação de suporte do Azure, escolha “Máquina virtual que executa o Cloud Foundry” como a categoria de serviço. 

## <a name="next-step"></a>Próxima etapa

No PCF 2.0, as métricas de desempenho da VM são transferidas para o Azure Log Analytics bocal pelo encaminhador de métricas do sistema e integradas ao espaço de trabalho Log Analytics. Você não precisa mais do agente do Log Analytics para as métricas de desempenho da VM. No entanto ainda pode usar o agente do Log Analytics para coletar informações de Syslog. O agente do Log Analytics é instalado como um complemento Bosh nas VMs do seu CF. 

Para saber mais, consulte [Implantar o agente do Log Analytics na implantação do Cloud Foundry](https://github.com/Azure/oms-agent-for-linux-boshrelease).