---
title: Opções agrupar e filtrar no Gerenciamento de Custos do Azure
description: Este artigo explica como usar as opções agrupar e filtrar no Gerenciamento de Custos do Azure.
author: bandersmsft
ms.author: banders
ms.date: 06/08/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: ac9828ca61009eb3ee39412169b2b454b9ecbd00
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131863"
---
# <a name="group-and-filter-options-in-cost-analysis"></a>Opções agrupar e filtrar na Análise de custo

A análise de custo tem muitas opções de agrupamento e filtragem. Este artigo ajuda você a entender quando usá-las.

Para assistir a um vídeo sobre opções de agrupamento e filtragem, assista ao vídeo [Relatórios do Gerenciamento de Custos por dimensões e tags](https://www.youtube.com/watch?v=2Vx7V17zbmk). Para assistir a outros vídeos, visite o [Canal do YouTube do Gerenciamento de Custos](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/2Vx7V17zbmk]

## <a name="group-and-filter-properties"></a>Propriedades agrupar e filtrar

A tabela a seguir lista algumas das opções mais comuns de agrupamento e filtragem disponíveis na Análise de custo e quando você deve usá-las.

| Propriedade | Quando usar | Observações |
| --- | --- | --- |
| **Zonas de Disponibilidade** | Divida os custos da AWS segundo a zona de disponibilidade. | Aplicável somente a escopos e grupos de gerenciamento da AWS. Os dados do Azure não incluem a zona de disponibilidade e aparecerão como **Não aplicável**. |
| **Período de cobrança** | Divida os custos do PAYG pelo mês em que foram, ou serão, faturados. | Use o **Período de cobrança** para obter uma representação precisa dos encargos de PAYG faturados. Inclua dois dias extras antes e depois do período de cobrança se estiver filtrando para um intervalo de datas personalizado. Limitar às datas exatas do período de cobrança não corresponderá à fatura. Fazer isso mostrará os custos de todas as faturas do período de cobrança. Use a **ID da Fatura** para filtrar para uma fatura específica. Aplicável somente a assinaturas com PAYG, porque assinaturas com EA e MCA são cobradas pelos meses do calendário. As contas EA/MCA podem usar meses do calendário no seletor de data ou a granularidade mensal para atingir a mesma meta. |
| **Tipo de preço** | Detalhe uso, compra, reembolso e custos de reserva não usada. | Compras e reembolsos de reserva só estão disponíveis quando são usados os custos reais, e não custos amortizados. Os custos da reserva não usada somente ficam disponíveis ao examinar os custos amortizados. |
| **Departamento** | Divida os custos segundo o departamento do EA. | Disponível somente para EA e grupos de gerenciamento. Assinaturas com PAYG não têm um departamento e aparecerão como **Não aplicável** ou **não atribuído**. |
| **Conta de registro** | Detalhe os custos pelo proprietário da conta EA. | Disponível somente para grupos de gerenciamento, departamentos e contas de cobrança de EA. Assinaturas com PAYG não têm contas de registro de EA e aparecerão como **Não aplicável** ou **Não atribuído**. |
| **Frequência** | Detalhe os custos com base no uso, únicos e recorrentes. | |
| **ID da Fatura** | Divida os custos segundo a fatura cobrada. | Preços não cobrados ainda não têm uma ID de fatura e os custos do EA não incluem detalhes da fatura e, portando, aparecerão como **Não aplicável**.  |
| **Medidor** | Divida os custos segundo o medidor de uso. | As compras e o uso do Marketplace aparecerão como **Não aplicável**. Veja o **Tipo de encargo** para identificar compras e o **Tipo de editor** para identificar encargos do Marketplace. |
| **Operação** | Divida os custos da AWS segundo a operação. | Aplicável somente a escopos e grupos de gerenciamento da AWS. Os dados do Azure não incluem a operação e aparecerão como **Não aplicável**; use o **Medidor** em vez deles. |
| **Modelo de preços** | Divida os custos segundo o uso sob demanda, de reserva ou de spot. | As compras aparecem como **OnDemand**. Se vir **Não aplicável**, agrupe por **Reserva** para determinar se o uso é referente a reservas ou sob demanda e por **Tipo de encargo** para identificar compras.
| **Provedor** | Detalhe os custos por AWS e Azure. | Disponível somente para grupos de gerenciamento. |
| **Tipo de publicador** | Detalhe os custos do AWS, do Azure e do Marketplace. |  |
| **Reserva** | Detalhe os custos por reserva. | Qualquer uso ou compra não associada a uma reserva aparecerá como **Não aplicável**. Agrupe segundo o **Tipo de editor** para identificar outras compras do Azure, da AWS ou do Marketplace. |
| **Recurso** | Detalhe os custos por recurso. | As compras aparecerão como **Não aplicável**, pois são aplicadas no nível do perfil de cobrança MCA ou de conta de cobrança EA/PAYG e não estão associadas a um recurso específico. Agrupe segundo o **Tipo de editor** para identificar outras compras do Azure, da AWS ou do Marketplace. |
| **Grupo de recursos** | Detalhe os custos por grupo de recursos. | Compras, recursos de locatário não associados a assinaturas, recursos de assinatura não implantados em um grupo de recursos e recursos clássicos não têm um grupo de recursos e aparecerão como **outros**, **serviços clássicos**, **$system** ou **Não aplicável**. |
| **Tipo de recurso** | Detalhe os custos por tipo de recurso. | As compras e os serviços clássicos não têm um tipo de recurso do Azure Resource Manager e aparecerão como **outros**, **serviços clássicos** ou **Não aplicável**. |
| **Localização do recurso** | Divida os custos por localização ou região. | As compras e o uso do Marketplace podem aparecer como **não atribuído**, **desconhecido**, **não mapeado** ou **não aplicável**. |
| **Nome do serviço** ou **Categoria do medidor** | Detalhe o custo por serviço do Azure. | As compras e o uso do Marketplace aparecerão como **Não aplicável** ou **não atribuído**. |
| **Camada de serviço** ou **Subcategoria de medidor** | Detalhe o custo por subclassificação do medidor de uso do Azure. | As compras e o uso do Marketplace aparecerão como **Não aplicável** ou **não atribuído**. |
| **Assinatura** | Divida os custos segundo a assinatura do Azure e a conta vinculada da AWS. | As compras e os recursos de locatário podem aparecer como **não aplicável**. |
| **Tag** | Detalhe os custos por valores de tag para uma chave de tag específica. | As tags não estão disponíveis para compras, recursos de locatário não associados a assinaturas, recursos de assinatura não implantados em um grupo de recursos ou recursos clássicos. Alguns serviços não incluem tags nos dados de uso. Saiba mais sobre o [suporte a marcas para cada tipo de recurso](../../azure-resource-manager/management/tag-support.md). |

Para obter mais informações sobre termos, confira [Compreender os termos usados no arquivo de preços e uso do Azure](../understand/understand-usage.md).

## <a name="next-steps"></a>Próximas etapas

- [Começar a analisar os custos](./quick-acm-cost-analysis.md).