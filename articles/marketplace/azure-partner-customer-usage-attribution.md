---
title: Parceiro do Marketplace comercial e atribuição de uso do cliente
description: Obtenha uma visão geral de como acompanhar o uso do cliente para soluções do Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: cpercy737
ms.author: camper
ms.date: 11/4/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 99e1e77a37afbdc1ed54767700574316ed03fae3
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525238"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>Parceiro do Marketplace comercial e atribuição de uso do cliente

A atribuição de uso do cliente é um método para associar os recursos do Azure em execução em assinaturas de clientes, implantados para executar sua solução, com você como um parceiro. Formar essas associações em sistemas internos da Microsoft traz maior visibilidade para a superfície do Azure que executa seu software. Ao adotar essa capacidade de acompanhamento, você se alinha às equipes de vendas da Microsoft e obtém crédito para os programas de parceiros da Microsoft.

Você pode formar a associação por meio do Azure Marketplace, do repositório do Início Rápido, dos repositórios privados do GitHub e das participações individuais do cliente que criam IP durável (como o desenvolvimento de um aplicativo).

A atribuição de uso do cliente dá suporte a três opções de implantação:

- Modelos do Azure Resource Manager: Os parceiros podem usar os modelos do Resource Manager para implantar os serviços do Azure a fim de executar o software do parceiro. Os parceiros podem criar um modelo do Resource Manager para definir a infraestrutura e a configuração de sua solução do Azure. Um modelo do Resource Manager permite que você e seus clientes implantem sua solução em todo o seu ciclo de vida. Você pode ter certeza de que seus recursos são implantados em um estado consistente.
- APIs do Azure Resource Manager: Os parceiros podem chamar as APIs do Resource Manager diretamente para implantar um modelo do Resource Manager ou gerar as chamadas da API para provisionar diretamente os serviços do Azure.
- Terraform: Os parceiros podem usar o Terraform para implantar um modelo do Resource Manager ou implantar diretamente os serviços do Azure.

>[!IMPORTANT]
>- A atribuição de uso do cliente não se destina a acompanhar o trabalho de integradores de sistemas, provedores de serviços gerenciados ou ferramentas projetadas para implantar e gerenciar software em execução no Azure.
>
>- A atribuição de uso do cliente destina-se a novas implantações e NÃO dá suporte à marcação de recursos existentes já implantados.
>
>- A atribuição de uso do cliente é obrigatória para ofertas do [Aplicativo Azure](./create-new-azure-apps-offer.md) publicadas no Azure Marketplace.
>
>- Nem todos os serviços do Azure são compatíveis com a atribuição de uso do cliente. Os AKS (serviços Kubernetess do Azure) e os conjuntos de dimensionamento de VMs têm problemas conhecidos atualmente que causam a geração de relatórios de uso.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>Criar GUIDs

Um GUID é um identificador de referência exclusivo que tem 32 dígitos hexadecimais. Para criar GUIDs para acompanhamento, você deve usar um gerador de GUID, por exemplo, por meio do PowerShell.

```powershell
[guid]::NewGuid()
```

Recomendamos que você crie um GUID exclusivo para cada oferta e canal de distribuição para cada produto. Você pode optar por usar um único GUID para os vários canais de distribuição do produto se não quiser que os relatórios sejam divididos.

Se você implantar um produto usando um modelo e ele estiver disponível no Azure Marketplace e no GitHub, você poderá criar e registrar dois GUIDs distintos:

- Produto A no Azure Marketplace
- Produto A no GitHub

O relatório é feito por ID e GUID do Microsoft Partner Network.

Também é possível acompanhar o uso em um nível mais granular registrando GUIDs adicionais e alterando GUIDs entre planos, em que os planos são variantes de uma oferta.

## <a name="register-guids"></a>Registrar GUIDs

Os GUIDs precisam ser registrados no Partner Center para habilitar a atribuição de uso do cliente.

Depois que você adicionar um GUID ao seu modelo ou no agente do usuário e registrar o GUID no Partner Center, as implantações são acompanhadas.

> [!NOTE]
> Se você estiver publicando sua oferta de [aplicativo Azure](./create-new-azure-apps-offer.md) no Azure Marketplace por meio do Partner Center, qualquer novo GUID usado dentro do modelo será registrado automaticamente em seu perfil do Partner Center quando o modelo for carregado.  

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard).

