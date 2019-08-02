---
title: Parceiro do Azure e atribuição de uso do cliente | Azure Marketplace
description: Visão geral de como acompanhar o uso do cliente para soluções do Microsoft Azure Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.topic: article
ms.date: 11/17/2018
ms.author: pabutler
ms.openlocfilehash: fa0a88ce3f74c20ab36931a6a78f8265bdb9d8be
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876248"
---
# <a name="azure-partner-customer-usage-attribution"></a>Atribuição de uso do cliente do parceiro do Azure

Como parceiro de software do Azure, suas soluções exigem componentes do Azure ou precisam ser implantadas diretamente na infraestrutura do Azure. Os clientes que implantam uma solução de parceiro e provisionam seus próprios recursos do Azure podem achar difícil obter visibilidade do status da implantação e obter uma ótica do impacto no crescimento do Azure. Quando você adiciona um nível mais alto de visibilidade, você se alinha às equipes de vendas da Microsoft e obtém crédito para os programas de parceiros da Microsoft.

A Microsoft agora oferece um método para ajudar os parceiros a rastrear melhor o uso do Azure de implantações de clientes de seus softwares no Azure. O novo método usa o Azure Resource Manager para orquestrar a implantação dos serviços do Azure.

Como parceiro da Microsoft, você pode associar o uso do Azure a quaisquer recursos do Azure fornecidos em nome de um cliente. Você pode formar a associação por meio do Azure Marketplace, do repositório do Quickstart, dos repositórios privados do GitHub e do envolvimento individual do cliente. A atribuição de uso do cliente dá suporte a três opções de implantação:

- Modelos do Azure Resource Manager: Os parceiros podem usar modelos do Resource Manager para implantar os serviços do Azure para executar o software do parceiro. Os parceiros podem criar um modelo do Resource Manager para definir a infraestrutura e a configuração de sua solução do Azure. Um modelo do Resource Manager permite que você e seus clientes implantem sua solução em todo o seu ciclo de vida. Você pode ter certeza de que seus recursos são implantados em um estado consistente.
- APIs do Azure Resource Manager: Os parceiros podem chamar as APIs do Resource Manager diretamente para implantar um modelo do Resource Manager ou gerar as chamadas da API para provisionar diretamente os serviços do Azure.
- Terraform Os parceiros podem usar o Orchestrator de nuvem como o Terraform para implantar um modelo do Resource Manager ou implantar diretamente os serviços do Azure.

A atribuição de uso do cliente destina-se à nova implantação e não dá suporte à marcação de recursos existentes que já foram implantados.

A atribuição de uso do cliente é necessária na [aplicativo Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer): oferta de modelo de solução publicada no Azure Marketplace.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-resource-manager-templates"></a>Use modelos do Gerenciador de Recursos
Muitas soluções de parceiros são implantadas na assinatura de um cliente usando modelos do Resource Manager. Se você tiver um modelo do Resource Manager que está disponível no Azure Marketplace, no GitHub ou como um guia de início rápido, o processo para modificar o modelo para habilitar a atribuição de uso do cliente deve ser direto para frente.

Para saber mais sobre como criar e publicar Modelos de Solução, confira

