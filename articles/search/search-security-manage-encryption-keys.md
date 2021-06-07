---
title: Criptografia em repouso usando chaves gerenciadas pelo cliente
titleSuffix: Azure Cognitive Search
description: Complemente a criptografia do lado do servidor sobre índices e mapas de sinônimos no Azure Pesquisa Cognitiva usando chaves que você cria e gerencia no Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: 6b1079797f1a753fa8362d6e920f3394087d7e9f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98119281"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Configurar chaves gerenciadas pelo cliente para criptografia de dados no Azure Pesquisa Cognitiva

O Azure Pesquisa Cognitiva criptografa automaticamente o conteúdo indexado em repouso com [chaves gerenciadas pelo serviço](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components). Se for necessária mais proteção, você poderá complementar a criptografia padrão com uma camada de criptografia adicional usando as chaves que você cria e gerencia no Azure Key Vault. Este artigo orienta você pelas etapas de configuração da criptografia de chave gerenciada pelo cliente.

A criptografia de chave gerenciada pelo cliente depende do [Azure Key Vault](../key-vault/general/overview.md). Você pode criar suas próprias chaves de criptografia e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves de criptografia. Com Azure Key Vault, você também pode auditar o uso da chave se [habilitar o registro em log](../key-vault/general/logging.md).  

A criptografia com chaves gerenciadas pelo cliente é aplicada a índices individuais ou mapas de sinônimos quando esses objetos são criados e não é especificado no próprio nível de serviço de pesquisa. Somente novos objetos podem ser criptografados. Você não pode criptografar o conteúdo que já existe.

As chaves nem todos precisam estar no mesmo cofre de chaves. Um único serviço de pesquisa pode hospedar vários índices criptografados ou mapas de sinônimos, cada um criptografado com suas próprias chaves de criptografia gerenciadas pelo cliente, armazenadas em diferentes cofres de chaves. Você também pode ter índices e mapas de sinônimos no mesmo serviço que não são criptografados usando chaves gerenciadas pelo cliente.

>[!Important]
> Se você implementar chaves gerenciadas pelo cliente, não se esqueça de seguir os procedimentos estritos durante a rotação de rotina das chaves do Key Vault e Active Directory os segredos e o registro do aplicativo. Sempre atualize todo o conteúdo criptografado para usar novos segredos e chaves antes de excluir os antigos. Se você perder essa etapa, o conteúdo não poderá ser descriptografado.

## <a name="double-encryption"></a>Criptografia dupla

