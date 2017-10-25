---
title: "Recursos do Log Analytics para provedores de serviços | Microsoft Docs"
description: "O Log Analytics pode ajudar provedores de serviços gerenciados (MSPs), grandes empresas, fornecedores de software independentes (ISVs) e provedores de serviço de hospedagem a gerenciar e monitorar servidores no local do cliente ou na infraestrutura de nuvem."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: richrund
ms.openlocfilehash: 8a67d9a9d345682e9e6c8f5c7779204a038f5f6a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-features-for-service-providers"></a>Recursos do Log Analytics para provedores de serviços
O Log Analytics pode ajudar provedores de serviços gerenciados (MSPs), grandes empresas, fornecedores de software independentes (ISVs) e provedores de serviço de hospedagem a gerenciar e monitorar servidores no local do cliente ou na infraestrutura de nuvem. 

Grandes empresas compartilham várias semelhanças com provedores de serviços, especialmente quando há uma equipe de TI centralizada que é responsável por gerenciar a TI para muitas unidades de negócios diferentes. Para simplificar, este documento usa o termo *provedor*, mas a mesma funcionalidade também está disponível para empresas e outros clientes.

## <a name="cloud-solution-provider"></a>Provedor de soluções de nuvem
Para parceiros e provedores de serviços que fazem parte do programa do [Provedor de soluções de nuvem (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview), o Log Analytics é um dos serviços do Azure disponíveis em uma assinatura do CSP. 

Para o Log Anlytics, os seguintes recursos estão habilitados nas assinaturas do *Provedor de soluções de nuvem*.

Como um *Provedor de Soluções de Nuvem*, você pode:

* Criar espaços de trabalho do Log Analytics na assinatura de um locatário (do cliente).
* Acessar espaços de trabalho criados por locatários. 
* Adicionar e remover o acesso do usuário ao espaço de trabalho usando o gerenciamento de usuário do Azure. Quando no espaço de trabalho do locatário no portal do OMS, a página Gerenciamento de usuário em configurações não está disponível
  * O Log Analytics não dá suporte ao acesso baseado em função ainda - dar a um usuário a permissão do `reader` no portal do Azure permite que eles façam alterações de configuração no portal do OMS

Para fazer logon em uma assinatura de locatário, você precisa especificar o identificador do locatário. O identificador do locatário costuma ser a última parte do endereço de e-mail usado para entrar.

* No portal do OMS, adicione `?tenant=contoso.com` na URL para o portal. Por exemplo, `mms.microsoft.com/?tenant=contoso.com`
* No PowerShell, use o parâmetro `-Tenant contoso.com` ao usar o cmdlet `Add-AzureRmAccount`
* O identificador do locatário é adicionado automaticamente quando você usa o link `OMS portal` no portal do Azure para abrir e fazer logon no portal do OMS no espaço de trabalho selecionado

Como um *cliente* de um Provedor de soluções de nuvem, você pode:

* Criar espaços de trabalho do Log Analytics em uma assinatura de CSP
* Acessar espaços de trabalho criados pelo CSP
  * Usar o link `OMS portal` no portal do Azure para abrir e fazer logon no portal do OMS no espaço de trabalho selecionado
* Exibir e usar a página de gerenciamento de usuário em configurações no portal do OMS

> [!NOTE]
> As soluções Backup e Site Recovery do Log Analytics incluídas não são capazes de se conectar a um cofre dos Serviços de recuperação e não podem ser configuradas em uma assinatura do CSP. 
> 
> 

## <a name="managing-multiple-customers-using-log-analytics"></a>Gerenciando vários clientes usando o Log Analytics
É recomendável que você crie um espaço de trabalho do Log Analytics para cada cliente que você gerencia. Um espaço de trabalho do Log Analytics fornece:

* Uma localização geográfica para os dados serem armazenados. 
* Granularidade de cobrança 
* Isolamento dos dados 
* Configuração exclusiva

Ao criar um espaço de trabalho por cliente, é possível manter os dados de cada cliente separado e também controlar o uso de cada cliente.

Mais detalhes sobre quando e por que criar vários espaços de trabalho são descritos em [gerenciar o acesso para o log analytics](log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need).

Criação e configuração de espaços de trabalho do cliente podem ser automatizadas usando [PowerShell](log-analytics-powershell-workspace-configuration.md), [modelos do Gerenciador de recursos](log-analytics-template-workspace-configuration.md), ou usando a [API REST](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/).

O uso de modelos do Gerenciador de recursos para a configuração do espaço de trabalho permite que você tenha uma configuração mestre que pode ser usada para criar e configurar espaços de trabalho. Você pode ter certeza de que, como espaços de trabalho são criados para os clientes, eles são automaticamente configurados para suas necessidades. Quando você atualiza seus requisitos, o modelo é atualizado e reaplicados os espaços de trabalho existentes. Esse processo garante que até mesmo os espaços de trabalho existentes atendem aos novos padrões.    

Ao gerenciar vários espaços de trabalho do Log Analytics, recomendamos a integração de cada espaço de trabalho com o sistema de emissão de tíquetes existente / console de operações usando a funcionalidade [alertas](log-analytics-alerts.md). Ao integrar os sistemas existentes, a equipe de suporte pode continuar a seguir os processos familiares. O Log Analytics verifica com frequência cada espaço de trabalho em relação aos critérios de alerta especificados e gera um alerta quando a ação é necessária.

Para exibições personalizadas de dados, use a funcionalidade de [painel](../azure-portal/azure-portal-dashboards.md) no portal do Azure.  

Para relatórios de nível executivo que resumem dados em espaços de trabalho, você pode usar a integração entre o Log Analytics e o [PowerBI](log-analytics-powerbi.md). Se precisar integrar com outro sistema de relatórios, você pode usar a API de pesquisa (por meio do PowerShell ou [REST](log-analytics-log-search-api.md)) para executar consultas e exportar resultados da pesquisa.

## <a name="next-steps"></a>Próximas etapas
* Automatizar a criação e configuração de espaços de trabalho usando [modelos do Gerenciador de recursos](log-analytics-template-workspace-configuration.md)
* Automatizar a criação de espaços de trabalho usando o [PowerShell](log-analytics-powershell-workspace-configuration.md) 
* Use [Alertas](log-analytics-alerts.md) para integrar com sistemas existentes
* Gerar relatórios de resumo usando o [Power BI](log-analytics-powerbi.md)

