---
title: Enviar dados de linhagem do Data Factory por push para o Azure Purview
description: Saiba mais sobre como enviar por push Data Factory dados de linhagem para o Azure alcance
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 3f2297ae619145ec19b53ba79d70b7c085cbcaab
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361439"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Enviar Data Factory dados de linhagem por push para o Azure alcance (versão prévia)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, você usará a interface do usuário do Data Factory para criar um pipeline que executa atividades e dados de linhagem de relatório para a conta do Azure alcance. Em seguida, você pode exibir todas as informações de linhagem em sua conta do Azure alcance.

## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura do Azure**. Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* **Azure data Factory**. Se você não tiver um Azure Data Factory, consulte [criar um Azure data Factory](./quickstart-create-data-factory-portal.md).
* **Conta do Azure alcance**. A conta alcance captura todos os dados de linhagem gerados pelo data factory. Se você não tiver uma conta do Azure alcance, consulte [criar um alcance do Azure](../purview/create-catalog-portal.md).


## <a name="run-data-factory-activities-and-push-lineage-data-to-azure-purview"></a>Executar Data Factory atividades e enviar dados de linhagem por push para o Azure alcance
### <a name="step-1--connect-data-factory-to-your-purview-account"></a>Etapa 1: conectar Data Factory à sua conta do alcance
Faça logon em sua conta do alcance no portal do alcance, vá para o **centro de gerenciamento**. Escolha **Data Factory** em **conexões externas** e clique no botão **novo** para criar uma conexão com um novo data Factory. 
[![Captura de tela para criar uma conexão entre Data Factory e conta ](./media/data-factory-purview/connect-adf-to-purview.png) alcance ](./media/data-factory-purview/connect-adf-to-purview.png#lightbox)

Na página pop-up, você pode escolher o Data Factory deseja se conectar a essa conta do alcance. 
![Captura de tela para uma nova conexão](./media/data-factory-purview/new-adf-purview-connection.png)

Você pode verificar o status depois de criar a conexão. **Conectado** significa que a conexão entre data Factory e esse alcance é conectada com êxito. 
> [!NOTE]
> Você precisa ser atribuído a qualquer uma das funções abaixo na conta alcance e Data Factory função **colaborador** para criar a conexão entre data Factory e o Azure alcance.
> - Proprietário
> - Administrador de Acesso do Usuário

### <a name="step-2-run-copy-and-dataflow-activities-in-data-factory"></a>Etapa 2: executar as atividades de cópia e Dataflow no Data Factory
Você pode criar pipelines, atividades de cópia e atividades de fluxo de os Data Factory. Você não precisa de nenhuma configuração adicional para a captura de dados de linhagem. Os dados de linhagem serão automaticamente capturados durante a execução das atividades.
![Captura de tela da atividade copiar e fluxo de ](./media/data-factory-purview/adf-activities-for-lineage.png) dados se você não souber como criar atividades de cópia e fluxo de dados, confira [copiar dados do armazenamento de BLOBs do Azure para um banco de dado no banco de dados SQL do azure usando Azure data Factory](./tutorial-copy-data-portal.md) e [transformar dados usando o mapeamento de fluxos](./tutorial-data-flow.md).

### <a name="step-3-run-execute-ssis-package-activities-in-data-factory"></a>Etapa 3: executar atividades executar pacote SSIS no Data Factory
Você pode criar pipelines, executar atividades de pacote SSIS no Data Factory. Você não precisa de nenhuma configuração adicional para a captura de dados de linhagem. Os dados de linhagem serão automaticamente capturados durante a execução das atividades.
![Captura de tela da atividade executar pacote SSIS](./media/data-factory-purview/ssis-activities-for-lineage.png)

Se você não souber como criar atividades executar pacote SSIS, consulte [executar pacotes SSIS no Azure](./tutorial-deploy-ssis-packages-azure.md).

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>Etapa 4: exibir informações de linhagem em sua conta do alcance
Volte para sua conta do alcance. Na home page, selecione **procurar ativos**. Escolha o ativo desejado e clique na guia linhagem. Você verá todas as informações de linhagem.
[![Captura de tela da conta ](./media/data-factory-purview/view-dataset.png) do alcance ](./media/data-factory-purview/view-dataset.png#lightbox)

Você pode ver dados de linhagem para a atividade de cópia.
[![Captura de tela da linhagem ](./media/data-factory-purview/copy-lineage.png) de cópia ](./media/data-factory-purview/copy-lineage.png#lightbox)

Você também pode ver dados de linhagem para a atividade de Dataflow.
[![Captura de tela da linhagem ](./media/data-factory-purview/dataflow-lineage.png) de Dataflow ](./media/data-factory-purview/dataflow-lineage.png#lightbox)

> [!NOTE] 
> Para a linhagem da atividade de fluxo de atividades, damos suporte apenas à origem e ao coletor. Ainda não há suporte para a linhagem para transformação de Dataflow.

Você também pode ver dados de linhagem para a atividade executar pacote SSIS.
[![Captura de tela da linhagem ](./media/data-factory-purview/ssis-lineage.png) do SSIS ](./media/data-factory-purview/ssis-lineage.png#lightbox)

> [!NOTE] 
> Para a linhagem da atividade executar pacote SSIS, damos suporte apenas à origem e ao destino. Ainda não há suporte para a linhagem para transformação.

## <a name="next-steps"></a>Próximas etapas
[Guia do usuário de linhagem de catálogo](../purview/catalog-lineage-user-guide.md)

[Conectar Data Factory ao Azure alcance](connect-data-factory-to-azure-purview.md)