1. Inscreva-se como um [editor do Marketplace comercial](https://aka.ms/JoinMarketplace).

   * Os parceiros precisam [ter um perfil no Partner Center](./partner-center-portal/create-account.md). Você é incentivado a listar a oferta no Azure Marketplace ou no AppSource.
   * Parceiros podem registrar vários GUIDs.
   * Os parceiros podem registrar GUIDs para os modelos e ofertas de soluções que não são do Marketplace.

1. Selecione **configurações** (ícone de engrenagem) no canto superior direito > **configurações da conta**.

1. Em   >  **identificadores** de perfil da organização, selecione **Adicionar GUID de acompanhamento**.

1. Na caixa **GUID**, insira seu GUID de acompanhamento. Insira apenas o GUID sem o `pid-` prefixo. Na caixa **Descrição**, insira o nome ou a descrição da sua oferta.

1. Para registrar mais de um GUID, selecione **Adicionar GUID de Rastreamento** novamente. Caixas adicionais aparecerão na página.

1. Clique em **Salvar**.

## <a name="use-resource-manager-templates"></a>Use modelos do Gerenciador de Recursos
Muitas soluções de parceiros são implantadas usando modelos do Azure Resource Manager. Se você tiver um modelo do Resource Manager que está disponível no Azure Marketplace, no GitHub ou como um guia de início rápido, o processo para modificar o modelo para habilitar a atribuição de uso do cliente é direto.

> [!NOTE]
> Para saber mais sobre como criar e publicar Modelos de Solução, confira
> * [Criar e implantar seu primeiro modelo do Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).
>* [Oferta de aplicativo do Azure](./create-new-azure-apps-offer.md).
>* Vídeo: [criando modelos de solução e aplicativos gerenciados para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


Para adicionar um identificador global exclusivo (GUID), você faz uma única modificação no arquivo de modelo principal:

1. [Crie um GUID](#create-guids) usando o método sugerido e [registre o GUID](#register-guids).

1. Abra o modelo do Resource Manager.

1. Adicione um novo recurso do tipo [Microsoft. Resources/Implantations](/azure/templates/microsoft.resources/deployments) no arquivo de modelo principal. O recurso precisa estar no arquivo **mainTemplate.json** ou **azuredeploy.json**, e não em nenhum modelo aninhado ou vinculado.

1. Insira o valor de GUID após o `pid-` prefixo como o nome do recurso. Por exemplo, se o GUID for eb7927c8-dd66-43e1-b0cf-c346a422063, o nome do recurso será _pid-eb7927c8-dd66-43e1-b0cf-c346a422063_.

1. Verifique o modelo para todos os erros.

1. Republique o modelo nos repositórios apropriados.

1. [Verificar o sucesso da GUID na implantação de modelo](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Exemplo de código de modelo do Resource Manager

Para habilitar recursos de acompanhamento para o modelo, você precisará adicionar o recurso adicional a seguir na seção de recursos. Não se esqueça de modificar o exemplo código abaixo com suas próprias entradas ao adicioná-lo ao arquivo de modelo principal.
O recurso precisa ser adicionado ao arquivo **mainTemplate.json** ou **azuredeploy.json**, e não em nenhum modelo aninhado ou vinculado.

```json
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
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

## <a name="use-the-resource-manager-apis"></a>Use as APIs do Gerenciador de recursos

Em alguns casos, talvez você prefira fazer chamadas diretamente contra as APIs REST do Resource Manager para implantar os serviços do Azure. [Azure dá suporte a vários SDKs](../index.yml?pivot=sdkstools) para habilitar essas chamadas. Você pode usar um dos SDKs, ou chamar as APIs REST diretamente para implantar recursos.

Se você estiver usando um modelo do Resource Manager, deverá marcar sua solução seguindo as instruções descritas anteriormente. Se você não estiver usando um modelo do Resource Manager e fazendo chamadas diretas à API, ainda poderá marcar sua implantação para associar o uso de recursos do Azure.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Marcar uma implantação com as APIs do Resource Manager

Para habilitar a atribuição de uso do cliente, ao criar suas chamadas à API, inclua um GUID no cabeçalho do agente do usuário na solicitação. Adicione o GUID para cada oferta ou SKU. Formate a cadeia de caracteres com o `pid-` prefixo e inclua o GUID gerado pelo parceiro. Aqui está um exemplo do formato GUID para inserção no agente do usuário:

![Formato GUID de exemplo](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!NOTE]
> O formato da cadeia de caracteres é importante. Se o `pid-` prefixo não estiver incluído, não será possível consultar os dados. Diferentes SDKs acompanham de forma diferente. Para implementar esse método, revise a abordagem de suporte e acompanhamento do seu SDK do Azure preferido.

#### <a name="example-the-python-sdk"></a>Exemplo: O SDK do Python

Para o Python, use o **config** atributo. Você só pode adicionar o atributo a um UserAgent. Aqui está um exemplo:

![Adicione o atributo para um agente do usuário](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!NOTE]
> Adicione o atributo para cada cliente. Não há nenhuma configuração estática global. Você pode marcar uma fábrica de clientes para ter certeza de que todos os clientes estão rastreando. Para obter mais informações, consulte este [exemplo de fábrica do cliente no GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="example-the-net-sdk"></a>Exemplo: o SDK do .NET

Para o .NET, certifique-se de definir o agente do usuário. A biblioteca [Microsoft. Azure. Management. Fluent](/dotnet/api/microsoft.azure.management.fluent) pode ser usada para definir o agente do usuário com o seguinte código (exemplo em C#):

```csharp

var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Marca uma implantação usando o Azure PowerShell

Se você implantar recursos por meio do Azure PowerShell, acrescente seu GUID usando o seguinte método:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Marca uma implantação usando a CLI do Azure

Quando você usa a CLI do Azure para anexar seu GUID, defina a variável de ambiente **AZURE_HTTP_USER_AGENT**. Você pode definir essa variável dentro do escopo de um script. Você também pode definir a variável globalmente para o escopo da shell:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

Para obter mais informações, confira [SDK do Azure para linguagem Go](/azure/developer/go/).

## <a name="use-terraform"></a>Usar o Terraform

O suporte para Terraform está disponível por meio do Provedor do Azure 1.21.0 versão: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Esse suporte se aplica a todos os parceiros que implantam a solução via Terraform e todos os recursos implantados e medidos pelo Provedor do Azure (versão 1.21.0 ou posterior).

O provedor do Azure para Terraform adicionou um novo campo opcional chamado [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id). Nele, você especifica o GUID de acompanhamento que você usa para sua solução. O valor desse campo também pode ser originado da variável de ambiente *ARM_PARTNER_ID*.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Os parceiros que desejam fazer a implantação por meio do Terraform acompanhado pela atribuição de uso do cliente precisam fazer o seguinte:

* Criar um GUID (que deve ser adicionado para cada oferta ou SKU)
* Atualizar o Provedor do Azure para definir o valor de *partner_id* para o GUID (NÃO coloque no GUID o prefixo "pid-". Basta defini-lo como o GUID real)

## <a name="verify-the-guid-deployment"></a>Verificar a implantação de GUID

Depois de modificar seu modelo e executar uma implantação de teste, use o seguinte script do PowerShell para recuperar os recursos implantados e marcados.

Você pode usar o script para verificar se o GUID foi adicionado com êxito ao seu modelo do Resource Manager. O script não se aplica a implantações da API do Resource Manager nem do Terraform.

Entre no Azure. Selecione a assinatura com a implantação que você deseja verificar antes de executar o script. Execute o script dentro do contexto de assinatura da implantação.

O **GUIDE** e o **grupo de recursos** nome da implantação são parâmetros obrigatórios.

Você pode obter [o script original](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) no GitHub.

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```
## <a name="report"></a>Relatório
Os relatórios para uso do Azure acompanhados por meio da atribuição de uso do cliente não estão disponíveis atualmente para parceiros ISV. A adição de relatórios ao programa do Marketplace comercial no Partner Center para cobrir a atribuição de uso do cliente é destinada ao segundo semestre de 2021.

## <a name="notify-your-customers"></a>Notificar os clientes

Os parceiros devem informar seus clientes sobre implantações que usam a atribuição de uso do cliente. A Microsoft relata o uso do Azure associado a essas implantações no parceiro. Os exemplos a seguir incluem conteúdo que você pode usar para notificar seus clientes sobre essas implantações. Nos exemplos, substitua \<PARTNER> pelo nome da sua empresa. Os parceiros devem certificar-se de que a notificação esteja alinhada com suas políticas de privacidade e coleta de dados, incluindo opções para os clientes serem excluídos do rastreamento.

### <a name="notification-for-resource-manager-template-deployments"></a>Notificação para implantações de modelos do Resource Manager

Quando você implanta esse modelo, a Microsoft é capaz de identificar a instalação do \<PARTNER> software com os recursos do Azure que são implantados. Microsoft é capaz de correlacionar os recursos do Azure que são usados para dar suporte ao software. A Microsoft coleta essas informações para fornecer as melhores experiências com seus produtos e operar seus negócios. Os dados são coletados e governados pelas políticas de privacidade da Microsoft, que podem ser encontradas em [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

### <a name="notification-for-sdk-or-api-deployments"></a>Notificação para implantações de SDK ou a API

Quando você implanta \<PARTNER> software, a Microsoft é capaz de identificar a instalação do \<PARTNER> software com os recursos do Azure que são implantados. Microsoft é capaz de correlacionar os recursos do Azure que são usados para dar suporte ao software. A Microsoft coleta essas informações para fornecer as melhores experiências com seus produtos e operar seus negócios. Os dados são coletados e governados pelas políticas de privacidade da Microsoft, que podem ser encontradas em [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

## <a name="get-support"></a>Obter suporte

Saiba mais sobre as opções de suporte no Marketplace comercial em [suporte para o programa do Marketplace comercial no Partner Center](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>Como enviar uma solicitação de consultoria técnica

1. Visite [Serviços Técnicos do Parceiro](https://aka.ms/TechnicalJourney).
1. Selecione Infraestrutura e gerenciamento de nuvem, e uma nova página será aberta para que você veja o percurso técnico.
1. Em Serviços de Implantação, clique no botão Enviar uma solicitação
1. Entre usando sua MSA (conta do MPN) ou seu AAD (conta do painel do parceiro); com base nas suas credenciais de entrada, um formulário de solicitação online será aberto:
    * Preencha/examine as informações de contato.
    * Os detalhes de consultoria podem ser preenchidos previamente ou selecionados nos menus suspensos.
    * Insira um título e a descrição do problema (forneça o máximo de detalhes possível).

1. Clique em Enviar

Veja as instruções passo a passo com capturas de tela em [Using Technical Presales and Deployment Services](https://aka.ms/TechConsultInstructions) (Uso dos serviços técnicos de pré-vendas e implantação).

### <a name="whats-next"></a>O que vem a seguir

Você será contatado por um consultor técnico de parceiros da Microsoft para agendar uma chamada e definir o escopo de suas necessidades.

## <a name="faq"></a>Perguntas frequentes

**Qual é o benefício de adicionar o GUIA ao modelo?**

A Microsoft fornece aos parceiros uma visão das implantações de clientes de suas soluções e insights sobre seu uso influenciado. Tanto a Microsoft quanto o parceiro podem usar essas informações para promover um envolvimento mais próximo entre as equipes de vendas. Tanto a Microsoft quanto o parceiro podem usar os dados para obter uma visão mais consistente do impacto de um parceiro individual no crescimento do Azure.

**Depois de um GUID é adicionado, ela pode ser alterada?**

Sim, um cliente ou parceiro de implementação pode personalizar o modelo e alterar ou remover o GUID. Sugerimos que os parceiros proativamente descrevem a função do recurso e do GUID a seus clientes e parceiros para evitar a remoção ou edições para o GUID. Alterar o GUID afeta somente implantações e recursos novos e não existentes.

**Posso acompanhar modelos implantados de um repositório não Microsoft como o GitHub?**

Sim, desde que o GUID esteja presente quando o modelo for implantado, o uso será rastreado. Os parceiros ainda precisam registrar seus GUIDs.

**O cliente que recebe a emissão de relatórios também?**

Os clientes podem acompanhar seu uso de recursos individuais ou grupos de recursos definido pelo cliente no portal do Azure. Os clientes não veem o uso dividido por GUID.

**Essa metodologia é semelhante ao DPOR (Parceiro Digital de Registro)?**

Esse novo método de conexão com a implantação e o uso de solução de um parceiro fornece um mecanismo para vincular a uma solução de parceiro ao uso do Azure. A DPOR destina-se a associar um parceiro de consultoria (Integrador de Sistemas) ou de gerenciamento (Provedor de Serviço Gerenciado) à assinatura do Azure de um cliente.

**Posso usar um VHD personalizado e particular para uma oferta de modelo de solução no Azure Marketplace?**

Não é possível. A imagem da máquina virtual deve vir do Azure Marketplace, consulte: [Guia de publicação para ofertas de máquina virtual no Azure Marketplace](marketplace-virtual-machines.md).

É possível criar uma oferta de VM no Marketplace usando seu VHD personalizado e marcá-la como particular para que ninguém possa vê-la. Em seguida, faça referência a essa VM em seu modelo de solução.

**Falha ao atualizar a propriedade *contentVersion* para o modelo principal?**

Esse é provavelmente um bug, em casos em que o modelo está sendo implantado usando um TemplateLink de outro modelo que espera um contentVersion mais antigo por algum motivo. A solução alternativa é usar a propriedade de metadados:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
