---
title: (PRETERIDO) Monitorar cluster DC/OS do Azure - Gerenciamento de Operações
description: Monitorar um cluster DC/OS do Serviço de Contêiner do Azure com Log Analytics.
services: container-service
author: keikhara
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 290141136672729060f5156d645c47ac303fa0c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810096"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(PRETERIDO) Monitorar um cluster DC/OS do Serviço de Contêiner do Azure com Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

O Log Analytics é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda você a gerenciar e proteger sua infraestrutura local e de nuvem. A Solução de Contêiner é uma solução do Log Analytics, que ajuda você a exibir o inventário, o desempenho e os logs de contêineres em um único local. É possível realizar auditoria e solucionar problemas de contêineres vendo os logs em um local centralizado, e localizar contêineres com ruídos e excesso de consumo em um host.

![](media/container-service-monitoring-oms/image1.png)

Para saber mais sobre a Solução de Contêiner, veja a [Solução de Contêiner do Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Configuração do Log Analytics a partir do universo DC/OS


Este artigo pressupõe que você configurou um DC/OS e implantou aplicativos de contêiner da Web simples no cluster.

### <a name="pre-requisite"></a>Pré-requisito
- [Assinatura do Microsoft Azure](https://azure.microsoft.com/free/) - você pode obter uma assinatura gratuitamente.  
- Configuração do espaço de trabalho do Log Analytics - consulte a "Etapa 3" abaixo
- [CLI do DC/OS](https://docs.mesosphere.com/1.12/cli) instalada.

1. No painel do DC/OS, clique no Universo e procure por ‘OMS’ conforme mostrado abaixo.

   >[!NOTE]
   >O OMS agora é chamado de Log Analytics.

   ![](media/container-service-monitoring-oms/image2.png)

2. Clique em **Instalar**. Você verá um pop-up com as informações de versão e um botão **Instalar Pacote** ou **Instalação Avançada**. Ao clicar em **Instalação Avançada**, você será levado até a página **Propriedades de configuração específicas do OMS**.

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Aqui, você receberá uma solicitação para inserir a `wsid` (a ID do espaço de trabalho do Log Analytics) e `wskey` (a chave primária para a ID do espaço de trabalho). Para obter `wsid` e `wskey`, você precisa criar uma conta em <https://mms.microsoft.com>.
   Siga as etapas a seguir para criar uma conta. Depois de criar a conta, você precisará obter o `wsid` e o `wskey` clicando em **Configurações**, em seguida, **Fontes Conectadas** e, em seguida, em **Servidores Linux**, conforme mostrado abaixo.

   ![](media/container-service-monitoring-oms/image5.png)

4. Selecione o número de instâncias que você deseja e clique no botão "Revisar e Instalar". Normalmente, convém ter uma quantidade de instâncias igual à quantidade de VMs existentes no cluster do agente. O Agente do Log Analytics para Linux é instalado como contêineres individuais em cada VM da qual deseja coletar informações para monitoramento e registro em log de informações.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Como configurar um painel simples do Log Analytics

Depois de instalar o agente do Log Analytics para Linux nas VMs, a próxima etapa é configurar o painel do Log Analytics. Você pode configurar o painel por meio do portal do Azure.

### <a name="azure-portal"></a>Portal do Azure 

Entrar no portal do Azure em <https://portal.microsoft.com/>. Acesse **Marketplace**, selecione **Monitoramento + gerenciamento** e clique em **Ver Tudo**. Em seguida, digite `containers` na pesquisa. Você verá "contêineres" nos resultados da pesquisa. Selecione **Contêineres** e clique em **Criar**.

![](media/container-service-monitoring-oms/image9.png)

Depois de clicar em **Criar**, ele solicitará seu workspace. Selecione seu workspace ou, se você não tiver um, crie um novo.

![](media/container-service-monitoring-oms/image10.PNG)

Depois de selecionar o workspace, clique em **Criar**.

![](media/container-service-monitoring-oms/image11.png)

Para saber mais sobre a Solução de Contêiner do Log Analytics, veja [Solução de Contêiner do Log Analytics](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Como dimensionar o agente do Log Analytics com o ACS DC/SO 

Caso você precisasse ter instalado o Agente do Log Analytics com uma contagem de nós inferior à real, ou se estiver aumentando o conjunto de dimensionamento de máquinas virtuais adicionando mais VMs, faça isso expandindo o serviço `msoms`.

Você pode acessar o Marathon ou a guia Serviços da interface de usuário do DC/OS e escalar verticalmente sua contagem de nós.

![](media/container-service-monitoring-oms/image12.PNG)

Isso será implantado em outros nós que ainda não implantaram o agente do Log Analytics.

## <a name="uninstall-ms-oms"></a>Desinstalar o MS OMS

Para desinstalar o MS OMS, insira o seguinte comando:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Fale conosco!!!
O que funciona? O que falta? O que mais você precisa para que isso seja útil a você? Fale conosco em <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Próximas etapas

 Agora que você configurou o Log Analytics para monitorar seus contêineres, [veja seu painel de contêineres](../../azure-monitor/insights/containers.md).
