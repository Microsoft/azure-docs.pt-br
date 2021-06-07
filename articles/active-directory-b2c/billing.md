---
title: Modelo de cobrança para Azure Active Directory B2C
description: Saiba mais sobre o modelo de cobrança MAU (usuários ativos mensais) do Azure AD B2C, como vincular um locatário Azure AD B2C a uma assinatura do Azure e como selecionar os preços apropriados da camada Premium.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a54ed9dc6557d9b613485bf28e74af0c59fc9e5e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99225201"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Modelo de cobrança para Azure Active Directory B2C

Os preços de Azure Active Directory B2C (Azure AD B2C) baseiam-se em usuários ativos mensais (MAU), que é a contagem de usuários exclusivos com atividade de autenticação em um mês civil. Esse modelo de cobrança se aplica aos locatários Azure AD B2C e à [colaboração do usuário convidado (B2B) do Azure ad](../active-directory/external-identities/external-identities-pricing.md). A cobrança do MAU ajuda a reduzir os custos oferecendo uma camada gratuita e preços flexíveis e previsíveis. Neste artigo, saiba mais sobre a cobrança do MAU, vinculando seus locatários do Azure AD B2C a uma assinatura e alterando o tipo de preço.

> [!IMPORTANT]
> Este artigo não contém detalhes de preços. Para obter as informações mais recentes sobre cobrança e preços de uso, consulte [preços de Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/). Consulte também [Azure ad B2C disponibilidade da região e residência de dados](data-residency.md) para obter detalhes sobre onde o serviço de Azure ad B2C está disponível e onde os dados do usuário são armazenados.

## <a name="what-do-i-need-to-do"></a>O que preciso fazer?

Para aproveitar a cobrança do MAU, seu locatário de Azure AD B2C deve estar vinculado a uma assinatura do Azure. Talvez você também precise mudar seu locatário Azure AD B2C para outro tipo de preço se quiser usar Azure AD B2C recursos premium P2, como acesso condicional com base em risco.

