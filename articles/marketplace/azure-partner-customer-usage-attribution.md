---
title: Atribuição de uso do cliente do Azure
description: Obtenha uma visão geral de como controlar o uso do cliente para aplicativos do Azure no Marketplace comercial e outro IP implantável desenvolvido por parceiros.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: cpercy737
ms.author: camper
ms.date: 03/22/2021
ms.custom: devx-track-terraform
ms.openlocfilehash: 53edd3ec9a8d30d0c25f994db4a8b6f0199c2169
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558407"
---
# <a name="azure-customer-usage-attribution"></a>Atribuição de uso do cliente do Azure

A atribuição de uso do cliente associa o uso de recursos do Azure em assinaturas de cliente criadas durante a implantação de seu IP com você como um parceiro. Formar essas associações em sistemas internos da Microsoft traz maior visibilidade para a superfície do Azure que executa seu software. Para [as ofertas de aplicativo Azure no mercado comercial](#commercial-marketplace-azure-apps), esse recurso de controle ajuda você a se alinhar com as equipes de vendas da Microsoft e obter o crédito dos programas de parceiros da Microsoft.

A atribuição de uso do cliente dá suporte a três opções de implantação:

1. Azure Resource Manager modelos (as bases comuns dos aplicativos do Azure, também referenciados no Marketplace comercial como "modelos de solução" ou "aplicativos gerenciados"): os parceiros criam modelos do Resource Manager para definir a infraestrutura e a configuração de suas soluções do Azure. Um modelo do Resource Manager permite que seus clientes implantem os recursos da solução em um estado consistente e repetível.
1. Azure Resource Manager APIs: os parceiros podem chamar as APIs do Gerenciador de recursos para implantar um modelo do Resource Manager ou provisionar diretamente os serviços do Azure.
1. Terraform: os parceiros podem usar o Terraform para implantar um modelo do Resource Manager ou implantar diretamente os serviços do Azure.

Há casos de uso secundários para atribuição de uso do cliente fora do Marketplace comercial descrito [posteriormente neste artigo](#other-use-cases).

>[!IMPORTANT]
>- A atribuição de uso do cliente não se destina a acompanhar o trabalho de integradores de sistemas, provedores de serviços gerenciados ou ferramentas projetadas principalmente para implantar e gerenciar recursos do Azure.
>- A atribuição de uso do cliente destina-se a novas implantações e não oferece suporte a recursos de acompanhamento que já foram implantados.
>- Nem todos os serviços do Azure são compatíveis com a atribuição de uso do cliente. Os AKS (serviços Kubernetess do Azure), os conjuntos de dimensionamento de VM e o lote do Azure têm problemas conhecidos que causam a geração de relatórios de uso.

## <a name="commercial-marketplace-azure-apps"></a>Aplicativos do Azure no Marketplace comercial

O controle do uso do Azure de aplicativos do Azure publicados no Marketplace comercial é amplamente automático. Ao carregar um modelo do Resource Manager como parte da [configuração técnica do plano do aplicativo do Azure do Marketplace](./create-new-azure-apps-offer-solution.md#define-the-technical-configuration), o Partner Center adicionará uma ID de rastreamento legível por Azure Resource Manager.

Se você usar APIs de Azure Resource Manager, será necessário adicionar sua ID de rastreamento de acordo com as [instruções abaixo](#use-resource-manager-apis) para passá-la para Azure Resource Manager à medida que o código implantar recursos. Essa ID é visível no Partner Center na página de configuração técnica do seu plano. 

> [!NOTE]
> Para aplicativos existentes do Azure, uma migração única começou em março de 2021 para atualizar as IDs de controle na configuração técnica de cada plano. O uso de implantações passadas dessas ofertas permanecerá acompanhado nos sistemas da Microsoft.
>
>Ao atualizar suas ofertas, você não precisa mais adicionar o tipo de recurso **Microsoft. Resources/Implantations** em seu arquivo de modelo principal.

## <a name="other-use-cases"></a>Outros casos de uso 

Você pode usar a atribuição de uso do cliente para acompanhar o uso do Azure de soluções não disponíveis no Marketplace comercial. Essas soluções geralmente residem no repositório de início rápido, repositórios do GitHub privado ou provenientes de 1:1 compromissos do cliente que criam um IP durável (como um aplicativo implantável e escalonável).

Há várias etapas manuais necessárias:

1. Crie um ou mais GUIDs para usar como suas IDs de controle.
1. Registre esses GUIDs no Partner Center.
1. Adicione seus GUIDs registrados ao aplicativo do Azure e/ou às cadeias de caracteres do agente do usuário.

### <a name="create-guids"></a>Criar GUIDs

Ao contrário das IDs de controle que o Partner Center cria em seu nome para os aplicativos do Azure no Marketplace comercial, outros usos do CUA exigem que você crie um GUID para usar como sua ID de rastreamento. Um GUID é um identificador de referência exclusivo que tem 32 dígitos hexadecimais. Para criar GUIDs para acompanhamento, você deve usar um gerador de GUID, por exemplo, por meio do PowerShell:

```powershell
[guid]::NewGuid()
```

Você deve criar um GUID exclusivo para cada produto e canal de distribuição. Você pode usar um único GUID para vários canais de distribuição de um produto se não quiser que os relatórios sejam divididos. O relatório ocorre por Microsoft Partner Network ID e GUID.

### <a name="register-guids"></a>Registrar GUIDs

Os GUIDs devem ser registrados no Partner Center para que possam ser associados a você como um parceiro:

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard).

1. Inscreva-se como um [editor do Marketplace comercial](https://aka.ms/JoinMarketplace).

1. Selecione **configurações** (ícone de engrenagem) no canto superior direito e, em seguida, **configurações de conta**.

1. Selecione identificadores de **perfil da organização**  >    >  **Adicionar GUID de acompanhamento**.

1. Na caixa **GUID**, insira seu GUID de acompanhamento. Insira apenas o GUID sem o `pid-` prefixo. Na caixa **Descrição** , insira o nome da solução ou a descrição.

1. Para registrar mais de um GUID, selecione **Adicionar GUID de Rastreamento** novamente. Caixas adicionais aparecerão na página.

1. Clique em **Salvar**.

### <a name="add-a-guid-to-a-resource-manager-template"></a>Adicionar um GUID a um modelo do Resource Manager

Para adicionar seu GUID registrado a um modelo do Resource Manager, faça uma única modificação no arquivo de modelo principal:

1. Abra o modelo do Resource Manager.

1. Adicione um novo recurso do tipo [Microsoft. Resources/Implantations](/azure/templates/microsoft.resources/deployments) no arquivo de modelo principal. O recurso precisa estar no **mainTemplate.js** ou **azuredeploy.jssomente no** arquivo, não em nenhum modelo aninhado ou vinculado.

1. Insira o valor de GUID após o `pid-` prefixo como o nome do recurso. Por exemplo, se o GUID for eb7927c8-dd66-43e1-b0cf-c346a422063, o nome do recurso será **pid-eb7927c8-dd66-43e1-b0cf-c346a422063**. Exemplo:
 
```json
{ // add this resource to the resources section in the mainTemplate.json
    "apiVersion": "2020-06-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```
4. Verifique se há erros no modelo.

1. Republique o modelo nos repositórios apropriados.

1. [Verificar o sucesso da GUID na implantação de modelo](#verify-deployments-tracked-with-a-guid).

> [!TIP]
> Para obter mais informações sobre como criar e publicar modelos do Resource Manager, consulte: [criar e implantar seu primeiro modelo do Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="verify-deployments-tracked-with-a-guid"></a>Verificar as implantações acompanhadas com um GUID

Depois de modificar o modelo e executar uma implantação de teste, use o seguinte script do PowerShell para recuperar os recursos implantados e marcados.

Você pode usar o script para verificar se o GUID foi adicionado com êxito ao seu modelo do Resource Manager. O script não se aplica a implantações da API do Resource Manager nem do Terraform.

Entre no Azure. Selecione a assinatura com a implantação que você deseja verificar antes de executar o script. Execute o script dentro do contexto de assinatura da implantação.

O **GUID** (abaixo chamado de "deploymentname") e o nome **resourceGroupName** da implantação são parâmetros obrigatórios.

Você pode obter [o script original](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1) no GitHub.

```powershell
Param(
    [string][Parameter(Mandatory=$true)]$deploymentName, # the full name of the deployment, e.g. pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the named deployment
$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name "$deploymentName").correlationId

# Find all deployments with that correlationId
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in all deployments with that correlationId as PowerShell doesn't surface outputResources on the deployment or correlationId on the deploymentOperation

foreach ($deployment in $deployments){
    # Get deploymentOperations by deploymentName
    # then the resourceIds for each resource
    ($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.targetResource -notlike "*Microsoft.Resources/deployments*"}).TargetResource
}
```

### <a name="notify-your-customers"></a>Notificar os clientes

Os parceiros devem informar seus clientes sobre implantações que usam a atribuição de uso do cliente. A Microsoft informa o uso do Azure associado a essas implantações ao parceiro. Os exemplos a seguir incluem conteúdo que você pode usar para notificar seus clientes sobre essas implantações. Nos exemplos, substitua \<PARTNER> pelo nome da sua empresa. Os parceiros devem garantir que a notificação se alinhe às políticas de privacidade e de coleta de dados, incluindo opções para que os clientes sejam excluídos do rastreamento.

#### <a name="notification-for-resource-manager-template-deployments"></a>Notificação para implantações de modelos do Resource Manager

Quando você implanta esse modelo, a Microsoft pode identificar a instalação do \<PARTNER> software com os recursos do Azure implantados. A Microsoft pode correlacionar esses recursos usados para dar suporte ao software. A Microsoft coleta essas informações para fornecer as melhores experiências com seus produtos e operar seus negócios. Os dados são coletados e governados pelas políticas de privacidade da Microsoft, localizadas em [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

#### <a name="notification-for-sdk-or-api-deployments"></a>Notificação para implantações de SDK ou a API

Quando você implanta \<PARTNER> software, a Microsoft pode identificar a instalação do \<PARTNER> software com os recursos do Azure implantados. A Microsoft pode correlacionar esses recursos usados para dar suporte ao software. A Microsoft coleta essas informações para fornecer as melhores experiências com seus produtos e operar seus negócios. Os dados são coletados e governados pelas políticas de privacidade da Microsoft, localizadas em [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

## <a name="use-resource-manager-apis"></a>Usar APIs do Gerenciador de recursos

Em alguns casos, você pode fazer chamadas diretamente em relação às APIs REST do Gerenciador de recursos para implantar os serviços do Azure. [Azure dá suporte a vários SDKs](../index.yml?pivot=sdkstools) para habilitar essas chamadas. Você pode usar um dos SDKs ou chamar as APIs REST diretamente para implantar recursos.

Para habilitar a atribuição de uso do cliente, quando você cria suas chamadas à API, inclua sua ID de rastreamento no cabeçalho do agente do usuário na solicitação. Formate a cadeia de caracteres com o `pid-` prefixo. Exemplos:

```xml
//Commercial Marketplace Azure app
pid-contoso-myoffer-partnercenter //copy the tracking ID exactly as it appears in Partner Center

//Other use cases
pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4 //enter your GUID after "pid-"
```
> [!IMPORTANT]
> Se você estiver usando APIs do Gerenciador de recursos com um aplicativo do Azure no Marketplace comercial, use a ID de rastreamento fornecida no Partner Center. Não use um GUID.

Vários SDKs interagem com as APIs do Gerenciador de recursos de maneira diferente e exigirão algumas diferenças no seu código. Os exemplos a seguir apresentam a abordagem do Marketplace não comercial usando um GUID e abrangem uma variedade dos SDKs mais populares do Azure.

#### <a name="example-python-sdk"></a>Exemplo: SDK do Python

Para o Python, use o **config** atributo. Você só pode adicionar o atributo a um UserAgent. Exemplo:

```python
client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
client.config.add_user_agent("pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4")
```
> [!IMPORTANT]
> Adicione o atributo para cada cliente. Não há nenhuma configuração estática global. Você pode marcar uma fábrica de cliente para garantir que cada cliente esteja rastreando. Para obter mais informações, consulte este [exemplo de fábrica do cliente no GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="example-net-sdk"></a>Exemplo: SDK do .NET

Para o .NET, certifique-se de definir o agente do usuário. Use a biblioteca [Microsoft. Azure. Management. Fluent](/dotnet/api/microsoft.azure.management.fluent) para definir o agente do usuário com o seguinte código (exemplo em C#):

```csharp
var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="example-azure-powershell"></a>Exemplo: Azure PowerShell

Se você implantar recursos por meio de Azure PowerShell, acrescente o GUID usando este método:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="example-azure-cli"></a>Exemplo: CLI do Azure

Ao usar o CLI do Azure para acrescentar o GUID, defina a variável de ambiente **AZURE_HTTP_USER_AGENT** no escopo de um script. Você também pode definir a variável globalmente para o escopo da shell:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

Para obter mais informações, confira [SDK do Azure para linguagem Go](/azure/developer/go/).

## <a name="use-terraform"></a>Usar o Terraform

O suporte para Terraform está disponível por meio da versão 1.21.0 do provedor do Azure: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019) . Isso se aplica a todos os parceiros que implantam sua solução por meio do Terraform e de todos os recursos implantados e medidos pelo provedor do Azure (versão 1.21.0 ou posterior).

O provedor do Azure para Terraform adicionou um novo campo opcional chamado [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) para especificar o GUID de rastreamento usado para sua solução. O valor desse campo também pode ser originado da variável de ambiente *ARM_PARTNER_ID*.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Defina o valor de *partner_id* como um GUID registrado. Não Prefixe o GUID com "PID-", basta defini-lo como o GUID real.

> [!IMPORTANT]
> Se você estiver usando o Terraform com um aplicativo do Azure no Marketplace comercial, use toda a ID de acompanhamento fornecida no Partner Center. Não use um GUID.

## <a name="get-support"></a>Obtenha suporte

Saiba mais sobre as opções de suporte no Marketplace comercial em [suporte para o programa do Marketplace comercial no Partner Center](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>Como enviar uma solicitação de consultoria técnica

1. Visite [Serviços Técnicos do Parceiro](https://aka.ms/TechnicalJourney).
1. Selecione **infraestrutura e gerenciamento de nuvem** para exibir a jornada técnica.
1. Selecione **Serviços**  >  **de implantação enviar uma solicitação**.
1. Entre usando sua MSA (conta do MPN) ou seu AAD (conta do painel do parceiro).
1. Conclua/revise as informações de contato no formulário que é aberto. Os detalhes de consultoria podem ser preenchidos previamente ou você pode ter opções suspensas.
1. Insira um título e uma descrição detalhada do problema.
1. Selecione **Enviar**.

Veja as instruções passo a passo com capturas de tela em [Using Technical Presales and Deployment Services](https://aka.ms/TechConsultInstructions) (Uso dos serviços técnicos de pré-vendas e implantação).

Você será contatado por um consultor técnico de parceiros da Microsoft para agendar uma chamada e definir o escopo de suas necessidades.

## <a name="report"></a>Relatório
Os relatórios para uso do Azure acompanhados por meio da atribuição de uso do cliente não estão disponíveis atualmente para parceiros ISV. A adição de relatórios ao programa do Marketplace comercial no Partner Center para cobrir a atribuição de uso do cliente é destinada ao segundo semestre de 2021.

## <a name="faq"></a>Perguntas frequentes

#### <a name="after-a-tracking-id-is-added-can-it-be-changed"></a>Depois que uma ID de rastreamento é adicionada, ela pode ser alterada?

As IDs de acompanhamento dos aplicativos do Azure no Marketplace comercial são gerenciadas automaticamente pelo Partner Center. No entanto, um cliente pode baixar um modelo e alterar ou remover a ID de rastreamento. Os parceiros devem descrever de forma proativa a função da ID de rastreamento para seus clientes para evitar a remoção ou edição. A alteração da ID de rastreamento afeta apenas as novas implantações e recursos, não os existentes.

#### <a name="can-i-track-templates-deployed-from-a-non-microsoft-repository-like-github"></a>Posso acompanhar modelos implantados de um repositório não Microsoft como o GitHub?

Sim, contanto que a ID de rastreamento esteja presente quando o modelo for implantado, o uso será acompanhado. Para manter a associação entre você como um Publicador e seu modelo implantado de um repositório não Microsoft, primeiro Baixe uma cópia do modelo publicado (que conterá a ID de rastreamento) da listagem do Marketplace comercial da sua oferta na portal do Azure. Publique essa versão no GitHub ou em outro repositório que não seja da Microsoft.

Se o seu modelo não estiver listado no Marketplace comercial e incluir um GUID registrado, verifique se o GUID está presente na versão que você publica no GitHub ou em outro repositório que não seja da Microsoft.

#### <a name="does-the-customer-receive-reporting-as-well"></a>O cliente que recebe a emissão de relatórios também?

Não. Os clientes podem controlar o uso de todos os recursos ou grupos de recursos dentro do portal do Azure. Os clientes não veem o uso dividido pela ID de acompanhamento do CUA.

#### <a name="is-customer-usage-attribution-similar-to-the-digital-partner-of-record-dpor-or-partner-admin-link-pal"></a>A atribuição de uso do cliente é semelhante ao DPOR (parceiro de registro digital) ou ao PAL (link de administrador do parceiro)?

A atribuição de uso do cliente é um mecanismo para associar o uso do Azure com o IP que é reproduzível e passível de ser implantado por um parceiro a associação no momento da implantação. DPOR e PAL destinam-se a associar um parceiro de consultoria (integrador de sistemas) ou de gerenciamento (provedor de serviços gerenciados) à superfície do Azure relevante de um cliente para o tempo enquanto o parceiro está envolvido no cliente.