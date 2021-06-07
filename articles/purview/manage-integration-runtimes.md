---
title: Criar e gerenciar tempos de execução de integração
description: Este artigo explica as etapas para criar e gerenciar tempos de execução de integração no Azure alcance.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 9276f845c95b5e736180159b282ddedc33523c17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99980738"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Criar e gerenciar um tempo de execução de integração auto-hospedado

Este artigo descreve como criar e gerenciar um SHIR (Integration Runtime) auto-hospedado que permite que você examine fontes de dados no Azure alcance.

> [!NOTE]
> O Integration Runtime alcance não pode ser compartilhado com uma análise Synapse do Azure ou Azure Data Factory Integration Runtime no mesmo computador. Ele precisa ser instalado em um computador separado.

## <a name="create-a-self-hosted-integration-runtime"></a>Criar um Integration Runtime auto-hospedado

1. Na home page do alcance Studio, selecione **centro de gerenciamento** no painel de navegação esquerdo.

2. Em **fontes e verificação** no painel esquerdo, selecione **tempos de execução de integração** e, em seguida, selecione **+ novo**.

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Clique em IR.":::

3. Na página **instalação do Integration Runtime** , selecione **auto-hospedado** para criar um Self-Hosted ir e, em seguida, selecione **continuar**.

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Criar novo SHIR.":::

4. Insira um nome para o IR e selecione criar.

5. Na página **configurações de Integration Runtime** , siga as etapas na seção **configuração manual** . Você precisará baixar o Integration Runtime do site de download para uma VM ou computador em que você pretende executá-lo.

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="obter chave":::

   - Copie e cole a chave de autenticação.

   - Baixe o tempo de execução de integração auto-hospedado de [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) em um computador Windows local. Execute o instalador.

   - Na página **registrar Integration Runtime (auto-hospedado)** , Cole uma das duas chaves salvas anteriormente e selecione **registrar**.

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="chave de entrada.":::

   - Na página **Novo nó do Integration Runtime (auto-hospedado)** , selecione **Concluir**.

6. Depois que o tempo de execução de integração auto-hospedado for registrado com êxito, você verá a seguinte janela:

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="registrado com êxito.":::

## <a name="manage-a-self-hosted-integration-runtime"></a>Gerenciar um Integration Runtime de hospedagem interna

Você pode editar um tempo de execução de integração auto-hospedado navegando até os **tempos de execução de integração** no centro de **Gerenciamento**, selecionando o ir e clicando em Editar. Agora você pode atualizar a descrição, copiar a chave ou regenerar novas chaves.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="editar IR.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="Edite os detalhes de IR.":::

Você pode excluir um tempo de execução de integração auto-hospedado navegando até os **tempos de execução de integração** no centro de gerenciamento, selecionando o ir e clicando em **excluir**. Depois que um IR for excluído, qualquer verificação contínua que dependa dele falhará.

## <a name="next-steps"></a>Próximas etapas

[Como os exames detectam os ativos excluídos](concept-detect-deleted-assets.md)
