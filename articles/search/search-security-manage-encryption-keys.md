---
title: Criptografia em repouso usando chaves gerenciadas pelo cliente no Azure Key Vault (visualização) - Azure Search
description: Criptografia do lado do servidor suplemento sobre índices e mapas de sinônimos no Azure Search por meio de chaves que você criar e gerenciar no Azure Key Vault.
author: NatiNimni
manager: jlembicz
ms.author: natinimn
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: ''
ms.openlocfilehash: 949628fa52b4b020d70b75f4a0e7895f1e0f8bba
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485310"
---
# <a name="azure-search-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Criptografia de pesquisa do Azure usando chaves gerenciadas pelo cliente no Azure Key Vault

> [!Note]
> Criptografia com chaves gerenciadas pelo cliente está em versão prévia e não destina-se para uso em produção. A [API REST versão 2019-05-06-versão prévia](search-api-preview.md) fornece esse recurso. Você também pode usar o SDK do .NET versão 8.0-preview.
>
> Esse recurso não está disponível para os serviços gratuitos. Você deve usar um serviço de pesquisa faturáveis criado a partir de 2019-01-01. Não há nenhum suporte do portal no momento.

Por padrão, o Azure Search criptografa o conteúdo de usuário em repouso com [chaves gerenciadas pelo serviço](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest#data-encryption-models). Você pode complementar a criptografia padrão com uma camada adicional de criptografia usando chaves que você criar e gerenciar no Azure Key Vault. Este artigo orienta você pelas etapas.

Criptografia do lado do servidor tem suporte por meio da integração com [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Você pode criar suas próprias chaves de criptografia e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves de criptografia. Com o Azure Key Vault, você também pode auditar o uso de chave. 

Criptografia com chaves gerenciadas pelo cliente é configurada no índice ou sinônimo mapa nível quando esses objetos são criados e não no nível de serviço de pesquisa. Não é possível criptografar o conteúdo que já existe. 

Você pode usar chaves diferentes de diferentes cofres de chave. Isso significa que um serviço de pesquisa único pode hospedar vários mapas de indexes\synonym criptografado, cada criptografados potencialmente usando uma chave diferente gerenciada pelo cliente, junto com os mapas de indexes\synonym que não são criptografados usando chaves gerenciadas pelo cliente. 

## <a name="prerequisites"></a>Pré-requisitos

Os serviços a seguir são usados neste exemplo. 

+ [Crie um serviço Azure Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. Você pode usar um serviço gratuito para este tutorial.

+ [Criar um recurso do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) ou encontrar um cofre existente em sua assinatura.

+ [O Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) ou [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) é usado para tarefas de configuração.

+ [Postman](search-get-started-postman.md), [Azure PowerShell](search-create-index-rest-api.md) e [SDK do Azure Search](https://aka.ms/search-sdk-preview) pode ser usado para chamar a API REST de visualização. Não há nenhum portal ou o suporte do SDK do .NET para criptografia gerenciada pelo cliente no momento.

## <a name="1---enable-key-recovery"></a>1 - habilitar a recuperação de chave

Esta etapa é opcional, mas altamente recomendado. Depois de criar o recurso de Cofre de chaves do Azure, habilite **exclusão reversível** e **limpar proteção** no cofre de chaves, executando os seguintes comandos do PowerShell ou CLI do Azure:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

>[!Note]
> Devido à natureza da criptografia com o recurso de chaves gerenciadas pelo cliente, do Azure Search não poderá recuperar seus dados se sua chave de Cofre de chaves do Azure é excluído. Para evitar a perda de dados causada por exclusões acidentais de chave de Cofre de chaves, é altamente recomendável habilitar exclusão reversível e limpeza de proteção no cofre de chaves selecionado. Para obter mais informações, consulte [exclusão reversível do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="2---create-a-new-key"></a>2 - criar uma nova chave

Se você estiver usando uma chave existente para criptografar o conteúdo do Azure Search, ignore esta etapa.

1. [Entre no portal do Azure](https://portal.azure.com) e navegue até o painel do Cofre de chaves.

1. Selecione o **teclas** configuração do painel de navegação esquerdo e, em seguida, clique em **+ gerar/importar**.

1. No **criar uma chave** painel, na lista de **opções**, escolha o método que você deseja usar para criar uma chave. Você pode **Generate** uma nova chave **carregar** existente da chave ou use **restaurar Backup** para selecionar um backup de uma chave.

1. Insira um **nome** para sua chave e, opcionalmente, selecione outras propriedades de chave.

1. Clique no **criar** botão para iniciar a implantação.

Anote o identificador de chave – isso é composto do **valor Uri de chave**, o **nome da chave**e o **versão da chave**. Você precisará deles para definir um índice criptografado no Azure Search.
 
![Criar uma nova chave de Cofre de chaves](./media/search-manage-encryption-keys/create-new-key-vault-key.png "criar uma nova chave de Cofre de chaves")

## <a name="3---create-a-service-identity"></a>3 - criar uma identidade de serviço

Atribuir uma identidade para o serviço search permite que você conceda permissões de acesso do Cofre de chaves para o serviço de pesquisa. O serviço de pesquisa usará sua identidade para autenticar com o Azure Key vault.

O Azure Search dá suporte a duas maneiras para atribuição de identidade: uma identidade gerenciada ou um aplicativo do Azure Active Directory gerenciado externamente. 

Se possível, use uma identidade gerenciada. É a maneira mais simples de atribuir uma identidade para o serviço de pesquisa e deve funcionar na maioria dos cenários. Se você estiver usando várias chaves para índices e mapas de sinônimos, ou se sua solução estiver em uma arquitetura distribuída que desqualifica autenticação baseada em identidade, use o avançado [gerenciado externamente a abordagem do Azure Active Directory](#aad-app)descrita no final deste artigo.

 Em geral, uma identidade gerenciada permite que o serviço de pesquisa autenticar no Azure Key Vault sem armazenar credenciais no código. O ciclo de vida desse tipo de identidade gerenciada está vinculado ao ciclo de vida do seu serviço de pesquisa, que pode ter apenas uma identidade gerenciada. [Saiba mais sobre as identidades gerenciado](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Para criar uma identidade gerenciada, [entrar no portal de toAzure](https://portal.azure.com) e abra o painel do serviço de pesquisa. 

1. Clique em **Identity** no painel de navegação à esquerda, alterar seu status para **na**e clique em **salvar**.

![Habilitar uma identidade gerenciada](./media/search-enable-msi/enable-identity-portal.png "habilitar uma identidade gerenciada")

## <a name="4---grant-key-access-permissions"></a>4 - conceder permissões de acesso à chave

Para habilitar o serviço de pesquisa usar sua chave de Cofre de chaves, você precisará conceder sua pesquisa determinadas permissões de acesso de serviço.

Permissões de acesso podem ser revogadas a qualquer momento. Depois revogado, qualquer pesquisa índice ou sinônimo mapa do serviço que usa esse Cofre de chaves se tornará inutilizável. Restaurar permissões de acesso do Cofre de chave em um momento posterior irá restaurar o acesso de mapa index\synonym. Para obter mais informações, consulte [proteger o acesso a um cofre de chaves](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Entre no portal do Azure](https://portal.azure.com) e abra sua página de visão geral do Cofre de chaves. 

1. Selecione o **políticas de acesso** configuração do painel de navegação esquerdo e, em seguida, clique em **+ adicionar novo**.

   ![Adicionar nova política de acesso do Cofre de chaves](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "adicionar nova política de acesso do Cofre de chaves")

1. Clique em **selecionar entidade** e selecione seu serviço Azure Search. Você pode procurá-lo por nome ou a ID de objeto que foi exibida depois de habilitar a identidade gerenciada.

   ![Entidade de política de acesso Select Cofre de chaves](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "entidade de política de acesso Select Cofre de chaves")

1. Clique em **permissões de chave** e selecione *obter*, *Unwrap Key* e *Wrap Key*. Você pode usar o *armazenamento do Azure Data Lake ou armazenamento do Azure* modelo para selecionar rapidamente as permissões necessárias.

   O Azure search deve ser concedido com o seguinte [permissões de acesso](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations):

   * *Obter* -permite que o serviço de pesquisa recuperar as partes públicas de sua chave em um cofre de chaves
   * *Wrap Key* -permite que o serviço de pesquisa usar sua chave para proteger a chave de criptografia interna
   * *Unwrap Key* -permite que o serviço de pesquisa usar sua chave para decodificar a chave de criptografia interna

   ![Selecione permissões chave de política de acesso do Cofre de chaves](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "selecione permissões chave de política de acesso do Cofre de chaves")

1. Clique em **Okey** e **salvar** as alterações de política de acesso.

> [!Important]
> O conteúdo criptografado no Azure search está configurado para usar uma chave específica do Azure Key Vault com um determinado **versão**. Se você alterar a chave ou a versão, o mapa de sinônimo ou de índice deve ser atualizado para usar o novo key\version **antes de** excluindo o key\version anterior. Não fazer isso, o índice será renderizado ou sinônimo mapear inutilizável, você não será capaz de descriptografar o conteúdo depois que o acesso à chave for perdido.   

## <a name="5---encrypt-content"></a>5 - criptografar conteúdo

Criando um mapa de índice ou sinônimo criptografado com chave gerenciada pelo cliente ainda não é possível usando o portal do Azure. Use a API de REST do Azure Search para criar tal um mapa de índice ou sinônimo.

Suporte um novo nível superior do mapa de índice e o sinônimo **encryptionKey** propriedade usada para especificar a chave. 

Usando o **Uri de Cofre de chaves**, **nome da chave** e o **versão da chave** da sua chave de Cofre de chave, podemos criar um **encryptionKey** definição:

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> Nenhum desses detalhes do Cofre de chaves são considerados segredo e poderia ser facilmente recuperados, navegando até a página de chave de Cofre de chaves do Azure relevante no portal do Azure.

Se você estiver usando um aplicativo do AAD para autenticação de Cofre de chaves em vez de usar uma identidade gerenciada, adicionar o aplicativo do AAD **credenciais de acesso** para sua chave de criptografia: 
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

## <a name="example-index-encryption"></a>Exemplo: Criptografia de índice
Detalhes da criação de um novo índice por meio da API REST podem ser encontrados no [criar índice (API de REST do serviço de pesquisa do Azure)](https://docs.microsoft.com/rest/api/searchservice/create-index), em que a única diferença aqui é que especifica os detalhes da chave de criptografia como parte da definição do índice: 

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
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
Você pode enviar a solicitação de criação de índice e, em seguida, começar a usar o índice normalmente.

## <a name="example-synonym-map-encryption"></a>Exemplo: Criptografia de mapa de sinônimo

Detalhes da criação de um novo mapa de sinônimos por meio da API REST podem ser encontrados no [criar o mapa de sinônimos (API de REST do serviço de pesquisa do Azure)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), em que a única diferença aqui é que especifica os detalhes da chave de criptografia como parte da definição de mapa de sinônimo: 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
Você pode enviar a solicitação de criação de mapa de sinônimo e, em seguida, começar a usá-lo normalmente.

>[!Important] 
> Embora **encryptionKey** não pode ser adicionado aos índices de Azure Search existentes ou mapas de sinônimo, ele pode ser atualizado, fornecendo valores diferentes para qualquer um dos três detalhes de Cofre de chaves (por exemplo, atualizando a versão de chave). Ao mudar para uma nova chave de Cofre de chaves ou uma nova versão de chave, qualquer mapa de sinônimo ou de índice de Azure Search que usa a chave deve ser atualizado primeiro para usar o novo key\version **antes de** excluindo o key\version anterior. Não fazer isso, o índice será renderizado ou inutilizável, pois ele não será capaz de descriptografar o chave de uma vez acesso ao conteúdo do mapa de sinônimos será perdido.   
> Restaurar permissões de acesso do Cofre de chave em um momento posterior irá restaurar o acesso ao conteúdo.

## <a name="aad-app"></a> Avançado: Usar um aplicativo gerenciado externamente do Azure Active Directory

Quando uma identidade gerenciada, não é possível, que você pode criar um aplicativo do Azure Active Directory com uma segurança de entidade para o serviço Azure Search. Especificamente, uma identidade gerenciada não é viável sob estas condições:

* Você não pode diretamente conceder a sua pesquisa permissões de acesso de serviço para o Cofre de chaves (por exemplo, se o serviço de pesquisa está em um locatário do Active Directory diferente que o Azure Key Vault).

* Um serviço de pesquisa único é necessário para hospedar vários mapas de indexes\synonym criptografado, cada um usando uma chave diferente em um cofre de chave diferente, em que cada Cofre de chaves deve usar **uma identidade diferente** para autenticação. Se não for um requisito usando uma identidade diferente para gerenciar os cofres de chaves diferentes, considere usar a opção de identidade gerenciada acima.  

Para acomodar essas topologias, o Azure search dá suporte ao uso de aplicativos do Azure Active Directory (AAD) para autenticação entre o serviço de pesquisa e o Cofre de chaves.    
Para criar um aplicativo do AAD no portal:

1. [Criar um aplicativo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Obter a chave de autenticação e ID do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) como aqueles serão necessárias para criar um índice criptografado. Você precisará fornecer os valores incluem **ID do aplicativo** e **chave de autenticação**.

>[!Important]
> Ao decidir usar um aplicativo do AAD de autenticação em vez de uma identidade gerenciada, considere o fato de que o Azure Search não está autorizado a gerenciar seu aplicativo do AAD em seu nome, e cabe a você gerenciar seu aplicativo do AAD, como rotação periódica de a chave de autenticação do aplicativo.
> Ao alterar um aplicativo do AAD ou sua chave de autenticação, qualquer mapa de sinônimo ou de índice de Azure Search que usa esse aplicativo pela primeira vez deve ser atualizado para usar o novo aplicativo ID\key **antes de** excluindo o aplicativo anterior ou seus autorização de chave e antes de revogar o acesso de Cofre de chaves a ela.
> Não fazer isso, o índice será renderizado ou inutilizável, pois ele não será capaz de descriptografar o chave de uma vez acesso ao conteúdo do mapa de sinônimos será perdido.   

## <a name="next-steps"></a>Próximas etapas

Se você não estiver familiarizado com a arquitetura de segurança do Azure, examine os [documentação de segurança do Azure](https://docs.microsoft.com/azure/security/)e, em particular, este artigo:

> [!div class="nextstepaction"]
> [Criptografia de dados em repouso](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