Para serviços criados após 1º de agosto de 2020 e em regiões específicas, o escopo da criptografia de chave gerenciada pelo cliente inclui discos temporários, obtendo [criptografia dupla completa](search-security-overview.md#double-encryption), atualmente disponível nessas regiões: 

+ Oeste dos EUA 2
+ Leste dos EUA
+ Centro-Sul dos Estados Unidos
+ Gov. dos EUA – Virgínia
+ Governo dos EUA do Arizona

Se você estiver usando uma região diferente ou um serviço criado antes de 1º de agosto, a criptografia de chave gerenciada será limitada apenas ao disco de dados, excluindo os discos temporários usados pelo serviço.

## <a name="prerequisites"></a>Pré-requisitos

As ferramentas e os serviços a seguir são usados neste cenário.

+ O [Azure pesquisa cognitiva](search-create-service-portal.md) em uma [camada Faturável](search-sku-tier.md#tier-descriptions) (básica ou acima, em qualquer região).
+ [Azure Key Vault](../key-vault/general/overview.md), você pode criar um cofre de chaves usando [portal do Azure](../key-vault//general/quick-create-portal.md), [CLI do Azure](../key-vault//general/quick-create-cli.md)ou [Azure PowerShell](../key-vault//general/quick-create-powershell.md). na mesma assinatura que o Pesquisa Cognitiva do Azure. O cofre de chaves deve ter a proteção de exclusão e **limpeza** **reversível** habilitada.
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Se você não tiver uma, [Configure um novo locatário](../active-directory/develop/quickstart-create-new-tenant.md).

Você deve ter um aplicativo de pesquisa que possa criar o objeto criptografado. Nesse código, você fará referência a uma chave do Key Vault e Active Directory informações de registro. Esse código pode ser um aplicativo em funcionamento ou um código de protótipo como o [exemplo de código C# DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK).

> [!TIP]
> Você pode usar o [postmaster](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)ou [Azure PowerShell](./search-get-started-powershell.md)para chamar as APIs REST que criam índices e mapas de sinônimos que incluem um parâmetro de chave de criptografia. Não há suporte do portal para adicionar uma chave a índices ou mapas de sinônimos no momento.

## <a name="1---enable-key-recovery"></a>1-Habilitar recuperação de chave

Devido à natureza da criptografia com chaves gerenciadas pelo cliente, ninguém poderá recuperar seus dados se a chave do cofre de chaves do Azure for excluída. Para evitar a perda de dados causada por exclusões de chave Key Vault acidental, a exclusão reversível e a proteção de limpeza devem ser habilitadas no cofre de chaves. A exclusão reversível é habilitada por padrão, de modo que você só encontrará problemas se o tiver desabilitado propositadamente. A proteção de limpeza não é habilitada por padrão, mas é necessária para a criptografia de chave gerenciada pelo cliente no Pesquisa Cognitiva. Para obter mais informações, consulte visões gerais de exclusão e [limpeza](../key-vault/general/soft-delete-overview.md#purge-protection) [reversível](../key-vault/general/soft-delete-overview.md) .

Você pode definir as duas propriedades usando o portal, o PowerShell ou os comandos CLI do Azure.

### <a name="using-azure-portal"></a>Usando o Portal do Azure

1. [Entre no portal do Azure](https://portal.azure.com) e abra a página Visão geral do cofre de chaves.

1. Na página **visão geral** em **Essentials**, habilite a proteção de exclusão e **limpeza** **reversível** .

### <a name="using-powershell"></a>Usando o PowerShell

1. Execute `Connect-AzAccount` para configurar suas credenciais do Azure.

1. Execute o seguinte comando para se conectar ao cofre de chaves, substituindo `<vault_name>` por um nome válido:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault é criado com a exclusão reversível habilitada. Se ele estiver desabilitado em seu cofre, execute o seguinte comando:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Habilitar a proteção de limpeza:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Salve suas atualizações:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Usando a CLI do Azure

+ Se você tiver uma [instalação do CLI do Azure](/cli/azure/install-azure-cli), poderá executar o comando a seguir para habilitar as propriedades necessárias.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2-criar uma chave no Key Vault

Ignore esta etapa se você já tiver uma chave em Azure Key Vault.

1. [Entre no portal do Azure](https://portal.azure.com) e abra a página Visão geral do cofre de chaves.

1. Selecione **chaves** à esquerda e, em seguida, selecione **+ gerar/importar**.

1. No painel **criar uma chave** , na lista de **Opções**, escolha o método que você deseja usar para criar uma chave. Você pode **gerar** uma nova chave, **carregar** uma chave existente ou usar **restore backup** para selecionar um backup de uma chave.

1. Insira um **nome** para a chave e, opcionalmente, selecione outras propriedades de chave.

1. Selecione **criar** para iniciar a implantação.

1. Anote o identificador de chave – ele é composto pelo **URI do valor da chave**, pelo nome da **chave** e pela **versão da chave**. Você precisará do identificador para definir um índice criptografado no Azure Pesquisa Cognitiva.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Criar uma nova chave do Key Vault":::

## <a name="3---register-an-app-in-active-directory"></a>3-registrar um aplicativo no Active Directory

1. Em [portal do Azure](https://portal.azure.com), localize o recurso de Azure Active Directory para sua assinatura.

1. À esquerda, em **gerenciar**, selecione **registros de aplicativo** e, em seguida, selecione **novo registro**.

1. Dê um nome ao registro, talvez um nome semelhante ao nome do aplicativo de pesquisa. Selecione **Registrar**.

1. Depois que o registro do aplicativo for criado, copie a ID do aplicativo. Você precisará fornecer essa cadeia de caracteres para seu aplicativo. 

   Se você estiver percorrendo o [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK), Cole esse valor na **appsettings.jsno** arquivo.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="ID do aplicativo na seção Essentials":::

1. Em seguida, selecione **certificados & segredos** à esquerda.

1. Selecione **Novo segredo do cliente**. Dê ao segredo um nome de exibição e selecione **Adicionar**.

1. Copie o segredo do aplicativo. Se você estiver percorrendo o exemplo, Cole esse valor na **appsettings.jsno** arquivo.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Segredo do aplicativo":::

## <a name="4---grant-key-access-permissions"></a>4-conceder permissões de acesso à chave

Nesta etapa, você criará uma política de acesso no Key Vault. Essa política fornece ao aplicativo que você registrou com Active Directory permissão para usar a chave gerenciada pelo cliente.

As permissões de acesso podem ser revogadas em um determinado momento. Depois de revogado, qualquer índice de serviço de pesquisa ou mapa de sinônimos que usa esse cofre de chaves ficará inutilizável. Restaurar as permissões de acesso do cofre de chaves posteriormente irá restaurar o acesso ao mapa do index\synonym. Para obter mais informações, consulte [proteger o acesso a um cofre de chaves](../key-vault/general/secure-your-key-vault.md).

1. Ainda na portal do Azure, abra a página de **visão geral** do cofre de chaves. 

1. Selecione as **políticas de acesso** à esquerda e selecione **+ Adicionar política de acesso**.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Adicionar nova política de acesso do cofre de chaves":::

1. Escolha **selecionar entidade de segurança** e selecione o aplicativo que você registrou com Active Directory. Você pode procurá-lo por nome.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Selecionar entidade de segurança de acesso do cofre de chaves":::

1. Em **permissões de chave**, *escolha obter*, *desencapsular chave* e *encapsular chave*.

1. Em **permissões de segredo**, selecione *obter*.

1. Em **permissões de certificado**, selecione *obter*.

1. Selecione **Adicionar** e **salvar**.

> [!Important]
> O conteúdo criptografado no Azure Pesquisa Cognitiva está configurado para usar uma chave de Azure Key Vault específica com uma **versão** específica. Se você alterar a chave ou a versão, o mapa de índice ou sinônimo deverá ser atualizado para usar o novo key\version **antes** de excluir o key\version. anterior A falha ao fazer isso tornará o mapa de índice ou sinônimo inutilizável, em você não poderá descriptografar o conteúdo depois que o acesso à chave for perdido.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5-criptografar conteúdo

Para adicionar uma chave gerenciada pelo cliente em um índice, fonte de dados, habilidades, indexador ou mapa de sinônimos, você deve usar a [API REST de pesquisa](/rest/api/searchservice/) ou um SDK. O portal não expõe mapas de sinônimos ou propriedades de criptografia. Quando você usa índices de API, fontes de dados, habilidades, indexadores e mapas de sinônimos válidos dão suporte a uma propriedade **encryptionKey** de nível superior.

Este exemplo usa a API REST, com valores para Azure Key Vault e Azure Active Directory:

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

> [!Note]
> Nenhum desses detalhes do cofre de chaves são considerados secretos e podem ser recuperados facilmente navegando até a página de chave Azure Key Vault relevante em portal do Azure.

## <a name="example-index-encryption"></a>Exemplo: index Encryption

Crie um índice criptografado usando a [API REST criar índice do Azure pesquisa cognitiva](/rest/api/searchservice/create-index). Use a `encryptionKey` propriedade para especificar a chave de criptografia a ser usada.
> [!Note]
> Nenhum desses detalhes do cofre de chaves são considerados secretos e podem ser recuperados facilmente navegando até a página de chave Azure Key Vault relevante em portal do Azure.

## <a name="rest-examples"></a>Exemplos de REST

Esta seção mostra o JSON completo para um índice criptografado e um mapa de sinônimos

### <a name="index-encryption"></a>Criptografia de índice

Os detalhes da criação de um novo índice por meio da API REST podem ser encontrados em [CREATE INDEX (REST API)](/rest/api/searchservice/create-index), em que a única diferença aqui é especificar os detalhes da chave de criptografia como parte da definição do índice:

```json
{
 "name": "hotels",
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Agora você pode enviar a solicitação de criação de índice e começar a usar o índice normalmente.

### <a name="synonym-map-encryption"></a>Criptografia do mapa de sinônimos

Crie um mapa de sinônimos criptografados usando a [API REST criar mapa de sinônimos do Azure pesquisa cognitiva](/rest/api/searchservice/create-synonym-map). Use a `encryptionKey` propriedade para especificar a chave de criptografia a ser usada.

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Agora você pode enviar a solicitação de criação de mapa de sinônimo e começar a usá-la normalmente.

## <a name="example-data-source-encryption"></a>Exemplo: criptografia de fonte de dados

Crie uma fonte de dados criptografada usando a [API REST de criação (Azure pesquisa cognitiva)](/rest/api/searchservice/create-data-source). Use a `encryptionKey` propriedade para especificar a chave de criptografia a ser usada.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Agora você pode enviar a solicitação de criação da fonte de dados e começar a usá-la normalmente.

## <a name="example-skillset-encryption"></a>Exemplo: criptografia do skillset

Crie um decodificador de qualificações criptografadas usando a [API REST de criação de conPesquisa Cognitiva do Azure](/rest/api/searchservice/create-skillset). Use a `encryptionKey` propriedade para especificar a chave de criptografia a ser usada.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Agora você pode enviar a solicitação de criação de contratação e começar a usá-la normalmente.

## <a name="example-indexer-encryption"></a>Exemplo: criptografia do indexador

Crie um indexador criptografado usando a [API REST criar indexador do Azure pesquisa cognitiva](/rest/api/searchservice/create-indexer). Use a `encryptionKey` propriedade para especificar a chave de criptografia a ser usada.

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Agora você pode enviar a solicitação de criação do indexador e começar a usá-la normalmente.

>[!Important]
> Embora `encryptionKey` não possa ser adicionado a índices de pesquisa ou mapas de sinônimos existentes, ele pode ser atualizado fornecendo valores diferentes para qualquer um dos três detalhes do cofre de chaves (por exemplo, atualizando a versão da chave). Ao mudar para uma nova chave de Key Vault ou uma nova versão de chave, qualquer índice de pesquisa ou mapa de sinônimos que usa a chave deve primeiro ser atualizado para usar o novo key\version **antes** de excluir o key\version. anterior A falha ao fazer isso tornará o mapa de índice ou sinônimo inutilizável, pois ele não poderá descriptografar o conteúdo depois que o acesso à chave for perdido. Embora a restauração das permissões de acesso do cofre de chaves posteriormente, o acesso ao conteúdo será restaurado.

## <a name="simpler-alternative-trusted-service"></a>Alternativa mais simples: serviço confiável

Dependendo da configuração do locatário e dos requisitos de autenticação, você poderá implementar uma abordagem mais simples para acessar uma chave do Key Vault. Em vez de criar e usar um aplicativo Active Directory, você pode tornar um serviço de pesquisa um serviço confiável, habilitando uma identidade gerenciada pelo sistema para ele. Em seguida, você usaria o serviço de pesquisa confiável como um princípio de segurança, em vez de um aplicativo registrado pelo AD, para acessar a chave do Key Vault.

Essa abordagem permite omitir as etapas para o registro do aplicativo e os segredos do aplicativo e simplifica uma definição de chave de criptografia apenas para os componentes do Key Vault (URI, nome do cofre, versão da chave).

Em geral, uma identidade gerenciada permite que o serviço de pesquisa se autentique em Azure Key Vault sem armazenar credenciais (ApplicationID ou ApplicationSecret) no código. O ciclo de vida desse tipo de identidade gerenciada está vinculado ao ciclo de vida do seu serviço de pesquisa, que só pode ter uma identidade gerenciada. Para obter mais informações sobre como as identidades gerenciadas funcionam, consulte [o que são identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

1. Torne seu serviço de pesquisa um serviço confiável.
   ![Habilitar a identidade gerenciada atribuída ao sistema](./media/search-managed-identities/turn-on-system-assigned-identity.png "Ativar a identidade gerenciada atribuída ao sistema")

1. Ao configurar uma política de acesso no Azure Key Vault, escolha o serviço de pesquisa confiável como o princípio (em vez do aplicativo registrado pelo AD). Atribua as mesmas permissões (Multiple obter, ENCAPSULAr, desencapsular) conforme instruído na etapa conceder permissões de chave de acesso.

1. Use uma construção simplificada do `encryptionKey` que omita as propriedades Active Directory.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

As condições que irão impedi-lo de adotar essa abordagem simplificada incluem:

+ Você não pode conceder permissões de acesso de serviço de pesquisa diretamente para o cofre de chaves (por exemplo, se o serviço de pesquisa estiver em um locatário de Active Directory diferente do Azure Key Vault).

+ Um único serviço de pesquisa é necessário para hospedar vários mapas indexes\synonym criptografados, cada um usando uma chave diferente de um cofre de chaves diferente, em que cada cofre de chaves deve usar **uma identidade diferente** para autenticação. Como um serviço de pesquisa pode ter apenas uma identidade gerenciada, os requisitos para várias identidades desqualificam a abordagem simplificada para seu cenário.  

## <a name="work-with-encrypted-content"></a>Trabalhar com conteúdo criptografado

Com a criptografia de chave gerenciada pelo cliente, você notará a latência para indexação e consultas devido ao trabalho extra de criptografia/descriptografia. O Azure Pesquisa Cognitiva não registra a atividade de criptografia, mas você pode monitorar o acesso à chave por meio do registro em log do Key Vault. Recomendamos que você [habilite o registro em log](../key-vault/general/logging.md) como parte da configuração do Key Vault.

Espera-se que a rotação de chaves ocorra ao longo do tempo. Sempre que você gira as chaves, é importante seguir esta sequência:

1. [Determine a chave usada por um índice ou mapa de sinônimos](search-security-get-encryption-keys.md).
1. [Crie uma nova chave no Key Vault](../key-vault/keys/quick-create-portal.md), mas deixe a chave original disponível.
1. [Atualize as propriedades encryptionKey](/rest/api/searchservice/update-index) em um mapa de índice ou sinônimo para usar os novos valores. Somente os objetos que foram criados originalmente com essa propriedade podem ser atualizados para usar um valor diferente.
1. Desabilite ou exclua a chave anterior no cofre de chaves. Monitore o acesso à chave para verificar se a nova chave está sendo usada.

Por motivos de desempenho, o serviço de pesquisa armazena em cache a chave por até várias horas. Se você desabilitar ou excluir a chave sem fornecer uma nova, as consultas continuarão a funcionar de forma temporária até que o cache expire. No entanto, quando o serviço de pesquisa não puder descriptografar o conteúdo, você receberá esta mensagem: "acesso proibido. A chave de consulta usada pode ter sido revogada-tente novamente. " 

## <a name="next-steps"></a>Próximas etapas

Se você não estiver familiarizado com a arquitetura de segurança do Azure, examine a [documentação de segurança do Azure](../security/index.yml)e, em particular, este artigo:

> [!div class="nextstepaction"]
> [Criptografia de dados em repouso](../security/fundamentals/encryption-atrest.md)