---
title: Planejar um aplicativo gerenciado do Azure para uma oferta de aplicativo do Azure
description: Saiba o que é necessário para criar um plano de aplicativo gerenciado para uma nova oferta de aplicativo do Azure usando o portal do Marketplace comercial no Microsoft Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 694f501efc565ed498c1c8d8e2e38326277e8605
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621409"
---
# <a name="plan-an-azure-managed-application-for-an-azure-application-offer"></a>Planejar um aplicativo gerenciado do Azure para uma oferta de aplicativo do Azure

Um plano de _aplicativo gerenciado_ do Azure é uma maneira de publicar uma oferta de aplicativo do Azure no Azure Marketplace. Se você ainda não fez isso, leia [planejar uma oferta de aplicativo Azure para o Marketplace comercial](plan-azure-application-offer.md).

Os aplicativos gerenciados são ofertas do Transact que são implantadas e cobradas por meio do Azure Marketplace. A opção de listagem que um usuário vê é obtê-la agora.

Use um Aplicativo Azure: plano de aplicativo gerenciado quando as seguintes condições forem necessárias:

- Você implantará uma solução baseada em assinatura para seu cliente usando uma VM (máquina virtual) ou uma solução baseada em IaaS (infraestrutura como serviço) inteira.
- Você ou seu cliente exige que a solução seja gerenciada por um parceiro. Por exemplo, um parceiro pode ser um integrador de sistemas ou um provedor de serviços gerenciado (MSP).

## <a name="managed-application-offer-requirements"></a>Requisitos da oferta de aplicativo gerenciado

