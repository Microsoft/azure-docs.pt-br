---
title: Conceder permissões para identidade gerenciada no espaço de trabalho Synapse
description: Um artigo que explica como configurar permissões para identidade gerenciada no espaço de trabalho Synapse do Azure.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 46fe27205a2c30d6cb2319bf620c6fe1ee34c31e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96459001"
---
# <a name="grant-permissions-to-workspace-managed-identity"></a>Conceder permissões para a identidade gerenciada do espaço de trabalho

Este artigo ensina como conceder permissões para a identidade gerenciada no espaço de trabalho Synapse do Azure. As permissões, por sua vez, permitem o acesso a pools dedicados do SQL no espaço de trabalho e ADLS Gen2 conta de armazenamento por meio do portal do Azure.

>[!NOTE]
>Essa identidade gerenciada do workspace será referida como identidade gerenciada no restante deste documento.

## <a name="grant-managed-identity-permissions-to-the-dedicated-sql-pool"></a>Conceder permissões de identidade gerenciadas para o pool SQL dedicado

A identidade gerenciada concede permissões aos pools do SQL dedicados no espaço de trabalho. Com as permissões concedidas, você pode orquestrar pipelines que executam atividades dedicadas relacionadas ao pool do SQL. Ao criar um espaço de trabalho Synapse do Azure usando portal do Azure, você pode conceder as permissões de controle de identidade gerenciadas em pools SQL dedicados.

Selecione **segurança** quando estiver criando seu espaço de trabalho Synapse do Azure. Em seguida, selecione **permitir pipelines (em execução como identidade atribuída pelo sistema do espaço de trabalho) para acessar pools SQL.**.

![Permissão CONTROL em pools SQL dedicados](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>Conceder as permissões de identidade gerenciada para ADLS Gen2 conta de armazenamento

Uma conta de armazenamento ADLS Gen2 é necessária para criar um espaço de trabalho Synapse do Azure. Para iniciar com êxito os pools do Spark no espaço de trabalho Synapse do Azure, a identidade gerenciada do Azure Synapse precisa da função *colaborador de dados do blob de armazenamento* nessa conta de armazenamento. A orquestração de pipeline no Azure Synapse também se beneficia dessa função.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Conceder permissões para identidade gerenciada durante a criação do espaço de trabalho

O Azure Synapse tentará conceder a função de colaborador de dados de blob de armazenamento para a identidade gerenciada depois de criar o espaço de trabalho Synapse do Azure usando portal do Azure. Você fornece os detalhes da conta de armazenamento ADLS Gen2 na guia **noções básicas** .

![Guia básico no fluxo de criação do espaço de trabalho](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Escolha a conta de armazenamento ADLS Gen2 e o sistema de arquivos em **nome da conta** e nome do **sistema de arquivos**.

![Fornecendo um ADLS Gen2 detalhes da conta de armazenamento](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Se o criador do espaço de trabalho também for o **proprietário** da conta de armazenamento ADLS Gen2, o Azure Synapse atribuirá a função de *colaborador de dados de blob de armazenamento* à identidade gerenciada. Você verá a seguinte mensagem abaixo dos detalhes da conta de armazenamento que você inseriu.

![Atribuição de colaborador de dados de blob de armazenamento bem-sucedida](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Se o criador do espaço de trabalho não for o proprietário da conta de armazenamento ADLS Gen2, o Azure Synapse não atribuirá a função de *colaborador de dados de blob de armazenamento* à identidade gerenciada. A mensagem exibida abaixo dos detalhes da conta de armazenamento notifica o criador do espaço de trabalho de que eles não têm permissões suficientes para conceder a função de *colaborador de dados de blob de armazenamento* à identidade gerenciada.

![Atribuição de colaborador de dados de blob de armazenamento malsucedida](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

À medida que a mensagem diz, você não pode criar pools do Spark, a menos que o *colaborador de dados de blob de armazenamento* seja atribuído à identidade gerenciada.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Conceder permissões para identidade gerenciada após a criação do espaço de trabalho

Durante a criação do espaço de trabalho, se você não atribuir o *colaborador de dados do blob de armazenamento* à identidade gerenciada, o **proprietário** da conta de armazenamento ADLS Gen2 atribuirá manualmente essa função à identidade. As etapas a seguir ajudarão você a realizar a atribuição manual.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Etapa 1: Navegue até a conta de armazenamento ADLS Gen2 no portal do Azure

Em portal do Azure, abra a conta de armazenamento de ADLS Gen2 e selecione **visão geral** no painel de navegação esquerdo. Você só precisará atribuir a função de *colaborador de dados de blob de armazenamento* no nível do contêiner ou do sistema de arquivos. Selecione **Contêineres**.  
![Visão geral da conta de armazenamento ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Etapa 2: selecionar o contêiner

A identidade gerenciada deve ter acesso a dados ao contêiner (sistema de arquivos) que foi fornecido quando o espaço de trabalho foi criado. Você pode encontrar esse contêiner ou sistema de arquivos em portal do Azure. Abra o espaço de trabalho Synapse do Azure em portal do Azure e selecione a guia **visão geral** no painel de navegação esquerdo.
![Contêiner de conta de armazenamento ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Selecione o mesmo contêiner ou sistema de arquivos para conceder a função de *colaborador de dados de blob de armazenamento* à identidade gerenciada.
![Captura de tela que mostra o contêiner ou o sistema de arquivos que você deve selecionar.](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>Etapa 3: navegar até o controle de acesso

Selecione **Controle de Acesso (IAM)** .

![Controle de acesso (IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>Etapa 4: adicionar uma nova atribuição de função

Selecione **+ Adicionar**.

![Adicionar nova atribuição de função](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-azure-role"></a>Etapa 5: selecionar a função do Azure

Selecione a função **colaborador de dados do blob de armazenamento** .

![Selecione a função do Azure](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>Etapa 6: selecionar a entidade de segurança do Azure AD

Selecione **usuário, grupo ou entidade de serviço do Azure ad** na lista suspensa **atribuir acesso a** .

![Selecionar entidade de segurança do AAD](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>Etapa 7: Pesquisar a identidade gerenciada

O nome da identidade gerenciada também é o nome do espaço de trabalho. Pesquise sua identidade gerenciada inserindo o nome do espaço de trabalho Synapse do Azure em **selecionar**. Você deve ver a identidade gerenciada listada.

![Localizar a identidade gerenciada](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>Etapa 8: selecionar a identidade gerenciada

Selecione a identidade gerenciada para os **Membros selecionados**. Selecione **salvar** para adicionar a atribuição de função.

![Selecionar a identidade gerenciada](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Etapa 9: verificar se a função colaborador de dados do blob de armazenamento está atribuída à identidade gerenciada

Selecione **controle de acesso (iam)** e, em seguida, selecione **atribuições de função**.

![Verificar atribuição de função](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

Você deve ver sua identidade gerenciada listada na seção **colaborador de dados de blob de armazenamento** com a função de colaborador de dados de blob de *armazenamento* atribuída a ela. 
![Seleção de contêiner de conta de armazenamento ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [identidade gerenciada do espaço de trabalho](./synapse-workspace-managed-identity.md)
