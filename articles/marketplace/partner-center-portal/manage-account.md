---
title: Como gerenciar uma conta do Marketplace comercial no Partner Center
description: Saiba como gerenciar uma conta do Marketplace comercial no Partner Center.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: e0c9f1fcf2d9d7e806645f1bdf9e8a6c74245a60
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619330"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>Como gerenciar sua conta do Marketplace comercial no Partner Center 

Depois que você tiver [criou uma conta no Partner Center](./create-account.md), você pode gerenciar sua conta e ofertas usando o [painel Marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

Neste artigo, vamos nos aprofundar em como gerenciar sua conta no Partner Center, incluindo como: 

- [Acessar as configurações de conta do Partner Center](#access-your-account-settings)
- [Localizar sua ID do Editor, ID do vendedor, ID de usuário e locatários do Azure AD](#account-details)
- [Atualizar informações de contato](#contact-info)
- [Gerenciar detalhes financeiros (conta de pagamento, perfil de imposto, status de espera de pagamento)](#financial-details)
- [Configurar GUIDs de acompanhamento para monitorar o uso do cliente](#tracking-guids)
- [Usuários do Gerenciador](#manage-users)
- [Grupos do Gerenciador](#manage-groups)
- [Aplicativos do Gerenciador do Azure AD](#manage-azure-ad-applications)
- [Definir permissões e funções de usuário](#define-user-roles-and-permissions)
- [Gerenciar Locatários do Azure AD (contas corporativas)](#manage-tenants)
- [Contratos Manager Partner Center](#agreements)


## <a name="access-your-account-settings"></a>Acessar as configurações de conta

Se você ainda não fez isso, você (ou o administrador da organização) deve acessar o [configurações de conta](https://partner.microsoft.com/dashboard/account/management) para sua conta no Partner Center para:
- verificar o status de verificação de conta da sua empresa
- Confirme sua ID do vendedor, ID do MPN, ID do publicador e informações de contato, incluindo o contato de vendedor e de aprovador da empresa
- Configurar detalhes financeiros de sua empresa, incluindo isenções de imposto, se apropriado
- criar contas de usuário para qualquer pessoa que usará sua conta comercial no Partner Center

### <a name="open-developer-settings"></a>Abra as configurações do desenvolvedor

Configurações de conta está localizado no canto superior direito de seu [painel Marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace) no Partner Center. Selecione o ícone de engrenagem (perto do canto superior direito do painel) e, em seguida, selecione **configurações do desenvolvedor**. 

![Menu de configurações de conta no Partner Center](./media/dashboard-developer-settings.png)

Dentro de **configurações de conta**, você poderá exibir seu:
- **Detalhes da conta**: Tipo de conta e o status da conta
- **IDs de publicador**: ID do vendedor, ID de usuário, ID do publicador, locatários do Azure AD, etc.
- **Informações de contato**: O nome de exibição do Editor, nome de contato do vendedor, email, telefone e endereço
- **Detalhes financeiros**: Conta de pagamento, o perfil de imposto e o status de espera de pagamento
- **Dispositivos**: Os dispositivos de testes associados a sua conta
- **GUIDs de acompanhamento**: Qualquer controle associado de GUIDs com sua conta

### <a name="account-details"></a>Detalhes da conta

Na seção de detalhes da conta, você pode ver informações básicas, como seu **tipo de conta** (empresa ou indivíduo) e o **status de verificação** da sua conta. Durante o processo de verificação de conta, essas configurações exibirá cada etapa necessária, incluindo verificação de email, verificação de emprego e verificação de negócios. Você também pode atualizar seu email aqui e reenviar a verificação, se necessário. 

### <a name="publisher-ids"></a>IDs de publicador

Na seção de IDs de publicador, você pode ver sua **ID do vendedor**, **ID do MPN**, e **ID do publicador**. Esses valores são atribuídas pela Microsoft para identificar exclusivamente a sua conta de desenvolvedor e não podem ser editados.

### <a name="contact-info"></a>Informações de contato

Na seção de informações de contato, você pode ver sua **nome de exibição do publicador**, **informações de contato do vendedor** (o nome de contato, email, número de telefone e endereço para o vendedor da empresa) e o **empresa aprovador** (o nome, email e número de telefone da pessoa com autoridade para aprovar as decisões da empresa). 

### <a name="financial-details"></a>Detalhes financeiros

Na seção detalhes financeiros, você pode fornecer ou atualizar suas informações financeiras, caso você publique aplicativos pagos, suplementos ou serviços. 

Se você planeja listar ofertas gratuitas, você não precisa configurar uma conta de pagamento ou preencher todos os formulários de imposto. Se você mudar de ideia posteriormente e decide que deseja vender por meio da Microsoft, você pode configurar sua conta de pagamento e preencher os formulários de imposto no momento. 

#### <a name="payout-account"></a>Conta de pagamento

Uma conta de pagamento é a conta bancária para o qual continua é enviadas de suas vendas. Essa conta bancária deve estar no mesmo país em que você registrou sua conta no Partner Center.

Para configurar sua conta de pagamento, você precisará **associar sua Account Microsoft**:
1. No **configurações de conta**, sob o **detalhes financeiros** seção, selecione **associar sua Account Microsoft**. 
2. Quando solicitado, entre com sua conta Microsoft (MSA). Essa conta já não pode ser associada a outra conta no Partner Center. 
3. Para concluir a configuração da sua conta de pagamento, log completamente fora do Partner Center, em seguida, faça logon novamente com sua Account da Microsoft (em vez de sua conta corporativa). 

Agora que sua Account da Microsoft está associado, para adicionar uma conta de pagamento, você precisará:
- **Escolha um método de pagamento**: Conta bancária ou do PayPal
- **Adicionar informações de pagamento**: Isso pode incluir o tipo de conta (corrente ou poupança), inserir o nome da conta de proprietário, o número de conta e roteamento de número, o endereço de cobrança, o número de telefone ou endereço de email do PayPal. \* Para obter mais informações sobre como usar o PayPal como seu método de pagamento da conta e descobrir se ele tem suporte em sua região de colocação no mercado, consulte [PayPal informações](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> A alteração da conta de pagamento pode atrasar os pagamentos em até um ciclo de pagamento. Esse atraso ocorre porque precisamos confirmar a alteração da conta, assim como podemos fazer ao configurar primeiro a conta de pagamento. Você ainda receberá o valor total após a verificação de sua conta; os pagamentos atrasados do ciclo de pagamento atual serão adicionados ao próximo.  

#### <a name="tax-profile"></a>Perfil fiscal

Examinar seu status atual de perfil de imposto, confirmando correto **tipo de entidade** e **informações de certificado de imposto** é exibida. Selecione **editar** atualizar ou conclua qualquer necessário formulários.

Para estabelecer seu status de imposto, você deve especificar seu país de residência e cidadania e concluir os formulários de imposto apropriado associados com seu país/região.

Independentemente do seu país de residência ou cidadania, você deve preencher formulários de imposto dos Estados Unidos para vender qualquer oferta por meio da Microsoft. Os parceiros que atendem a certos requisitos de residência dos Estados Unidos devem preencher um formulário w-9 do IRS. Outros parceiros fora dos Estados Unidos devem preencher um formulário w-8 do IRS. Você pode preencher esses formulários online enquanto você conclui seu perfil de imposto.

Um número de identificação de contribuinte individuais dos Estados Unidos (ou ITIN) não é necessário para receber pagamentos da Microsoft ou de declaração de imposto tratados benefícios.

Você pode concluir e enviar os formulários de imposto eletronicamente no Partner Center; Na maioria dos casos, você não precisa de impressão e todos os formulários de email.

Regiões e países diferentes têm requisitos de outro imposto. O valor exato que você deve pagar em impostos depende de países e regiões em que você vende suas ofertas. Microsoft remits vendas e imposto sobre o uso em seu nome em alguns países. Esses países serão identificados no processo de listagem da sua oferta. Em outros países, dependendo de onde você está registrado, você talvez precise faremos a remessa de vendas e imposto sobre o uso de suas vendas diretamente à autoridade de imposto local. Além disso, o continua vendas que receber talvez tributável como renda. Recomendamos que você entre em contato com a autoridade relevante para seu país ou região que melhor ajudará a determinar as informações de imposto correto para suas transações de vendas da Microsoft.

##### <a name="withholding-rates"></a>As taxas de retenção
As informações que você enviar em seus formulários de imposto determina o taxa de retenção de imposto apropriado. A taxa de retenção só se aplica a vendas que tornam os Estados Unidos; vendas feitas em locais fora dos EUA não estão sujeitos a retenção. As taxas de retenção variam, mas para a maioria dos desenvolvedores registrando fora dos Estados Unidos, a taxa padrão é 30 %). Você tem a opção de reduzir essa taxa se seu país concordou em um imposto de renda tratados com os Estados Unidos.

##### <a name="tax-treaty-benefits"></a>Benefícios do imposto tratados
Se você estiver fora dos Estados Unidos, você poderá tirar proveito do imposto sobre benefícios tratados. Esses benefícios variam de país para país e podem permitir que você a reduzir a quantidade de impostos que retém da Microsoft. Você pode solicitar imposto tratados benefícios ao concluir a parte II do formulário W-8BEN. É recomendável que você se comunicar com os recursos apropriados no seu país ou região para determinar se esses benefícios se aplicam a você.

[Saiba mais sobre os detalhes de imposto para os desenvolvedores de aplicativo e de jogos do Windows e do Azure Marketplace](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Status de espera de pagamento

Por padrão, a Microsoft envia pagamentos mensalmente. No entanto, você tem a opção para colocar seus pagamentos em espera, o que impedirá o envio de pagamentos para sua conta. Se você optar por colocar seus pagamentos em espera, continuaremos a registrar a qualquer receita que você ganha e forneça os detalhes no seu **resumo de pagamento**. No entanto, não enviamos os pagamentos à sua conta até que você remova a suspensão. 

Para colocar os pagamentos em espera, vá para **configurações de conta**. Sob **detalhes financeiros**, no **status de espera de pagamento** seção, alterne o controle deslizante para **em**. Você pode alterar seu status de espera de pagamento a qualquer momento, mas lembre-se de que a sua decisão afetará o próximo pagamento mensal. Por exemplo, se você quiser manter o pagamento de abril, certifique-se de definir seu status de espera de pagamento **em** antes do final de março.

Depois de ter definido seu pagamento mantenha o status para **no**, todos os pagamentos serão em espera até que você alterne o controle deslizante de volta ao **Off**. Quando você fizer isso, você ficará incluído durante o próximo ciclo de pagamento mensal (desde que os limites de pagamento aplicáveis tiverem sido atendidos). Por exemplo, se você já teve seus pagamentos em espera, mas seria gostam de ter um pagamento gerado em junho, certifique-se de alternar o pagamento mantenha status **desativar** antes do final de maio.

> [!NOTE]
> Sua **status de espera de pagamento** seleção se aplica à **todos os** fontes de receita que são pagos por meio do Microsoft Partner Center, incluindo o Azure Marketplace, AppSource, Microsoft Store, publicidade, etc.). Não é possível selecionar o status de espera diferentes para cada fonte de receita.

### <a name="devices"></a>Dispositivos

As configurações de gerenciamento de dispositivo se aplicam somente a publicação de UWP. [Saiba mais](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>GUIDs de acompanhamento

Globalmente identificadores exclusivos (GUIDs) são números de referência exclusivo (com 32 dígitos hexadecimais) que podem ser usados para acompanhar o uso do Azure. 

Para criar GUIDs para rastreamento, você deve usar um gerador de GUID. A equipe do Armazenamento do Azure criou um [formulário gerador de GUID](https://aka.ms/StoragePartners) que enviará a você por email um GUID no formato correto e que pode ser reutilizado entre os sistemas de controle diferentes.

Recomendamos que você crie um GUID exclusivo para cada oferta e canal de distribuição para cada produto. Você pode optar por usar um único GUID para os vários canais de distribuição do produto se não quiser que os relatórios sejam divididos.

Se você implantar um produto usando um modelo e ele estiver disponível no Azure Marketplace e no GitHub, você poderá criar e registrar 2 GUIDS distintos:

*   Produto A no Azure Marketplace
*   Produto A no GitHub

Emissão de relatórios é feita, o valor de parceiro (ID de parceiro da Microsoft) e os GUIDs. Você também pode acompanhar os GUIDs em um nível mais granular, alinhando para cada plano de dentro de sua oferta.

Para obter mais informações, consulte o [uso do cliente do Azure de acompanhamento com perguntas Frequentes de GUIDs](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).



## <a name="multi-user-account-management"></a>Gerenciamento de conta de multiusuário

Aproveita o Partner Center [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) para acesso de conta de multiusuário e gerenciamento. Sua organização do Azure AD é associado automaticamente a sua conta no Partner Center como parte do processo de registro. 

## <a name="manage-users"></a>Gerenciar Usuários

O **os usuários** seção do Partner Center (sob **configurações de conta**) vamos usar o Azure AD para gerenciar os usuários, grupos e aplicativos do Azure AD que têm acesso à sua conta do Partner Center. Observe que para gerenciar usuários, você deve estar conectado com seu [conta de trabalho](./company-work-accounts.md) (de locatário do Azure AD associado). Para gerenciar usuários em uma conta de trabalho diferentes / locatário, você precisará inscrever-se e entre novamente como um usuário com **Manager** permissões nessa conta de trabalho / locatário. 

Depois de fazer logon com sua conta corporativa (locatário do Azure AD), você pode:
- [Adicionar ou remover usuários](#add-or-remove-users)
- [Alterar uma senha de usuário](#change-a-user-password)
- [Adicionar ou remover grupos](#add-or-remove-users)
- [Adicionar ou remover aplicativos do Azure AD](#add-new-azure-ad-applications)
- [Gerenciar chaves para um aplicativo do Azure AD](#manage-keys-for-an-azure-ad-application)
- [Definir permissões e funções de usuário](#define-user-roles-and-permissions)


Lembre-se de que todos os usuários do Partner Center (incluindo grupos e aplicativos do Azure AD) devem ter uma conta de trabalho ativos em um [locatário do Azure AD](#manage-tenants) que está associado com sua conta no Partner Center. 

### <a name="add-or-remove-users"></a>Adicionar ou remover usuários

Sua conta deve ter [ **Manager nível** ](#define-user-roles-and-permissions) permissões para o [(locatário do Azure AD) da conta de trabalho](./company-work-accounts.md) no qual você deseja adicionar ou editar usuários.

#### <a name="add-existing-users"></a>Adicione usuários existentes

Para adicionar usuários à sua conta do Centro de parceiros que já existem na sua empresa [(locatário do Azure AD) da conta de trabalho](./company-work-accounts.md):

1. Vá para **os usuários** (sob **configurações de conta**) e selecione **adicionar usuários**.
2. Selecione um ou mais usuários na lista exibida. Você pode usar a caixa de pesquisa para pesquisar usuários específicos.
\* Se você selecionar mais de um usuário para adicionar à sua conta no Partner Center, você deve atribui-los a mesma função ou conjunto de permissões personalizadas. Para adicionar vários usuários com permissões ou funções diferentes, repita essas etapas para cada função ou um conjunto de permissões personalizadas.
3.  Quando você terminar de escolher os usuários, clique em **adicionar selecionada**.
5.  No **funções** seção, especifique a função (ões) ou permissões personalizadas para os usuários selecionados.
6.  Clique em **Salvar**.

#### <a name="create-new-users"></a>Criar novos usuários

Para criar contas de usuário totalmente nova, você deve ter uma conta com [ **Administrador Global** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) permissões. 

1. Vá para **os usuários** (sob **configurações de conta**), selecione **adicionar usuários**, em seguida, escolha **criar novos usuários**.
1. Insira um nome, sobrenome e nome de usuário para cada novo usuário. 
1. Se você quiser que o novo usuário para ter uma conta de administrador global no diretório da sua organização, marque a caixa denominada **tornar este usuário um Administrador Global no AD do Azure, com controle total sobre todos os recursos de diretório**. Isso dará ao usuário acesso total a todos os recursos administrativos em sua empresa do AD do Azure. Eles poderá adicionar e gerenciar usuários na conta de trabalho da sua organização (locatário do Azure AD), embora não está no Partner Center, a menos que você conceder à conta as permissões ou função apropriadas. 
1. Se você tiver marcado a caixa para **tornar este usuário um Administrador Global**, você precisará fornecer um **email de recuperação de senha** para o usuário a recuperar sua senha, se necessário.
1. No **associação de grupo** , selecione todos os grupos aos quais você deseja que o novo usuário deve pertencer.
1. No **funções** seção, especifique as permissões personalizadas para o usuário ou função (ões).
1. Clique em **Salvar**.

Criando um novo usuário no Partner Center também criará uma conta para que o usuário na conta de trabalho (locatário do Azure AD) para o qual você está conectado. Fazendo alterações em um nome de usuário no Partner Center para fazer as mesmas alterações na conta de trabalho da sua organização (locatário do Azure AD).

#### <a name="invite-new-users-by-email"></a>Convidar novos usuários por email

Para convidar usuários que não fazem parte de sua conta de trabalho da empresa (locatário do Azure AD) por meio de email no momento, você deve ter uma conta com [ **Administrador Global** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) permissões. 

1. Vá para **os usuários** (sob **configurações de conta**), selecione **adicionar usuários**, em seguida, escolha **convidar usuários por email**.
2. Insira o email de um ou mais endereços (até dez), separados por vírgula ou ponto e vírgula.
3. No **funções** seção, especifique as permissões personalizadas para o usuário ou função (ões).
4. Clique em **Salvar**.

Os usuários convidados por você receberá um convite por email para ingressar em sua conta no Partner Center. Uma nova conta de usuário convidado será criada na sua conta corporativa (locatário do Azure AD). Cada usuário precisará aceitar o convite antes que possam acessar sua conta.

Se você precisar reenviar um convite, visite o **os usuários** , localize o convite na lista de usuários, selecione o seu endereço de email (ou o texto que diz *convite pendente*). Em seguida, na parte inferior da página, selecione **reenviar o convite**.
 

> [!NOTE]
> Se sua organização usa [integração de diretórios](https://go.microsoft.com/fwlink/p/?LinkID=724033) para sincronizar o serviço de diretório local com o Azure AD, você não poderá criar novos usuários, grupos ou aplicativos do Azure AD no Partner Center. Você (ou outro administrador em seu diretório local) será necessário criá-los diretamente no diretório local antes de você poderá ver e adicioná-los no Partner Center.

#### <a name="remove-a-user"></a>Remover um usuário

Para remover um usuário de sua conta corporativa (locatário do Azure AD), vá para **os usuários** (sob **configurações de conta**), selecione o usuário que você gostaria de remover usando a caixa de seleção na coluna mais à direita, em seguida, escolha  **Remover** das ações disponíveis. Uma janela pop-up será exibida para você confirmar que você deseja remover os usuários selecionados.

#### <a name="change-a-user-password"></a>Alterar uma senha de usuário

Se um dos seus usuários precisar alterar sua senha, eles podem fazer isso em si, se você tiver fornecido um **email de recuperação de senha** ao criar a conta de usuário. Você também pode atualizar uma senha de usuário seguindo as etapas abaixo. Para alterar a senha do usuário em sua conta de trabalho da empresa (locatário do Azure AD), você deve estar conectado em uma conta com [ **Administrador Global** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) permissões. Observe que isso alterará a senha do usuário no locatário do AD do Azure, junto com a senha que eles usam para acessar o Centro de parceiros.

1.  Dos **os usuários** página (sob **configurações de conta**), selecione o nome da conta de usuário que você deseja editar.
2.  Selecione o **redefinição de senha** botão na parte inferior da página.
3.  Uma página de confirmação será exibida mostrando as informações de logon para o usuário, incluindo uma senha temporária. Não se esqueça de imprimir ou copiar essas informações e fornecê-la para o usuário, pois você não poderá acessar a senha temporária depois que você sair desta página.


## <a name="manage-groups"></a>Gerenciar grupos

Grupos permitem controlar várias funções de usuário e permissões todos juntas.

#### <a name="add-an-existing-group"></a>Adicionar um grupo existente

Para adicionar que um grupo que já existe na conta de trabalho da sua organização (locatário do Azure AD) para sua conta no Partner Center: 

1.  Dos **os usuários** página (sob **configurações de conta**), selecione **adicionar grupos**.
2.  Selecione um ou mais grupos na lista que aparece. Você pode usar a caixa de pesquisa para procurar grupos específicos.
Se você selecionar mais de um grupo para adicionar à sua conta no Partner Center, você deve atribui-los a mesma função ou conjunto de permissões personalizadas. Para adicionar vários grupos com permissões ou funções diferentes, repita essas etapas para cada função ou um conjunto de permissões personalizadas.
3.  Quando você terminar de escolher os grupos, clique em **adicionar selecionada**.
4.  No **funções** seção, especifique a função (ões) ou permissões personalizadas para o grupo selecionado (s). Todos os membros do grupo será capazes de acessar sua conta no Partner Center com as permissões que se aplicam ao grupo, independentemente das funções e permissões associadas à sua conta individual.
5.  Clique em **Salvar**.

Quando você adiciona um grupo existente, cada usuário que é um membro desse grupo será capaz de acessar sua conta no Partner Center, com as permissões associadas com a função atribuída do grupo.

#### <a name="add-a-new-group"></a>Adicionar um novo grupo

Para adicionar um grupo novo em folha para sua conta no Partner Center: 

1.  Dos **os usuários** página (sob **configurações de conta**), selecione **adicionar grupos**.
2.  Na próxima página, selecione **novo grupo**.
3.  Insira o nome de exibição para o novo grupo.
4.  Especifique as permissões personalizadas para o grupo ou função (ões). Todos os membros do grupo será capazes de acessar sua conta no Partner Center com as permissões que se aplicam aqui, independentemente das permissões ou funções associadas com sua conta individual.
5.  Selecione o (s) para o novo grupo na lista que aparece. Você pode usar a caixa de pesquisa para pesquisar usuários específicos.
6.  Quando você terminar de selecionar usuários, clique em **adicionar selecionada** para adicioná-los ao novo grupo.
7.  Clique em **Salvar**.

Observe que esse novo grupo será criado na conta de trabalho da sua organização (locatário do Azure AD) Além disso, não apenas na sua conta no Partner Center.

#### <a name="remove-a-group"></a>Remover um grupo

Para remover um grupo de sua conta corporativa (locatário do Azure AD), vá para **os usuários** (sob **configurações de conta**), selecione o grupo que você gostaria de remover usando a caixa de seleção na coluna mais à direita, em seguida, escolha  **Remover** das ações disponíveis. Uma janela pop-up será exibida para você confirmar que você deseja remover o grupo selecionado (s).

## <a name="manage-azure-ad-applications"></a>Gerenciar aplicativos do Azure AD

Você pode permitir que aplicativos ou serviços que fazem parte de Azure do sua empresa AD para acessar sua conta no Partner Center. 

#### <a name="add-existing-azure-ad-applications"></a>Adicionar aplicativos existentes do AD do Azure 

Para adicionar os aplicativos que já existem no Active Directory do Azure da sua empresa: 

1.  Dos **os usuários** página (sob **configurações de conta**), selecione **adicionar aplicativos do Azure AD**.
2.  Selecione um ou mais aplicativos do Azure AD na lista exibida. Você pode usar a caixa de pesquisa para procurar específicos de aplicativos do Azure AD. Se você selecionar mais de um aplicativo do Azure AD para adicionar à sua conta no Partner Center, você deve atribui-los a mesma função ou conjunto de permissões personalizadas. Para adicionar vários aplicativos do AD do Azure com funções/permissões diferentes, repita essas etapas para cada função ou um conjunto de permissões personalizadas.
3.  Quando você terminar de selecionar os aplicativos do Azure AD, clique em **adicionar selecionada**.
5.  No **funções** seção, especifique as permissões personalizadas para o selecionado ou função (ões) aplicativo (s) do Azure AD.
6.  Clique em **Salvar**.

#### <a name="add-new-azure-ad-applications"></a>Adicionar o novo Azure aplicativos do AD 

Se você deseja conceder acesso do Partner Center para o Azure uma totalmente nova conta de aplicativo do AD, você pode criar uma na **usuários** seção. Observe que isso irá criar uma nova conta na sua conta de trabalho da empresa (locatário do Azure AD), não apenas na sua conta no Partner Center. Se você principalmente estiver usando este aplicativo do Azure AD para autenticação do Partner Center e não é necessário que os usuários acessá-la diretamente, você pode inserir qualquer endereço válido para o **URL de resposta** e **URI da ID do aplicativo**, desde como esses valores não são usados por outro aplicativo do Azure AD em seu diretório.

1.  Dos **os usuários** página (sob **configurações de conta**), selecione **adicionar aplicativos do Azure AD**.
2.  Na próxima página, selecione **aplicativo de novo do Azure AD**.
3.  Insira o **URL de resposta** para o novo aplicativo do Azure AD. Esta é a URL onde os usuários podem entrar e usar seu aplicativo do Azure AD (às vezes, também conhecido como a URL do aplicativo ou a URL de logon). O **URL de resposta** não pode ter mais de 256 caracteres e deve ser exclusivo dentro do seu diretório.
4.  Insira o **URI da ID do aplicativo** para o novo aplicativo do Azure AD. Este é um identificador lógico para o aplicativo do Azure AD que é apresentado quando uma solicitação de logon única é enviada para o Azure AD. Observe que o **URI da ID do aplicativo** deve ser exclusivo para cada aplicativo do Azure AD em seu diretório. Essa ID não pode ter mais de 256 caracteres. Para obter mais informações sobre o URI da ID do aplicativo, consulte [integrando aplicativos com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts).
5.  No **funções** seção, especifique a função (ões) ou permissões personalizadas para o aplicativo do Azure AD.
6.  Clique em **Salvar**.

Depois de adicionar ou criar um aplicativo do Azure AD, você pode retornar para o **usuários** seção e selecione o nome do aplicativo para examinar as configurações do aplicativo, incluindo a ID de locatário, ID do cliente, a URL de resposta e URI da ID do aplicativo.

#### <a name="remove-an-application"></a>Remover um aplicativo

Para remover um aplicativo de sua conta corporativa (locatário do Azure AD), vá para **os usuários** (sob **configurações de conta**), selecione o aplicativo que você gostaria de remover usando a caixa de seleção na coluna mais à direita, em seguida, escolha **remover** das ações disponíveis. Uma janela pop-up será exibida para você confirmar que você deseja remover os aplicativos selecionados.

#### <a name="manage-keys-for-an-azure-ad-application"></a>Gerenciar chaves para um aplicativo do Azure AD

Se seu aplicativo do AD do Azure lê e grava dados no Microsoft Azure AD, que ele precisará de uma chave. Você pode criar chaves para um aplicativo do Azure AD por meio da edição de suas informações no Partner Center. Você também pode remover as chaves que não são mais necessários.

1.  Dos **os usuários** página (sob **configurações de conta**), selecione o nome do aplicativo do Azure AD. Você verá todas as chaves do Active Directory para o aplicativo do Azure AD, incluindo a data em que a chave foi criada e quando ela irá expirar. 
2. Para remover uma chave que não é mais necessário, selecione **remover**.
3.  Para adicionar uma nova chave, selecione **adicionar nova chave**.
4.  Você verá uma tela mostrando a **ID do cliente** e **valores de chave**. Certifique-se imprimir ou copiar essas informações, pois você não poderá acessá-lo novamente depois que você sair desta página.
4.  Se você quiser criar mais chaves, selecione **adicionar outra chave**.


### <a name="define-user-roles-and-permissions"></a>Definir permissões e funções de usuário

Os usuários da sua empresa podem ser atribuídos, as seguintes funções e permissões no programa de Marketplace comercial no Partner Center. 

Observe que as funções de locatário do Azure Active Directory (AAD) incluem administrador global, administrador de usuários e funções CSP. Funções do AAD não são essas funções que não gerencia o locatário e elas incluem MPN admin, administrador de perfil de negócios, administração de indicação, incentivo administrador e usuário incentivo.


|**Função**|**Permissões**|
|----------------------------------|:---------------------------------|
|Administrador global|• Podem acessar todos os Microsoft/serviços de conta com privilégios totais
|      |• Criar tíquetes de suporte para o Centro de parceiros
||• Exibir contratos, listas de preços e ofertas
||• Exibir, criar e gerenciar os usuários do parceiro|
|Gerente|• Podem acessar todos os recursos de conta Microsoft, exceto as configurações de imposto e pagamento
|      |• Pode gerenciar usuários, funções e trabalhar contas (locatários)|
|Desenvolvedores|• Pode carregar pacotes, enviar aplicativos e complementos e exibir o relatório de uso para obter detalhes de telemetria
|      |• Não é possível acessar configurações de informações ou a conta financeiras|
|Colaborador de negócios|• Pode acessar informações financeiras e definir os detalhes de preços
|      |• Não é possível criar ou enviar novos aplicativos e complementos|
|Colaborador financeiro|• Podem exibir relatórios de pagamento
|      |• Não é possível fazer alterações em aplicativos ou configurações|
|Profissional de marketing|• Pode responder a revisões de cliente e não-financeiros relatórios
|      |• Não é possível fazer alterações em aplicativos ou configurações|

Para obter mais informações sobre como gerenciar funções e permissões em outras áreas do Partner Center, como o Azure Active Directory (AD), o provedor de solução de nuvem (CSP), fornecedores de painel de controle (CPV), os usuários convidados ou Microsoft Partner Network (MPN), consulte [atribuir funções de usuários e permissões no Partner Center](https://docs.microsoft.com/partner-center/permissions-overview).


## <a name="manage-tenants"></a>Gerenciar Locatários

Um locatário do Azure Active Directory (AD), também conhecido como seu "conta de trabalho" em toda esta documentação é uma representação da sua organização configurar no portal do Azure e ajuda você a gerenciar uma instância específica de serviços de nuvem da Microsoft para seu interno e usuários externos. Se sua organização se inscreveu em um serviço de nuvem da Microsoft, como o Azure, Microsoft Intune ou Office 365, um locatário Azure AD foi estabelecido para você. 

Você pode configurar vários locatários para usar com o Partner Center. Qualquer usuário com o **Manager** função na conta do Partner Center terá a opção de adicionar e remover locatários do Azure AD da conta.  

### <a name="add-an-existing-tenant"></a>Adicionar um locatário existente

Para associar outro locatário do AD do Azure com sua conta no Partner Center:

1.  Dos **locatários** página (sob **configurações de conta**), selecione **associar outro locatário do Azure AD**.
2. Insira suas credenciais do Azure AD para o locatário que você deseja associar.
3. Examine o nome da organização e o domínio para seu locatário do Azure AD. Para concluir a associação, selecione **confirmar**.

Se a associação for bem-sucedida, em seguida, estará pronto para adicionar e gerenciar usuários de conta de **usuários** seção no Partner Center.

### <a name="create-a-new-tenant"></a>Criar um novo locatário

Para criar um locatário de marca novo AD do Azure com sua conta no Partner Center:

1.  Dos **locatários** página (sob **configurações de conta**), selecione **AD do Azure de criar um novo locatário**.
2. Insira as informações de diretório para o novo Azure AD:
    - **Nome de domínio**: O nome exclusivo que usaremos para seu domínio do AD do Azure, junto com ". onmicrosoft.com". Por exemplo, se você inseriu "exemplo", seu domínio do AD do Azure seria "example.onmicrosoft.com".
    - **Entre em contato com o email**: Um endereço de email onde podemos contatá-lo sobre a sua conta se necessário.
    - **Informações de conta de usuário de administrador global**: O primeiro nome, último nome, nome de usuário e senha que você deseja usar para a nova conta de administrador global.
3. Selecione **criar** para confirmar as novas informações de domínio e a conta.
4. Entrar com seu Azure novo nome de usuário de administrador global do AD e a senha para começar [adicionando e gerenciando usuários](#manage-users).

Para obter mais informações sobre a criação de novos locatários dentro de seu portal do Azure, em vez de por meio do portal do Partner Center, consulte o artigo [criar um novo locatário no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

### <a name="remove-a-tenant"></a>Remover um locatário

Para remover um locatário de sua conta no Partner Center, localizar seu nome na **locatários** página (na **configurações de conta**), em seguida, selecione **remover**. Você será solicitado a confirmar que você deseja remover o locatário. Quando você fizer isso, não há usuários nesse locatário será capazes de entrar na conta do Partner Center e quaisquer permissões que você configurou para os usuários serão removidos.

Quando você remove um locatário, todos os usuários que foram adicionados à conta do Partner Center desse locatário não poderá entrar na conta.

> [!TIP]
> Você não pode remover um locatário se você está inscrito no Partner Center usando uma conta no mesmo locatário. Para remover um locatário, você deve entrar no Partner Center como um **Manager** para outro locatário que está associado com a conta. Se houver apenas um locatário associado à conta, que o locatário só pode ser removido após entrar com a conta da Microsoft que abriu a conta.


## <a name="agreements"></a>Contratos

O **contratos** seção do Partner Center (sob **configurações de conta**) vamos você exibir uma lista dos contratos de publicação autorizados por você. Esses contratos são listados de acordo com o nome e número de versão, incluindo a data em que ele foi aceito e o nome do usuário que aceitou o contrato. 

**As ações necessárias** podem aparecer na parte superior desta página se há atualizações de contrato que exigem sua atenção. Para aceitar um contrato atualizado, primeiro ler a versão de contrato vinculado e selecione **aceitá-** . 

Para obter informações sobre os contratos de provedor de solução de nuvem (CSP) no Partner Center, visite [contratos de nuvem da Microsoft por região e idioma](https://docs.microsoft.com/partner-center/agreements).

## <a name="next-steps"></a>Próximas etapas

- [Criar uma nova oferta de SaaS](./create-new-saas-offer.md)
