---
title: Segurança para soluções dos Gêmeos Digitais do Azure
titleSuffix: Azure Digital Twins
description: Entenda as práticas recomendadas de segurança com o Azure digital gêmeos.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 81a44d4d0025c841cf56e19d6afee5e95bd44a55
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730500"
---
# <a name="secure-azure-digital-twins"></a>Proteger o gêmeos digital do Azure

Por segurança, o Azure digital gêmeos permite o controle de acesso preciso sobre dados, recursos e ações específicos em sua implantação. Ele faz isso por meio de uma função granular e uma estratégia de gerenciamento de permissão chamada **controle de acesso baseado em função do Azure (RBAC do Azure)**. Você pode ler sobre os princípios gerais do RBAC do Azure [aqui](../role-based-access-control/overview.md).

O Azure digital gêmeos também dá suporte à criptografia de dados em repouso.

## <a name="roles-and-permissions-with-azure-rbac"></a>Funções e permissões com o RBAC do Azure

O RBAC do Azure é fornecido para o gêmeos digital do Azure por meio da integração com o [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD).

Você pode usar o Azure RBAC para conceder permissões a uma *entidade de segurança*, que pode ser um usuário, um grupo ou uma entidade de serviço de aplicativo. A entidade de segurança é autenticada pelo Azure AD e recebe um token 2,0 do OAuth em retorno. Esse token pode ser usado para autorizar uma solicitação de acesso a uma instância do gêmeos digital do Azure.

### <a name="authentication-and-authorization"></a>Autenticação e autorização

Com o Azure AD, o Access é um processo de duas etapas. Quando uma entidade de segurança (um usuário, grupo ou aplicativo) tenta acessar o Azure digital gêmeos, a solicitação deve ser *autenticada* e *autorizada*. 

1. Primeiro, a identidade da entidade de segurança é *autenticada* e um token OAuth 2,0 é retornado.
2. Em seguida, o token é passado como parte de uma solicitação para o serviço de gêmeos digital do Azure, para *autorizar* o acesso ao recurso especificado.

A etapa de autenticação requer que qualquer solicitação de aplicativo contenha um token de acesso OAuth 2,0 em tempo de execução. Se um aplicativo estiver sendo executado em uma entidade do Azure, como um aplicativo [Azure Functions](../azure-functions/functions-overview.md) , ele poderá usar uma **identidade gerenciada** para acessar os recursos. Leia mais sobre identidades gerenciadas na próxima seção.

A etapa de autorização requer que uma função do Azure seja atribuída à entidade de segurança. As funções atribuídas a uma entidade de segurança determinam as permissões que o principal terá. O Azure digital gêmeos fornece funções do Azure que abrangem conjuntos de permissões para recursos do gêmeos digital do Azure. Essas funções são descritas posteriormente neste artigo.

Para saber mais sobre funções e atribuições de função com suporte no Azure, confira [*entender as diferentes funções*](../role-based-access-control/rbac-and-directory-admin-roles.md) na documentação do RBAC do Azure.

#### <a name="authentication-with-managed-identities"></a>Autenticação com identidades gerenciadas

[Identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) é um recurso entre o Azure que permite que você crie uma identidade segura associada à implantação na qual o código do aplicativo é executado. Em seguida, você pode associar essa identidade a funções de controle de acesso, para conceder permissões personalizadas para acessar recursos específicos do Azure que seu aplicativo precisa.

Com identidades gerenciadas, a plataforma do Azure gerencia essa identidade de runtime. Você não precisa armazenar e proteger as chaves de acesso no código ou na configuração do aplicativo, seja para a identidade ou para os recursos que você precisa acessar. Um aplicativo cliente do Azure digital gêmeos em execução dentro de um aplicativo de serviço Azure App não precisa manipular regras e chaves de SAS nem quaisquer outros tokens de acesso. O aplicativo cliente precisa apenas do endereço do ponto de extremidade do namespace do gêmeos digital do Azure. Quando o aplicativo se conecta, o Azure digital gêmeos associa o contexto da entidade gerenciada ao cliente. Quando ele estiver associado a uma identidade gerenciada, o cliente de gêmeos digital do Azure poderá realizar todas as operações autorizadas. A autorização será concedida por meio da Associação de uma entidade gerenciada a uma função do Azure digital gêmeos do Azure (descrita abaixo).

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Autorização: funções do Azure para o gêmeos digital do Azure

