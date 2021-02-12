---
title: Configurar as chaves gerenciadas pelo cliente para a API do Azure para FHIR
description: Recurso Bring Your Own Key com suporte na API do Azure para FHIR por meio do Cosmos DB
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: ginle
ms.openlocfilehash: ae78aa80594e46b02d77adcafed961e801780d4f
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430252"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Configurar chaves gerenciada pelo cliente em repouso

Quando você criar uma conta da API do Azure para FHIR, os seus dados serão criptografados usando as chaves gerenciadas pela Microsoft por padrão. Agora, você pode adicionar uma segunda camada de criptografia nos dados usando a própria chave escolhida e gerenciada por conta própria.

No Azure, isso normalmente é feito usando uma chave de criptografia no Azure Key Vault do cliente. O SQL do Azure, o Armazenamento do Azure e o Cosmos DB são alguns exemplos que oferecem essa funcionalidade hoje em dia. A API do Azure para FHIR aproveita esse suporte do Cosmos DB. Ao criar uma conta, você terá a opção de especificar um URI de chave do Azure Key Vault. Essa chave será passada para o Cosmos DB quando a conta do BD for provisionada. Quando uma solicitação FHIR é feita, o Cosmos DB busca a sua chave e a usa para criptografar/descriptografar os dados. Para começar, você pode ver os seguintes links:

- [Registrar o provedor de recursos do Azure Cosmos DB para a sua assinatura do Azure](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Configurar sua instância do Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Adicionar uma política de acesso à sua instância do Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Gerar uma chave no Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="specify-the-azure-key-vault-key"></a>Especificar a chave do Azure Key Vault

Ao criar a conta da API do Azure para FHIR no portal do Azure, você poderá ver uma opção de configuração "Criptografia de Dados" em "Configurações do Banco de Dados" na guia "Configurações Adicionais". Por padrão, a opção de chave gerenciada pelo serviço será escolhida. 

Você pode escolher a chave no KeyPicker:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="KeyPicker":::

Ou pode especificar a sua chave do Azure Key Vault aqui selecionando a opção "Chave gerenciada pelo cliente". Você pode inserir o URI de chave aqui:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Criar a API do Azure para FHIR":::

Para contas do FHIR existentes, você pode ver a opção de criptografia de chave (chave gerenciada pelo cliente ou pelo serviço) na folha "Banco de Dados", conforme descrito abaixo. A opção de configuração não poderá ser modificada depois de ser escolhida. No entanto, você pode modificar e atualizar a sua chave.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Backup de banco de dados":::

Além disso, você pode criar uma versão da chave especificada, após a qual os seus dados serão criptografados com a nova versão sem nenhuma interrupção do serviço. Você também pode remover o acesso à chave para remover o acesso aos dados. Quando a chave estiver desabilitada, as consultas resultarão em um erro. Se a chave for habilitada novamente, as consultas terão sucesso novamente.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a configurar chaves gerenciadas pelo cliente inativas. Em seguida, você pode conferir a seção de perguntas frequentes do Azure Cosmos DB: 
 
>[!div class="nextstepaction"]
>[Cosmos DB: como configurar CMK](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#frequently-asked-questions)
