---
title: Como verificar arquivos do Azure
description: Este guia de instruções descreve os detalhes de como verificar os arquivos do Azure.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/01/2020
ms.openlocfilehash: a0bd7a4cd8afafc16f05b4a37cd5723304ad931e
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551583"
---
# <a name="register-and-scan-azure-files"></a>Registrar e verificar arquivos do Azure

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Os arquivos do Azure dão suporte a verificações completas e incrementais para capturar os metadados e aplicar classificações nos metadados, com base nas classificações do sistema e do cliente.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registrar as fontes de dados, crie uma conta do Azure alcance. Para obter mais informações sobre como criar uma conta do alcance, consulte [início rápido: criar uma conta do Azure alcance](create-catalog-portal.md).
- Você precisa ser um administrador de fonte de dados para configurar e agendar verificações, consulte [permissões de catálogo](catalog-permissions.md) para obter detalhes.

## <a name="register-an-azure-files-storage-account"></a>Registrar uma conta de armazenamento de arquivos do Azure

Para registrar uma nova conta dos arquivos do Azure em seu catálogo de dados, faça o seguinte:

1. Navegue até o catálogo de dados do alcance.
1. Selecione **centro de gerenciamento** no painel de navegação esquerdo.
1. Selecione **fontes de dados** em **fontes e varredura**.
1. Selecione **+ Novo**.
1. Em **registrar fontes**, selecione **arquivos do Azure**. Selecione **Continuar**.

:::image type="content" source="media/register-scan-azure-files/register-new-data-source.png" alt-text="registrar nova fonte de dados" border="true":::

Na tela **registrar fontes (arquivos do Azure)** , faça o seguinte:

1. Insira um **nome** no qual a fonte de dados será listada no catálogo.
1. Escolha como você deseja apontar para a conta de armazenamento desejada:
   1. Selecione **da assinatura do Azure**, selecione a assinatura apropriada na caixa suspensa **assinatura do Azure** e a conta de armazenamento apropriada na caixa suspensa nome da **conta de armazenamento** .
   1. Ou, você pode selecionar **inserir manualmente** e inserir um ponto de extremidade de serviço (URL).
1. **Concluir** para registrar a fonte de dados.

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="opções de registrar fontes" border="true":::

## <a name="set-up-authentication-for-a-scan"></a>Configurar a autenticação para uma verificação

Para configurar a autenticação para o armazenamento de arquivos do Azure usando uma chave de conta, faça o seguinte:

1. Selecione o método de autenticação como **chave de conta**.
2. Selecione a opção **de assinatura do Azure** .
3. Escolha sua assinatura do Azure em que a conta de arquivos do Azure existe.
4. Escolha o nome da conta de armazenamento na lista.
5. Clique em **Concluir**.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Próximas etapas

- [Procurar o catálogo de dados do Azure alcance](how-to-browse-catalog.md)
- [Pesquisar no catálogo de dados do Azure alcance](how-to-search-catalog.md)