O Azure fornece **duas funções internas do Azure** para autorizar o acesso às [APIs do plano de dados](how-to-use-apis-sdks.md#overview-data-plane-apis)do Azure digital gêmeos. Você pode consultar as funções por nome ou por ID:

| Função interna | Descrição | ID | 
| --- | --- | --- |
| Proprietário de Dados dos Gêmeos Digitais do Azure | Fornece acesso completo nos recursos de gêmeos digitais do Azure | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
| Leitor de dados do Azure digital gêmeos | Fornece acesso somente leitura aos recursos de gêmeos digitais do Azure | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |

Você pode atribuir funções de duas maneiras:
* por meio do painel controle de acesso (IAM) para o gêmeos digital do Azure no portal do Azure (consulte [*atribuir funções do Azure usando o portal do Azure*](../role-based-access-control/role-assignments-portal.md))
* por meio de comandos da CLI para adicionar ou remover uma função

Para obter etapas mais detalhadas sobre como fazer isso, experimente no tutorial do gêmeos digital do Azure [*: conectar uma solução de ponta a ponta*](tutorial-end-to-end.md).

Para obter mais informações sobre como as funções internas são definidas, consulte [*entender as definições de função*](../role-based-access-control/role-definitions.md) na documentação do RBAC do Azure. Para obter informações sobre como criar funções personalizadas do Azure, consulte [*funções personalizadas do Azure*](../role-based-access-control/custom-roles.md).

##### <a name="automating-roles"></a>Automatizando funções

Ao fazer referência a funções em cenários automatizados, é recomendável consultá-las por suas **IDs** , em vez de seus nomes. Os nomes podem ser alterados entre as versões, mas as IDs não vão, tornando-as uma referência mais estável na automação.

> [!TIP]
> Se você estiver assiging funções com um cmdlet, como `New-AzRoleAssignment` ([referência](/powershell/module/az.resources/new-azroleassignment)), poderá usar o `-RoleDefinitionId` parâmetro em vez de `-RoleDefinitionName` para passar uma ID em vez de um nome para a função.

### <a name="permission-scopes"></a>Escopos de permissão

Antes de atribuir uma função do Azure a uma entidade de segurança, determine o escopo do acesso que essa entidade de segurança deve ter. As práticas recomendadas ditam que é melhor conceder apenas o escopo mais estreito possível.

A lista a seguir descreve os níveis nos quais você pode fazer o escopo de acesso aos recursos de gêmeos digitais do Azure.
* Modelos: as ações para este recurso determinam o controle sobre os [modelos](concepts-models.md) carregados no Azure digital gêmeos.
* Consultar grafo digital gêmeos: as ações para esse recurso determinam a capacidade de executar [operações de consulta](concepts-query-language.md) no gêmeos digital no grafo do Azure digital gêmeos.
* Digital: as ações desse recurso fornecem controle sobre as operações CRUD em [gêmeos digital](concepts-twins-graph.md) no grafo de entrelaçamento.
* Relação de gêmeos digital: as ações para esse recurso definem o controle sobre as operações CRUD em [relações](concepts-twins-graph.md) entre digital no grafo de entrelaçamento.
* Rota de eventos: as ações para este recurso determinam as permissões para [rotear eventos](concepts-route-events.md) do Azure digital gêmeos para um serviço de ponto de extremidade como [Hub de eventos](../event-hubs/event-hubs-about.md), [grade de eventos](../event-grid/overview.md)ou barramento de [serviço](../service-bus-messaging/service-bus-messaging-overview.md).

### <a name="troubleshooting-permissions"></a>Permissões de solução de problemas

Se um usuário tentar executar uma ação não permitida por sua função, ele poderá receber um erro da leitura da solicitação de serviço `403 (Forbidden)` . Para obter mais informações e etapas de solução de problemas, consulte [*solução de problemas: falha na solicitação do Azure digital gêmeos com o status: 403 (proibido)*](troubleshoot-error-403.md).

## <a name="managed-identity-for-accessing-other-resources-preview"></a>Identidade gerenciada para acessar outros recursos (visualização)

Configurar uma **identidade gerenciada** [do Azure Active Directory (AD do Azure)](../active-directory/fundamentals/active-directory-whatis.md) para uma instância do gêmeos digital do Azure pode permitir que a instância acesse facilmente outros recursos protegidos pelo azure AD, como [Azure Key Vault](../key-vault/general/overview.md). A identidade é gerenciada pela plataforma do Azure e não exige que você provisione ou gire segredos. Para obter mais informações sobre identidades gerenciadas no Azure AD, consulte  [*identidades gerenciadas para recursos do Azure*](../active-directory/managed-identities-azure-resources/overview.md). 

O Azure dá suporte a dois tipos de identidades gerenciadas: atribuído pelo sistema e atribuído pelo usuário. Atualmente, o Azure digital gêmeos dá suporte apenas **a identidades atribuídas pelo sistema**. 

Você pode usar uma identidade gerenciada atribuída pelo sistema para sua instância digital do Azure para autenticar em um [ponto de extremidade definido pelo personalizado](concepts-route-events.md#create-an-endpoint). O Azure digital gêmeos dá suporte à autenticação baseada em identidades atribuída pelo sistema para pontos de extremidade para os destinos do [Hub de eventos](../event-hubs/event-hubs-about.md) e do [barramento de serviço](../service-bus-messaging/service-bus-messaging-overview.md)   e para um ponto de extremidades do [contêiner de armazenamento do Azure](../storage/blobs/storage-blobs-introduction.md)   para [eventos de mensagens mortas](concepts-route-events.md#dead-letter-events). [Grade](../event-grid/overview.md)   de eventos Atualmente, não há suporte para pontos de extremidade para identidades gerenciadas.

Para obter instruções sobre como habilitar uma identidade gerenciada pelo sistema para o gêmeos digital do Azure e usá-la para rotear eventos, consulte [*como: habilitar uma identidade gerenciada para eventos de roteamento (visualização)*](./how-to-enable-managed-identities-portal.md).

## <a name="private-network-access-with-azure-private-link-preview"></a>Acesso à rede privada com o link privado do Azure (versão prévia)

O [link privado do Azure](../private-link/private-link-overview.md) é um serviço que permite que você acesse recursos do Azure (como [hubs de eventos do Azure](../event-hubs/event-hubs-about.md), [armazenamento do Azure](../storage/common/storage-introduction.md)e [Azure Cosmos DB](../cosmos-db/introduction.md)) e serviços de parceiros e clientes hospedados no Azure por meio de um ponto de extremidade privado em sua [VNet (rede virtual) do Azure](../virtual-network/virtual-networks-overview.md). 

Da mesma forma, você pode usar pontos de extremidade privados para sua instância de cópia digital do Azure para permitir que os clientes localizados em sua rede virtual acessem com segurança a instância por meio do link privado. 

O ponto de extremidade privado usa um endereço IP do seu espaço de endereço de VNet do Azure. O tráfego de rede entre um cliente em sua rede privada e a instância de gêmeos digital do Azure atravessa a VNet e um link privado na rede de backbone da Microsoft, eliminando a exposição à Internet pública. Aqui está uma representação visual deste sistema:

:::image type="content" source="media/concepts-security/private-link.png" alt-text="Um diagrama que mostra uma rede para uma empresa PowerGrid que é uma VNET protegida sem acesso à Internet/à nuvem pública, conectando-se por meio de um link privado para uma instância do gêmeos digital do Azure chamada CityOfTwins.":::

Configurar um ponto de extremidade privado para sua instância do gêmeos digital do Azure permite proteger sua instância do gêmeos digital do Azure e eliminar a exposição pública, bem como evitar dados vazamento de sua VNet.

Para obter instruções sobre como configurar o link privado para o Azure digital gêmeos, consulte [*como habilitar o acesso privado com o link privado (visualização)*](./how-to-enable-private-link-portal.md).

### <a name="design-considerations"></a>Considerações sobre o design 

Ao trabalhar com o link privado para o gêmeos digital do Azure, aqui estão alguns fatores que talvez você queira considerar:
* **Preços**: para obter detalhes de preços, consulte  [preço do link privado do Azure](https://azure.microsoft.com/pricing/details/private-link). 
* **Disponibilidade regional**: para o gêmeos digital do Azure, esse recurso está disponível em todas as regiões do Azure em que o Azure digital gêmeos está disponível. 
* **Número máximo de pontos de extremidade privados por instância de gêmeos digital do Azure**: 10

Para obter informações sobre os limites do link privado, consulte [documentação do link privado do Azure: limitações](../private-link/private-link-service-overview.md#limitations).

## <a name="service-tags"></a>Marcas de serviço

Uma **marca de serviço** representa um grupo de prefixos de endereço IP de um determinado serviço do Azure. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços, minimizando a complexidade de atualizações frequentes das regras de segurança de rede. Para obter mais informações sobre marcas de serviço, consulte  [*marcas de rede virtual*](../virtual-network/service-tags-overview.md). 

Você pode usar marcas de serviço para definir os controles de acesso à rede em [grupos de segurança de rede](../virtual-network/network-security-groups-overview.md#security-rules)   ou no [Firewall do Azure](../firewall/service-tags.md), usando as marcas de serviço no lugar de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (nesse caso, **AzureDigitalTwins**) no campo de *origem*   ou *destino* apropriado   de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. 

Abaixo estão os detalhes da marca de serviço **AzureDigitalTwins** .

| Marca | Finalidade | É possível usar entrada ou saída? | Pode ser regional? | É possível usar com o Firewall do Azure? |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Gêmeos Digitais do Azure<br>Observação: esta marca ou os endereços IP cobertos por essa marca podem ser usados para restringir o acesso a pontos de extremidade configurados para [rotas de eventos](concepts-route-events.md). | Entrada | Não | Sim |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>Usando marcas de serviço para acessar pontos de extremidade de rota de eventos 

Aqui estão as etapas para acessar pontos de extremidade de [rota de eventos](concepts-route-events.md) usando marcas de serviço com o gêmeos digital do Azure.

1. Primeiro, Baixe esta referência de arquivo JSON mostrando intervalos de IP do Azure e marcas de serviço: [*intervalos de IP do Azure e marcas de serviço*](https://www.microsoft.com/download/details.aspx?id=56519). 

2. Procure os intervalos de IP "AzureDigitalTwins" no arquivo JSON.  

3. Consulte a documentação do recurso externo conectado ao ponto de extremidade (por exemplo, a [grade de eventos](../event-grid/overview.md), o Hub de [eventos](../event-hubs/event-hubs-about.md), o [barramento de serviço](../service-bus-messaging/service-bus-messaging-overview.md)ou o armazenamento do [Azure](../storage/blobs/storage-blobs-overview.md) para [eventos de mensagens mortas](concepts-route-events.md#dead-letter-events)) para ver como definir filtros IP para esse recurso.

4. Defina os filtros IP nos recursos externos usando os intervalos de IP da *etapa 2*.  

5. Atualize periodicamente os intervalos de IP conforme necessário. Os intervalos podem mudar ao longo do tempo, portanto, é uma boa ideia verificá-los regularmente e atualizá-los quando necessário. A frequência dessas atualizações pode variar, mas é uma boa ideia verificá-las uma vez por semana.

## <a name="encryption-of-data-at-rest"></a>Criptografia de dados em repouso

O Azure digital gêmeos fornece criptografia de dados em repouso e em trânsito conforme eles são gravados em nossos data centers e descriptografa-os para você conforme você o acessa. Essa criptografia ocorre usando uma chave de criptografia gerenciada pela Microsoft.

## <a name="cross-origin-resource-sharing-cors"></a>CORS (Compartilhamento de Recursos entre Origens)

O Azure digital gêmeos atualmente não dá suporte a **CORS (compartilhamento de recursos entre origens)**. Como resultado, se você estiver chamando uma API REST de um aplicativo de navegador, uma interface de [Gerenciamento de API (APIM)](../api-management/api-management-key-concepts.md) ou um conector de [aplicativos de energia](/powerapps/powerapps-overview) , você poderá ver um erro de política.

Para resolver esse erro, você pode executar uma das seguintes ações:
* Remova o cabeçalho CORS `Access-Control-Allow-Origin` da mensagem. Esse cabeçalho indica se a resposta pode ser compartilhada. 
* Como alternativa, crie um proxy CORS e faça a solicitação da API REST do Azure digital gêmeos. 

## <a name="next-steps"></a>Próximas etapas

* Veja esses conceitos em ação em [*como: configurar uma instância e uma autenticação*](how-to-set-up-instance-portal.md).

* Veja como interagir com esses conceitos do código do aplicativo cliente em [*como: escrever o código de autenticação do aplicativo*](how-to-authenticate-client.md).

* Leia mais sobre o [RBAC do Azure](../role-based-access-control/overview.md).