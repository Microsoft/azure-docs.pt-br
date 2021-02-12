---
title: Criar um ponto de extremidade privado gerenciado para se conectar aos resultados da fonte de dados
description: Este artigo ensinará como criar um ponto de extremidade privado gerenciado para suas fontes de dados de um espaço de trabalho do Azure Synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: e0309b4c96b2ae25eb568e390717ba76cfd84fa5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461321"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source"></a>Criar um ponto de extremidade privado gerenciado para sua fonte de dados

Este artigo ensinará como criar um ponto de extremidade privado gerenciado para sua fonte de dados no Azure. Consulte [pontos de extremidade privados gerenciados](./synapse-workspace-managed-private-endpoints.md) para saber mais.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Etapa 1: abrir seu espaço de trabalho Synapse do Azure no portal do Azure

Você pode criar um ponto de extremidade privado gerenciado para sua fonte de dados do Azure Synapse Studio. Selecione a guia **visão geral** em portal do Azure e selecione **abrir** na placa abrir o Synapse Studio na seção Introdução.

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Etapa 2: Navegue até a guia redes virtuais gerenciadas no Synapse Studio

No Azure Synapse Studio, selecione a guia **gerenciar** na navegação à esquerda. Selecione **pontos de extremidade privados gerenciados** e, em seguida, selecione **+ novo**.
![Criar um novo ponto de extremidade privado gerenciado](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Etapa 3: selecionar o tipo de fonte de dados

Selecione o tipo de fonte de dados. Nesse caso, a fonte de dados de destino é uma conta de ADLS Gen2. Selecione **Continuar**.
![Selecionar um tipo de fonte de dados de destino](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Etapa 4: inserir informações sobre a fonte de dados

Na próxima janela, insira informações sobre a fonte de dados. Neste exemplo, estamos criando um ponto de extremidade privado gerenciado para uma conta de ADLS Gen2. Insira um **nome** para o ponto de extremidade privado gerenciado. Forneça uma **assinatura do Azure** e um **nome de conta de armazenamento**. Selecione **Criar**.
![Inserir detalhes da fonte de dados de destino](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Etapa 5: verificar se o ponto de extremidade privado gerenciado foi criado com êxito

Depois de enviar a solicitação, você verá seu status. Para verificar se a criação bem-sucedida de seu ponto de extremidade privado gerenciado foi criada, verifique seu *estado de provisionamento*. Talvez seja necessário aguardar 1 minuto e selecionar **Atualizar** para atualizar o estado de provisionamento. Você pode ver que o ponto de extremidade privado gerenciado para a conta de ADLS Gen2 foi criado com êxito.

Você também pode ver que o *estado de aprovação* está *pendente*. O proprietário do recurso de destino pode aprovar ou negar a solicitação de conexão de ponto de extremidade privado. Se o proprietário aprovar a solicitação de conexão de ponto de extremidade privada, um link privado será estabelecido. Se negado, um link privado não é estabelecido.
![Status da solicitação de criação de ponto de extremidade privado gerenciado](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [Pontos de extremidade privados gerenciados](./synapse-workspace-managed-private-endpoints.md)