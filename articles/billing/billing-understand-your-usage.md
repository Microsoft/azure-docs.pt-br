---
title: Compreenda o uso detalhado do Azure | Microsoft Docs
description: "Saiba como ler e entender as seções do CSV de uso detalhado para a sua assinatura do Azure"
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: a99e0dfea16f9fe9776d24fc94eac9ee18c7b2b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-terms-on-your-microsoft-azure-detailed-usage-charges"></a>Compreenda os termos nos encargos de uso detalhado do Microsoft Azure 
O arquivo CSV de encargos de uso detalhado contém encargos de uso de nível de medidor e de uso diário para o período de cobrança atual. 

Para obter o arquivo de uso detalhado, confira [How to get your Azure billing invoice and daily usage data](billing-download-azure-invoice-daily-usage-date.md) (Como obter sua fatura de cobrança e os dados de uso diário do Azure).
Ele está disponível em um formato de arquivo .csv (valores separados por vírgulas) que pode ser aberto em um aplicativo de planilhas. Se você vir duas versões disponíveis, baixe a versão 2. Esse é o formato de arquivo mais recente.

Encargos de uso são o total de encargos **mensais** em uma assinatura. Os encargos de uso não levam em consideração nenhum crédito nem desconto.


<div style="padding-top: 56.25%; position: relative; width: 100%;">
<iframe style="position: absolute;top: 0;left: 0;right: 0;bottom: 0;" width="100%" height="100%" src="https://www.youtube.com/embed/p13S350M2Vk" frameborder="0" allowfullscreen></iframe>
</div>

## <a name="detailed-terms-and-descriptions-of-your-detailed-usage-file"></a>Descrições e termos detalhados do arquivo de uso detalhado
As seções a seguir descrevem os termos importantes mostrados na versão 2 do arquivo de uso detalhado.

### <a name="statement"></a>Instrução
A seção superior do arquivo CSV de uso detalhado mostra os serviços que você usou durante o período de cobrança do mês. A tabela a seguir lista os termos e as descrições mostradas nesta seção.

| Termo | Descrição |
| --- | --- |
|Período de Cobrança |O período de cobrança quando os medidores foram usados |
|Categoria de medidor |Identifica o serviço de nível superior para o uso |
|Subcategoria de medidor |Define o tipo de serviço do Azure e pode afetar a tarifa |
|Nome do medidor |Identifica a unidade de medida para o medidor sendo consumido |
|Região do medidor |Identifica o local do datacenter para determinados serviços que são cobrados com base no local do datacenter |
|SKU |Identifica o identificador de sistema exclusivo para cada medidor do Azure |
|Unidade |Identifica a unidade em que o serviço é cobrado. Por exemplo, GB, horas, 10.000 s. |
|Quantidade consumida |O valor do medidor usado durante o período de cobrança |
|Quantidade incluída |O valor do medidor que está incluído gratuitamente em seu período de cobrança atual |
|Quantidade de excesso |Mostra a diferença entre a Quantidade Consumida e a Quantidade Incluída. A cobrança é feita com base nessa quantidade. Para ofertas pré-pagas sem quantidade incluída na oferta, esse total é igual à Quantidade Consumida. |
|Dentro do Compromisso |Mostra os encargos do medidor que são subtraídos do valor de compromisso associado à sua oferta de seis ou 12 meses. Os encargos do medidor são subtraídos em ordem cronológica. |
|Moeda |A moeda usada em seu período de cobrança atual |
|Excedente |Mostra os encargos de medidor que excedem o valor de compromisso associado à sua oferta de seis ou 12 meses |
|Tarifa de Compromisso |Mostra a tarifa de compromisso com base no valor de compromisso total associado à sua oferta de seis ou 12 meses |
|Tarifa |A tarifa cobrada por unidade faturável |
|Valor |Mostra o resultado da multiplicação da coluna Quantidade Excedente pela coluna Tarifa. Se a Quantidade Consumida não exceder a Quantidade Incluída, nenhum encargo será exibido nessa coluna. |

### <a name="daily-usage"></a>Uso diário

A seção de Uso diário do arquivo CSV mostra os detalhes de uso que afetam as taxas de cobrança. A tabela a seguir lista os termos e as descrições mostradas nesta seção.

| Termo | Descrição |
| --- | --- |
|Data de Uso |A data na qual o medidor foi usado |
|Categoria de medidor |Identifica o serviço de nível superior ao qual esse uso pertence |
|ID de medidor |O identificador de medidor cobrado que é usado para uso de cobrança de preços |
|Subcategoria de medidor |Define o tipo de serviço do Azure e pode afetar a tarifa |
|Nome do medidor |Identifica a unidade de medida para o medidor sendo consumido |
|Região do medidor |Identifica o local do datacenter para determinados serviços que são cobrados com base no local do datacenter |
|Unidade |Identifica a unidade em que o medidor é cobrado. Por exemplo, GB, horas, 10.000 s. |
|Quantidade consumida |A quantidade do medidor que foi consumida naquele dia |
|Local do recurso |Identifica o datacenter em que o medidor está sendo executado |
|Serviço consumido |O serviço da plataforma do Azure que você usou |
|Grupo de recursos |O grupo de recursos no qual o medidor implantado está sendo executado. <br/><br/>Para saber mais, consulte [Visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
|ID da instância | O identificador do medidor. <br/><br/> O identificador contém o nome especificado para o medidor quando ele foi criado. É o nome do recurso ou a ID do Recurso totalmente qualificada. Para saber mais, confira [API do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). |
|Marcas | Marcação atribuída ao medidor. Use marcas para agrupar registros de cobrança.<br/><br/>Por exemplo, você pode usar marcas para distribuir os custos entre os departamentos que usam o medidor. Os serviços que dão suporte à emissão de marcas são as máquinas virtuais, armazenamento e serviços de rede provisionados com a [API do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). Para saber mais, confira [Organizar os recursos do Azure com marcas](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/). |
|Informações Adicionais |Metadados específicos ao serviço. Por exemplo, um tipo de imagem para uma máquina virtual. |
|Informações do Serviço 1 |O nome do projeto ao qual o serviço pertence em sua assinatura |
|Informações do Serviço 2 |Campo herdado que captura os metadados específicos do serviço opcional |

## <a name="how-do-i-make-sure-that-the-charges-in-my-detailed-usage-file-are-correct"></a>Como fazer para ter certeza de que os encargos em meu arquivo de uso detalhado estão corretos?
Se há um encargo em seu arquivo de uso detalhado sobre o qual você deseja obter mais detalhes, consulte [Entenda sua fatura do Microsoft Azure.](./billing-understand-your-bill.md)

## <a name="external"></a>E quanto a cobranças de serviço externo?
Serviços externos (também conhecidos como pedidos do Marketplace) são fornecidos por fornecedores de serviços independentes e são cobrados separadamente. Os encargos não aparecem na fatura do Azure. Para saber mais, confira [Entender os encargos dos serviços externos do Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.
Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?) para resolver seu problema rapidamente.
