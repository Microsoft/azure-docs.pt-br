---
title: Registrar e verificar Azure Data Lake Storage (ADLS) Gen1
description: Este tutorial descreve como verificar dados de Azure Data Lake Storage Gen1 para o Azure alcance.
author: shsandeep123
ms.author: sandeepshah
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: a831681f892de9f6aae50fa9a2fcf71e883fe6ba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693708"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>Registrar e verificar Azure Data Lake Storage Gen1

Este artigo descreve como registrar Azure Data Lake Storage Gen1 como fonte de dados no Azure alcance e configurar uma verificação nele.

> [!Note]
> O Azure Data Lake Storage Gen2 já se encontra disponível ao público em geral. É recomendável que você comece a usá-lo hoje mesmo. Para saber mais, consulte a [página do produto](https://azure.microsoft.com/services/storage/data-lake-storage/).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

A fonte de dados Azure Data Lake Storage Gen1 dá suporte à seguinte funcionalidade:

- **Verificações completas e incrementais** para capturar metadados e classificação no Azure data Lake Storage Gen1

- **Linhagem** entre os ativos de dados para as atividades de cópia/Dataflow do ADF

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registrar as fontes de dados, crie uma conta do Azure alcance. Para obter mais informações sobre como criar uma conta do alcance, consulte [início rápido: criar uma conta do Azure alcance](create-catalog-portal.md).
- Você precisa ser um administrador de fonte de dados do Azure alcance

## <a name="setting-up-authentication-for-a-scan"></a>Configurar a autenticação para uma verificação

Os seguintes métodos de autenticação têm suporte para Azure Data Lake Storage Gen1:

- Identidade Gerenciada
- Entidade de serviço

### <a name="managed-identity-recommended"></a>Identidade gerenciada (recomendado)

Para facilitar e segurança, talvez você queira usar o alcance MSI para autenticar com seu armazenamento de dados.

Para configurar uma verificação usando a MSI do catálogo de dados, primeiro você deve conceder à sua conta do alcance a permissão para verificar a fonte de dados. Esta etapa deve ser feita *antes* de configurar a verificação ou a verificação falhará.

#### <a name="adding-the-data-catalog-msi-to-an-azure-data-lake-storage-gen1-account"></a>Adicionando o MSI do catálogo de dados a uma conta de Azure Data Lake Storage Gen1

Você pode adicionar o MSI do catálogo à assinatura, ao grupo de recursos ou ao nível de recurso, dependendo do que você deseja que ele tenha permissões de verificação.

> [!Note]
> Você precisa ser um proprietário da assinatura para poder adicionar uma identidade gerenciada em um recurso do Azure.

1. Na [portal do Azure](https://portal.azure.com), localize a assinatura, o grupo de recursos ou o recurso (por exemplo, uma conta de armazenamento de Azure data Lake Storage Gen1) que você deseja permitir que o catálogo Verifique.

2. Clique em **visão geral** e selecione **Data Explorer**

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Escolha o controle de acesso":::

3. Clique em **acesso** na barra de navegação superior

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Clique em acesso":::

4. Clique em **Adicionar**. Adicione o **Catálogo alcance** na seleção Selecionar usuário ou grupo. Selecione permissões de **leitura** e **execução** . Certifique-se de escolher **essa pasta e todos os filhos** na opção Adicionar a, conforme mostrado na captura de tela abaixo e clique em **OK** 
    :::image type="content" source="./media/register-scan-adls-gen1/managed-instance-authentication.png" alt-text="detalhes de autenticação do MSI":::

5. Se o cofre de chaves ainda não estiver conectado ao alcance, você precisará [criar uma nova conexão de Key Vault](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Por fim, [crie uma credencial](manage-credentials.md#create-a-new-credential) usando a entidade de serviço para configurar a verificação
> [!Note]
> Depois de adicionar o MSI do catálogo na fonte de dados, aguarde até 15 minutos para que as permissões se propaguem antes de configurar uma verificação.

Após cerca de 15 minutos, o catálogo deve ter as permissões apropriadas para poder verificar os recursos.

### <a name="service-principal"></a>Entidade de serviço

Para usar uma entidade de serviço, você deve primeiro criar uma das seguintes etapas:

1. Navegue até o [Portal do Azure](https://portal.azure.com).

2. Selecione **Azure Active Directory** no menu do lado esquerdo.

3. Selecione **Registros do Aplicativo**.

4. Selecione **+Novo registro de aplicativo**.

5. Insira um nome para o **aplicativo** (o nome da entidade de serviço).

6. Escolha **Somente contas neste diretório organizacional**.

7. Para **URI de redirecionamento** , selecione **Web** e insira qualquer URL desejada; Ele não precisa ser real nem funcionar.

8. Em seguida, selecione **Registrar**.

9. Copie os valores do nome de exibição e da ID do aplicativo.

#### <a name="adding-the-data-catalog-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>Adicionando a entidade de serviço do catálogo de dados a uma conta de Azure Data Lake Storage Gen1
1. Na [portal do Azure](https://portal.azure.com), localize a assinatura, o grupo de recursos ou o recurso (por exemplo, uma conta de armazenamento de Azure data Lake Storage Gen1) que você deseja permitir que o catálogo Verifique.

2. Clique em **visão geral** e selecione **Data Explorer**

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Escolha o controle de acesso":::

3. Clique em **acesso** na barra de navegação superior

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Clique em acesso":::

4. Clique em **Adicionar**. Adicione o **aplicativo de entidade de serviço** na seleção Selecionar usuário ou grupo. Selecione permissões de **leitura** e **execução** . Certifique-se de escolher **essa pasta e todos os filhos** na opção Adicionar a, conforme mostrado na captura de tela abaixo e clique em **OK** 
    :::image type="content" source="./media/register-scan-adls-gen1/service-principal-authentication.png" alt-text="detalhes de autenticação da entidade de serviço":::

5. Se o cofre de chaves ainda não estiver conectado ao alcance, você precisará [criar uma nova conexão de Key Vault](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Por fim, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando a entidade de serviço para configurar sua verificação.

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>Registrar Azure Data Lake Storage Gen1 fonte de dados

Para registrar uma nova conta de ADLS Gen1 no catálogo de dados, faça o seguinte:

1. Navegue até o catálogo de dados do alcance.
2. Selecione **Fontes** no painel de navegação à esquerda.
3. Escolha **Registrar**
4. Em **registrar fontes**, selecione **Azure data Lake Storage Gen1**. 
5. Selecione **Continuar**.

Na tela registrar fontes (Azure Data Lake Storage Gen1), faça o seguinte:

1. Insira um **Nome** com a qual a fonte de dados será listada no Catálogo.
2. Escolha sua assinatura para filtrar as contas de armazenamento
3. Selecione uma conta de armazenamento
4. Selecionar ou criar uma coleção (opcional)
5. Conclua a etapa para registrar a fonte de dados.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Próximas etapas

- [Navegar pelo Catálogo de Dados do Azure Purview](how-to-browse-catalog.md)
- [Pesquisar no Catálogo de Dados do Azure Purview](how-to-search-catalog.md)