| Requisitos | Detalhes |
| ------------ | ------------- |
| Uma assinatura do Azure | Os aplicativos gerenciados devem ser implantados na assinatura de um cliente, mas podem ser gerenciados por terceiros. |
| Cobrança e medição | Os recursos são fornecidos na assinatura do Azure de um cliente. As VMs que usam o modelo de pagamento pago conforme o uso são transacionadas com o cliente pela Microsoft e cobradas por meio da assinatura do Azure do cliente. <br><br> Para suas VMs traga sua própria licença, a Microsoft cobra os custos de infraestrutura incorridos na assinatura do cliente, mas você tem transações de licenciamento de software diretamente com o cliente. |
| VHD (disco rígido virtual) compatível com Azure | As VMs devem ser criadas em Windows ou Linux. Para obter mais informações, consulte:<br> • [Criar um ativo técnico de VM do Azure](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (para VHDs do Windows).<br> •  [Distribuições do Linux endossadas no Azure](../virtual-machines/linux/endorsed-distros.md) (para VHDs do Linux). |
| Atribuição de uso do cliente | Todas as novas ofertas de aplicativos do Azure devem incluir um GUID [atribuição de uso do cliente de parceiro do Azure](azure-partner-customer-usage-attribution.md). Para obter mais informações sobre a atribuição de uso do cliente e como habilitá-la, consulte [atribuição de uso do cliente do parceiro do Azure](azure-partner-customer-usage-attribution.md). |
| Pacote de implantação | Você precisará de um pacote de implantação que permitirá que os clientes implantem seu plano. Se você criar vários planos que exigem a mesma configuração técnica, poderá usar o mesmo pacote. Para obter detalhes, consulte a próxima seção: pacote de implantação. |
|||

> [!NOTE]
> Os aplicativos gerenciados devem ser implantados por meio do Azure Marketplace. Se a comunicação do cliente for uma preocupação, entre em contato com os clientes interessados depois de habilitar o compartilhamento de leads.

## <a name="deployment-package"></a>Pacote de implantação

O pacote de implantação contém todos os arquivos de modelo necessários para esse plano, bem como quaisquer recursos adicionais, empacotados como um arquivo. zip.

Todos os aplicativos do Azure devem incluir esses dois arquivos na pasta raiz de um arquivo. zip:

- Um arquivo de modelo do Resource Manager chamado [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). Este modelo define os recursos a serem implantados na assinatura do Azure do cliente. Para obter exemplos de modelos do Resource Manager, consulte a [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/) ou o repositório de [modelos do github: Azure Resource Manager início rápido](https://github.com/azure/azure-quickstart-templates) correspondente.
- Uma definição de interface do usuário para a experiência de criação do aplicativo do Azure denominada [createUiDefinition.json](../azure-resource-manager/managed-applications/create-uidefinition-overview.md). Na interface do usuário, você especifica os elementos que permitem aos consumidores fornecer valores de parâmetro.

Os tamanhos máximos de arquivo com suporte são:

- Até 1 GB no tamanho total de arquivo compactado. zip
- Até 1 GB para qualquer arquivo não compactado individual dentro do arquivo. zip

Todas as novas ofertas de aplicativos do Azure devem incluir um GUID [atribuição de uso do cliente de parceiro do Azure](azure-partner-customer-usage-attribution.md).

## <a name="azure-regions"></a>Regiões do Azure

Você pode publicar seu plano na região pública do Azure, na região do Azure governamental ou em ambos. Antes de publicar para o [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md), teste e valide seu plano no ambiente, pois certos pontos de extremidade podem ser diferentes. Para configurar e testar seu plano, solicite uma conta de avaliação do [Microsoft Azure Government](https://azure.microsoft.com/global-infrastructure/government/request/).

Você, como editor, é responsável por eventuais controles de conformidade, medidas de segurança e melhores práticas. O Azure Government usa datacenters isolados fisicamente e redes (localizadas apenas nos EUA).

Para obter uma lista de países e regiões com suporte no Marketplace comercial, confira [disponibilidade geográfica e suporte a moeda](marketplace-geo-availability-currencies.md).

Os serviços do Azure Government manipulam os dados que estão sujeitos a determinadas normas e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para oferecer reconhecimento das suas certificações para esses programas, você pode fornecer até 100 links que as descrevem. Eles podem ser links para sua listagem diretamente no programa ou links para descrições de sua conformidade com eles em seus próprios sites. Esses links são visíveis somente para clientes do Azure Government.

## <a name="choose-who-can-see-your-plan"></a>Escolha quem pode ver seu plano

Você pode configurar cada plano para ser visível para todos (público) ou para apenas um público específico (privado). Você pode criar até 100 planos e até 45 deles podem ser privados. Talvez você queira criar um plano privado para oferecer opções de preços diferentes ou configurações técnicas a clientes específicos.

Você concede acesso a um plano privado usando IDs de assinatura do Azure com a opção de incluir uma descrição de cada ID de assinatura atribuída. Você pode adicionar no máximo 10 IDs de assinatura manualmente ou até 10.000 IDs de assinatura usando um. Arquivo CSV. As IDs de assinatura do Azure são representadas como GUIDs e todas as letras precisam estar em minúsculas.

Não há suporte para planos privados com assinaturas do Azure estabelecidas por meio de um revendedor do CSP (programa de provedor de soluções na nuvem). Para obter mais informações, consulte [ofertas privadas no Microsoft Commercial Marketplace](private-offers.md).

> [!NOTE]
> Se você publicar um plano privado, poderá alterar sua visibilidade para o público posteriormente. No entanto, depois de publicar um plano público, você não pode alterar sua visibilidade para particular.

## <a name="define-pricing"></a>Definir preços

Você deve fornecer o preço por mês para cada plano. Esse preço é além de qualquer infraestrutura do Azure ou custos de software pago conforme o uso incorridos pelos recursos implantados por essa solução.

Além do preço por mês, você também pode definir preços para consumo de unidades não padrão usando [cobrança limitada](partner-center-portal/azure-app-metered-billing.md). Você pode definir o preço por mês como zero e cobrar exclusivamente usando a cobrança limitada, se desejar.

Os preços são definidos em USD (USD = Estados Unidos dólar) são convertidos na moeda local de todos os mercados selecionados usando as taxas de câmbio atuais quando salvos. Mas você pode optar por definir os preços do cliente para cada mercado.

## <a name="just-in-time-jit-access"></a>Acesso just in time (JIT)

O acesso JIT permite que você solicite acesso elevado a recursos de um aplicativo gerenciado para solução de problemas ou manutenção. Você sempre tem acesso somente leitura aos recursos, mas, para um período de tempo específico, você pode ter mais acesso. Para obter mais informações, consulte [Habilitar e solicitar o acesso just-in-time para aplicativos gerenciados do Azure](../azure-resource-manager/managed-applications/request-just-in-time-access.md).

> [!NOTE]
> Certifique-se de atualizar o arquivo de `createUiDefinition.json` para dar suporte a esse recurso.

## <a name="deployment-mode"></a>Modo de implantação

Você pode configurar um plano de aplicativo gerenciado para usar o modo de implantação **completo** ou **incremental** . No modo completo, uma reimplantação do aplicativo pelo cliente resultará na remoção de recursos no grupo de recursos gerenciado se os recursos não estiverem definidos no [mainTemplate.jsem](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). Em modo incremental, uma reimplantação do aplicativo deixa os recursos existentes inalterados. Para saber mais, confira [Azure Resource Manager modos de implantação](../azure-resource-manager/templates/deployment-modes.md).

## <a name="notification-endpoint-url"></a>URL do ponto de extremidade de notificação

Opcionalmente, você pode fornecer um ponto de extremidade de webhook HTTPS para receber notificações sobre todas as operações CRUD em instâncias de aplicativo gerenciado de um plano.

## <a name="customize-allowed-customer-actions-optional"></a>Personalizar ações de cliente permitidas (opcional)

Opcionalmente, você pode especificar quais ações os clientes podem executar nos recursos gerenciados, além das `*/read` ações que estão disponíveis por padrão.

Se você escolher essa opção, precisará fornecer as ações de controle ou as ações de dados permitidas, ou ambas. Para obter mais informações, confira [Compreender atribuições de negação de recursos do Azure](../role-based-access-control/deny-assignments.md). Para as ações disponíveis, confira [Operações do provedor de recursos do Azure Resource Manager](../role-based-access-control/resource-provider-operations.md). Por exemplo, para permitir que os consumidores reiniciem as máquinas virtuais, adicione `Microsoft.Compute/virtualMachines/restart/action` às ações permitidas.

## <a name="choose-who-can-manage-the-application"></a>Escolha quem pode gerenciar o aplicativo

Você deve indicar quem pode gerenciar um aplicativo gerenciado em cada uma das nuvens selecionadas: _Azure público_ e _nuvem do Azure governamental_. Colete as seguintes informações:

- **Azure Active Directory ID de locatário** – a ID de locatário do Azure AD (também conhecida como ID de diretório) que contém as identidades dos usuários, grupos ou aplicativos aos quais você deseja conceder permissões. Você pode encontrar sua ID de locatário do Azure AD na portal do Azure, em [Propriedades para Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).
- **Autorizações** – adicione a ID de objeto de Azure Active Directory de cada usuário, grupo ou aplicativo para o qual você deseja receber permissão para o grupo de recursos gerenciado. Identifique o usuário por sua ID de entidade de segurança, que pode ser encontrada na [folha de usuários do Azure Active Directory no portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Para cada ID de entidade de segurança, você associará uma das funções internas do Azure AD (proprietário ou colaborador). A função selecionada descreve as permissões que a entidade de segurança terá nos recursos na assinatura do cliente. Para obter mais informações, veja [Funções internas do Azure](../role-based-access-control/built-in-roles.md). Para obter mais informações sobre controle de acesso baseado em função (RBAC), consulte [Introdução ao RBAC no portal do Azure](../role-based-access-control/overview.md).

> [!NOTE]
> Embora você possa adicionar até 100 autorizações por região do Azure, geralmente é mais fácil criar um grupo de usuários Active Directory e especificar sua ID na "ID da entidade de segurança". Isso permite que você adicione mais usuários ao grupo de gerenciamento após a implantação do plano e reduza a necessidade de atualizar o plano apenas para adicionar mais autorizações.

## <a name="policy-settings"></a>Configurações de política

Você pode aplicar [políticas do Azure](../governance/policy/index.yml) ao seu aplicativo gerenciado para especificar os requisitos de conformidade para a solução implantada. Para definições de política e o formato dos valores de parâmetro, veja [Exemplos de Azure Policy](../governance/policy/samples/index.md).

Você pode configurar um máximo de cinco políticas e apenas uma instância de cada tipo de política. Alguns tipos de política exigem parâmetros adicionais.

| Tipo de política | Parâmetros de política necessários |
| ------------ | ------------- |
| Criptografia do banco de dados SQL do Azure | Não |
| Configurações de auditoria do Azure SQL Server | Sim |
| Azure Data Lake Store criptografia | Não |
| Configuração de diagnóstico de auditoria | Sim |
| Conformidade do local do recurso de auditoria | Não |
|||

Para cada tipo de política que você adicionar, você deve associar a SKU de política padrão ou gratuita. A SKU Standard é necessária para as políticas de auditoria. Os nomes de política são limitados a 50 caracteres.

## <a name="next-steps"></a>Próximas etapas

- [Como criar uma oferta de aplicativo do Azure no Marketplace comercial](create-new-azure-apps-offer.md)