|Se seu locatário for:  |Você precisa:  |
|---------|---------|
| Um locatário Azure AD B2C já cobrado de acordo com a MAU     | Não fazer nada. Quando os usuários se autenticarem no locatário do Azure AD B2C, você será cobrado automaticamente usando o modelo de cobrança baseado em MAU.        |
| Um locatário Azure AD B2C ainda não está vinculado a uma assinatura     |  [Vincule seu locatário do Azure ad B2C a uma assinatura](#link-an-azure-ad-b2c-tenant-to-a-subscription) para ativar a cobrança do mau.     |
| Um locatário Azure AD B2C que foi vinculado a uma assinatura antes de 1º de novembro de 2019    | [Alterne para a cobrança do mau (recomendado)](#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants)ou permaneça no modelo de cobrança por autenticação.     |
| Um locatário Azure AD B2C e você deseja usar recursos premium (como acesso condicional com base em risco)    | [Altere para um tipo de preço do Azure ad](#change-your-azure-ad-pricing-tier) que dê suporte aos recursos que você deseja usar.        |
|  |  |

## <a name="about-the-monthly-active-users-mau-billing-model"></a>Sobre o modelo de cobrança de usuários ativos mensais (MAU)

A cobrança do MAU entrará em vigor para locatários Azure AD B2C em **1º de novembro de 2019**. Todos os locatários Azure AD B2C que você criou e vinculou a uma assinatura ou após essa data foram cobrados por MAU. Se você tiver um locatário Azure AD B2C que não tenha sido vinculado a uma assinatura, você precisará fazer isso agora. Se você tiver um locatário Azure AD B2C existente que foi vinculado a uma assinatura antes de 1º de novembro de 2019, recomendamos que você atualize para o modelo de cobrança de usuários ativos mensais (MAU), ou você pode permanecer no modelo de cobrança por autenticação.
  
Seu locatário de Azure AD B2C também deve ser vinculado ao tipo de preço apropriado do Azure com base nos recursos que você deseja usar. Os recursos premium exigem Azure AD B2C [preço premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory-b2c/). Talvez seja necessário atualizar seu tipo de preço ao usar novos recursos. Por exemplo, acesso condicional, você precisará selecionar o tipo de preço Azure AD B2C Premium P2 para seu locatário.

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Vincular um locatário Azure AD B2C a uma assinatura

Os encargos de uso de Azure Active Directory B2C (Azure AD B2C) são cobrados em uma assinatura do Azure. Você precisa vincular explicitamente um locatário de Azure AD B2C a uma assinatura do Azure criando um *recurso* de Azure ad B2C na assinatura de destino do Azure. Vários recursos de Azure AD B2C podem ser criados em uma única assinatura do Azure, juntamente com outros recursos do Azure, como máquinas virtuais, contas de armazenamento e aplicativos lógicos. Você pode ver todos os recursos em uma assinatura acessando o locatário do Azure Active Directory (Azure AD) ao qual a assinatura está associada.

Uma assinatura vinculada a um locatário Azure AD B2C pode ser usada para a cobrança de Azure AD B2C uso ou outros recursos do Azure, incluindo recursos de Azure AD B2C adicionais. Ele não pode ser usado para adicionar outros serviços baseados em licença do Azure ou licenças do Office 365 no locatário Azure AD B2C.

### <a name="prerequisites"></a>Pré-requisitos

* [Assinatura do Azure](https://azure.microsoft.com/free/)
* [Azure ad B2C locatário](tutorial-create-tenant.md) que você deseja vincular a uma assinatura
  * Você deve ser um administrador de locatários
  * O locatário já não deve estar vinculado a uma assinatura

### <a name="create-the-link"></a>Criar o link

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione o **diretório +** filtro de assinatura no menu superior e selecione o diretório que contém a assinatura do Azure que você gostaria de usar (*não* o diretório que contém o locatário Azure ad B2C).
3. Selecione **criar um recurso**, insira `Active Directory B2C` no campo **Pesquisar no Marketplace** e, em seguida, selecione **Azure Active Directory B2C**.
4. Selecione **Criar**.
5. Selecione **Vincular um Locatário do Azure AD B2C existente à minha assinatura do Azure**.
6. Selecione um **locatário Azure ad B2C** na lista suspensa. Somente os locatários para os quais você é um administrador global e que ainda não estão vinculados a uma assinatura são mostrados. O campo **nome do recurso de Azure ad B2C** é populado com o nome de domínio do locatário de Azure ad B2C que você selecionar.
7. Selecione uma **assinatura** ativa do Azure da qual você é um administrador.
8. Em **grupo de recursos**, selecione **criar novo** e especifique o **local do grupo de recursos**. As configurações do grupo de recursos aqui não têm impacto sobre o local Azure AD B2C locatário, o desempenho ou o status de cobrança.
9. Selecione **Criar**.

    ![A página de criação de recursos de Azure AD B2C no portal do Azure](./media/billing/portal-01-create-b2c-resource-page.png)

Depois de concluir essas etapas para um locatário Azure AD B2C, sua assinatura do Azure será cobrada com base nos detalhes do Azure Direct ou Contrato Enterprise, se aplicável.

## <a name="change-your-azure-ad-pricing-tier"></a>Alterar o tipo de preço do Azure AD

Um locatário deve ser vinculado ao tipo de preço apropriado do Azure com base nos recursos que você deseja usar com seu locatário Azure AD B2C. Os recursos premium exigem Azure AD B2C Premium P1 ou P2, conforme descrito no [Azure Active Directory B2C preços](https://azure.microsoft.com/pricing/details/active-directory-b2c/). Em alguns casos, você precisará atualizar seu tipo de preço ao usar novos recursos. Por exemplo, se você quiser usar a proteção de identidade, o acesso condicional com base em risco e quaisquer recursos futuros de P2 Premium com Azure AD B2C, você precisará selecionar o tipo de preço Azure AD B2C Premium P2 para seu locatário.

Para alterar o tipo de preço, siga estas etapas.

1. Entre no portal do Azure.

2. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém a assinatura do Azure à qual seu locatário do Azure B2C está vinculado (*não* selecione o próprio locatário do Azure ad B2C).

3. Na caixa de pesquisa na parte superior do portal, insira o nome do seu locatário de Azure AD B2C. Em seguida, selecione o locatário nos resultados da pesquisa em **recursos**.

4. Na página **visão geral** do recurso, em **tipo de preço**, selecione **alterar**.

   ![Alterar tipo de preço](media/billing/change-pricing-tier.png)
 
5. Selecione o tipo de preço que inclui os recursos que você deseja habilitar.

   ![Selecione o tipo de preço](media/billing/select-tier.png)

## <a name="switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants"></a>Alternar para a cobrança do MAU (pré-novembro de 2019 Azure AD B2C locatários)

Se você vinculou seu locatário de Azure AD B2C a uma assinatura antes de **1º de novembro de 2019**, o modelo de cobrança por autenticação anterior está sendo usado. Recomendamos que você atualize para o modelo de cobrança MAU (usuários ativos mensais). As opções de cobrança são configuradas no recurso de Azure AD B2C.

A mudança para a cobrança de usuários ativos mensais (MAU) é **irreversível**. Depois de converter um recurso de Azure AD B2C no modelo de cobrança baseado em MAU, não é possível reverter esse recurso para o modelo de cobrança por autenticação.

Veja como fazer a mudança para a cobrança de MAU para um recurso de Azure AD B2C existente:

1. Entre no [portal do Azure](https://portal.azure.com) como o proprietário da assinatura com acesso administrativo ao recurso Azure ad B2C.

2. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o Azure ad B2C diretório que você deseja atualizar para a cobrança do mau.<br/>

    ![Filtro de diretório e assinatura no portal do Azure](./media/billing/portal-mau-01-select-b2c-directory.png)

3. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.

4. Na página **visão geral** do locatário do Azure ad B2C, selecione o link em **nome do recurso**. Você é direcionado para o recurso de Azure AD B2C em seu locatário do Azure AD.<br/>

    ![Azure AD B2C link de recurso realçado em portal do Azure](./media/billing/portal-mau-02-b2c-resource-link.png)

5. Na página **visão geral** do recurso Azure ad B2C, em **unidades faturáveis**, selecione o link **por autenticação (alterar para mau)** .<br/>

    ![Alterar para o link MAU realçado em portal do Azure](./media/billing/portal-mau-03-change-to-mau-link.png)

6. Selecione **confirmar** para concluir a atualização para a cobrança do mau.<br/>

    ![Caixa de diálogo de confirmação de cobrança baseada em MAU no portal do Azure](./media/billing/portal-mau-04-confirm-change-to-mau.png)


### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>O que esperar ao fazer a transição para a cobrança do MAU de cobrança por autenticação

A medição baseada em MAU é habilitada assim que você, o proprietário da assinatura/recurso, confirma a alteração. Sua fatura mensal refletirá as unidades de autenticação cobradas até a alteração e as novas unidades de MAU começando com a alteração.

Os usuários não são contados duas vezes durante o mês de transição. Usuários ativos exclusivos que se autenticam antes da alteração são cobrados por uma taxa por autenticação em um mês civil. Esses mesmos usuários não estão incluídos no cálculo de MAU para o restante do ciclo de cobrança da assinatura. Por exemplo:

* O locatário contoso B2C tem 1.000 usuários. 250 os usuários estão ativos em um determinado mês. O administrador da assinatura muda de por autenticação para usuários ativos mensais (MAU) no décimo do mês.
* A cobrança por 1º-10 é cobrada usando o modelo por autenticação.
  * Se 100 usuários entrarem durante esse período (1º de 10º), esses usuários serão marcados como *pagos pelo mês*.
* A cobrança do 10º (o tempo de transição efetivo) é cobrada na taxa de MAU.
  * Se outros usuários 150 entrarem durante esse período (10 a 30), somente os 150 adicionais serão cobrados.
  * A atividade contínua dos primeiros 100 usuários não afeta a cobrança pelo restante do mês do calendário.

Durante o período de cobrança da transição, o proprietário da assinatura provavelmente verá entradas para os dois métodos (por autenticação e por MAU) exibidos em seu demonstrativo de cobrança de assinatura do Azure:

* Uma entrada para o uso até a data/hora da alteração que reflete por autenticação.
* Uma entrada para o uso após a alteração que reflete os usuários ativos mensais (MAU).

Para obter as informações mais recentes sobre cobrança de uso e preços para Azure AD B2C, consulte [preços de Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Gerenciar seus recursos de locatário do Azure AD B2C

Depois de criar o recurso de Azure AD B2C em uma assinatura do Azure, você deverá ver um novo recurso do tipo "locatário do B2C" que aparece com outros recursos do Azure.

Você pode usar esse recurso para:

* Navegue até a assinatura para examinar as informações de cobrança
* Obter a ID do locatário do Azure AD B2C locatário no formato GUID
* Ir até seu locatário do Azure AD B2C
* Enviar uma solicitação de suporte
* Mover seu Azure AD B2C recurso de locatário para outra assinatura do Azure ou grupo de recursos

### <a name="regional-restrictions"></a>Restrições regionais

Se você estabeleceu restrições regionais para a criação de recursos do Azure em sua assinatura, essa restrição pode impedi-lo de criar o recurso de Azure AD B2C.

Para atenuar esse problema, relaxe suas restrições regionais.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Assinaturas do CSP (provedores de soluções na nuvem) do Azure

Há suporte para as assinaturas do CSP (Provedores de Soluções de Nuvem) do Azure no Azure AD B2C. A funcionalidade está disponível por meio de APIs ou pelo portal do Azure para o Azure AD B2C e todos os recursos do Azure. Os administradores de assinatura do CSP podem vincular, mover e excluir relações com Azure AD B2C como feito com outros recursos do Azure.

O gerenciamento do Azure AD B2C usando o controle de acesso baseado em função não é afetado pela associação entre o locatário do Azure AD B2C e uma assinatura do Azure CSP. O controle de acesso baseado em função é obtido usando funções baseadas em locatário, não funções baseadas em assinatura.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Alterar a assinatura de cobrança de locatário Azure AD B2C

### <a name="move-using-azure-resource-manager"></a>Mover usando Azure Resource Manager

Azure AD B2C locatários podem ser movidos para outra assinatura usando Azure Resource Manager se as assinaturas de origem e de destino existirem dentro do mesmo locatário Azure Active Directory.

Para saber como mover os recursos do Azure como seu locatário de Azure AD B2C para outra assinatura, consulte [mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Antes de começar a mudança, leia todo o artigo para entender totalmente as limitações e os requisitos para tal movimentação. Além de instruções para mover recursos, ele inclui informações críticas, como uma lista de verificação de pré-movimentação e como validar a operação de movimentação.

### <a name="move-by-unlinking-and-relinking"></a>Mover desvinculando e revinculando

Se as assinaturas de origem e de destino estiverem associadas a locatários Azure Active Directory diferentes, você não poderá realizar a movimentação por meio de Azure Resource Manager, conforme explicado acima. No entanto, você ainda pode obter o mesmo resultado desvinculando o locatário Azure AD B2C da assinatura de origem e revinculando-o à assinatura de destino. Esse método é seguro porque o único objeto que você exclui é o *link de cobrança*, não o próprio locatário Azure ad B2C. Nenhum dos usuários, aplicativos, fluxos de usuário, etc. será afetado.

1. No próprio diretório Azure AD B2C, [convide um usuário convidado](user-overview.md#guest-user) do locatário do Azure AD de destino (aquele ao qual a assinatura do Azure de destino está vinculada) e verifique se esse usuário tem a função de **administrador global** no Azure ad B2C.
1. Navegue até o *recurso do Azure* que representa Azure ad B2C na sua assinatura de origem do Azure, conforme explicado na seção [gerenciar seus recursos de locatário do Azure ad B2C](#manage-your-azure-ad-b2c-tenant-resources) acima. Não mude para o locatário de Azure AD B2C real.
1. Selecione o botão **excluir** na página **visão geral** . Isso *não* exclui os usuários ou aplicativos de Azure ad B2C do locatário relacionados. Ele simplesmente remove o link de cobrança da assinatura de origem.
1. Entre no portal do Azure com a conta de usuário que foi adicionada como administrador no Azure AD B2C na etapa 1. Em seguida, navegue até a assinatura de destino do Azure, que está vinculada ao locatário de Azure Active Directory de destino. 
1. Restabeleça o link de cobrança na assinatura de destino seguindo o procedimento [criar o link](#create-the-link) acima.
1. Seu recurso de Azure AD B2C agora foi movido para a assinatura de destino do Azure (vinculada ao Azure Active Directory de destino) e será cobrado por meio desta assinatura de agora em diante.

## <a name="next-steps"></a>Próximas etapas

Para obter as informações de preços mais recentes, consulte [preços de Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).