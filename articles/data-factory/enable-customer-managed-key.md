---
title: Criptografar o Azure Data Factory com uma chave gerenciada pelo cliente
description: Aprimorar a segurança do Data Factory com o BYOK (Bring Your Own Key)
author: chez-charlie
ms.service: data-factory
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: chez
ms.reviewer: mariozi
ms.openlocfilehash: a18d06e3a0324889a4cb9936fb339fd9d8f9b816
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222668"
---
# <a name="encrypt-azure-data-factory-with-customer-managed-keys"></a>Criptografar o Azure Data Factory com chaves gerenciadas pelo cliente

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

O Azure Data Factory criptografa dados em repouso, incluindo definições de entidade e todos os dados armazenados em cache enquanto as execuções estão em andamento. Por padrão, os dados são criptografados com uma chave gerenciada pela Microsoft gerada aleatoriamente, atribuída exclusivamente ao seu data factory. Para maior garantia de segurança, agora você pode habilitar a opção BYOK (Bring Your Own Key) com o recurso de chaves gerenciadas pelo cliente no Azure Data Factory. Quando você especifica uma chave gerenciada pelo cliente, o Data Factory usa __ambas__, a chave de sistema e a CMK, para criptografar os dados do cliente. A ausência de qualquer uma delas resultaria na Negação de Acesso aos dados e ao alocador.

O Azure Key Vault é obrigatório para armazenar as chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves. O cofre de chaves e o Data Factory devem estar no mesmo locatário do Azure AD (Azure Active Directory) e na mesma região, mas podem estar em assinaturas diferentes. Para obter mais informações sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](../key-vault/general/overview.md)

## <a name="about-customer-managed-keys"></a>Sobre as chaves gerenciadas pelo cliente

O diagrama a seguir mostra como o Data Factory usa o Azure Active Directory e o Azure Key Vault para fazer solicitações usando a chave gerenciada pelo cliente:

  :::image type="content" source="media/enable-customer-managed-key/encryption-customer-managed-keys-diagram.png" alt-text="Diagrama que mostra como as chaves gerenciadas pelo cliente funcionam no Azure Data Factory.":::

A seguinte lista explica as etapas enumeradas no diagrama:

1. Um administrador do Azure Key Vault concede permissões a chaves de criptografia para a identidade gerenciada associada ao Data Factory
1. Um administrador do Data Factory habilita o recurso de chave gerenciada pelo cliente no alocador
1. O Data Factory usa a identidade gerenciada associada ao alocador para autenticar o acesso ao Azure Key Vault por meio do Azure Active Directory
1. O Data Factory encapsula a chave de criptografia do alocador com a chave do cliente no Azure Key Vault
1. Para operações de leitura/gravação, o Data Factory envia solicitações ao Azure Key Vault para desencapsular a chave de criptografia da conta a fim de executar operações de criptografia e descriptografia

Há duas maneiras de adicionar criptografia de chave gerenciada pelo cliente às fábricas de dados. Uma é durante o tempo de criação de fábrica no portal do Azure, e a outra é após a criação da fábrica, na interface do usuário do Data Factory.

## <a name="prerequisites---configure-azure-key-vault-and-generate-keys"></a>Pré-requisitos – Configurar o Azure Key Vault e gerar chaves

### <a name="enable-soft-delete-and-do-not-purge-on-azure-key-vault"></a>Habilitar as propriedades de Exclusão Temporária e Não Limpar no Azure Key Vault

O uso de chaves gerenciadas pelo cliente com o Data Factory exige que duas propriedades sejam definidas no Key Vault: __Exclusão Temporária__ e __Não Limpar__. Essas propriedades podem ser habilitadas usando o PowerShell ou a CLI do Azure em um cofre de chaves novo ou existente. Para saber como habilitar essas propriedades em um cofre de chaves existente, confira [Gerenciamento de recuperação do Azure Key Vault com exclusão temporária e proteção de limpeza](../key-vault/general/key-vault-recovery.md)

Se você estiver criando um novo Azure Key Vault por meio do portal do Azure, as propriedades __Exclusão Temporária__ e __Não Limpar__ podem ser habilitadas da seguinte maneira:

  :::image type="content" source="media/enable-customer-managed-key/01-enable-purge-protection.png" alt-text="Captura de tela mostrando como habilitar a Exclusão Temporária e Proteção de Limpeza ao criar o Key Vault.":::

### <a name="grant-data-factory-access-to-azure-key-vault"></a>Permitir ao Data Factory acesso ao Azure Key Vault

