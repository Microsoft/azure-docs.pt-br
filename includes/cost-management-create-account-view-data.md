---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cost-management
author: bandersmsft
ms.service: cost-management
ms.topic: include
ms.date: 09/17/2018
ms.author: banders
manager: dougeby
ms.custom: include file
ms.openlocfilehash: 1ffa56caebf16b588dffaba249a844915f9f44c7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171664"
---
## <a name="view-cost-data"></a>Exibir dados de custo

O Gerenciamento de Custos do Azure por Cloudyn fornece acesso a todos os seus dados de recursos de nuvem. Dos relatórios de painel, você pode encontrar relatórios padrão e personalizados em uma exibição com guias. A seguir, exemplos de um painel popular e de um relatório que mostram dados de custo imediatamente.

![Painel de gerenciamento](./media/cost-management-create-account-view-data/mgt-dash.png)

Neste exemplo, o painel de gerenciamento mostra custos consolidados para a empresa Contoso em todos os seus recursos de nuvem. A Contoso usa o Azure, o AWS e o Google. Os painéis fornecem informações gerais e são uma forma rápida de navegar em relatórios.  

Se você não tiver certeza da finalidade do relatório em um painel, focalize o símbolo **i** para ver uma explicação. Clique em qualquer relatório em um painel para exibir o relatório completo.

Você também pode exibir relatórios usando o menu de relatórios na parte superior do portal. Vamos dar uma olhada nos gastos do recurso do Azure da Contoso nos últimos 30 dias. Clique em **Custos** > **Análise de Custo** > **Análise de Custo Real**. Desmarque quaisquer valores, se houver qualquer conjunto de marcas, grupos ou filtros em seu relatório.

![Análise de Custo Real](./media/cost-management-create-account-view-data/actual-cost-01.png)

Neste exemplo, US$ 122.273 é o custo total e o orçamento é US$ 290.000.

Agora, vamos modificar o formato de relatório e definir grupos e filtros para restringir os resultados para os custos do Azure. Defina o **Intervalo de Datas** dos últimos 30 dias. No canto superior direito, clique no símbolo de coluna para formatar como um gráfico de barras e em grupos, selecione **Provedor**. Em seguida, defina um filtro para **Provedor** como **Azure**.

![Análise de Custo Real filtrado](./media/cost-management-create-account-view-data/actual-cost-02.png)

Neste exemplo, o custo total de recursos do Azure foi de US$ 3.309 nos últimos 30 dias.

Clique com botão direito do mouse na barra do provedor (Azure) e faça drilldown até **Tipos de recurso**.

![fazer drill down](./media/cost-management-create-account-view-data/actual-cost-03.png)

A imagem a seguir mostra os custos de recursos do Azure incorridos pela Contoso. O total foi US$ 3.309. Neste exemplo, cerca de metade dos custos foram para Standard_A1 VMs e sobre a outra metade dos custos foram para vários serviços do Azure e instâncias de VM.

![tipos de recurso](./media/cost-management-create-account-view-data/actual-cost-04.png)

Clique com o botão direito do mouse em um tipo de recurso e selecione **Entidades de Custo** para exibir entidades e os serviços que consumiram o recurso. Na imagem de exemplo a seguir o armazenamento com redundância local é definido como o tipo de recurso. A Contoso | Armazenamento do Azure consumido US $15.65. Engenharia | O armazenamento do Azure consumido US $164.25. Infra-estrutura compartilhada | Armazenamento do Azure consumido US $116.58. O custo total para os serviços é US $296.

![entidades e serviços de custo](./media/cost-management-create-account-view-data/actual-cost-05.png)

Para assistir a um vídeo de tutorial sobre como exibir seus dados de cobrança de nuvem, consulte [Analisar seus dados de cobrança de nuvem com o Gerenciamento de Custos do Azure por Cloudyn](https://youtu.be/G0pvI3iLH-Y).