* [Criar e implantar seu primeiro modelo do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
* [Oferta de aplicativo do Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer).
* Vídeo: [Compilar modelos de solução e aplicativos gerenciados para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


## <a name="add-a-guid-to-your-template"></a>Adicionar um GUID para o modelo

Para adicionar um identificador global exclusivo (GUID), você faz uma única modificação no arquivo de modelo principal:

1. [Crie um GUID](#create-guids) usando o método sugerido e [registre o GUID](#register-guids-and-offers).

1. Abra o modelo do Resource Manager.

1. Adicione um novo recurso no arquivo de modelo principal. O recurso precisa estar no arquivo **mainTemplate.json** ou **azuredeploy.json**, e não em nenhum modelo aninhado ou vinculado.

1. Digite o valor de GUID após o prefixo **pid-** (por exemplo, pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Verifique o modelo para todos os erros.

1. Republique o modelo nos repositórios apropriados.

1. [Verificar o sucesso da GUID na implantação de modelo](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Exemplo de código de modelo do Resource Manager

Para habilitar recursos de acompanhamento para o modelo, você precisará adicionar o recurso adicional a seguir na seção de recursos. Não se esqueça de modificar o exemplo código abaixo com suas próprias entradas ao adicioná-lo ao arquivo de modelo principal.
O recurso precisa ser adicionado ao arquivo **mainTemplate.json** ou **azuredeploy.json**, e não em nenhum modelo aninhado ou vinculado.

```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
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

Em alguns casos, talvez você prefira fazer chamadas diretamente contra as APIs REST do Resource Manager para implantar os serviços do Azure. [Azure dá suporte a vários SDKs](https://docs.microsoft.com/azure/#pivot=sdkstools) para habilitar essas chamadas. Você pode usar um dos SDKs, ou chamar as APIs REST diretamente para implantar recursos.

Se você estiver usando um modelo do Resource Manager, deverá marcar sua solução seguindo as instruções descritas anteriormente. Se você não estiver usando um modelo do Resource Manager e fazendo chamadas diretas à API, ainda poderá marcar sua implantação para associar o uso de recursos do Azure.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Marcar uma implantação com as APIs do Resource Manager

Para habilitar a atribuição de uso do cliente, quando você cria suas chamadas à API, inclua um GUID no cabeçalho do agente do usuário na solicitação. Adicione o GUID para cada oferta ou SKU. Formatar a cadeia de caracteres com o **pid -** de prefixo e incluir o GUID gerado pelo parceiro. Aqui está um exemplo do formato GUID para inserção no agente do usuário:

![Formato GUID de exemplo](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> O formato da cadeia de caracteres é importante. Se o prefixo **pid-** não estiver incluído, não será possível consultar os dados. Diferentes SDKs acompanham de forma diferente. Para implementar esse método, revise a abordagem de suporte e acompanhamento do seu SDK do Azure preferido.

#### <a name="example-the-python-sdk"></a>Exemplo: O SDK do Python

Para o Python, use o **config** atributo. Você só pode adicionar o atributo a um UserAgent. Veja um exemplo:

![Adicione o atributo para um agente do usuário](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Adicione o atributo para cada cliente. Não há nenhuma configuração estática global. Você pode marcar uma fábrica de clientes para ter certeza de que todos os clientes estão rastreando. Para obter mais informações, consulte este [exemplo de fábrica do cliente no GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Marca uma implantação usando o Azure PowerShell

Se você implantar recursos por meio do Azure PowerShell, acrescente seu GUID usando o seguinte método:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Marca uma implantação usando a CLI do Azure

Quando você usa a CLI do Azure para anexar seu GUID, defina a variável de ambiente **AZURE_HTTP_USER_AGENT**. Você pode definir essa variável dentro do escopo de um script. Você também pode definir a variável globalmente para o escopo da shell:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Para obter mais informações, consulte [SDK do Azure para linguagem go](https://docs.microsoft.com/go/azure/).

## <a name="use-terraform"></a>Usar Terraform

O suporte para Terraform está disponível por meio da versão 1.21.0 do provedor [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019)do Azure:.  Esse suporte se aplica a todos os parceiros que implantam sua solução via Terraform e todos os recursos implantados e medidos pelo provedor do Azure (versão 1.21.0 ou posterior).

O provedor do Azure para Terraform adicionou um novo campo opcional chamado [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) , que é onde você especifica o GUID de rastreamento que você usa para sua solução. O valor desse campo também pode ser originado da variável de ambiente *ARM_PARTNER_ID* .

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = “xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Os parceiros que desejam fazer sua implantação por meio do Terraform acompanhado pela atribuição de uso do cliente precisam fazer o seguinte:

* Criar um GUID (o GUID deve ser adicionado para cada oferta ou SKU)
* Atualize seu provedor do Azure para definir o valor de *partner_id* para o GUID (não corrija o GUID com "PID-", basta defini-lo como o GUID real)

## <a name="create-guids"></a>Criar GUIDs

Um GUID é um número de referência exclusivo que tem 32 dígitos hexadecimais. Para criar GUIDs para rastreamento, você deve usar um gerador de GUID. A equipe do Armazenamento do Azure criou um [formulário gerador de GUID](https://aka.ms/StoragePartners) que enviará a você por email um GUID no formato correto e que pode ser reutilizado entre os sistemas de controle diferentes.

> [!Note]
> É altamente recomendável que você use o [formulário gerador de GUID do armazenamento do Azure](https://aka.ms/StoragePartners) para criar seu GUID. Para mais informações, consulte nossas [FAQ](#faq).

Recomendamos que você crie um GUID exclusivo para cada oferta e canal de distribuição para cada produto. Você pode optar por usar um único GUID para os vários canais de distribuição do produto se não quiser que os relatórios sejam divididos.

Se você implantar um produto usando um modelo e ele estiver disponível no Azure Marketplace e no GitHub, você poderá criar e registrar 2 GUIDS distintos:

*   Produto A no Azure Marketplace
*   Produto A no GitHub

Emissão de relatórios é feita, o valor de parceiro (ID de parceiro da Microsoft) e os GUIDs.

Você também pode rastrear GUIDs em um nível mais granular, como o SKU, em que as SKUs são variantes de uma oferta.

## <a name="register-guids-and-offers"></a>Registrar os GUIDs e ofertas

Os GUIDs devem ser registrados para habilitar a atribuição de uso do cliente.

Todos os registros para GUIDs de modelo são feitos por meio do Portal do Parceiro do Azure Marketplace Cloud (CPP).

Depois de adicionar o GUID ao seu modelo ou no agente do usuário e registrar o GUID no CPP, todas as implantações são rastreadas.

1. Aplicar a [do Azure Marketplace](https://aka.ms/listonazuremarketplace) e obter acesso para o CPP.

   * Os parceiros são necessários para [tiver um perfil no CPP](https://docs.microsoft.com/azure/marketplace/become-publisher). Você é incentivado a listar a oferta no Azure Marketplace ou no AppSource.
   * Parceiros podem registrar vários GUIDs.
   * Os parceiros podem registrar um GUID para os modelos e ofertas de soluções que não são do Marketplace.

1. Entre no [Portal de Parceiros de Nuvem](https://cloudpartner.azure.com/).

1. No canto superior direito, selecione o ícone da sua conta e, em seguida, selecione **perfil do editor**.

   ![Selecione o perfil do Editor](media/marketplace-publishers-guide/guid-image-for-lu.png)

1. Na **página Perfil**, selecione **Adicionar GUID de rastreamento.**

   ![Selecione Adicionar GUID de rastreamento](media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

1. Na caixa **GUID de rastreamento**, insira seu GUID de rastreamento. Insira o GUID sem as **pid -** prefixo. Na caixa **Descrição personalizada**, insira o nome ou a descrição da sua oferta.

   ![Página de perfil](media/marketplace-publishers-guide/guid-dev-center-login.png)

   ![Insira o GUID e descrição da oferta](media/marketplace-publishers-guide/guid-dev-center-example.png)

1. Para registrar mais de um GUID, selecione **Adicionar GUID de Rastreamento** novamente. Caixas adicionais aparecerão na página.

   ![Selecione Adicionar GUID de rastreamento novamente](media/marketplace-publishers-guide/guid-dev-center-example-add.png)

   ![Insira outro GUID e descrição da oferta](media/marketplace-publishers-guide/guid-dev-center-example-description.png)

1. Clique em **Salvar**.

   ![Selecionar Salvar](media/marketplace-publishers-guide/guid-dev-center-save.png)

Depois de adicionar o GUID ao seu modelo ou no agente do usuário e registrar o GUID no CPP, todas as implantações são rastreadas.

## <a name="verify-the-guid-deployment"></a>Verificar a implantação de GUID

Depois de modificar seu modelo e executar uma implantação de teste, use o seguinte script do PowerShell para recuperar os recursos implantados e marcados.

Você pode usar o script para verificar se o GUID foi adicionado com êxito ao seu modelo do Resource Manager. O script não se aplica à API do Resource Manager nem a implantações de Terraform.

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

Você pode encontrar o relatório para atribuição de uso do cliente em seu parceiro do Partner Center analisar o painel. ([https://partner.microsoft.com/en-us/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)). Para ver o relatório, você precisa usar suas credenciais do Partner Center para entrar. Se você encontrar problemas com o relatório ou entrar, crie uma solicitação de suporte seguindo a instrução na seção obter suporte.

Escolha modelo controlado na lista suspensa do tipo de associação de parceiro para ver o relatório.

![Relatório de atribuição de uso do cliente](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Notificar os clientes

Os parceiros devem informar seus clientes sobre implantações que usam a atribuição de uso do cliente. A Microsoft relata o uso do Azure associado a essas implantações no parceiro. Os exemplos a seguir incluem conteúdo que você pode usar para notificar seus clientes sobre essas implantações. Nos exemplos, substitua \<parceiro > com o nome da sua empresa. Os parceiros devem certificar-se de que a notificação esteja alinhada com suas políticas de privacidade e coleta de dados, incluindo opções para os clientes serem excluídos do rastreamento.

### <a name="notification-for-resource-manager-template-deployments"></a>Notificação para implantações de modelos do Resource Manager

Quando você implanta esse modelo, a Microsoft consegue identificar a instalação do software \<PARTNER> com os recursos do Azure implantados. Microsoft é capaz de correlacionar os recursos do Azure que são usados para dar suporte ao software. A Microsoft coleta essas informações para fornecer as melhores experiências com seus produtos e operar seus negócios. Os dados são coletados e regidos pelas políticas de privacidade da Microsoft, que podem ser encontradas em https://www.microsoft.com/trustcenter.

### <a name="notification-for-sdk-or-api-deployments"></a>Notificação para implantações de SDK ou a API

Quando você implanta o software \<PARTNER>, a Microsoft pode identificar a instalação do software \< PARTNER> com os recursos do Azure implantados. Microsoft é capaz de correlacionar os recursos do Azure que são usados para dar suporte ao software. A Microsoft coleta essas informações para fornecer as melhores experiências com seus produtos e operar seus negócios. Os dados são coletados e regidos pelas políticas de privacidade da Microsoft, que podem ser encontradas em https://www.microsoft.com/trustcenter.

## <a name="get-support"></a>Obtenha suporte

Há dois canais de suporte, dependendo dos problemas que você está enfrentando.

Se você encontrar problemas no Partner Center, como ver o relatório de atribuição de uso do cliente ou entrar, crie uma solicitação de suporte com a equipe de suporte do Partner Center aqui:[https://partner.microsoft.com/en-US/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Se você precisar de assistência para integração do Marketplace e/ou atribuição de uso do cliente em geral, por exemplo, como configurar a atribuição de uso do cliente, siga as etapas abaixo:

1. Vá para a [página de suporte](https://go.microsoft.com/fwlink/?linkid=844975).

1. Em **Tipo de problema**, selecione **Onboarding do Marketplace**.

1. Escolha o **categoria** para seu problema:

   - Para problemas de uso de associação, selecione **outros**.
   - Para problemas de acesso com o Azure Marketplace CPP, selecione **problema de acesso**.

     ![Escolha a categoria do problema](media/marketplace-publishers-guide/lu-article-incident.png)

1. Selecione **Iniciar solicitação**.

1. Na próxima página, insira os valores necessários. Selecione **Continuar**.

1. Na próxima página, insira os valores necessários.

   > [!Important]
   > Na caixa **Título do incidente**, insira **Acompanhamento de uso do ISV**. Descreva seu problema em detalhes.

   ![Insira o controle de uso do ISV para o título do incidente](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Preencha o formulário e selecione **Enviar**.

Você também pode receber orientações técnicas de um consultor técnico de parceiros da Microsoft para cenários técnicos de pré-vendas, implantação e desenvolvimento de aplicativos para entender e incorporar a atribuição de uso do cliente.

### <a name="how-to-submit-a-technical-consultation-request"></a>Como enviar uma solicitação de consultoria técnica

1. Visite [https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney).
1. Selecione infraestrutura e gerenciamento de nuvem e uma nova página será aberta para que você veja a jornada técnica.
1. Em serviços de implantação, clique no botão Enviar uma solicitação
1. Entre usando sua MSA (conta do MPN) ou seu AAD (conta do painel do parceiro); com base nas suas credenciais de entrada, um formulário de solicitação online será aberto:
    * Conclua/revise as informações de contato.
    * Os detalhes de consultoria podem ser preenchidos previamente ou selecionados nos menus suspensos.
    * Insira um título e a descrição do problema (forneça o máximo de detalhes possível).
1. Clique em enviar

Veja as instruções passo a passo com capturas de tela [https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions)em.

### <a name="whats-next"></a>O que vem a seguir

Você será contatado por um consultor técnico de parceiros da Microsoft para configurar uma chamada para definir o escopo de suas necessidades.

## <a name="faq"></a>Perguntas Frequentes

**Qual é o benefício de adicionar o GUIA ao modelo?**

A Microsoft fornece aos parceiros uma visão das implantações de clientes de suas soluções e informações sobre o uso influenciado. Tanto a Microsoft quanto o parceiro podem usar essas informações para promover um envolvimento mais próximo entre as equipes de vendas. Tanto a Microsoft quanto o parceiro podem usar os dados para obter uma visão mais consistente do impacto de um parceiro individual no crescimento do Azure.

**Depois de um GUID é adicionado, ela pode ser alterada?**

Sim, um cliente ou parceiro de implementação pode personalizar o modelo e alterar ou remover o GUID. Sugerimos que os parceiros descrevam proativamente a função do recurso e do GUID para seus clientes e parceiros para evitar a remoção ou edição no GUID. Alterar o GUID afeta somente implantações e recursos novos e não existentes.

**Posso acompanhar modelos implantados de um repositório não Microsoft como o GitHub?**

Sim, desde que o GUID esteja presente quando o modelo for implantado, o uso será rastreado. Os parceiros precisam ter um perfil no CPP para registrar GUIDs usados para a implantação fora do Azure Marketplace.

**O cliente que recebe a emissão de relatórios também?**

Os clientes podem acompanhar seu uso de recursos individuais ou grupos de recursos definido pelo cliente no portal do Azure.

**Essa é uma metodologia semelhante ao DPOR (parceiro digital de registro)?**

Esse novo método de conexão com a implantação e o uso de solução de um parceiro fornece um mecanismo para vincular a uma solução de parceiro ao uso do Azure. A DPOR destina-se a associar um parceiro de consultoria (Integrador de Sistemas) ou de gerenciamento (Provedor de Serviço Gerenciado) à assinatura do Azure de um cliente.

**Qual é o benefício de usar o formulário Gerador de GUID do Armazenamento do Azure?**

O formulário Gerador de GUID do Armazenamento do Azure tem a garantia de gerar um GUID do formato necessário. Além disso, se você estiver usando qualquer um dos métodos de rastreamento de plano de dados do Armazenamento do Azure, poderá aproveitar o mesmo GUID para o rastreamento do plano de controle do Marketplace. Isso permite que você aproveite um único GUIDE unificado para atribuição de parceiros sem precisar manter GUIDES separados.

**Posso usar um VHD personalizado e privado para uma oferta de modelo de solução no Azure Marketplace?**

Não, você não pode. A imagem da máquina virtual deve vir do Azure Marketplace, consulte: [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines).

Você pode criar uma oferta de VM no Marketplace usando seu VHD personalizado e marcá-la como particular para que ninguém possa vê-la. Em seguida, faça referência a essa VM em seu modelo de solução.

**Falha ao atualizar a propriedade *contentVersion* para o modelo principal?**

Provavelmente um bug em alguns casos, quando o modelo está sendo implantado usando um TemplateLink de outro modelo que espera contentVersion mais antiga por algum motivo. A solução alternativa é usar a propriedade Metadata:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
