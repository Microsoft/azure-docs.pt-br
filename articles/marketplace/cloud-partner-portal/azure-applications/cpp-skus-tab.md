---
title: Configurar SKUs para uma oferta de aplicativo do Azure | Microsoft Docs
description: Como configurar as SKUs para um aplicativo gerenciado do Azure e um modelo de solução do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 5d6ec0197699f603c79f414e015cdebcde6b9f60
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744976"
---
# <a name="azure-application-skus-tab"></a>Guia de SKUs de aplicativo do Azure

Este artigo descreve como usar a guia SKUs a fim de criar SKUs para seu aplicativo do Azure. 

> [!IMPORTANT]
> As etapas para configurar uma SKU são diferentes para uma oferta de aplicativo gerenciado e uma oferta de modelo de solução. Essas diferenças estão documentadas neste artigo. 

## <a name="configure-azure-application-skus"></a>Configurar SKUs de aplicativo do Azure

### <a name="create-a-new-sku"></a>Criar nova SKU

Use estas etapas para criar uma nova SKU:

1. Selecione a guia **SKUs**.
2. Em SKUs, selecione **+ Nova SKU**.

    ![Prompt de Nova SKU](./media/azureapp-plus-sku.png)

3. Na janela pop-up da nova SKU, digite uma **ID de SKU**. Essa ID é limitada a 50 caracteres e deve consistir apenas de caracteres em minúsculas, alfanuméricos, traços ou sublinhados. A ID de SKU não pode terminar com um traço.
4. A ID de SKU fica visível para os clientes em URLs de produto, modelos do Resource Manager (se aplicável) e relatórios de cobrança. Não é possível modificar essa ID depois que a oferta é publicada.

### <a name="sku-details-for-a-solution-template"></a>Detalhes da SKU para um modelo de solução

Forneça as seguintes configurações de SKU:

- **Título** – um título para o SKU. Esse título é mostrado na galeria para este item.
- **Resumo** – uma breve descrição resumida do SKU. (O comprimento máximo é de 100 caracteres.)
- **Descrição** – uma descrição detalhada do SKU.
- **Tipo de SKU** – uma lista suspensa com estes valores: "Modelo de solução" e "Aplicativo gerenciado". Para este cenário, selecione **Modelo de solução**.
- **Disponibilidade de nuvem** – o local do SKU. O padrão é **Azure público**.
Público do Azure - essa máquina virtual poderá ser implantada nos clientes em todas as regiões públicas do Azure que têm a integração do Marketplace.
- **Nuvem do Azure Governamental** – esta máquina virtual será implantada na nuvem do Azure Governamental. Antes de publicar no [Azure Governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), a Microsoft recomenda que os fornecedores testem e validem se a solução funciona no ambiente conforme o esperado. Para preparação e teste, solicite [conta de avaliação](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).

  >[!NOTE] 
  >O Microsoft Azure Governamental é uma nuvem de comunidade do governo, com acesso controlado para clientes federais, estaduais, locais ou tribais dos EUA e parceiros qualificados para atender a essas entidades.

- **Trata-se de uma SKU privada?** – selecione Sim se o SKU está disponível apenas para um grupo seleto de clientes.

    ![Formulário de detalhes de SKU para o modelo de solução](./media/azureapp-sku-details-solutiontemplate.png)

### <a name="sku-details-for-managed-application"></a>Detalhes de SKU do aplicativo gerenciado

A próxima captura de tela mostra o formulário de detalhes de SKU para um aplicativo gerenciado.

   ![Formulário de detalhes de SKU do aplicativo gerenciado](./media/azureapp-sku-details-managedapplication.png)

Defina as seguintes configurações de SKU:

- **Título** – um título para o SKU. Esse título é mostrado na galeria para este item.
- **Resumo** – uma breve descrição resumida do SKU. (O comprimento máximo é de 100 caracteres.)
- **Descrição** – uma descrição detalhada do SKU.
- **Tipo de SKU** – uma lista suspensa com estes valores: "Modelo de solução" e "Aplicativo gerenciado". Para este cenário, selecione **Aplicativo Gerenciado**.
- **Disponibilidade de nuvem** – o local do SKU. O padrão é **Azure público**.
- **Público do Azure** - essa máquina virtual poderá ser implantada nos clientes em todas as regiões públicas do Azure que têm a integração do Marketplace.
- **Nuvem do Azure Governamental** – esta máquina virtual será implantada na nuvem do Azure Governamental. Antes de publicar no [Azure Governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), a Microsoft recomenda que os fornecedores testem e validem se a solução funciona no ambiente conforme o esperado. Para preparação e teste, solicite [conta de avaliação](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).

  >[!NOTE] 
  >O Microsoft Azure Governamental é uma nuvem de comunidade do governo, com acesso controlado para clientes federais, estaduais, locais ou tribais dos EUA e parceiros qualificados para atender a essas entidades.

- **Trata-se de uma SKU privada?** – selecione Sim se o SKU está disponível apenas para um grupo seleto de clientes.
- **Disponibilidade de país/região** – use **Selecionar regiões** para exibir a lista de países/regiões disponíveis. Verifique cada país/região e selecione **OK** para salvar suas escolhas. 

   ![Lista de disponibilidade de país e região](./media/azure-app-select-country-region.png)

- **Preço Antigo** – insira o preço da SKU, em USD por mês. Os preços são definidos em moeda local usando taxas de câmbio atuais após a configuração. Valide-os, já que essas configurações são suas. Para definir ou exibir o preço de cada país/região individualmente, exporte a planilha de preços e importe-a com preços personalizados.

  >[!NOTE]
  >Salve as alterações de preços para habilitar a exportação/importação de dados sobre preços.

- **Preços Simplificados da Moeda** – insira o preço da SKU, em USD por mês. Deve ser igual ao Preço Antigo. Para obter mais informações, consulte [Preços simplificados da moeda](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer).

### <a name="package-details-for-solution-template"></a>Detalhes do pacote do modelo de solução

Forneça os seguintes detalhes do pacote:

- **Versão** – a versão do pacote do qual você fará upload. As tags de versão devem ter o formato X.Y.Z, onde X, Y e Z são inteiros.
- **Arquivo de pacote (.zip)** – o pacote contém os arquivos a seguir, que estão salvos em um arquivo .zip.
  - MainTemplate.json – o arquivo do modelo de implantação que é usado para implantar a solução/aplicativo e criar os recursos definidos para a solução. Para obter mais informações, confira [Como criar arquivos de modelo de implantação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - createUIDefinition.json - Esse arquivo é usado pelo portal do Azure para gerar a interface do usuário para o provisionamento dessa solução/aplicação. Para obter mais informações, confira [Criar uma interface do usuário do portal do Azure para seu aplicativo gerenciado](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

  >[!IMPORTANT] 
  >Esse pacote deve conter outros modelos ou scripts aninhados que sejam necessários para provisionar o aplicativo. Os arquivos MainTemplate.json e createUIDefinition.json precisam estar na pasta raiz.

   ![Detalhes do pacote do modelo de solução](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

### <a name="package-details-for-managed-application"></a>Detalhes de pacote do aplicativo gerenciado

Forneça os seguintes detalhes do pacote:

- **Versão** – a versão do pacote do qual você fará upload. As tags de versão devem ter o formato X.Y.Z, onde X, Y e Z são inteiros.
- **Arquivo de pacote (.zip)** – o pacote contém os arquivos a seguir, que estão salvos em um arquivo .zip.
  - applianceMainTemplate.json – o arquivo do modelo de implantação que é usado para implantar a solução/aplicativo e criar os recursos definidos. Para saber mais, confira [Início Rápido: Crie e implante modelos do Azure Resource Manager usando o portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition.json – esse arquivo é usado pelo portal do Azure para gerar a interface do usuário para o provisionamento dessa solução/aplicativo. Para obter mais informações, confira [Criar uma interface do usuário do portal do Azure para seu aplicativo gerenciado](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate.json – o arquivo de modelo que contém somente o recurso Microsoft.Solution/appliances. Para obter mais informações, confira [Noções básicas de estrutura e sintaxe dos Modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Observe as seguintes propriedades principais desse recurso:
    - “kind” – o valor deve ser “Marketplace” no caso de aplicativos gerenciados pelo Marketplace.
    - “ManagedResourceGroupId” – o grupo de recursos na assinatura do cliente em que todos os recursos definidos no applianceMainTemplate.json serão implantados.
    - “PublisherPackageId” – a cadeia de caracteres que identifica exclusivamente o pacote. O valor deve ser criado da seguinte forma: é uma concatenação de [publisherId].[OfferId]-preview[SKUID].[PackageVersion].

  >[!IMPORTANT] 
  >Esse pacote deve conter outros modelos ou scripts aninhados que sejam necessários para provisionar o aplicativo. Estes arquivos devem estar na pasta raiz:  MainTemplate.json, applianceMainTemplate.json e applianceCreateUIDefinition.json.

- **ID do locatário** – ID do locatário do Azure Active Directory da sua organização.
- **Habilitar acesso JIT?** – selecione **Sim** a fim de habilitar o acesso de gerenciamento Just-In-Time para implantações de cliente usando esta oferta.

  >[!NOTE] 
  >Se você habilitar o JIT, deverá atualizar o arquivo CreateUiDefinition.json para dar suporte a acesso JIT.

   ![Detalhes de pacote do aplicativo gerenciado](./media/azureapp-sku-pkgdetails-managedapplication.png)

Para um aplicativo gerenciado, você deve definir as configurações de política e autorização.

#### <a name="authorization"></a>Autorização

Adicione o identificador do Azure Active Directory do usuário, grupo ou aplicativo ao qual você deseja conceder a permissão para o grupo de recursos gerenciado. A permissão concedida é indicada pela ID de definição de função. Pode ser um proprietário, colaborador ou qualquer função personalizada.

#### <a name="policy-settings"></a>Configurações de política

Adicione as políticas com as quais o aplicativo gerenciado está em conformidade. Saiba mais sobre políticas de recursos do Azure, confira [O que é o Azure Policy?](../../../governance/policy/overview.md)


   ![Configurações de autorização e política para um aplicativo gerenciado](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Para criar uma nova regra de autorização:**

1. Em **Autorização**, selecione **+ Nova Autorização**.
2. Em **ID da entidade**, digite o identificador do Azure Active Directory do usuário, grupo ou aplicativo ao qual você deseja conceder a permissão para o grupo de recursos gerenciado. A permissão concedida é indicada pela definição de função.
3. Em **Definição de Função**, selecione uma destas opções na lista suspensa:  Proprietário ou Colaborador. Para obter mais informações, consulte [Funções internas dos recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Várias autorizações podem ser adicionadas. No entanto, é recomendável criar um grupo de usuários do Active Directory e especificar sua ID em “PrincipalId”. Isso permitirá a adição de mais usuários ao grupo de usuários sem necessidade de atualizar a SKU.

**Para criar uma nova política:**

1. Em **Configurações de Política**, selecione **+ Nova Política**.
2. Em **Nome da Política**, insira um nome para a política. O nome pode ter no máximo 50 caracteres.
3. Em **Políticas**, selecione uma das opções na lista suspensa. Escolha a política que o provedor de dados deseja ter habilitada quando o aplicativo usar os dados. Para obter mais informações, confira os [Exemplos do Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Configurações de política para um aplicativo gerenciado](./media/azureapp-sku-policy-settings.png)

4. Em **SKU de política**, selecione Gratuito ou Standard como o tipo de SKU de política. A SKU Standard é necessária para as políticas de auditoria.

## <a name="next-steps"></a>Próximas etapas

[Guia Marketplace](./cpp-marketplace-tab.md)
