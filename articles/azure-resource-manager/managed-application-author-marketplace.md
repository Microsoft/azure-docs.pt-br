---
title: Aplicativos gerenciados do Azure no Marketplace | Microsoft Docs
description: "Descreve os aplicativos gerenciados do Azure que estão disponíveis por meio do Marketplace."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: 58ac7665abf7e75a43bb0b92bdf6f41005c3efe8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-managed-applications-in-the-marketplace"></a>Aplicativos gerenciados do Azure no Marketplace

 MSPs, ISVs e ISs (integradores de sistema) podem usar os aplicativos gerenciados do Azure para oferecer suas soluções para todos os clientes do Azure Marketplace. Essas soluções reduzem a sobrecarga de manutenção e de serviço para os clientes. Os editores podem vender infraestrutura e software por meio do Marketplace. Eles podem anexar serviços e suporte operacional para aplicativos gerenciados. Para saber mais, consulte [Visão geral do aplicativo gerenciado](managed-application-overview.md).

Este artigo explica como um MSP, ISV ou SI pode publicar um aplicativo no Marketplace e torná-lo amplamente disponível aos clientes.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Pré-requisitos para publicar um aplicativo gerenciado

Pré-requisitos para a listagem no Marketplace:

* Técnicos

    *  Para obter informações sobre a estrutura básica e a sintaxe de modelos do Azure Resource Manager, consulte [Modelos do Azure Resource Manager](resource-group-authoring-templates.md).
    *  Para exibir as soluções de modelo completas, consulte [Modelos de Início Rápido do Azure](https://azure.microsoft.com/en-us/documentation/templates/) ou [Repositório de modelos Quickstart](https://github.com/azure/azure-quickstart-templates).
    *  Para saber mais sobre como criar a interface para clientes que implantam seu aplicativo por meio do Marketplace, consulte [Criar um arquivo de definição de interface do usuário](managed-application-createuidefinition-overview.md).

* Não técnico (requisitos de negócios)

    *   Sua empresa, ou subsidiária, deve estar localizada em um país no qual as vendas têm suporte no Marketplace.
    *   O produto deve ser licenciado de forma que seja compatível com modelos de cobrança com suporte no Marketplace.
    *   Você é responsável por disponibilizar o suporte técnico a clientes de modo comercialmente razoável. O suporte pode ser gratuito, pago ou oferecido por meio de suporte da comunidade.
    *   Você é responsável pelo licenciamento do software e quaisquer dependências de software de terceiros.
    *   Você deve fornecer conteúdo que atenda aos critérios da oferta a serem listados no Marketplace e no Portal do Azure.
    *   Você deve concordar com os termos das Políticas de Participação do Azure Marketplace e com o Contrato do Publicador.
    *   Você deve concordar em cumprir os Termos de uso, a Política de privacidade da Microsoft e o Contrato do programa de certificação do Microsoft Azure.

## <a name="create-a-new-azure-application-offer"></a>Criar uma nova oferta de aplicativo do Azure

Após atender aos pré-requisitos, você estará pronto para criar sua oferta de aplicativo gerenciado. Vamos analisar rapidamente uma visão geral de uma oferta e um SKU.

### <a name="offer"></a>Oferta

A oferta para um aplicativo gerenciado corresponde a uma classe de oferta de produtos de um publicador. Se você tiver um novo tipo de solução/aplicativo que queira disponibilizar no Marketplace, configure-o como uma oferta nova. Uma oferta é uma coleção de SKUs. Cada oferta aparece como sua própria entidade no Marketplace.

### <a name="sku"></a>SKU

Uma SKU é a menor unidade de compra de uma oferta. Use um SKU dentro da mesma classe de produto (oferta) para diferenciar entre:

* Diferentes recursos com suporte.
* Se a oferta é gerenciada ou não.
* Modelos de cobrança com suporte.

Um SKU aparece sob a oferta pai no Marketplace. Ele aparece como sua própria entidade disponível para compra no portal do Azure.

### <a name="set-up-an-offer"></a>Configurar uma oferta

1. Entre no [Portal do Cloud Partner](https://cloudpartner.azure.com/).

2. No painel de navegação à esquerda, selecione **+ Nova oferta** > **Aplicativos do Azure**.

    ![Nova oferta](./media/managed-application-author-marketplace/newOffer.png)

3. Preencha os formulários à esquerda no modo de exibição do **Editor**. Os campos obrigatórios estão marcados com um asterisco vermelho (*).

    ![Configurações da oferta](./media/managed-application-author-marketplace/newOffer_OfferSettings.png)

    Quatro formulários principais são usados para criar um aplicativo gerenciado:

    a. Configurações da oferta

    b. SKUs

    c. Marketplace

    d. Suporte

Esses formulários são descritos mais detalhadamente nas seções a seguir.

## <a name="offer-settings-form"></a>Formulário de Configurações de Oferta
Use este formulário básico para especificar as configurações da oferta.

1. Preencha o formulário **Configuração da Oferta**. Os diferentes campos são:

    a. **ID da Oferta**: esse identificador exclusivo identifica a oferta em um perfil de publicador. Essa ID está visível em URLs de produto, modelos do Resource Manager e relatórios de cobrança. Ele só pode ser composto de caracteres alfanuméricos minúsculos ou traços (-). A ID não pode terminar com um traço. Ela tem um limite máximo de 50 caracteres. Após a ativação da oferta, esse campo é bloqueado.

    b. **ID do Publicador**: use essa lista suspensa para escolher o perfil de publicador no qual você deseja publicar essa oferta. Após a ativação da oferta, esse campo é bloqueado.

    c. **Nome**: este nome de exibição de sua oferta aparece no Marketplace e no portal. Ele pode ter um máximo de 50 caracteres. Inclua um nome de marca reconhecível para o seu produto. Não inclua o nome de sua empresa aqui, a menos que seja a maneira como ela é comercializada. Se você estiver comercializando essa oferta em seu próprio site, certifique-se de que o nome seja exatamente como aparece em seu site.

2. Selecione **Salvar** para salvar o progresso. 

## <a name="skus-form"></a>Formulário de SKUs
A próxima etapa é adicionar SKUs para a sua oferta.

1. Selecione **SKUs** > **Novo SKU**. 

    ![Selecionar novo SKU](./media/managed-application-author-marketplace/newOffer_skus.png)

2. Insira uma **ID de SKU**. Uma ID de SKU é um identificador exclusivo para o SKU dentro de uma oferta. Essa ID está visível em URLs de produto, modelos do Resource Manager e relatórios de cobrança. Ele só pode ser composto de caracteres alfanuméricos minúsculos ou traços (-). A ID não pode terminar com um traço e está limitada a 50 caracteres no máximo. Após a ativação da oferta, esse campo é bloqueado. Pode existir várias SKUs em uma oferta. Você precisa de um SKU para cada imagem que planeja publicar.

3. Preencha a seção **Detalhes do SKU** no seguinte formulário:

    ![Fornecer novo SKU](./media/managed-application-author-marketplace/newOffer_newsku.png)

    Preencha os campos a seguir:
    
    a. **Título**: insira um título para esse SKU. Esse título aparecerá na galeria para este item.

    b. **Resumo**: insira um breve resumo para esse SKU. Esse texto aparece abaixo do título.

    c. **Descrição**: insira uma descrição detalhada sobre a SKU.

    d. **Tipo de SKU**: os valores permitidos são **Aplicativo Gerenciado** e **Modelos de Solução**. Nesse caso, selecione **Aplicativo Gerenciado**.

4. Preencha a seção **Detalhes do Pacote** no seguinte formulário:

    ![Pacote](./media/managed-application-author-marketplace/newOffer_newsku_package.png)

    Preencha os campos a seguir:

    a. **Versão Atual**: insira uma versão para o pacote que você carregar. Ela deve estar no formato `{number}.{number}.{number}{number}`.

    b. **Selecionar um arquivo de pacote**: esse pacote contém os seguintes arquivos que estão compactados em um arquivo .zip:
    * **applianceMainTemplate.json**: o arquivo do modelo de implantação que é usado para implantar a solução/aplicativo. Para saber mais sobre como criar arquivos de modelo de implantação, confira [Criar seu primeiro modelo do Azure Resource Manager](resource-manager-create-first-template.md).
    * **appliancecreateUIDefinition.json**: esse arquivo é usado pelo portal do Azure para gerar a interface do usuário usada para provisionar essa solução/aplicativo. Para saber mais, consulte [Introdução a CreateUiDefinition](managed-application-createuidefinition-overview.md).
    * **mainTemplate.json**: esse arquivo de modelo que contém somente o recurso Microsoft.Solution/appliances. O arquivo mainTemplate inclui as seguintes propriedades:

        *  **tipo**: use **Marketplace** para aplicativos gerenciados no Marketplace.
        *  **ManagedResourceGroupId**: esse grupo de recursos na assinatura do cliente em que todos os recursos definidos no applianceMainTemplate.json são implantados.
        *  **PublisherPackageId**: essa cadeia de caracteres que identifica exclusivamente o pacote. Forneça o valor no formato de `{publisherId}.{OfferId}.{SKUID}.{PackageVersion}`.

Obtenha a **ID da oferta**  e a **ID do publicador** do portal de publicação, conforme mostra a imagem a seguir:

![ID da oferta](./media/managed-application-author-marketplace/UniqueString_pubid_offerid.png)
        
Obtenha a **ID do SKU**, conforme mostra a imagem a seguir:

![Id do SKU](./media/managed-application-author-marketplace/UniqueString_skuid.png)
        
Obtenha a **Versão** do pacote, conforme mostra a imagem a seguir:

![Versão do pacote](./media/managed-application-author-marketplace/UniqueString_packageversion.png)
    
  Com base nos exemplos anteriores, o valor de **PublisherPackageId** é `azureappliance-test.ravmanagedapptest.ravpreviewmanagedsku.1.0.0`.

  Exemplo de mainTemplate.json:

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "storageAccountNamePrefix": {
        "type": "string",
        "metadata": {
          "description": "Specify the name of the storage account"
        }
      },
      "storageAccountType": {
        "type": "string"
      }
    },
    "variables": {
      "managedResourceGroup": "[concat(resourceGroup().id,uniquestring(resourceGroup().id))]"
    },
    "resources": [{
      "type": "Microsoft.Solutions/appliances",
      "apiVersion": "2016-09-01-preview",
      "name": "[concat(parameters('storageAccountNamePrefix'), '-', 'managed')]",
      "location": "[resourceGroup().location]",
      "kind": "marketplace",
      "properties": {
        "managedResourceGroupId": "[variables('managedResourceGroup')]",
        "PublisherPackageId":"azureappliancetest.ravmanagedapptest.ravpreviewmanagedsku.1.0.0",
        "parameters": {
          "storageAccountName": {
            "value": "[parameters('storageAccountNamePrefix')]"
          },
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }],
    "outputs": {

    }
  }
  ```

Esse pacote deve conter quaisquer outros modelos ou scripts aninhados que sejam necessários para provisionar com êxito esse aplicativo. Os arquivos mainTemplate.json, applianceMainTemplate.json e applianceCreateUIDefinition.json devem estar presentes na pasta raiz.

* **Autorizações**: essa propriedade define quem obtém acesso e o nível de acesso aos recursos em assinaturas de clientes. O publicador pode usar isso para gerenciar o aplicativo em nome do cliente.
* **PrincipalId**: essa propriedade é o identificador do Azure AD (Azure Active Directory) de um usuário, grupo de usuários ou aplicativo que recebeu determinadas permissões nos recursos na assinatura de clientes. A Definição de Função descreve as permissões. 
* **Definição de Função**: essa propriedade é uma lista de todas as funções RBAC (Controle de acesso baseado em função) internas fornecidas pelo Azure AD. Você pode selecionar a função mais apropriada para gerenciar os recursos em nome do cliente.

É possível adicionar várias autorizações. Recomendamos que você crie um grupo de usuários do AD e especifica sua ID em **PrincipalId**. Dessa forma, você pode adicionar mais usuários ao grupo de usuários sem a necessidade de atualizar o SKU.

Para saber mais sobre RBAC, consulte [Introdução ao RBAC no Portal do Azure](../active-directory/role-based-access-control-what-is.md).

## <a name="marketplace-form"></a>Formulário do Marketplace

O formulário do Marketplace pede campos que aparecem no [Azure Marketplace](https://azuremarketplace.microsoft.com) e no [portal do Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>IDs de assinatura para versão prévia

Insira uma lista de IDs de assinatura do Azure que podem acessar a oferta após a publicação. Use essas assinaturas da lista branca para testar a oferta de versão prévia antes de ativá-la. Você pode compilar uma lista branca com até 100 assinaturas no portal de parceiros.

### <a name="suggested-categories"></a>Categorias sugeridas

Selecione até cinco categorias da lista às quais sua oferta pode ser associada. Essas categorias selecionadas serão usadas para mapear sua oferta para as categorias de produto disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com) e no [Portal do Azure](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Marketplace

O resumo de seu aplicativo gerenciado exibe os campos a seguir:

![Resumo do marketplace](./media/managed-application-author-marketplace/publishvm10.png)

A guia **Visão geral** de seu aplicativo gerenciado exibe os campos a seguir:

![Visão geral do Marketplace](./media/managed-application-author-marketplace/publishvm11.png)

A guia **Planos + Preços** de seu aplicativo gerenciado exibe os campos a seguir:

![Planos do marketplace](./media/managed-application-author-marketplace/publishvm15.png)

#### <a name="azure-portal"></a>Portal do Azure

O resumo de seu aplicativo gerenciado exibe os campos a seguir:

![Resumo do portal](./media/managed-application-author-marketplace/publishvm12.png)

A guia visão geral de seu aplicativo gerenciado exibe os campos a seguir:

![Visão geral do portal](./media/managed-application-author-marketplace/publishvm13.png)

#### <a name="logo-guidelines"></a>Diretrizes de logotipo

Siga estas diretrizes para qualquer logotipo carregado no Portal do Cloud Partner:

*   O design do Azure tem uma paleta de cores simples. Limite o número de cores primárias e secundárias em seu logotipo.
*   As cores do tema do portal são branca e preta. Não use essas cores como a cor de fundo de seu logotipo. Use uma cor que destaque seu logotipo no portal. É recomendável usar cores primárias simples. *Se você usar um plano de fundo transparente, certifique-se de que o logotipo e o texto não sejam branco, preto ou azul.*
*   Não use um fundo gradiente no logotipo.
*   Não coloque texto no logotipo, nem mesmo o nome ou marca da sua empresa. A aparência de seu logotipo deve ser simples e evitar gradientes.
*   Verifique se o logotipo não está esticado.

#### <a name="hero-logo"></a>Logotipo Hero

O logotipo hero é opcional. O publicador pode optar por não carregar um logotipo hero. Após o upload do logotipo hero, não é possível exclui-lo. Nessa ocasião, o parceiro deve seguir as diretrizes do Azure Marketplace para ícones hero.

Siga estas diretrizes para o ícone do logotipo hero:

*   O nome de exibição do publicador, o título do plano e o resumo longo da oferta são exibidas em branco. Portanto, não use uma cor clara de fundo do ícone hero. Não há permissão para telas de fundo pretas, brancas ou transparentes para ícones hero.
*   Após a listagem da oferta, o nome de exibição do publicador, o título do plano, o resumo longo da oferta e o botão **Criar** são inseridos programaticamente no logotipo hero. Consequentemente, não insira qualquer texto enquanto projeta o logotipo hero. Deixe o espaço vazio à direita, pois o texto é incluído nesse espaço por meio de programação. O espaço vazio para o texto deve ter 415 x 100 pixels à direita. É um deslocamento de 370 pixels a partir da esquerda.

    ![Exemplo de logotipo hero](./media/managed-application-author-marketplace/publishvm14.png)

## <a name="support-form"></a>Formulário de suporte

Preencha o formulário **Suporte** com contatos de suporte de sua empresa. Essas informações podem ser de contatos da engenharia e do atendimento ao cliente.

## <a name="publish-an-offer"></a>Publicar uma oferta

Depois de preencher todas as seções, selecione **Publicar** para iniciar o processo de disponibilização de sua oferta aos clientes.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados](managed-application-overview.md).
* Para obter informações sobre como consumir um aplicativo gerenciado do Marketplace, consulte [Consumir aplicativos gerenciados pelo Azure no Marketplace](managed-application-consume-marketplace.md).
* Para obter informações sobre como publicar um aplicativo gerenciado do Catálogo de Serviços, consulte [Criar e publicar um aplicativo gerenciado do Catálogo de Serviços](managed-application-publishing.md).
* Para obter informações sobre como consumir um aplicativo gerenciado do Catálogo de Serviços, consulte [Consumir um aplicativo gerenciado do Catálogo de Serviços](managed-application-consumption.md).
