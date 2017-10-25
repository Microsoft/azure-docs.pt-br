---
title: "Visão geral de aplicativos gerenciados do Azure | Microsoft Docs"
description: Descreve os conceitos de aplicativos gerenciados do Azure
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/19/2017
ms.author: gauravbh
ms.openlocfilehash: 96b07bb3b923a5120e3d43c3fa60b3b1308010b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-managed-applications-overview"></a>Visão geral de aplicativos gerenciados do Azure

Os fornecedores que usam Azure podem oferecer soluções para clientes no mundo todo. O Azure Marketplace é uma galeria composta por centenas de modelos complexos com vários recursos próprios e de terceiros. Em questão de minutos, os clientes podem implantar e começar a usar aplicativos PaaS (plataforma como serviço) e SaaS (software como serviço). 

Embora o Marketplace forneça uma ótima maneira para os clientes implantarem uma oferta rapidamente, o cliente ainda é responsável por manter e atualizar a solução. Os fornecedores não podem cobrar os clientes pelo uso de um aplicativo, além da cobrança de imagem de máquina virtual. Além disso, os fornecedores não podem impedir os clientes de modificar recursos essenciais de aplicativos. Os fornecedores também não podem bloquear o acesso à propriedade intelectual que compõe um aplicativo. Os aplicativos gerenciados do Azure fornecem soluções para esses problemas. 

Um aplicativo gerenciado é semelhante a um modelo de solução no Marketplace, com uma diferença importante. Em um aplicativo gerenciado, os recursos são provisionados para um grupo de recursos gerenciado pelo fornecedor. O grupo de recursos está presente na assinatura do cliente, mas uma identidade no locatário do fornecedor tem acesso ao grupo de recursos.

## <a name="advantages-of-managed-applications"></a>Vantagens dos aplicativos gerenciados

MSPs (Provedores de serviço gerenciado), ISVs e equipes de TI centrais corporativas podem usar aplicativos gerenciados para fornecer soluções por meio do Marketplace ou do Catálogo de Serviços. Embora os clientes implantem esses aplicativos gerenciados em suas assinaturas, eles não precisam fazer a manutenção, atualizar nem repará-las. Como os fornecedores gerenciam e dão suporte aos aplicativos, os clientes não precisam desenvolver um conhecimento de domínio específico ao aplicativo para gerenciar esses aplicativos. Os clientes podem adquirir automaticamente atualizações do aplicativo sem precisar se preocupar com solução de problemas e diagnóstico problemas com o aplicativo.

Para fornecedores e provedores, os aplicativos gerenciados criam um canal para vender infraestrutura e software por meio do Marketplace. Os aplicativos gerenciados também fornecem uma maneira de agregar serviços e suporte operacional aos clientes do Azure. Os fornecedores podem cobrar os clientes usando o sistema de cobrança do Azure. Eles podem usar modelos para gerenciar o ciclo de vida dos aplicativos implantados. Essas soluções são independentes e seladas para o cliente de modo que os fornecedores possam fornecer serviço de alta qualidade. Essa abordagem beneficia fornecedores de PaaS e SaaS. Ela também ajuda as equipes de plataforma central corporativa e SIs (integradores de sistema) que desejam empacotar e revender suas soluções.

## <a name="managed-application-types"></a>Tipos de aplicativos gerenciados
Os aplicativos gerenciados do Azure vêm em dois tipos: Catálogo de Serviços e Marketplace.
 
### <a name="service-catalog"></a>Catálogo de Serviços  

Com o Catálogo de Serviços, os clientes podem criar um catálogo de soluções aprovadas para o Azure a fim de serem usadas por pessoas na organização. Manter esse catálogo de soluções é útil para as equipes de TI centrais em empresas. Elas podem usar o catálogo para assegurar a conformidade com certos padrões organizacionais enquanto oferecerem soluções excelentes para suas organizações. Elas podem controlar, atualizar e realizar a manutenção desses aplicativos. Os funcionários podem usar o catálogo para descobrir facilmente um conjunto sofisticado de aplicativos recomendados e aprovados pelo departamento de TI. Os clientes veem os aplicativos gerenciados do Catálogo de Serviços que criaram. Eles também podem ver os aplicativos gerenciados que outras pessoas na organização compartilham com eles.
 
