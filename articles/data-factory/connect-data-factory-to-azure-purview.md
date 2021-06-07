---
title: Conectar um Data Factory ao Azure Purview
description: Saiba mais sobre como conectar um Data Factory ao Azure alcance
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 44f093f96d0f4653a6fcca94aaa97264c93e3c7d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727933"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Conectar Data Factory ao Azure alcance (versão prévia)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explicará como conectar Data Factory ao Azure alcance e como relatar a linhagem de dados de Azure Data Factory copiar dados, fluxo de dados e executar pacote SSIS.


## <a name="connect-data-factory-to-azure-purview"></a>Conectar Data Factory ao Azure alcance
O Azure alcance é um novo serviço de nuvem para uso por usuários de dados centralmente gerenciam a governança de dados em seu espaço de dados abrangendo ambientes locais e de nuvem. Você pode conectar seu Data Factory ao Azure alcance e a conexão permite que você use o alcance do Azure para capturar dados de linhagem de cópia, fluxo de dados e executar pacote SSIS. Você tem duas maneiras de se conectar data factory ao Azure alcance:
### <a name="register-azure-purview-account-to-data-factory"></a>Registrar a conta do Azure alcance no Data Factory
1. No portal do ADF, vá para **gerenciar**  ->  **Azure alcance**. Selecione **Conectar-se a uma conta do Purview**. 

:::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Captura de tela para registrar uma conta do alcance.":::
2. Escolha **Da assinatura do Azure** ou **Inserir manualmente**. Em **Da assinatura do Azure**, é possível selecionar a conta à qual você tem acesso. 
3. Uma vez conectado, você deve ser capaz de ver o nome da conta alcance na guia **alcance conta**. 
4. Você pode usar a barra de pesquisa no centro superior do Azure Data Factory portal para pesquisar dados. 

Se você vir um aviso no portal Azure Data Factory depois de registrar a conta do Azure alcance em Data Factory, siga as etapas abaixo para corrigir o problema:

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Captura de tela para aviso de registro de uma conta do alcance.":::

1. Vá para portal do Azure e localize sua data factory. Escolha a seção "marcas" e veja se há uma marca chamada **catalogUri**. Caso contrário, desconecte e reconecte a conta do Azure alcance no portal do ADF.

:::image type="content" source="./media/data-factory-purview/register-purview-account-tag.png" alt-text="Captura de tela para marcas de registro de uma conta do alcance.":::

2. Verifique se a permissão é concedida para registrar uma conta do Azure alcance para Data Factory. Veja [como conectar Azure data Factory e o Azure alcance](../purview/how-to-link-azure-data-factory.md#create-new-data-factory-connection)

### <a name="register-data-factory-in-azure-purview"></a>Registrar Data Factory no Azure alcance
Para saber como registrar Data Factory no Azure alcance, consulte [como conectar Azure data Factory e o Azure alcance](../purview/how-to-link-azure-data-factory.md). 

## <a name="report-lineage-data-to-azure-purview"></a>Relatar dados de linhagem para o Azure alcance
Quando os clientes executam a atividade copiar, fluxo de dados ou executar pacote SSIS no Azure Data Factory, os clientes podem obter a relação de dependência e ter uma visão geral de alto nível de todo o processo de fluxo de trabalho entre fontes de dados e destino.
Para saber como coletar a linhagem de Azure Data Factory, consulte [Data Factory linhagem](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities).

## <a name="next-steps"></a>Próximas etapas
[Guia do usuário de linhagem de catálogo](../purview/catalog-lineage-user-guide.md)

[Tutorial: enviar Data Factory dados de linhagem por push para o Azure alcance](turorial-push-lineage-to-purview.md)