Verifique se o Azure Key Vault e o Azure Data Factory estão no mesmo locatário do Azure AD (Azure Active Directory) e na _mesma região_. No controle de acesso do Azure Key Vault, conceda ao data factory as seguintes permissões: _Get_, _Desencapsular Chave_ e _Encapsular Chave_. Essas permissões são obrigatórias para habilitar as chaves gerenciadas pelo cliente no Data Factory.

* Se você quiser adicionar a criptografia de chave gerenciada pelo cliente [após a criação da fábrica na interface do usuário do Data Factory](#post-factory-creation-in-data-factory-ui), verifique se a MSI (Identidade de Serviço Gerenciado) do Data Factory tem as três permissões para o Key Vault
* Se você quiser adicionar a criptografia de chave gerenciada pelo cliente [durante a criação da fábrica no portal do Azure](#during-factory-creation-in-azure-portal), verifique se a UA-MI (identidade gerenciada atribuída pelo usuário) tem as três permissões para o Key Vault

  :::image type="content" source="media/enable-customer-managed-key/02-access-policy-factory-managed-identities.png" alt-text="Captura de tela mostrando como habilitar o acesso do Data Factory ao Key Vault.":::

### <a name="generate-or-upload-customer-managed-key-to-azure-key-vault"></a>Gerar ou carregar uma chave gerenciada pelo cliente no Azure Key Vault

Você pode criar suas próprias chaves e armazená-las em um cofre de chaves. Ou você pode usar as APIs do Azure Key Vault para gerar chaves. Somente chaves RSA de 2.048 bits são compatíveis com a criptografia do Data Factory. Para obter mais informações, consulte [Sobre chaves, segredos e certificados](../key-vault/general/about-keys-secrets-certificates.md).

  :::image type="content" source="media/enable-customer-managed-key/03-create-key.png" alt-text="Captura de tela mostrando como gerar uma chave gerenciada pelo cliente.":::

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

### <a name="post-factory-creation-in-data-factory-ui"></a>Criação depois da fábrica na interface do usuário do Data Factory

Esta seção percorre o processo para adicionar a criptografia de chave gerenciada pelo cliente à interface do usuário do Data Factory, _depois_ que a fábrica é criada.

> [!NOTE]
> Uma chave gerenciada pelo cliente só pode ser configurada em um data factory vazio. O data factory não pode conter nenhum recurso, como serviços vinculados, pipelines ou fluxos de dados. É recomendável habilitar a chave gerenciada pelo cliente logo após a criação do alocador.

> [!IMPORTANT]
> Essa abordagem não funciona com fábricas habilitadas para rede virtual gerenciada. Considere a [rota alternativa](#during-factory-creation-in-azure-portal) se quiser criptografar tais fábricas.

1. Verifique se a MSI (Identidade de Serviço Gerenciado) do data factory tem as permissões _Get_, _Desencapsular Chave_ e _Encapsular Chave_ sobre o Key Vault.

1. Verifique se o Data Factory está vazio. O data factory não pode conter nenhum recurso, como serviços vinculados, pipelines ou fluxos de dados. Por enquanto, implantar uma chave gerenciada pelo cliente em um alocador não vazio resultará em um erro.

1. Para localizar o URI da chave no portal do Azure, navegue até o Azure Key Vault e selecione a configuração de Chaves. Selecione a chave desejada, depois clique sobre ela para exibir as versões dela. Selecione uma versão da chave para exibir as configurações

1. Copie o valor do campo identificador de chave, que fornece a :::image type="content" source="media/enable-customer-managed-key/04-get-key-identifier.png" alt-text="captura de tela do URI para obter o URI de chave do Key Vault.":::

1. Inicie o portal do Azure Data Factory e, usando a barra de navegação à esquerda, vá para o Portal de Gerenciamento do Data Factory

1. Clique no ícone de __chave gerenciada pelo cliente__. :::image type="content" source="media/enable-customer-managed-key/05-customer-managed-key-configuration.png" alt-text="Captura de tela mostrando como habilitar a chave gerenciada pelo cliente na interface do usuário do Data Factory.":::

1. Insira o URI da chave gerenciada pelo cliente que você copiou anteriormente

1. Clique em __Salvar__ e a criptografia da chave gerenciada pelo cliente será habilitada para o Data Factory

### <a name="during-factory-creation-in-azure-portal"></a>Durante a criação da fábrica no portal do Azure

Esta seção percorre as etapas para adicionar a criptografia de chave gerenciada pelo cliente no portal do Azure, _durante_ a implantação da fábrica.

Para criptografar a fábrica, o Data Factory precisa primeiro recuperar a chave gerenciada pelo cliente do Key Vault. Como a implantação de fábrica ainda está em andamento, a MSI (Identidade de Serviço Gerenciado) ainda não está disponível para autenticação com o Key Vault. Dessa forma, para usar essa abordagem, o cliente precisa atribuir uma UA-MI (identidade gerenciada atribuída pelo usuário) ao data factory. Vamos pressupor as funções definidas na UA-MI e autenticar com o Key Vault.

Para saber mais sobre a identidade gerenciada atribuída ao usuário, confira os [Tipos de identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) e as [Atribuições de função para identidade gerenciada atribuída ao usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Verifique se a UA-MI (identidade gerenciada atribuída pelo usuário) tem as permissões _Get_, _Desencapsular Chave_ e _Encapsular Chave_ sobre o Key Vault

1. Na guia __Avançado__, marque a caixa _Habilitar a criptografia usando uma chave gerenciada pelo cliente_
  :::image type="content" source="media/enable-customer-managed-key/06-user-assigned-managed-identity.png" alt-text="Captura de tela da guia Avançado para obter experiência com a criação do data factory em portal do Azure.":::

1. Forneça a URL para a chave gerenciada pelo cliente armazenada no Key Vault

1. Selecione uma identidade gerenciada atribuída ao usuário apropriada para autenticar com Key Vault

1. Continuar com a implantação de fábrica

## <a name="update-key-version"></a>Atualizar a versão da chave

Ao criar uma versão de uma chave, atualize o data factory para usar a nova versão. Siga etapas semelhantes, conforme descrito na seção [Interface do usuário do Data Factory](#post-factory-creation-in-data-factory-ui), incluindo:

1. Localize o URI da nova versão da chave por meio do portal do Azure Key Vault

1. Navegue até as configurações da __Chave gerenciada pelo cliente__

1. Substitua e cole o URI da nova chave

1. Clique em __Salvar__ e o Data Factory passará a criptografar com a nova versão da chave

## <a name="use-a-different-key"></a>Usar uma chave diferente

Para alterar a chave usada para a criptografia do Data Factory, você precisa atualizar manualmente as configurações do Data Factory. Siga etapas semelhantes, conforme descrito na seção [Interface do usuário do Data Factory](#post-factory-creation-in-data-factory-ui), incluindo:

1. Localize o URI da nova chave por meio do portal do Azure Key Vault

1. Navegue até as configurações da __Chave gerenciada pelo cliente__

1. Substitua e cole o URI da nova chave

1. Clique em __Salvar__ e o Data Factory passará a criptografar com a nova chave

## <a name="disable-customer-managed-keys"></a>Desabilitar as chaves gerenciadas pelo cliente

Por padrão, uma vez que o recurso de chave gerenciada pelo cliente está habilitado, você não pode remover a etapa de segurança extra. Sempre vamos esperar uma chave fornecida pelo cliente para criptografar o alocador e os dados.

## <a name="customer-managed-key-and-continuous-integration-and-continuous-deployment"></a>Chave gerenciada pelo cliente e integração contínua e implantação contínua

Por padrão, a configuração da CMK não está incluída no modelo do ARM (Azure Resource Manager) do factory. Para incluir as configurações de criptografia de chave gerenciada pelo cliente no modelo do ARM para CI/CD (integração contínua):

1. Verifique se o factory está no modo Git
1. Navegue até o portal de gerenciamento, seção da chave gerenciada pelo cliente
1. Marque a opção _Incluir no modelo do ARM_

  :::image type="content" source="media/enable-customer-managed-key/07-include-in-template.png" alt-text="Captura de tela da inclusão da configuração da chave gerenciada pelo cliente no modelo do ARM.":::

As configurações a seguir serão adicionadas ao modelo do ARM. Essas propriedades podem ser parametrizadas em pipelines de entrega e integração contínua pela edição da [configuração de parâmetros do Azure Resource Manager](continuous-integration-deployment.md#use-custom-parameters-with-the-resource-manager-template)

  :::image type="content" source="media/enable-customer-managed-key/08-template-with-customer-managed-key.png" alt-text="Captura de tela da inclusão da configuração da chave gerenciada pelo cliente no modelo do Azure Resource Manager.":::

> [!NOTE]
> A adição da configuração de criptografia aos modelos do ARM inclui uma configuração no nível do factory que substituirá outras configurações no nível do factory, como as configurações do Git, em outros ambientes. Se você tiver essas configurações habilitadas em um ambiente com privilégios elevados, como UAT ou PROD, veja [Parâmetros globais na CI/CD](author-global-parameters.md#cicd).

## <a name="next-steps"></a>Próximas etapas

Percorra os [tutoriais](tutorial-copy-data-dot-net.md) para saber mais sobre o uso do Data Factory em mais cenários.
