---
title: Como verificar Data Explorer do Azure
description: Este guia de instruções descreve os detalhes de como verificar o Azure Data Explorer.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 7adc7f568fb82692f2c96f610575076e397bd99c
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896080"
---
# <a name="register-and-scan-azure-data-explorer"></a>Registrar e verificar Data Explorer do Azure

Este artigo descreve como registrar uma conta de Data Explorer do Azure no Azure alcance e configurar uma verificação.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

O Azure Data Explorer dá suporte a verificações completas e incrementais para capturar os metadados e o esquema. As verificações também classificam os dados automaticamente com base em regras de classificação personalizada e do sistema.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registrar as fontes de dados, crie uma conta do Azure alcance. Para obter mais informações sobre como criar uma conta do alcance, consulte [início rápido: criar uma conta do Azure alcance](create-catalog-portal.md).
- Você precisa ser um administrador de fonte de dados do Azure alcance

## <a name="setting-up-authentication-for-a-scan"></a>Configurar a autenticação para uma verificação

Há apenas uma maneira de configurar a autenticação para o Azure data Explorer:

- Entidade de Serviço

### <a name="service-principal"></a>Entidade de serviço

Para usar a autenticação de entidade de serviço para verificações, você pode usar uma existente ou criar uma nova. 

> [!Note]
> Caso precise criar uma entidade de serviço, siga estas etapas:
> 1. Navegue até o [Portal do Azure](https://portal.azure.com).
> 1. Selecione **Azure Active Directory** no menu do lado esquerdo.
> 1. Selecione **Registros do Aplicativo**.
> 1. Selecione **+Novo registro de aplicativo**.
> 1. Insira um nome para o **aplicativo** (o nome da entidade de serviço).
> 1. Escolha **Somente contas neste diretório organizacional**.
> 1. Em URI de Redirecionamento, selecione **Web** e insira qualquer URL desejada; ela não precisa ser real nem funcionar.
> 1. Em seguida, selecione **Registrar**.

É necessário obter a ID e o segredo do aplicativo da entidade de serviço:

1. Navegue até a sua entidade de serviço no [portal do Azure](https://portal.azure.com)
1. Copie os valores de **ID do Aplicativo (cliente)** em **Visão geral** e de **Segredo do cliente** em **Certificados e segredos**.
1. Navegue até o Key Vault
1. Selecione **Configurações > Segredos**
1. Selecione **+ Gerar/Importar** e insira o **Nome** de sua escolha e o **Valor** como o **Segredo do cliente** da entidade de serviço
1. Selecione **Criar** para terminar
1. Se o cofre de chaves ainda não estiver conectado ao Purview, [crie uma conexão do cofre de chaves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Por fim, [crie uma credencial](manage-credentials.md#create-a-new-credential) usando a entidade de serviço para configurar a verificação

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>Concedendo acesso à entidade de serviço à instância do Gerenciador de dados do Azure

1. Navegue até o Portal do Azure. Em seguida, navegue até a instância do Gerenciador de dados do Azure.

1. Adicione a entidade de serviço à função **AllDatabasesViewer** na guia **permissões** , conforme mostrado na captura de tela a seguir.

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="Captura de tela para adicionar a entidade de serviço nas permissões" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>Registrar uma conta de Data Explorer do Azure

Para registrar uma nova conta de Data Explorer do Azure (Kusto) em seu catálogo de dados, faça o seguinte:

1. Acesse sua conta do Purview
1. Selecionar **Fontes** no painel de navegação à esquerda
1. Escolha **Registrar**
1. Em **registrar fontes**, selecione **Data Explorer do Azure**
1. Selecione **Continuar**

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="registrar uma nova fonte de dados" border="true":::

Na tela **registrar fontes (data Explorer do Azure (Kusto))** , faça o seguinte:

1. Insira um **Nome** com a qual a fonte de dados será listada no Catálogo.
1. Escolha como deseja apontá-la para a conta de armazenamento desejada:
   1. Selecione **da assinatura do Azure**, selecione a assinatura apropriada na caixa suspensa **assinatura do Azure** e o cluster apropriado na caixa suspensa **cluster** .
   1. Ou, você pode selecionar **inserir manualmente** e inserir um ponto de extremidade de serviço (URL).
1. **Conclua** a etapa para registrar a fonte de dados.

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="opções de registro de fontes" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans-azure-data-explorer.md)]

## <a name="next-steps"></a>Próximas etapas

- [Navegar pelo Catálogo de Dados do Azure Purview](how-to-browse-catalog.md)
- [Pesquisar no Catálogo de Dados do Azure Purview](how-to-search-catalog.md)
