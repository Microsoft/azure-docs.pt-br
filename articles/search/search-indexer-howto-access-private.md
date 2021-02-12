---
title: Conexões do indexador por meio de um ponto de extremidade privado
titleSuffix: Azure Cognitive Search
description: Configure as conexões do indexador para acessar o conteúdo de outros recursos do Azure que são protegidos por meio de um ponto de extremidade privado.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 762db9d165358f3347fc9b7f3aaaf39f0c762308
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063189"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>Fazer conexões do indexador por meio de um ponto de extremidade privado

Muitos recursos do Azure, como contas de armazenamento do Azure, podem ser configurados para aceitar conexões de uma lista de redes virtuais e recusar conexões externas que se originam de uma rede pública. Se você estiver usando um indexador para indexar dados no Azure Pesquisa Cognitiva e sua fonte de dados estiver em uma rede privada, você poderá criar uma [conexão de ponto de extremidade privada](../private-link/private-endpoint-overview.md) de saída para alcançar os dados.

Esse método de conexão do indexador está sujeito aos dois requisitos a seguir:

+ O recurso do Azure que fornece conteúdo ou código deve ser registrado anteriormente com o [serviço de link privado do Azure](https://azure.microsoft.com/services/private-link/).

+ O serviço de Pesquisa Cognitiva do Azure deve estar na camada básica ou superior. O recurso não está disponível na camada gratuita. Além disso, se o indexador tiver um tipo de habilidade, a camada deverá ser Standard 2 (S2) ou superior. Para obter mais informações, consulte [limites de serviço](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

## <a name="shared-private-link-resources-management-apis"></a>APIs de gerenciamento de recursos de link privado compartilhado

Pontos de extremidade privados de recursos protegidos que são criados por meio de APIs de Pesquisa Cognitiva do Azure são chamados de *recursos de link privado compartilhado*. Isso ocorre porque você está "compartilhando" o acesso a um recurso, como uma conta de armazenamento, que foi integrado com o [serviço de link privado do Azure](https://azure.microsoft.com/services/private-link/).

Por meio de sua API REST de gerenciamento, o Azure Pesquisa Cognitiva fornece uma operação [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) que você pode usar para configurar o acesso de um indexador de pesquisa cognitiva do Azure.

Você pode criar conexões de ponto de extremidade privadas para alguns recursos usando a versão de visualização da API de gerenciamento de pesquisa (versão *2020-08-01-Preview* ou posterior), que é designada de *Visualização* na tabela a seguir. Recursos sem uma designação de *Visualização* podem ser criados com a versão de API de visualização ou de disponibilidade geral (*2020-08-01* ou posterior).

A tabela a seguir lista os recursos do Azure para os quais você pode criar pontos de extremidade privados de saída do Azure Pesquisa Cognitiva. Para criar um recurso de link privado compartilhado, insira os valores de **ID de grupo** exatamente como eles são gravados na API. Os valores diferenciam maiúsculas de minúsculas.

| Recursos do Azure | ID do Grupo |
| --- | --- |
| Armazenamento do Azure-Blob (ou) ADLS Gen 2 | `blob`|
| Armazenamento do Azure-tabelas | `table`|
| API do Azure Cosmos DB-SQL | `Sql`|
| Banco de Dados SQL do Azure | `sqlServer`|
| Banco de dados do Azure para MySQL (visualização) | `mysqlServer`|
| Cofre de Chave do Azure | `vault` |
| Azure Functions (versão prévia) | `sites` |

Você também pode consultar os recursos do Azure para os quais há suporte para conexões de ponto de extremidade privadas de saída usando a [lista de APIs com suporte](/rest/api/searchmanagement/privatelinkresources/listsupported).

No restante deste artigo, uma combinação dos [CLI do Azure](https://docs.microsoft.com/cli/azure/) (ou [ARMClient](https://github.com/projectkudu/ARMClient) se preferir) e o [postmaster](https://www.postman.com/) (ou qualquer outro cliente http, como a [ondulação](https://curl.se/) , se preferir) é usado para demonstrar as chamadas à API REST.

> [!NOTE]
> Os exemplos neste artigo baseiam-se nas seguintes suposições:
> * O nome do serviço de pesquisa é _contoso-Search_, que existe no grupo de recursos da _contoso_ de uma assinatura com a ID de assinatura _00000000-0000-0000-0000-000000000000_. 
> * A ID de recurso deste serviço de pesquisa é _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/Providers/Microsoft.Search/searchServices/contoso-Search_.

O restante dos exemplos mostra como o serviço de _pesquisa da Contoso_ pode ser configurado para que seus indexadores possam acessar dados da conta de armazenamento seguro _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/Providers/Microsoft.Storage/storageAccounts/contoso-Storage_.

## <a name="secure-your-storage-account"></a>Proteger sua conta de armazenamento

Configure a conta de armazenamento para [permitir o acesso somente de sub-redes específicas](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network). Na portal do Azure, se você selecionar essa opção e deixar o conjunto vazio, significa que nenhum tráfego de redes virtuais é permitido.

   ![Captura de tela do painel "firewalls e redes virtuais", mostrando a opção de permitir o acesso a redes selecionadas. ](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

> [!NOTE]
> Você pode usar a [abordagem de serviço confiável da Microsoft](../storage/common/storage-network-security.md#trusted-microsoft-services) para ignorar a rede virtual ou restrições de IP em uma conta de armazenamento. Você também pode habilitar o serviço de pesquisa para acessar dados na conta de armazenamento. Para fazer isso, consulte [acesso do indexador ao armazenamento do Azure com a exceção de serviço confiável](search-indexer-howto-access-trusted-service-exception.md). 
>
> No entanto, quando você usa essa abordagem, a comunicação entre o Azure Pesquisa Cognitiva e sua conta de armazenamento ocorre por meio do endereço IP público da conta de armazenamento, por meio da rede de backbone segura da Microsoft.

### <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>Etapa 1: criar um recurso de link privado compartilhado para a conta de armazenamento

Para solicitar que o Azure Pesquisa Cognitiva crie uma conexão de ponto de extremidade privada de saída para a conta de armazenamento, faça a seguinte chamada de API, por exemplo, com o [CLI do Azure](https://docs.microsoft.com/cli/azure/): 

`az rest --method put --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 --body @create-pe.json`

Ou, se você preferir usar [ARMClient](https://github.com/projectkudu/ARMClient):

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

O conteúdo do *create-pe.jsno* arquivo, que representa o corpo da solicitação para a API, é o seguinte:

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

Uma `202 Accepted` resposta é retornada em caso de êxito. O processo de criação de um ponto de extremidade de saída particular é uma operação de execução longa (assíncrona). Ele envolve a implantação dos seguintes recursos:

* Um ponto de extremidade privado, alocado com um endereço IP privado em um `"Pending"` estado. O endereço IP privado é obtido do espaço de endereço alocado para a rede virtual do ambiente de execução para o indexador particular específico do serviço de pesquisa. Após a aprovação do ponto de extremidade privado, qualquer comunicação do Azure Pesquisa Cognitiva à conta de armazenamento é originada do endereço IP privado e de um canal de link privado seguro.

* Uma zona DNS privada para o tipo de recurso, com base no `groupId` . Ao implantar esse recurso, você garante que qualquer pesquisa de DNS para o recurso privado utiliza o endereço IP associado ao ponto de extremidade privado.

Certifique-se de especificar o correto `groupId` para o tipo de recurso para o qual você está criando o ponto de extremidade privado. Qualquer incompatibilidade resultará em uma mensagem de resposta não bem-sucedida.

Como em todas as operações assíncronas do Azure, a `PUT` chamada retorna um `Azure-AsyncOperation` valor de cabeçalho semelhante ao seguinte:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

Você pode sondar esse URI periodicamente para obter o status da operação. Antes de prosseguir, recomendamos que você aguarde até que o status da operação de recurso de vínculo privado compartilhado tenha atingido um estado de terminal (ou seja, o status da operação seja *bem-sucedido*).

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01`

Ou usando ARMClient:

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>Etapa 2a: aprovar a conexão de ponto de extremidade particular para a conta de armazenamento

> [!NOTE]
> Nesta seção, você usa o portal do Azure para percorrer o fluxo de aprovação de um ponto de extremidade privado para o armazenamento. Como alternativa, você pode usar a [API REST](/rest/api/storagerp/privateendpointconnections) que está disponível por meio do provedor de recursos de armazenamento.
>
> Outros provedores, como Azure Cosmos DB ou SQL Server do Azure, oferecem APIs de provedor de recursos de armazenamento semelhantes para gerenciar conexões de ponto de extremidade privadas.

1. Na portal do Azure, selecione a guia **conexões de ponto de extremidade privado** da sua conta de armazenamento. Após a operação assíncrona ter sido bem-sucedida, deve haver uma solicitação para uma conexão de ponto de extremidade privada com a mensagem de solicitação da chamada à API anterior.

   ![Captura de tela da portal do Azure, mostrando o painel "conexões de ponto de extremidade privado".](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. Selecione o ponto de extremidade privado que o Azure Pesquisa Cognitiva criado. Na coluna **ponto de extremidade privado** , identifique a conexão de ponto de extremidade particular pelo nome especificado na API anterior, selecione **aprovar** e, em seguida, insira uma mensagem apropriada. O conteúdo da mensagem não é significativo. 

   Certifique-se de que a conexão de ponto de extremidade particular apareça conforme mostrado na captura de tela a seguir. Pode levar de um a dois minutos para que o status seja atualizado no Portal.

   ![Captura de tela da portal do Azure, mostrando um status "aprovado" no painel "conexões de ponto de extremidade privado".](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

Depois que a solicitação de conexão de ponto de extremidade particular é aprovada, o tráfego é *capaz* de fluir pelo ponto de extremidade privado. Depois que o ponto de extremidade privado for aprovado, o Azure Pesquisa Cognitiva criará os mapeamentos de zona DNS necessários na zona DNS que é criada para ele.

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Etapa 2B: consultar o status do recurso de link privado compartilhado

Para confirmar se o recurso de link privado compartilhado foi atualizado após a aprovação, obtenha seu status usando a [API Get](/rest/api/searchmanagement/sharedprivatelinkresources/get).

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Ou usando ARMClient:

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Se o `properties.provisioningState` do recurso for `Succeeded` e `properties.status` for `Approved` , significa que o recurso de link privado compartilhado é funcional e o indexador pode ser configurado para se comunicar por meio do ponto de extremidade privado.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

### <a name="step-3-configure-the-indexer-to-run-in-the-private-environment"></a>Etapa 3: configurar o indexador para ser executado no ambiente privado

> [!NOTE]
> Você pode executar essa etapa antes que a conexão de ponto de extremidade particular seja aprovada. Até que a conexão de ponto de extremidade particular seja aprovada, qualquer indexador que tentar se comunicar com um recurso seguro (como a conta de armazenamento) terminará em um estado de falha transitória. Novos indexadores não serão criados. Assim que a conexão de ponto de extremidade particular for aprovada, os indexadores poderão acessar a conta de armazenamento particular.

1. [Crie uma fonte de dados](/rest/api/searchservice/create-data-source) que aponte para a conta de armazenamento seguro e um contêiner apropriado dentro da conta de armazenamento. A captura de tela a seguir mostra essa solicitação no postmaster.

   ![Captura de tela mostrando a criação de uma fonte de dados na interface do usuário do postmaster.](media\search-indexer-howto-secure-access\create-ds.png )

1. Da mesma forma, [crie um índice](/rest/api/searchservice/create-index) e, opcionalmente, [crie um](/rest/api/searchservice/create-skillset) configurador de habilidades usando a API REST.

1. [Crie um indexador](/rest/api/searchservice/create-indexer) que aponte para a fonte de dados, o índice e o skillset que você criou na etapa anterior. Além disso, Force o indexador a ser executado no ambiente de execução particular definindo a propriedade de configuração do indexador `executionEnvironment` como `private` .

   ![Captura de tela mostrando a criação de um indexador na interface do usuário do postmaster.](media\search-indexer-howto-secure-access\create-idr.png)

   Depois que o indexador for criado com êxito, ele deverá começar a indexar o conteúdo da conta de armazenamento pela conexão de ponto de extremidade privada. Você pode monitorar o status do indexador usando a API de [status do indexador](/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> Se você já tiver indexadores existentes, poderá atualizá-los por meio da [API Put](/rest/api/searchservice/create-indexer) definindo o `executionEnvironment` para `private` .

## <a name="troubleshooting"></a>Solução de problemas

- Se a criação do indexador falhar com uma mensagem de erro como "as credenciais da fonte de dados são inválidas", isso significa que o status da conexão de ponto de extremidade particular ainda não foi *aprovado* ou a conexão não está funcionando. Para corrigir o problema: 
  * Obtenha o status do recurso de link privado compartilhado usando a [API Get](/rest/api/searchmanagement/sharedprivatelinkresources/get). Se o status for *aprovado*, verifique o `properties.provisioningState` do recurso. Se o status for aqui `Incomplete` , isso significa que algumas das dependências subjacentes para o recurso não foram configuradas. Emitir `PUT` novamente a solicitação para recriar o recurso de link privado compartilhado deve corrigir o problema. Uma reaprovação pode ser necessária. Verifique novamente o status do recurso para verificar se o problema foi corrigido.

- Se você criar o indexador sem definir sua `executionEnvironment` propriedade, a criação poderá ser bem-sucedida, mas seu histórico de execução mostrará que as execuções do indexador não são bem-sucedidas. Para corrigir o problema:
   * [Atualize o indexador](/rest/api/searchservice/update-indexer) para especificar o ambiente de execução.

- Se você criou o indexador sem definir a `executionEnvironment` propriedade e ele é executado com êxito, isso significa que o Azure pesquisa cognitiva decidiu que seu ambiente de execução é o ambiente *particular* específico do serviço de pesquisa. Isso pode mudar, dependendo dos recursos consumidos pelo indexador, da carga no serviço de pesquisa e de outros fatores, e pode falhar mais tarde. Para corrigir o problema:
  * É altamente recomendável que você defina a `executionEnvironment` propriedade como `private` para garantir que ela não falhe no futuro.

[Cotas e limites](search-limits-quotas-capacity.md) determinam quantos recursos de vínculo privado compartilhado podem ser criados e dependem do SKU do serviço de pesquisa.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre pontos de extremidade privados:

- [O que são pontos de extremidade privados?](../private-link/private-endpoint-overview.md)
- [Configurações de DNS necessárias para pontos de extremidade privados](../private-link/private-endpoint-dns.md)
