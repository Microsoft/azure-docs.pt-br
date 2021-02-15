---
title: Como gerenciar uma conta do marketplace comercial no Microsoft Partner Center
description: Saiba como gerenciar uma conta do marketplace comercial no Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: parthpandyaMSFT
ms.author: parthp
ms.date: 11/02/2020
ms.openlocfilehash: cc853285e193dd3ac4e348525b8bf0db9f8cb023
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533937"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Gerenciar sua conta do marketplace comercial no Partner Center

Depois que você [criou uma conta do Partner Center](./create-account.md), use o [painel do marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) para gerenciar sua conta e suas ofertas.

Neste artigo, vamos nos aprofundar no gerenciamento da sua conta do Partner Center, incluindo como:

- [Acessar as configurações de conta do Partner Center](#access-your-account-settings)
- [Localizar sua ID de Editor, ID da Symantec, ID de Vendedor, ID de Usuário, ID da MPN e locatários do Azure AD](#account-details)
- [Atualizar informações de contato](#contact-info)
- [Configurar GUIDs de acompanhamento para monitorar o uso do cliente](#tracking-guids)
- [Gerenciar usuários](#manage-users)
- [Gerenciar grupos](#manage-groups)
- [Gerenciar aplicativos do Azure AD](#manage-azure-ad-applications)
- [Definir permissões e funções de usuário](#define-user-roles-and-permissions)
- [Gerenciar locatários do Azure AD (contas corporativas)](#manage-tenants)
- [Gerenciar contratos do Partner Center](#agreements)

## <a name="access-your-account-settings"></a>Acessar configurações da sua conta

Se ainda não tiver feito isso, você (ou o administrador da sua organização) deverá acessar as [configurações da conta](https://partner.microsoft.com/dashboard/account/management) da sua conta do Partner Center para:

- Verifique o status de verificação da conta da sua empresa.
- Confirme sua ID do Symantec, ID do vendedor, ID do Microsoft Partner Network (MPN), ID do editor e informações de contato, incluindo o aprovador da empresa e o contato do vendedor.
- Crie contas de usuário para qualquer pessoa que usará sua conta de negócios no Partner Center.

### <a name="open-account-settings"></a>Abrir configurações de conta

No [painel do Marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace) no Partner Center, selecione **configurações** (ícone de engrenagem) no canto superior direito e, em seguida, **configurações da conta**.

![Menu de configurações de conta no Partner Center](./media/dashboard-developer-settings.png)

Em **configurações de conta** , você pode exibir:

- **Detalhes da conta**: Tipo de conta e Status da conta
- **IDs do Publicador**: ID do vendedor, ID de usuário, ID do editor, locatários do Azure AD e assim por diante
- **Informações de contato**: Nome de exibição do editor, nome do contato, email, telefone e endereço do vendedor
- **GUIDs de Acompanhamento**: GUIDs de acompanhamento associados à sua conta

### <a name="account-details"></a>Detalhes da conta

Na seção Detalhes da conta, você pode ver informações básicas, como o **Tipo de conta** (Corporativa ou Individual) e o **Status de verificação** da sua conta. Durante o processo de verificação da sua conta, essas configurações exibirão cada etapa necessária, incluindo verificação de email, verificação de emprego e verificação empresarial. Você também pode atualizar seu email aqui e reenviar a verificação, se necessário.

### <a name="publisher-ids"></a>IDs do Editor

Na seção IDs do Editor, você pode ver sua **ID da Symantec**, **ID de Vendedor**, **ID de Usuário**, **ID da MPN** e os **locatários do Azure AD**. Esses valores são atribuídos pela Microsoft para identificar exclusivamente sua conta de desenvolvedor e não podem ser editados.

### <a name="contact-info"></a>Informações de contato

Na seção Informações de contato, você pode ver o **Nome de exibição de editor**, as **Informações de contato do vendedor** (o nome de contato, o email, o número de telefone e o endereço do vendedor da empresa) e o **Aprovador da empresa** (o nome, o email e o número de telefone da pessoa com autoridade para aprovar decisões na empresa).

#### <a name="payout-account"></a>Conta de pagamento

Uma conta de pagamento é a conta bancária para a qual os rendimentos de suas vendas são enviados. Essa conta bancária deve estar no mesmo país/região em que você registrou a conta do Partner Center.

Para configurar sua conta de pagamento:

1. Acesse a [página de visão geral do marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) no Partner Center.
2. Na seção Perfil, ao lado de **Perfil de Pagamento**, selecione **Atualizar**.
3. **Escolha uma forma de pagamento**: Conta bancária ou PayPal.
4. **Adicione informações de pagamento**: Isso pode incluir escolher um tipo de conta (corrente ou poupança), inserir o nome do titular da conta, o número da conta e o número do banco, o endereço para cobrança, o número de telefone ou o endereço de email do PayPal. *Para obter mais informações sobre como usar o PayPal como a forma de pagamento da conta e descobrir se é compatível com sua região de mercado, confira [Informações sobre o PayPal](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> A alteração da conta de pagamento pode atrasar os pagamentos em até um ciclo de pagamento. Esse atraso ocorre porque precisamos confirmar a alteração da conta, como fazemos na primeira configuração da conta de pagamento. Você ainda receberá o valor total após a verificação de sua conta; os pagamentos atrasados do ciclo de pagamento atual serão adicionados ao próximo.  

#### <a name="tax-profile"></a>Perfil fiscal

Examine o status atual do perfil fiscal, confirmando se **Tipo de entidade** e **Informações do Certificado de Imposto** são exibidos corretamente. Selecione **Editar** para atualizar ou preencher os formulários necessários.

Para estabelecer o status fiscal, você deve especificar seu país/região de residência e cidadania e preencher os formulários de impostos apropriados associados ao seu país/região.

Independentemente do seu país/sua região de residência ou cidadania, você deve preencher formulários de impostos dos Estados Unidos para vender ofertas por meio da Microsoft. Os parceiros que atendem a determinados requisitos de residência dos Estados Unidos devem preencher um formulário IRS W-9. Outros parceiros de fora do Estados Unidos devem preencher um formulário IRS W-8. Você pode preencher esses formulários online enquanto completa seu perfil fiscal.

Um ITIN (número de identificação de contribuinte individual) dos Estados Unidos não é necessário para receber pagamentos da Microsoft nem para reivindicar benefícios de tratados de impostos.

Você pode preencher e enviar seus formulários de impostos eletronicamente no Partner Center; na maioria dos casos, você não precisa imprimir e enviar nenhum formulário por correio.

Países e regiões diferentes têm requisitos de impostos diferentes. O valor exato que você deve pagar em impostos depende dos países e regiões em que você vende suas ofertas. A Microsoft paga os impostos sobre vendas e sobre o consumo em seu nome em alguns países/regiões. Esses países/regiões serão identificados no processo de listagem de sua oferta. Em outros países/regiões, dependendo de onde você está registrado, talvez seja necessário pagar impostos sobre vendas e sobre o consumo para suas vendas diretamente para a autoridade tributária local. Além disso, os lucros das vendas que você recebe podem ser tributáveis como renda. Recomendamos que você entre em contato com a autoridade pertinente do seu país ou sua região, que possa ajudá-lo a determinar as informações de impostos corretas para suas transações de vendas da Microsoft.

##### <a name="withholding-rates"></a>Taxas de retenção

As informações que você envia em seus formulários de imposto determinam a taxa de retenção de imposto apropriada. A taxa de retenção se aplica somente às vendas que você faz nos Estados Unidos; as vendas feitas em locais fora dos EUA não estão sujeitas à retenção. As taxas de retenção variam, mas para a maioria dos desenvolvedores que se registram fora do Estados Unidos, a taxa padrão é de 30%. Você tem a opção de reduzir essa taxa se o seu país/sua região tiver acordado um tratado de imposto de renda com os Estados Unidos.

##### <a name="tax-treaty-benefits"></a>Benefícios de tratados fiscais

Se você estiver fora do Estados Unidos, poderá aproveitar os benefícios de tratados fiscais. Esses benefícios variam de país/região a país/região e podem permitir que você reduza o valor de impostos que a Microsoft retém. Você pode reivindicar benefícios de tratados fiscais concluindo a Parte II do formulário W-8BEN. Recomendamos que você se comunique com os recursos apropriados em seu país ou sua região para determinar se esses benefícios se aplicam a você.

[Saiba mais sobre detalhes de impostos para desenvolvedores de jogos/aplicativos do Windows e editores do Azure Marketplace](/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Status de pagamento em espera

Por padrão, a Microsoft envia pagamentos mensalmente. No entanto, você pode, opcionalmente, colocar seus pagamentos em espera, o que impedirá o envio de pagamento à sua conta. Se você optar por colocar seus pagamentos em espera, continuaremos registrando as receitas obtidas e forneceremos os detalhes no **Resumo de pagamentos**. No entanto, não enviaremos nenhum pagamento à sua conta até que você remova a espera.

Para colocar seus pagamentos em espera, acesse **Configurações da conta**. Em **Detalhes financeiros**, na seção **Status de pagamento em espera**, alterne o controle deslizante para **Ativado**. Você pode alterar o status de pagamento em espera a qualquer momento, mas lembre-se de que sua decisão afetará o próximo pagamento mensal. Por exemplo, se você quiser colocar o pagamento de abril em espera, defina o status de pagamento em espera para **Ativado** antes do final de março.

Depois de definir o status de pagamento em espera para **Ativado**, todos os pagamentos ficarão em espera até que você alterne o controle deslizante de volta para **Desativado**. Ao fazer isso, você será incluído no próximo ciclo de pagamento mensal (desde que todos os limites de pagamentos aplicáveis tenham sido atendidos). Por exemplo, se você colocou seus pagamentos em espera, mas quiser ter um pagamento gerado em junho, alterne o status de pagamento em espera para **Desativado** antes do final de maio.

> [!NOTE]
> Seu seleção de **Status de pagamento em espera** se aplica a **todas** as fontes de receita pagas pelo Microsoft Partner Center, incluindo o Azure Marketplace, o AppSource, a Microsoft Store, publicidade etc.). Não é possível selecionar diferentes status de retenção para cada fonte de receita.

### <a name="devices"></a>Dispositivos

As configurações de gerenciamento de dispositivo se aplicam somente à publicação da UWP. [Saiba mais](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>GUIDs de Acompanhamento

Os GUIDs (identificadores globalmente exclusivos) são números de referência exclusivos (com 32 dígitos hexadecimais) que podem ser usados para acompanhar o uso do Azure. 

Para criar GUIDs para rastreamento, você deve usar um gerador de GUID. A equipe do Armazenamento do Azure criou um [formulário gerador de GUID](https://aka.ms/StoragePartners) que enviará a você por email um GUID no formato correto e que pode ser reutilizado entre os sistemas de controle diferentes.

Recomendamos que você crie um GUID exclusivo para cada oferta e canal de distribuição para cada produto. Você pode optar por usar um único GUID para os vários canais de distribuição do produto se não quiser que os relatórios sejam divididos.

Se você implantar um produto usando um modelo e ele estiver disponível no Azure Marketplace e no GitHub, você poderá criar e registrar dois GUIDs distintos:

- Produto A no Azure Marketplace
- Produto A no GitHub

Emissão de relatórios é feita, o valor de parceiro (ID de parceiro da Microsoft) e os GUIDs. Você também pode acompanhar GUIDs em um nível mais granular, alinhando a cada plano de sua oferta.

Para obter mais informações, consulte as [perguntas frequentes sobre como controlar o uso do cliente do Azure com GUIDs](../azure-partner-customer-usage-attribution.md#faq)).

## <a name="create-a-billing-profile"></a>Criação de um perfil de cobrança

Se você estiver publicando uma oferta [Dynamics 365 for Customer Engagement e Power Apps](./create-new-customer-engagement-offer.md) ou [Dynamics 365 for Operations](./create-new-operations-offer.md), será necessário completar seu **perfil de cobrança**.

O endereço para cobrança é preenchido previamente de sua pessoa jurídica e você pode atualizar esse endereço mais tarde. Os campos de ID do IVA e IMPOSTO são opcionais.  O nome do país/da região e o nome da empresa não podem ser editados.

## <a name="multi-user-account-management"></a>Gerenciamento de contas de vários usuários

O Partner Center usa o [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) para acesso e gerenciamento de contas de vários usuários. O Azure AD da sua organização é associado automaticamente à sua conta do Partner Center como parte do processo de registro.

## <a name="manage-users"></a>Gerenciar usuários

A seção **Usuários** do Partner Center (em **Configurações de Conta**) permite que você use o Azure AD para gerenciar usuários, grupos e aplicativos do Azure AD que têm acesso à sua conta do Partner Center. Sua conta deve ter permissões de [**Nível de gerente**](#define-user-roles-and-permissions) para a [conta corporativa (locatário do Azure AD)](./company-work-accounts.md) na qual você deseja adicionar ou editar usuários. Para gerenciar usuários em uma conta corporativa/um locatário diferente, você precisará sair e entrar novamente como um usuário com permissões de **Gerente** nessa conta corporativa/nesse locatário.

Depois de entrar com sua conta corporativa (locatário do Azure AD), você pode:

- [Adicionar ou remover usuários](#add-existing-users)
- [Alterar uma senha de usuário](#change-a-user-password)
- [Adicionar ou remover grupos](#manage-groups)
- [Adicionar ou remover aplicativos do Azure AD](#add-new-azure-ad-applications)
- [Gerenciar chaves para um aplicativo do Azure AD](#manage-keys-for-an-azure-ad-application)
- [Definir permissões e funções de usuário](#define-user-roles-and-permissions)

Lembre-se que todos os usuários do Partner Center (incluindo grupos e aplicativos do Azure AD) devem ter uma conta corporativa ativa em um [locatário do Azure AD](#manage-tenants) associada à sua conta do Partner Center.

### <a name="add-existing-users"></a>Adicionar usuários existentes

Para adicionar usuários à conta do Partner Center que já existe na [conta corporativa (locatário do Azure AD)](./company-work-accounts.md) da sua empresa:

1. Acesse **Usuários** (em **Configurações da conta**) e selecione **Adicionar usuários**.
2. Selecione um ou mais usuários na lista que aparecerá. Você pode usar a caixa de pesquisa para procurar usuários específicos.
*Se você selecionar mais de um usuário para adicionar à sua conta do Partner Center, deverá atribuir-lhe a mesma função ou conjunto de permissões personalizadas. Para adicionar vários usuários com diferentes funções/permissões, repita essas etapas para cada função ou conjunto de permissões personalizadas.
3. Quando terminar de escolher usuários, selecione **Adicionar selecionado**.
4. Na seção **Funções**, especifique as funções ou as permissões personalizadas para os usuários selecionados.
5. Clique em **Salvar**.

### <a name="create-new-users"></a>Criar usuários

Para criar contas de usuário totalmente novas, você deve ter uma conta com permissões de [**Administrador global**](../../active-directory/roles/permissions-reference.md).

1. Acesse **Usuários** (em **Configurações de conta**), selecione **Adicionar usuários** e, em seguida, escolha **Criar usuários**.
1. Insira um nome, o sobrenome e o nome de usuário para cada novo usuário. 
1. Se você quiser que o novo usuário tenha uma conta de administrador global no diretório da sua organização, marque a caixa denominada **Torne este usuário um Administrador global em seu Azure AD, com controle total sobre todos os recursos do diretório**. Isso dará ao usuário acesso completo a todos os recursos administrativos no Azure AD da sua empresa. Eles poderão adicionar e gerenciar usuários na conta corporativa da sua organização (locatário do Azure AD), embora não estejam no Partner Center, a menos que você conceda à conta as permissões/funções apropriadas.
1. Se você tiver marcado a caixa para **Tornar esse usuário um Administrador global**, precisará fornecer um **Email de recuperação de senha** para que o usuário recupere a senha, se necessário.
1. Na seção **Associação de grupo**, selecione os grupos aos quais você deseja que o novo usuário pertença.
1. Na seção **Funções**, especifique as funções ou as permissões personalizadas para o usuário.
1. Clique em **Salvar**.

A criação de um usuário no Partner Center também criará uma conta para esse usuário na conta corporativa (locatário do Azure AD) na qual você está conectado. Fazer alterações no nome de um usuário no Partner Center fará as mesmas alterações na conta corporativa da sua organização (locatário do Azure AD).

### <a name="invite-new-users-by-email"></a>Convidar novos usuários por email

Para convidar usuários que atualmente não fazem parte de sua conta corporativa (locatário do Azure AD) por email, você deve ter uma conta com permissões de [**Administrador global**](../../active-directory/roles/permissions-reference.md).

1. Acesse **Usuários** (em **Configurações de conta**), selecione **Adicionar usuários** e, em seguida, escolha **Convidar usuários por email**.
2. Insira um ou mais endereços de email (até 10), separados por vírgulas ou pontos-e-vírgulas.
3. Na seção **Funções**, especifique as funções ou as permissões personalizadas para o usuário.
4. Clique em **Salvar**.

Os usuários que você convidou receberão um convite por email para ingressar na sua conta do Partner Center. Uma conta de usuário convidado será criada em sua conta corporativa (locatário do Azure AD). Cada usuário precisará aceitar seu convite antes de acessar sua conta.

Se você precisar reenviar um convite, visite a página **Usuários**, localize o convite na lista de usuários, selecione o endereço de email (ou o texto que diz *Convite pendente*). Em seguida, na parte inferior da página, selecione **Reenviar convite**.

> [!NOTE]
> Se a sua organização usar [integração de diretórios](/previous-versions/azure/azure-services/jj573653(v=azure.100)) para sincronizar o serviço de diretório local com o Azure AD, você não poderá criar usuários, grupos nem aplicativos do Azure AD no Partner Center. Você (ou outro administrador em seu diretório local) precisará criá-los diretamente no diretório local para que você possa vê-los e adicioná-los no Partner Center.

### <a name="remove-a-user"></a>Remover um usuário

Para remover um usuário de sua conta corporativa (locatário do Azure AD), acesse **usuários** (em **Configurações de conta**), selecione o usuário que você deseja remover usando a caixa de seleção na coluna mais à direita e escolha **Remover** nas ações disponíveis. Uma janela pop-up será exibida para que você confirme se deseja remover os usuários selecionados.

### <a name="change-a-user-password"></a>Alterar uma senha de usuário

Se um dos seus usuários precisar alterar a senha, ele poderá fazê-lo se você tiver fornecido um **Email de recuperação de senha** ao criar a conta de usuário. Você também pode atualizar a senha de um usuário seguindo as etapas abaixo. Para alterar a senha de um usuário em sua conta corporativa (locatário do Azure AD), você deve estar conectado em uma conta com permissões de [**Administrador global**](../../active-directory/roles/permissions-reference.md). Isso alterará a senha do usuário em seu locatário do Azure AD, juntamente com a senha que eles usam para acessar o Partner Center.

1. Na página **Usuários** (em **Configurações de conta**), selecione o nome da conta de usuário que você deseja editar.
2. Selecione o botão **Redefinir senha** na parte inferior da página.
3. Uma página de confirmação será exibida para mostrar as informações de logon para o usuário, incluindo uma senha temporária. Imprima ou copie essas informações e forneça ao usuário, pois você não poderá acessar a senha temporária depois de sair dessa página.

## <a name="manage-groups"></a>Gerenciar grupos

Os grupos permitem controlar várias funções de usuário e permissões ao mesmo tempo.

### <a name="add-an-existing-group"></a>Adicionar um grupo existente

Para adicionar um grupo que já existe na conta corporativa da sua organização (locatário do Azure AD) à sua conta do Partner Center:

1. Na página **Usuários** (em **Configurações de conta**), selecione **Adicionar grupos**.
2. Selecione um ou mais grupos na lista que aparecerá. Você pode usar a caixa de pesquisa para procurar grupos específicos.
Se você selecionar mais de um grupo para adicionar à sua conta do Partner Center, deverá atribuir-lhe a mesma função ou conjunto de permissões personalizadas. Para adicionar vários grupos com diferentes funções/permissões, repita essas etapas para cada função ou conjunto de permissões personalizadas.
3. Quando terminar de escolher grupos, selecione **Adicionar selecionado**.
4. Na seção **Funções**, especifique as funções ou as permissões personalizadas para os grupos selecionados. Todos os membros do grupo poderão acessar sua conta do Partner Center com as permissões que você aplicar ao grupo, independentemente das funções e permissões associadas a cada conta individual.
5. Clique em **Salvar**.

Quando você adiciona um grupo existente, todos os usuários que são membros desse grupo poderão acessar sua conta do Partner Center, com as permissões associadas à função atribuída do grupo.

### <a name="add-a-new-group"></a>Adicionar um novo grupo

Para adicionar um grupo totalmente novo à sua conta do Partner Center:

1. Na página **Usuários** (em **Configurações de conta**), selecione **Adicionar grupos**.
2. Na próxima página, selecione **Novo grupo**.
3. Insira o nome de exibição para o novo grupo.
4. Especifique as funções ou as permissões personalizadas para o grupo. Todos os membros do grupo poderão acessar sua conta do Partner Center com as permissões que você aplicar aqui, independentemente das funções/permissões associadas a cada conta individual.
5. Selecione usuário(s) para o novo grupo na lista que aparecerá. Você pode usar a caixa de pesquisa para procurar usuários específicos.
6. Quando terminar de selecionar usuários, selecione **Adicionar selecionado** para adicioná-los ao novo grupo.
7. Clique em **Salvar**.

Esse novo grupo será criado na conta corporativa da sua organização (locatário do Azure AD) também, não apenas na sua conta do Partner Center.

### <a name="remove-a-group"></a>Remover um grupo

Para remover um grupo de sua conta corporativa (locatário do Azure AD), acesse **usuários** (em **Configurações de conta**), selecione o grupo que você deseja remover usando a caixa de seleção na coluna mais à direita e escolha **Remover** nas ações disponíveis. Uma janela pop-up será exibida para que você confirme se deseja remover os grupos selecionados.

## <a name="manage-azure-ad-applications"></a>Gerenciar aplicativos do Azure AD

Você pode permitir que aplicativos ou serviços que fazem parte do Azure AD da sua empresa acessem sua conta do Partner Center.

### <a name="add-existing-azure-ad-applications"></a>Adicionar aplicativos existentes do Azure AD

Para adicionar aplicativos que já existem no Azure Active Directory da sua empresa:

1. Na página **Usuários** (em **Configurações de conta**), selecione **Adicionar aplicativos do Azure AD**.
2. Selecione um ou mais aplicativos do Azure AD na lista exibida. Você pode usar a caixa de pesquisa para procurar aplicativos específicos do Azure AD. Se você selecionar mais de um aplicativo do Azure AD para adicionar à sua conta do Partner Center, deverá atribuir-lhe a mesma função ou conjunto de permissões personalizadas. Para adicionar vários aplicativos do Azure AD com diferentes funções/permissões, repita essas etapas para cada função ou conjunto de permissões personalizadas.
3. Quando terminar de selecionar aplicativos do Azure AD, selecione **Adicionar selecionado**.
4. Na seção **Funções**, especifique as funções ou as permissões personalizadas para os aplicativos do Azure AD selecionados.
5. Clique em **Salvar**.

### <a name="add-new-azure-ad-applications"></a>Adicionar novos aplicativos do Azure AD

Se você quiser conceder acesso ao Partner Center a uma conta de aplicativo do Azure AD totalmente nova, poderá criar uma na seção **Usuários**. Isso criará uma nova conta em sua conta corporativa corporativa (locatário do Azure AD), não apenas na sua conta do Partner Center. Se você estiver usando esse aplicativo do Azure AD principalmente para a autenticação no Partner Center e não precisar que os usuários o acessem diretamente, poderá inserir qualquer endereço válido para a **URL de Resposta** e **URI da ID do Aplicativo**, desde que esses valores não sejam usados por nenhum outro aplicativo do Azure AD em seu diretório.

1. Na página **Usuários** (em **Configurações de conta**), selecione **Adicionar aplicativos do Azure AD**.
2. Na próxima página, selecione **Novo aplicativo do Azure AD**.
3. Insira a **URL de Resposta** para o novo aplicativo do Azure AD. Essa é a URL em que os usuários podem entrar e usar o aplicativo do Azure AD (também conhecida como URL do aplicativo ou URL de entrada). A **URL de Resposta** não pode ter mais de 256 caracteres e deve ser exclusiva em seu diretório.
4. Insira o **URI da ID do Aplicativo** para o novo aplicativo do Azure AD. Esse é um identificador lógico para o aplicativo do Azure AD que é apresentado quando uma solicitação de logon único é enviada ao Azure AD. O **URI da ID do aplicativo** deve ser exclusivo para cada aplicativo do Azure AD em seu diretório. Essa ID não pode ter mais de 256 caracteres. Para obter mais informações sobre o URI da ID do aplicativo, consulte [integrando aplicativos com Azure Active Directory](../../active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts)).
5. Na seção **Funções**, especifique as funções ou as permissões personalizadas para o aplicativo do Azure AD.
6. Clique em **Salvar**.

Depois de adicionar ou criar um aplicativo do Azure AD, você pode retornar à seção **Usuários** e selecionar o nome do aplicativo para examinar as configurações do aplicativo, incluindo a ID do Locatário, a ID do Cliente, a URL de Resposta e o URI da ID do Aplicativo.

### <a name="remove-an-azure-ad-application"></a>Remover um aplicativo do Azure AD

Para remover um aplicativo de sua conta corporativa (locatário do Azure AD), acesse **usuários** (em **Configurações de conta**), selecione o aplicativo que você deseja remover usando a caixa de seleção na coluna mais à direita e escolha **Remover** nas ações disponíveis. Uma janela pop-up será exibida para que você confirme se deseja remover os aplicativos selecionados.

### <a name="manage-keys-for-an-azure-ad-application"></a>Gerenciar chaves para um aplicativo do Azure AD

Se o seu aplicativo do Azure AD ler e gravar dados no Microsoft Azure AD, ele precisará de uma chave. Você pode criar chaves para um aplicativo do Azure AD editando as informações no Partner Center. Você também pode remover chaves que não são mais necessárias.

1. Na página **Usuários** (em **Configurações de conta**), selecione o nome do aplicativo do Azure AD. Você verá todas as chaves ativas para o aplicativo do Azure AD, incluindo a data em que a chave foi criada e quando ela expirará em 50.
2. Para remover uma chave que não seja mais necessária, selecione **Remover**.
3. Para adicionar uma nova chave, selecione **Adicionar nova chave**.
4. Você verá uma tela mostrando a **ID do Cliente** e os **Valores de chave**. Imprima ou copie essas informações, pois você não poderá acessá-las novamente depois de sair desta página.
5. Se você quiser criar mais chaves, selecione **Adicionar outra chave**.

## <a name="define-user-roles-and-permissions"></a>Definir permissões e funções de usuário

Os usuários da sua empresa podem receber as seguintes funções e permissões para o programa comercial do Marketplace no Partner Center:

- **Proprietário**
  - Essa é a pessoa que criou a conta pela primeira vez e tem acesso completo a ela, incluindo a capacidade de criar e editar todos os usuários da conta e alterar todas as configurações financeiras e de conta. Normalmente, uma conta tem apenas um proprietário.
- **Gerente**
  - Pode acessar todos os recursos da conta Microsoft, exceto as configurações de imposto e pagamento
  - Pode gerenciar usuários, funções e contas corporativas (locatários)
- **Desenvolvedor**
  - Pode gerenciar e publicar ofertas
  - Pode exibir alguns relatórios do editor

> [!NOTE]
> Para o programa Marketplace Comercial, as funções Administrador global, Colaborador comercial, Colaborador financeiro e Comerciante não são usadas. A atribuição dessas funções a usuários não tem nenhum efeito. Somente as funções Gerente e Desenvolvedor concedem permissões aos usuários.

Para obter mais informações sobre como gerenciar funções e permissões em outras áreas do Partner Center, como Azure AD (Active Directory), CSP (Provedor de Soluções na Nuvem), CPV (fornecedor de painel de controle), Usuários convidados ou MPN (Microsoft Partner Network), confira [Atribuir funções e permissões de usuários no Partner Center](/partner-center/permissions-overview).

## <a name="manage-tenants"></a>Gerenciar locatários

Um locatário do Azure AD (Active Directory), também conhecido como "conta corporativa", em toda esta documentação é uma representação da configuração da sua organização no portal do Azure e ajuda a gerenciar uma instância específica dos serviços em nuvem da Microsoft para seus usuários internos e externos. Se sua organização assinou um serviço de nuvem da Microsoft, como o Azure, Microsoft Intune ou Microsoft 365, um locatário do Azure AD foi estabelecido para você.

Você pode configurar vários locatários para usar com o Partner Center. Qualquer usuário com a função **Gerente** na conta do Partner Center terá a opção de adicionar e remover locatários do Azure AD da conta.  

### <a name="add-an-existing-tenant"></a>Adicionar um locatário existente

Para associar outro locatário do Azure AD com sua conta do Partner Center:

1. Na página **Locatários** (em **Configurações de conta**), selecione **Associar outro locatário do Azure AD**.
2. Insira suas credenciais do Azure AD para o locatário que você deseja associar.
3. Examine a organização e o nome de domínio do locatário do Azure AD. Para concluir a associação, selecione **Confirmar**.

Se a associação for bem-sucedida, você estará pronto para adicionar e gerenciar usuários da conta na seção **Usuários** no Partner Center.

### <a name="create-a-new-tenant"></a>Criar um locatário

Para criar um locatário totalmente novo do Azure AD com sua conta do Partner Center:

1. Na página **Locatários** (em **Configurações de conta**), selecione **Criar um locatário do Azure AD**.
2. Insira as informações de diretório para seu novo Azure AD:
    - **Nome de domínio**: O nome exclusivo que usaremos para seu domínio do Azure AD, junto com ".onmicrosoft.com". Por exemplo, se você inseriu "example", seu domínio do Azure AD seria "example.onmicrosoft.com".
    - **Email de contato**: Um endereço de email no qual podemos contatá-lo sobre sua conta, se necessário.
    - **Informações da conta de usuário administrador global**: Nome, sobrenome, nome de usuário e senha que você deseja usar para a nova conta de administrador global.
3. Selecione **Criar** para confirmar as novas informações de domínio e conta.
4. Entre com seu novo nome de usuário e senha de administrador global do Azure AD para começar a [adicionar e gerenciar usuários](#manage-users).

Para obter mais informações sobre como criar locatários dentro do portal do Azure em vez de fazê-lo no portal do Partner Center, confira o artigo [Criar um locatário no Azure Active Directory](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

### <a name="remove-a-tenant"></a>Remover um locatário

Para remover um locatário da conta do Partner Center, localize o nome na página **Locatários** (em **Configurações da conta**) e, em seguida, selecione **Remover**. Você receberá uma solicitação para confirmar se deseja remover o locatário. Depois de fazer isso, nenhum usuário desse locatário poderá entrar na conta do Partner Center e todas as permissões configuradas para esses usuários serão removidas.

Quando você remove um locatário, todos os usuários que foram adicionados à conta do Partner Center desse locatário não poderão mais entrar na conta.

> [!TIP]
> Você não poderá remover um locatário se estiver conectado ao Partner Center usando uma conta no mesmo locatário. Para remover um locatário, você deve entrar no Partner Center como um **Gerente** de outro locatário associado à conta. Se houver apenas um locatário associado à conta, esse locatário só poderá ser removido depois de entrar com a conta Microsoft que abriu a conta.

## <a name="agreements"></a>Contratos

A seção **contratos** do Partner Center (em **configurações de conta**) permite exibir uma lista dos contratos de publicação que você autorizou. Esses contratos são listados de acordo com o nome e o número de versão, incluindo a data em que foi aceito e o nome do usuário que aceitou o contrato.

**Ações necessárias** poderão aparecer na parte superior desta página se houver atualizações de contrato que precisam de sua atenção. Para aceitar um contrato atualizado, primeiro leia a Versão do Contrato vinculado e, em seguida, selecione **Aceitar contrato**.
