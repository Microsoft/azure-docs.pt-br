---
title: "Monitorar cluster do Azure DC/OS - Gerenciamento de Operações | Microsoft Docs"
description: "Monitorar um cluster DC/OS do Serviço de Contêiner do Azure com o Microsoft Operations Management Suite."
services: container-service
documentationcenter: 
author: keikhara
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 9b8f96b34b53982c469273a3df9751ceb7930d60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-operations-management-suite"></a>Monitorar um cluster DC/OS do Serviço de Contêiner do Azure com o Operations Management Suite

O OMS (Microsoft Operations Management Suite) é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda a gerenciar e proteger sua infraestrutura local e de nuvem. A Solução de Contêiner é uma solução do OMS Log Analytics, que ajuda você a exibir o inventário, o desempenho e os logs de contêineres em um único local. É possível realizar auditoria e solucionar problemas de contêineres vendo os logs em um local centralizado, e localizar contêineres com ruídos e excesso de consumo em um host.

![](media/container-service-monitoring-oms/image1.png)

Para saber mais sobre a Solução de Contêiner, veja [Solução de Contêiner do Log Analytics](../../log-analytics/log-analytics-containers.md).

## <a name="setting-up-oms-from-the-dcos-universe"></a>Configuração do OMS a partir do universo DC/OS


Este artigo pressupõe que você configurou um DC/OS e implantou aplicativos de contêiner da Web simples no cluster.

### <a name="pre-requisite"></a>Pré-requisito
- [Assinatura do Microsoft Azure](https://azure.microsoft.com/free/) - você pode obter uma gratuitamente.  
- Configuração do espaço de trabalho do Microsoft OMS - consulte a "Etapa 3" abaixo
- [CLI do DC/OS](https://dcos.io/docs/1.8/usage/cli/install/) instalada.

1. No painel do DC/OS, clique no Universo e procure por ‘OMS’ conforme mostrado abaixo.

![](media/container-service-monitoring-oms/image2.png)

2. Clique em **Instalar**. Você verá um pop-up com as informações de versão do OMS e um botão **Instalar Pacote** ou **Instalação Avançada**. Ao clicar em **Instalação Avançada**, você será levado até a página **Propriedades de configuração específicas do OMS**.

![](media/container-service-monitoring-oms/image3.png)

![](media/container-service-monitoring-oms/image4.png)

3. Aqui, você receberá uma solicitação para inserir a `wsid` (a ID do espaço de trabalho do OMS) e `wskey` (a chave primária do OMS para a ID do espaço de trabalho). Para obter `wsid` e `wskey` você precisa criar uma conta do OMS no <https://mms.microsoft.com>. Execute as etapas a seguir para criar uma conta. Depois de criar a conta, você precisará obter o `wsid` e o `wskey` clicando em **Configurações**, em seguida, **Fontes Conectadas** e, em seguida, em **Servidores Linux**, conforme mostrado abaixo.

 ![](media/container-service-monitoring-oms/image5.png)

4. Selecione o número de instâncias do OMS que você deseja e clique no botão ‘Revisar e Instalar’. Normalmente, convém ter uma quantidade de instâncias do OMS igual à quantidade de VMs existentes no cluster do agente. O Agente do OMS para Linux é instalado como contêineres individuais em cada VM da qual deseja coletar informações para monitoramento e registro em log de informações.

## <a name="setting-up-a-simple-oms-dashboard"></a>Configuração de um painel OMS simples

Depois de instalar o Agente do OMS para Linux nas VMs, a próxima etapa é configurar o painel do OMS. Há duas maneiras de fazer isso: Portal do OMS ou Portal do Azure.

### <a name="oms-portal"></a>Portal do OMS 

Faça logon Portal do OMS (<https://mms.microsoft.com>) e acesse **Galeria de Soluções**.

![](media/container-service-monitoring-oms/image6.png)

Quando você estiver na **Galeria de Soluções**, selecione **Contêineres**.

![](media/container-service-monitoring-oms/image7.png)

Depois de selecionar a Solução de Contêiner, você verá o bloco na página do Painel de Visão Geral do OMS. Após a indexação dos dados de contêiner ingeridos, você verá o bloco preenchido com informações sobre os blocos de exibição da solução.

![](media/container-service-monitoring-oms/image8.png)

### <a name="azure-portal"></a>Portal do Azure 

Faça logon no Portal do Azure em <https://portal.azure.com>. Acesse **Marketplace**, selecione **Monitoramento + gerenciamento** e clique em **Ver Tudo**. Em seguida, digite `containers` na pesquisa. Você verá "contêineres" nos resultados da pesquisa. Selecione **Contêineres** e clique em **Criar**.

![](media/container-service-monitoring-oms/image9.png)

Depois de clicar em **Criar**, ele solicitará seu espaço de trabalho. Selecione seu espaço de trabalho ou, se você não tiver um, crie um novo.

![](media/container-service-monitoring-oms/image10.PNG)

Depois de selecionar o espaço de trabalho, clique em **Criar**.

![](media/container-service-monitoring-oms/image11.png)

Para saber mais sobre a Solução de Contêiner do OMS, veja [Solução de Contêiner do Log Analytics](../../log-analytics/log-analytics-containers.md).

### <a name="how-to-scale-oms-agent-with-acs-dcos"></a>Como dimensionar o Agente do OMS com o ACS DC/SO 

Caso você precisasse ter instalado o Agente do OMS com uma contagem de nós inferior à real, ou se estiver aumentando o VMSS adicionando mais VMs, faça isso expandindo o serviço `msoms`.

Você pode acessar o Marathon ou a guia Serviços da interface de usuário do DC/OS e escalar verticalmente sua contagem de nós.

![](media/container-service-monitoring-oms/image12.PNG)

Isso será implantado em outros nós que ainda não implantaram o agente do OMS.

## <a name="uninstall-ms-oms"></a>Desinstalar o MS OMS

Para desinstalar o MS OMS, insira o seguinte comando:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Fale conosco!!!
O que funciona? O que falta? O que mais você precisa para que isso seja útil a você? Fale conosco em <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Próximas etapas

 Agora que você configurou o OMS para monitorar seus contêineres, [veja seu painel de contêineres](../../log-analytics/log-analytics-containers.md).
