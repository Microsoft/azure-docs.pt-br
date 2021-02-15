---
title: Visão geral do portal do desenvolvedor no gerenciamento de API do Azure
titleSuffix: Azure API Management
description: Saiba mais sobre o portal do desenvolvedor no gerenciamento de API-um site personalizável, onde os consumidores de API podem explorar suas APIs.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 30487218fc95be75d22b5a9ea5a6dbc224ffd025
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074790"
---
# <a name="overview-of-the-developer-portal"></a>Visão geral do portal do desenvolvedor

O portal do desenvolvedor é um site da Web, totalmente personalizado e gerado com a documentação de suas APIs. É aí que os consumidores de API podem descobrir suas APIs, saber como usá-las, solicitar acesso e experimentá-las.

Este artigo descreve as diferenças entre as versões autohospedadas e gerenciadas do portal do desenvolvedor no gerenciamento de API. Ele também fornece respostas para perguntas frequentes.

![Portal do desenvolvedor do Gerenciamento da API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>Migração do portal herdado

> [!IMPORTANT]
> O portal do desenvolvedor herdado foi preterido e receberá apenas atualizações de segurança. Você pode continuar usando-o, como de costume, até a desativação dele em outubro de 2023, quando ele será removido de todos os serviços de Gerenciamento de API.

A migração para o novo portal do desenvolvedor é descrita no [artigo de documentação dedicada](developer-portal-deprecated-migration.md).

## <a name="customization-and-styling"></a>Personalização e estilo

O portal do desenvolvedor pode ser personalizado e estilizado por meio do editor visual interno, com o recurso de arrastar e soltar. Consulte [este tutorial](api-management-howto-developer-portal-customize.md) para obter mais detalhes.

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> Extensibilidade

O serviço de gerenciamento de API inclui um portal de desenvolvedor **gerenciado** e sempre atualizado. Você pode acessá-lo na interface portal do Azure.

Se você precisar estendê-lo com lógica personalizada, que não tem suporte pronto para uso, poderá modificar sua base de código. A base de código do portal está [disponível em um repositório GitHub][1]. Por exemplo, você pode implementar um novo widget, que se integra a um sistema de suporte de terceiros. Ao implementar a nova funcionalidade, você pode escolher uma das seguintes opções:

- **Hospede internamente** o portal resultante fora do seu serviço de gerenciamento de API. Quando você hospeda o portal por conta própria, você se torna seu mantenedor e é responsável por suas atualizações. A assistência do suporte do Azure é limitada apenas à configuração básica de portais de hospedagem interna, conforme documentado na [seção wiki do repositório][2].
- Abra uma solicitação de pull para a equipe de gerenciamento de API para mesclar novas funcionalidades para a base de código do portal **gerenciado** .

Para obter detalhes e instruções de extensibilidade, consulte o [repositório do GitHub][1] e [os tutoriais sobre como implementar um widget][3]. O [tutorial para personalizar o portal gerenciado](api-management-howto-developer-portal-customize.md) orienta você pelo painel administrativo do portal, que é comum para versões **gerenciadas** e **hospedadas internamente** .

## <a name="frequently-asked-questions"></a><a name="faq"></a> Perguntas frequentes

Nesta seção, respondemos a perguntas comuns sobre o portal do desenvolvedor, que são de natureza geral. Para perguntas específicas para a versão hospedada internamente, consulte [a seção wiki do repositório do GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a> Como posso migrar da versão de visualização do portal?

Quando você iniciou pela primeira vez a versão de visualização do portal do desenvolvedor, provisionou a versão de visualização de seu conteúdo padrão no serviço de gerenciamento de API. O conteúdo padrão foi significativamente modificado na versão disponível para o público geral. Por exemplo, a versão de visualização do conteúdo padrão não inclui botões do OAuth nas páginas de logon, ele usa widgets diferentes para exibir APIs e depende de recursos limitados para estruturar páginas do portal do desenvolvedor. Mesmo que haja diferenças no conteúdo, o mecanismo do portal (incluindo widgets subjacentes) é atualizado automaticamente toda vez que você publica o portal do desenvolvedor.

Se você tiver personalizado bastante seu portal com base na versão de visualização do conteúdo, poderá continuar a usá-lo como está e posicionar novos widgets manualmente nas páginas do Portal. Caso contrário, é recomendável substituir o conteúdo do portal pelo novo conteúdo padrão.

Para redefinir o conteúdo em um portal gerenciado, selecione **Redefinir Conteúdo** na seção do menu **operações** . Esta operação removerá todo o conteúdo do portal e provisionar o novo conteúdo padrão. Você perderá todas as personalizações e alterações do portal do desenvolvedor. **Não é possível desfazer esta ação** .

![Redefinir o conteúdo do portal](media/api-management-howto-developer-portal/reset-content.png)

Se você estiver usando a versão hospedada automaticamente, execute `scripts.v2/cleanup.bat` e `scripts.v2/generate.bat` scripts do repositório GitHub para remover o conteúdo existente e provisionar novo conteúdo. Certifique-se de atualizar o código do portal para a versão mais recente do repositório do GitHub com antecedência.

Se você tiver acessado o portal pela primeira vez após o anúncio de disponibilidade geral em novembro de 2019, ele já deverá apresentar o novo conteúdo padrão e nenhuma ação adicional será necessária.

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>A funcionalidade necessária não tem suporte no portal

Você pode abrir uma solicitação de recurso no [repositório GitHub][1] ou [implementar a funcionalidade ausente por conta própria][3]. Consulte a seção **extensibilidade** acima para obter mais detalhes.

### <a name="how-can-i-automate-portal-deployments"></a><a id="automate"></a> Como posso automatizar as implantações do portal?

Você pode acessar e gerenciar programaticamente o conteúdo do portal do desenvolvedor por meio da API REST, independentemente de estar usando uma versão gerenciada ou hospedada internamente.

A API está documentada na [seção wiki do repositório do GitHub][2]. Ele pode ser usado para automatizar migrações de conteúdo do portal entre ambientes, por exemplo, de um ambiente de teste para o ambiente de produção. Você pode saber mais sobre esse processo [neste artigo de documentação](https://aka.ms/apimdocs/migrateportal) sobre o github.

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>Como fazer mudar do gerenciado para a versão hospedada internamente?

Consulte o artigo detalhado na [seção wiki do repositório do portal do desenvolvedor no GitHub][2].

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Posso ter vários portais de desenvolvedor em um serviço de gerenciamento de API?

Você pode ter um portal gerenciado e vários portais hospedados internamente. O conteúdo de todos os portais é armazenado no mesmo serviço de gerenciamento de API, portanto, será idêntico. Se você quiser diferenciar a aparência e a funcionalidade dos portais, poderá hospedá-los internamente com seus próprios widgets personalizados que personalizam dinamicamente as páginas no tempo de execução, por exemplo, com base na URL.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>O portal dá suporte a modelos de Azure Resource Manager e/ou é compatível com o DevOps Resource Kit de gerenciamento de API?

Não.

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>O conteúdo do portal foi salvo com a funcionalidade de backup/restauração no gerenciamento de API?

Não.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>É necessário habilitar a conectividade VNet adicional para as dependências do portal gerenciado?

Na maioria dos casos, não.

Se o serviço de gerenciamento de API estiver em uma VNet interna, o portal do desenvolvedor só poderá ser acessado de dentro da rede. O nome do host do ponto de extremidade de gerenciamento deve ser resolvido para o VIP interno do serviço do computador que você usa para acessar a interface administrativa do Portal. Verifique se o ponto de extremidade de gerenciamento está registrado no DNS. No caso de uma configuração incorreta, você verá um erro: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Se o serviço de gerenciamento de API estiver em uma VNet interna e você o estiver acessando por meio do gateway de aplicativo da Internet, certifique-se de habilitar a conectividade com o portal do desenvolvedor e os pontos de extremidade de gerenciamento do gerenciamento de API. Talvez seja necessário desabilitar as regras de firewall do aplicativo Web. Consulte [Este artigo de documentação](api-management-howto-integrate-internal-vnet-appgateway.md) para obter mais detalhes.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Atribuí um domínio de gerenciamento de API personalizado e o portal publicado não funciona

Depois de atualizar o domínio, você precisará [republicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Adicionei um provedor de identidade e não consigo vê-lo no portal

Depois de configurar um provedor de identidade (por exemplo, o Azure AD, Azure AD B2C), você precisará [republicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor. Verifique se as páginas do portal do desenvolvedor incluem o widget botões do OAuth.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Configurei a delegação e o portal não a utiliza

Depois de configurar a delegação, você precisará [republicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Minhas outras alterações de configuração de gerenciamento de API não foram propagadas no portal do desenvolvedor

A maioria das alterações de configuração (por exemplo, VNet, entrada, termos do produto) requer [a republicação do portal](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Estou recebendo um erro de CORS ao usar o console interativo

O console interativo faz uma solicitação de API do lado do cliente do navegador. Resolva o problema de CORS adicionando [uma política de CORS](api-management-cross-domain-policies.md#CORS) em suas API (s).

Você pode verificar o status da política de CORS na seção **visão geral do portal** do serviço de gerenciamento de API no portal do Azure. Uma caixa de aviso indica uma política ausente ou configurada incorretamente.

![Captura de tela que mostra onde você pode verificar o status da sua política de CORS.](media/api-management-howto-developer-portal/cors-azure-portal.png)

Aplique automaticamente a política CORS clicando no botão **habilitar CORS** .

Você também pode habilitar o CORS manualmente.

1. Selecione o **aplicar manualmente no link de nível global** para ver o código de política gerado.
2. Navegue até **todas as APIs** na seção **APIs** do serviço de gerenciamento de API no portal do Azure.
3. Selecione o **</>** ícone na seção **processamento de entrada** .
4. Insira a política na **<inbound>** seção do arquivo XML. Verifique se o **<origin>** valor corresponde ao domínio do seu portal do desenvolvedor.

> [!NOTE]
> 
> Se você aplicar a política CORS no escopo do produto, em vez do escopo de API (s), e sua API usar a autenticação de chave de assinatura por meio de um cabeçalho, o console não funcionará.
>
> O navegador emite automaticamente uma solicitação HTTP OPTIONS, que não contém um cabeçalho com a chave de assinatura. Devido à chave de assinatura ausente, o gerenciamento de API não pode associar a chamada de opções a um produto, portanto, ele não pode aplicar a política CORS.
>
> Como alternativa, você pode passar a chave de assinatura em um parâmetro de consulta.

> [!NOTE]
> 
> Apenas uma política CORS é executada. Se você especificou várias políticas de CORS (por exemplo, no nível de API e no nível de todas as APIs), o console interativo poderá não funcionar conforme o esperado.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Quais permissões preciso para editar o portal do desenvolvedor?

Se você estiver vendo o `Oops. Something went wrong. Please try again later.` erro ao abrir o portal no modo administrativo, talvez você não esteja perdendo as permissões necessárias (RBAC do Azure).

Os portais herdados exigiam a permissão `Microsoft.ApiManagement/service/getssotoken/action` no escopo do serviço ( `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` ) para permitir que o administrador do usuário acesse os portais. O novo portal requer a permissão `Microsoft.ApiManagement/service/users/token/action` no escopo `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` .

Você pode usar o seguinte script do PowerShell para criar uma função com a permissão necessária. Lembre-se de alterar o `<subscription-id>` parâmetro. 

```powershell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
Depois que a função é criada, ela pode ser concedida a qualquer usuário da seção de **controle de acesso (iam)** no portal do Azure. A atribuição dessa função a um usuário atribuirá a permissão no escopo do serviço. O usuário poderá gerar tokens SAS em nome de *qualquer* usuário no serviço. No mínimo, essa função precisa ser atribuída ao administrador do serviço. O comando do PowerShell a seguir demonstra como atribuir a função a um usuário `user1` no escopo mais baixo para evitar a concessão de permissões desnecessárias ao usuário: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Depois que as permissões tiverem sido concedidas a um usuário, o usuário deverá sair e entrar novamente no portal do Azure para que as novas permissões entrem em vigor.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Estou vendo o `Unable to start the portal. See if settings are specified correctly (...)` erro

Esse erro é mostrado quando uma `GET` chamada a `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` falha. A chamada é emitida pelo navegador pela interface administrativa do Portal.

Se o serviço de gerenciamento de API estiver em uma VNet, consulte a pergunta de conectividade de VNet acima.

A falha de chamada também pode ser causada por um certificado TLS/SSL, que é atribuído a um domínio personalizado e não é confiável para o navegador. Como uma mitigação, você pode remover o ponto de extremidade de gerenciamento domínio personalizado-o gerenciamento de API voltará ao ponto de extremidade padrão com um certificado confiável.

### <a name="whats-the-browser-support-for-the-portal"></a>Qual é o suporte do navegador para o portal?

| Navegador                     | Com suporte       |
|-----------------------------|-----------------|
| Apple Safari                | Sim<sup>1</sup> |
| Google Chrome               | Sim<sup>1</sup> |
| Microsoft Edge              | Sim<sup>1</sup> |
| Microsoft Internet Explorer | No              |
| Mozilla Firefox             | Sim<sup>1</sup> |

 <small><sup>1</sup> com suporte nas duas versões de produção mais recentes.</small>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o novo portal do desenvolvedor:

- [Acessar e personalizar o portal do desenvolvedor gerenciado](api-management-howto-developer-portal-customize.md)
- [Configurar a versão hospedada internamente do portal][2]
- [Implementar seu próprio widget][3]

Procurar outros recursos:

- [Repositório GitHub com o código-fonte][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