Para obter informações sobre como publicar um aplicativo gerenciado do Catálogo de Serviços, consulte [Criar e publicar um aplicativo gerenciado do Catálogo de Serviços](managed-application-publishing.md).
 
Para obter informações sobre como consumir um aplicativo gerenciado do Catálogo de Serviços, consulte [Consumir um aplicativo gerenciado do Catálogo de Serviços](managed-application-consumption.md).
 
### <a name="marketplace"></a>Marketplace

Aplicativos gerenciados estão disponíveis por meio do Marketplace no portal do Azure. Após o fornecedor publicar esses aplicativos, eles ficam disponíveis para todos de dentro e fora da organização. Com essa abordagem, os MSPs, ISVs e SIs (integradores de sistema) podem oferecer suas soluções a todos os clientes do Azure. Os clientes se beneficiam do uso dessas soluções complexas sem necessidade de investir na compreensão e na manutenção das soluções. 

Atualmente, os publicadores podem disponibilizar suas ofertas como um aplicativo gerenciado ou um modelo de solução não gerenciado. Os principais componentes da publicação de um aplicativo gerenciado incluem o arquivo de modelo e o arquivo de definição de interface do usuário. O arquivo de modelo descreve os recursos provisionados. O arquivo de definição de interface do usuário descreve como as entradas necessárias para provisionar esses recursos são exibidas no portal. Os arquivos necessários são empacotados em um arquivo .zip e carregados por meio do portal de publicação.
 
Para saber mais sobre como publicar um aplicativo gerenciado no Marketplace, consulte [Aplicativos gerenciados do Azure no Marketplace](managed-application-author-marketplace.md).

Para obter informações sobre como consumir um aplicativo gerenciado do Marketplace, consulte [Consumir aplicativos gerenciados pelo Azure no Marketplace](managed-application-consume-marketplace.md).

## <a name="key-concepts"></a>Principais conceitos

### <a name="managed-resource-group"></a>Grupo de recursos gerenciado
O grupo de recursos gerenciado é onde todos os recursos do Azure provisionados no modelo são criados. Por exemplo, se o aplicativo for usado para criar uma conta de armazenamento, esse grupo de recursos conterá o recurso de conta de armazenamento. Ele não conterá o recurso do aplicativo.

### <a name="application-package"></a>Pacote de aplicativos
O editor cria um pacote que contém o arquivo de modelo e o arquivo createUIDefinition. Especificamente, ele contém os seguintes arquivos:

- **mainTemplate.json**: esse arquivo de modelo define todos os recursos que são provisionados pelo aplicativo. Esse arquivo é um arquivo de modelo regular usado para a criação de recursos.

- **createUIDefinition.json**: esse arquivo descreve como a interface do usuário necessária para os parâmetros definidos no modelo é renderizada.

### <a name="authorization"></a>Autorização
O publicador precisa especificar as permissões exigidas pelo fornecedor para gerenciar os recursos em nome do cliente. Essa permissão se aplica ao grupo de recursos gerenciado. Defina os seguintes valores:

- **PrincipalID**: o identificador do Azure AD (Azure Active Directory) para o usuário, grupo ou aplicativo que é usado para conceder acesso ao grupo de recursos gerenciado. Esse identificador pertence ao locatário do editor.

- **RoleDefinitionID**: o identificador do Azure AD da função atribuída à ID da entidade anterior. Pode ser qualquer uma das funções internas de Controle de acesso baseado em função no locatário do publicador. Para saber mais, consulte [Funções internas para Controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como publicar aplicativos gerenciados para o Marketplace, consulte [Aplicativos Gerenciados do Azure no Marketplace](managed-application-author-marketplace.md).
* Para obter informações sobre como consumir um aplicativo gerenciado do Marketplace, consulte [Consumir aplicativos gerenciados pelo Azure no Marketplace](managed-application-consume-marketplace.md).
* Para obter informações sobre como publicar um aplicativo gerenciado do Catálogo de Serviços, consulte [Criar e publicar um aplicativo gerenciado do Catálogo de Serviços](managed-application-publishing.md).
* Para obter informações sobre como consumir um aplicativo gerenciado do Catálogo de Serviços, consulte [Consumir um aplicativo gerenciado do Catálogo de Serviços](managed-application-consumption.md).
* Para criar um arquivo de definição de interface do usuário, consulte [Introdução a CreateUiDefinition](managed-application-createuidefinition-overview.md).
