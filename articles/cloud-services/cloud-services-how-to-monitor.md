---
title: Monitorar um serviço de nuvem do Azure (clássico) | Microsoft Docs
description: Descreve o que envolve o monitoramento de um Serviço de Nuvem do Azure e quais são algumas das opções.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 60f320f86860cca482cdf25c7d93f84dae8c4e5f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743365"
---
# <a name="introduction-to-cloud-service-classic-monitoring"></a>Introdução ao monitoramento de serviço de nuvem (clássico)

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

Você pode monitorar as principais métricas de desempenho de qualquer serviço de nuvem. Toda função de serviço de nuvem coleta o mínimo de dados: uso da CPU, uso da rede e utilização do disco. Se o serviço de nuvem tiver a extensão `Microsoft.Azure.Diagnostics` aplicada a uma função, essa função poderá coletar outros pontos de dados. Este artigo fornece uma introdução aos Diagnósticos do Azure para Serviços de Nuvem.

Com o monitoramento básico, os dados do contador de desempenho das instâncias de função passam por amostragem e são coletados em intervalos de três minutos. Esses dados de monitoramentos básico não são armazenados em sua conta de armazenamento, e não há custo adicional associado a eles.

Com o monitoramento avançado, as métricas adicionais passam por amostragem e são coletadas em intervalos de cinco minutos, uma hora e 12 horas. Os dados agregados são armazenados em uma conta de armazenamento, em tabelas, e são excluídos após 10 dias. A conta de armazenamento usada é configurada pela função; use contas de armazenamento diferentes para funções diferentes. Isso é configurado com uma cadeia de conexão nos arquivos [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) e [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg).


## <a name="basic-monitoring"></a>Monitoramento básico

Conforme afirmado na introdução, um serviço de nuvem coleta automaticamente os dados de monitoramentos básico da máquina virtual do host. Esses dados incluem a porcentagem da CPU, entrada/saída de rede e leitura/gravação no disco. Os dados de monitoramento coletados são exibidos automaticamente nas páginas de visão geral e de métricas do serviço de nuvem, no Portal do Azure. 

O monitoramento básico não exige uma conta de armazenamento. 

![blocos de monitoramento básico de serviço de nuvem](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Monitoramento avançado

O monitoramento avançado envolve o uso da extensão **diagnóstico do Azure** (e, opcionalmente, o SDK do Application insights) na função que você deseja monitorar. A extensão de diagnóstico usa um arquivo de configuração (por função) chamado **diagnostics.wadcfgx** para configurar as métricas de diagnóstico monitoradas. A extensão Diagnóstico do Azure coleta e armazena dados em uma conta de Armazenamento do Azure. Essas configurações são definidas nos arquivos **.wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) e [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg). Isso significa que há um custo extra associado ao monitoramento avançado.

À medida que cada função é criada, o Visual Studio adiciona a extensão de Diagnóstico do Azure a ela. Essa extensão de diagnóstico pode coletar os seguintes tipos de informações:

* Contadores de desempenho personalizados
* Logs de aplicativo
* Logs de eventos do Windows
* Fonte de evento do .NET
* Logs IIS
* Manifesto com base no ETW
* Despejos de memória
* Logs de erro do cliente

> [!IMPORTANT]
> Embora todos esses dados sejam agregados na conta de armazenamento, o portal não **fornece uma** maneira nativa de gráfico dos dados. É altamente recomendável integrar outro serviço, como o Application Insights, ao seu aplicativo.

## <a name="setup-diagnostics-extension"></a>Extensão de diagnóstico da configuração

Primeiro, se você não tiver uma conta de armazenamento **clássica**, [crie uma](../storage/common/storage-account-create.md). Crie a conta de armazenamento com o **Modelo de implantação clássico** especificado.

Em seguida, navegue até o recurso **Conta de armazenamento (clássico)**. Selecione **configurações**  >  **chaves de acesso** e copie o valor da **cadeia de conexão primária** . Você precisa desse valor para o serviço de nuvem. 

Você precisa alterar dois arquivos de configuração para habilitar o diagnóstico avançado, **ServiceDefinition.csdef** e **ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

No arquivo **ServiceDefinition.csdef**, adicione uma nova configuração chamada `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` para cada função que usa diagnóstico avançado. O Visual Studio adiciona esse valor ao arquivo quando você cria um novo projeto. Caso não esteja presente, adicione-o agora. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Isso define uma nova configuração que deve ser adicionada a cada arquivo **ServiceConfiguration.cscfg**. 

Provavelmente, você tem dois arquivos **.cscfg**, um chamado **ServiceConfiguration.cloud.cscfg** para a implantação no Azure e outro chamado **ServiceConfiguration.local.cscfg**, usado para implantações locais no ambiente emulado. Abra e altere cada arquivo **.cscfg**. Adicione uma configuração chamada `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`. Defina o valor como a **Cadeia de conexão primária** da conta de armazenamento clássica. Se deseja usar o armazenamento local no computador de desenvolvimento, use `UseDevelopmentStorage=true`.

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Usar o Application insights

Ao publicar o Serviço de Nuvem do Visual Studio, você recebe a opção de enviar os dados de diagnóstico ao Application Insights. Você pode criar o recurso do Azure no Application Insights a qualquer momento ou enviar os dados para um recurso existente do Azure. Seu serviço de nuvem pode ser monitorado pelo Application Insights com relação à disponibilidade, ao desempenho, falhas e uso. É possível adicionar gráficos personalizados ao Application Insights, para que você veja os dados mais importantes. Os dados de instância da função podem ser coletados usando o SDK do Application Insights em seu projeto de serviço de nuvem. Para saber mais sobre como integrar o Application Insights, consulte [Application Insights com Serviços de Nuvem](../azure-monitor/app/cloudservices.md).

Perceba que embora você possa usar o Application Insights para exibir os contadores de desempenho (e as outras configurações) especificados por meio da extensão de Diagnóstico do Windows Azure, você só terá uma experiência mais rica integrando o SDK do Application Insights às suas funções de trabalho e da Web.


## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre o Application Insights com o Serviços de Nuvem](../azure-monitor/app/cloudservices.md)
- [Configurar contadores de desempenho](diagnostics-performance-counters.md)




