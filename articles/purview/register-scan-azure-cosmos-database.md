---
title: Como verificar o banco de dados Cosmos do Azure (API do SQL)
description: Este guia de instruções descreve os detalhes de como verificar o banco de dados Cosmos do Azure (API do SQL).
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 1aaeed1973ebd15af312b722ab61938aa4271947
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696253"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Registrar e verificar o banco de dados Cosmos do Azure (API do SQL)

Este artigo descreve como registrar uma conta do banco de dados do cosmos do Azure (API do SQL) no Azure alcance e configurar uma verificação.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

O banco de dados Cosmos do Azure (API do SQL) dá suporte a verificações completas e incrementais para capturar os metadados e o esquema. As verificações também classificam os dados automaticamente com base em regras de classificação personalizada e do sistema.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registrar as fontes de dados, crie uma conta do Azure alcance. Para obter mais informações sobre como criar uma conta do alcance, consulte [início rápido: criar uma conta do Azure alcance](create-catalog-portal.md).
- Você precisa ser um administrador de fonte de dados do Azure alcance

## <a name="setting-up-authentication-for-a-scan"></a>Configurar a autenticação para uma verificação

Há apenas uma maneira de configurar a autenticação para o banco de dados Cosmos do Azure (API do SQL):

- Chave de conta
 
### <a name="account-key"></a>Chave de conta

Quando o método de autenticação selecionado é **chave de conta**, você precisa obter sua chave de acesso e armazenar no cofre de chaves:

1. Navegue até sua conta de Cosmos DB no portal do Azure 
1. Selecionar   >  **chaves** de configurações 
1. Copie sua *chave* e salve-a em algum lugar para as próximas etapas
1. Navegue até o Key Vault
1. Selecione **Configurações > Segredos**
1. Selecione **+ gerar/importar** e insira o **nome** e o **valor** como a *chave* de sua conta de armazenamento
1. Selecione **Criar** para terminar
1. Se o cofre de chaves ainda não estiver conectado ao Purview, [crie uma conexão do cofre de chaves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Por fim, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando a chave para configurar sua verificação

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Registrar uma conta do banco de dados Cosmos do Azure (API do SQL)

Para registrar uma nova conta do banco de dados Cosmos do Azure (API do SQL) em seu catálogo de data, faça o seguinte:

1. Acesse sua conta do Purview
1. Selecionar **Fontes** no painel de navegação à esquerda
1. Escolha **Registrar**
1. Em **registrar fontes**, selecione **Azure Cosmos dB (API do SQL)**
1. Selecione **Continuar**

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="registrar uma nova fonte de dados" border="true":::

Na tela **registrar fontes (Azure Cosmos dB (API do SQL)** , faça o seguinte:

1. Insira um **Nome** com a qual a fonte de dados será listada no Catálogo.
1. Escolha como deseja apontá-la para a conta de armazenamento desejada:
   1. Selecione **da assinatura do Azure**, selecione a assinatura apropriada na caixa suspensa **assinatura do Azure** e a conta cosmosDB apropriada na caixa suspensa nome da **conta do cosmos DB** .
   1. Ou, você pode selecionar **inserir manualmente** e inserir um ponto de extremidade de serviço (URL).
1. **Conclua** a etapa para registrar a fonte de dados.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="opções de registro de fontes" border="true":::


[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Próximas etapas

- [Navegar pelo Catálogo de Dados do Azure Purview](how-to-browse-catalog.md)
- [Pesquisar no Catálogo de Dados do Azure Purview](how-to-search-catalog.md)
