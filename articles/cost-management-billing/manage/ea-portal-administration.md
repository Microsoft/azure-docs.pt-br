---
title: Administração do Portal do EA do Azure
description: Este artigo explica as tarefas comuns que um administrador realiza no Portal do EA do Azure.
author: bandersmsft
ms.author: banders
ms.date: 11/13/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.custom: contperf-fy21q1
ms.openlocfilehash: 3ce13a103b605ea7b6066e8b2868fa974f4e64e5
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050559"
---
# <a name="azure-ea-portal-administration"></a>Administração do Portal do EA do Azure

Este artigo explica as tarefas comuns que um administrador realiza no Portal do EA do Azure (https://ea.azure.com). O Portal do EA do Azure é um portal de gerenciamento online que ajuda os clientes a gerenciar o custo dos respectivos serviços de EA do Azure. Para obter informações introdutórias sobre o Portal do EA do Azure, confira o artigo [Introdução ao Portal do EA do Azure](ea-portal-get-started.md).

## <a name="activate-your-enrollment"></a>Ative seu registro

Para ativar o serviço, o administrador corporativo inicial abre o [Azure Enterprise Portal](https://ea.azure.com) e entra usando o endereço de email do email de convite.

Se você for configurado como o administrador corporativo, não precisará receber o email de ativação. Acesse o [Azure Enterprise Portal](https://ea.azure.com) e entre com o endereço de email e a senha da conta Microsoft, corporativa ou de estudante.

Se você tiver mais de um registro, escolha um para ativar. Por padrão, somente os registros ativos são mostrados. Para exibir o histórico de registros, desmarque a opção **Ativo** no canto superior direito do Azure Enterprise Portal.

Em **Registro**, o status mostra **Ativo**.

![Exemplo mostrando um registro ativo](./media/ea-portal-administration/ea-enrollment-status.png)

Somente administradores corporativos do Azure existentes podem criar outros administradores corporativos.

### <a name="create-another-enterprise-administrator"></a>Criar outro administrador corporativo

Para adicionar outro administrador corporativo:

1. Entre no [Azure Enterprise Portal](https://ea.azure.com).
1. Acesse **Gerenciar** > **Detalhes do Registro**.
1. Selecione **+ Adicionar Administrador** na parte superior à direita.

Verifique se você tem o endereço de email do usuário e o método de autenticação preferencial, por exemplo, uma conta Microsoft, corporativa ou de estudante.

Se você não for o administrador corporativo, entre em contato com um administrador corporativo para solicitar que ele o adicione a um registro. Depois que for adicionado a um registro, você receberá um email de ativação.

Se o administrador corporativo não puder ajudá-lo, crie uma [solicitação de suporte no Azure Enterprise Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Forneça as seguintes informações:

- Número de registro
- Endereço de email para adicionar e tipo de autenticação (conta Microsoft, corporativa ou de estudante)
- Aprovação de email de um administrador corporativo existente
  - Se o administrador corporativo existente não estiver disponível, entre em contato com seu parceiro ou consultor de software para solicitar que ele altere os detalhes de contato por meio da ferramenta VLSC (Centro de Serviços de Licenciamento por Volume da Microsoft).

## <a name="create-an-azure-enterprise-department"></a>Criar um departamento do Azure Enterprise

Administradores corporativos e administradores de departamento usam departamentos para organizar e relatar os serviços e o uso do Azure Enterprise por departamento e centro de custo. O administrador corporativo é capaz de:

- Adicionar ou remover departamentos.
- Associar uma conta a um departamento.
- Criar administradores de departamento.
- Permitir que administradores de departamento exibam preços e custos.

Um administrador de departamento pode adicionar novas contas aos próprios departamentos. Pode também remover contas desses departamentos, mas não do registro.

Para adicionar um departamento:

1. Entre no Azure Enterprise Portal.
1. No painel esquerdo, selecione **Gerenciar**.
1. Selecione a guia **Departamento** e, em seguida, selecione **+ Adicionar Departamento**.
1. Insira as informações.
   O nome do departamento é o único campo obrigatório. Ele precisa ter, pelo menos, três caracteres.
1. Quando concluir, selecione **Adicionar**.

## <a name="add-a-department-administrator"></a>Adicionar um administrador de departamento

Depois que um departamento é criado, o administrador corporativo pode adicionar administradores de departamento e associar cada um deles a um departamento. Os administradores de departamento podem executar as seguintes ações para seus departamentos:

- Criar outros administradores de departamento
- Exibir e editar propriedades do departamento, tais como o nome ou o centro de custo
- Adicionar contas
- Remover contas
- Baixar detalhes de uso
- Exibir o uso mensal e os encargos <sup>1</sup>

> <sup>1</sup> Um administrador corporativo deve conceder essas permissões. Se você tiver recebido permissão para exibir o uso mensal e os encargos do departamento mas não puder vê-los, entre em contato com seu parceiro.

### <a name="to-add-a-department-administrator"></a>Para adicionar um administrador de departamento

Como um administrador corporativo:

1. Entre no Azure Enterprise Portal.
1. No painel esquerdo, selecione **Gerenciar**.
1. Selecione a guia **Departamento** e, em seguida, selecione o departamento.
1. Clique em **+ Adicionar Administrador** e adicione as informações necessárias.
1. Para acesso somente leitura, defina a opção **Somente Leitura** como **Sim** e, em seguida, selecione **Adicionar**.

![Exemplo mostrando a caixa de diálogo Adicionar Administrador do Departamento](./media/ea-portal-administration/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Para definir o acesso somente leitura

Você pode permitir acesso somente leitura aos administradores de departamento.

- Ao criar um administrador de departamento, defina a opção somente leitura como **Sim**.

- Para editar um administrador do departamento existente:
   1. Selecione um departamento e, em seguida, selecione o símbolo de lápis ao lado do **Administrador do Departamento** que você deseja editar.
   1. Defina a opção somente leitura como **Sim** e, em seguida, selecione **Salvar**.

Os administradores corporativos obtêm permissões de administrador do departamento automaticamente.

## <a name="add-an-account"></a>Adicionar uma conta

A estrutura das contas e das assinaturas afeta o modo como elas são administradas e como elas aparecem em suas faturas e relatórios. Exemplos de estruturas organizacionais típicas incluem divisões de negócios, equipes funcionais e localizações geográficas.

Para adicionar uma conta:

1. No Azure Enterprise Portal, selecione **Gerenciar** na área de navegação à esquerda.
1. Selecione a guia **Conta**. Na página **Conta**, selecione **+Adicionar Conta**.
1. Selecione um departamento ou deixe-o como não atribuído e, em seguida, selecione o tipo de autenticação desejado.
1. Digite um nome amigável para identificar a conta no relatório.
1. Insira o endereço de **Email do Proprietário da Conta** para associar à nova conta.
1. Confirme o endereço de email e, em seguida, selecione **Adicionar**.

![Exemplo mostrando a lista de contas e a opção + Adicionar Conta](./media/ea-portal-administration/create-ea-add-an-account.png)

Para adicionar outra conta, selecione **Adicionar Outra Conta** ou **Adicionar** no canto inferior direito da barra de ferramentas à esquerda.

Para confirmar a propriedade da conta:

1. Entre no Azure Enterprise Portal.
1. Exiba o status.

   O status deve mudar de **Pendente** para **Data de Início/Término**. A Data de Início/Término é a data em que o usuário se conectou pela primeira vez e a data de término do contrato.
1. Quando a mensagem de **Aviso** aparecer, o proprietário de conta precisará selecionar **Continuar** para ativar a conta na primeira vez que entrar no Azure Enterprise Portal.

## <a name="change-azure-subscription-or-account-ownership"></a>Alterar a propriedade da conta ou da assinatura do Azure

Os administradores corporativos podem usar o Azure Enterprise Portal para transferir a propriedade da conta de assinaturas selecionadas ou de todas as assinaturas de um registro.

Quando você conclui a transferência de propriedade de uma conta ou uma assinatura, a Microsoft atualiza o proprietário da conta.

Antes de realizar a transferência de propriedade, obtenha noções básicas sobre estas políticas do RBAC do Azure (controle de acesso baseado em função do Azure):

- Ao realizar transferências de propriedade de conta ou de assinatura entre duas IDs organizacionais no mesmo locatário, as políticas do RBAC do Azure e as funções de administrador de serviços e de coadministrador existentes são preservadas.
- As transferências de propriedade de conta ou de assinatura entre locatários resultam na perda das políticas e das atribuições de função do RBAC do Azure.
- Políticas e funções de administrador não são transferidas entre diretórios diferentes. Os administradores de serviços são atualizados para o proprietário da conta de destino.
- Para evitar a perda de políticas RBAC do Azure e atribuições de função ao transferir a assinatura entre locatários, mantenha **desmarcada** a caixa de seleção **Mover as assinaturas para o locatário do Azure AD do destinatário**. Isso manterá os serviços, as funções do Azure e as políticas no locatário atual do Azure AD e transferirá apenas a propriedade da cobrança referente à conta.  
    :::image type="content" source="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Imagem mostrando a caixa de seleção desmarcada para mover assinaturas para o locatário do Azure AD" lightbox="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" :::


Antes de alterar um proprietário da conta:

1. No Azure Enterprise Portal exiba a guia **Conta** e identifique a conta de origem. A conta de origem precisa estar ativa.
1. Identifique a conta de destino e verifique se ela está ativa.

Para transferir a propriedade da conta para todas as assinaturas:

1. Entre no Azure Enterprise Portal.
1. Na área de navegação esquerda, selecione **Gerenciar**.
1. Selecione a guia **Conta** e passe o mouse sobre uma conta.
1. Selecione o ícone de mudar o proprietário da conta à direita. O ícone se assemelha a uma pessoa.  
    ![Imagem mostrando o símbolo Alterar Proprietário da Conta](./media/ea-portal-administration/create-ea-create-sub-transfer-account-ownership-of-sub.png)
1. Escolha a conta de destino para a qual será feita a transferência e selecione **Avançar**.
1. Caso deseje transferir a propriedade da conta entre locatários do Azure AD, marque a caixa de seleção **Mover as assinaturas para o locatário do Azure AD do destinatário**.  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Imagem mostrando a caixa de seleção selecionada para mover assinaturas para o locatário do Azure AD" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Confirme a transferência e selecione **Enviar**.

Para transferir a propriedade da conta para uma assinatura única:

1. Entre no Azure Enterprise Portal.
1. Na área de navegação esquerda, selecione **Gerenciar**.
1. Selecione a guia **Conta** e passe o mouse sobre uma conta.
1. Selecione o ícone de transferir assinaturas à direita. O ícone se assemelha a uma página.  
    ![Imagem mostrando o símbolo Transferir Assinaturas](./media/ea-portal-administration/ea-transfer-subscriptions.png)
1. Escolha a conta de destino para transferir a assinatura e selecione **Avançar**.
1. Caso deseje transferir a propriedade da assinatura entre locatários do Azure AD, marque a caixa de seleção **Mover as assinaturas para o locatário do Azure AD do destinatário**.  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Imagem mostrando a caixa de seleção selecionada para mover assinaturas para o locatário do Azure AD" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Confirme a transferência e, em seguida, selecione **Enviar**.


## <a name="associate-an-account-to-a-department"></a>Associar uma conta a um departamento

Os administradores corporativos podem associar contas existentes a Departamentos no registro.

### <a name="to-associate-an-account-to-a-department"></a>Para associar uma conta a um departamento

1. Entre no Portal do EA do Azure como um administrador corporativo.
1. Selecione **Gerenciar** na área de navegação à esquerda.
1. Selecione **Departamento**.
1. Posicione o cursor sobre a linha com a conta e selecione o ícone de lápis à direita.
1. Escolha o departamento no menu suspenso.
1. Clique em **Salvar**.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Associar uma conta existente à sua assinatura de Pagamento Conforme o Uso

Se já tiver uma conta do Microsoft Azure existente no portal do Azure, insira a conta Microsoft, corporativa ou de estudante pertinente para associá-la ao seu registro do Contrato Enterprise.

### <a name="associate-an-existing-account"></a>Associar uma conta existente

1. No Azure Enterprise Portal, selecione **Gerenciar**.
1. Selecione a guia **Conta**.
1. Selecione **+Adicionar uma conta**.
1. Insira a conta corporativa ou de estudante ou a conta Microsoft associada à conta do Azure existente.
1. Confirme a conta associada à conta do Azure existente.
1. Forneça um nome que você gostaria de usar para identificar esta conta nos relatórios.
1. Selecione **Adicionar**.
1. Para adicionar outra conta, selecione novamente a opção **+Adicionar uma conta** ou retorne à página inicial escolhendo o botão **Admin**.
1. Se você exibir a página **Conta**, a conta recém-adicionada será exibida com o status **Pendente**.

### <a name="confirm-account-ownership"></a>Confirmar a propriedade da conta

1. Entre na conta de email associada à conta corporativa ou de estudante ou à conta Microsoft que você forneceu.
1. Abra a notificação de email intitulada _"Convite para ativar a conta no serviço do Microsoft Azure de licenciamento por volume da Microsoft"_ .
1. No convite, selecione o link **Entrar no Microsoft Azure Enterprise Portal**.
1. Selecione **Entrar**.
1. Insira a conta Microsoft, corporativa ou de estudante e a senha para entrar e confirmar a propriedade da conta.

### <a name="azure-marketplace"></a>Azure Marketplace

Embora a maioria das assinaturas possa converter do ambiente de Pagamento Conforme o Uso para o Contrato Enterprise do Azure, os serviços do Azure Marketplace não fazem isso. Para ter um modo de exibição único de todas as assinaturas e preços, recomendamos adicionar os serviços do Azure Marketplace ao Azure Enterprise Portal.

1. Entre no Azure Enterprise Portal.
1. Selecione **Gerenciar** na área de navegação à esquerda.
1. Selecione a **EnrollmentTab**.
1. Exiba a seção **Detalhes do Registro**.
1. À direita do campo Azure Marketplace, selecione o ícone de lápis para habilitar. Clique em **Salvar**.

O proprietário da conta agora pode comprar serviços do Azure Marketplace que antes pertenciam à assinatura de Pagamento Conforme o Uso.

Depois que as novas assinaturas do Azure Marketplace forem ativadas no registro do EA do Azure, cancele os serviços do Azure Marketplace que foram criados no ambiente de Pagamento Conforme o Uso. Esta etapa é crítica para que suas assinaturas do Azure Marketplace não entrem em um estado inadequado quando seu meio de pagamento do Pagamento Conforme o Uso expirar.

### <a name="msdn"></a>MSDN

As assinaturas da MSDN são automaticamente convertidas em Desenvolvimento/Teste da MSDN e a oferta do EA do Azure perderá os créditos monetários existentes.

### <a name="azure-in-open"></a>Azure via Open

Se você associar uma assinatura do Azure via Open com um Contrato Enterprise, perderá os créditos não consumidos do Azure via Open. Portanto, recomendamos que você consuma todo o crédito da assinatura Azure via Open antes de adicionar a conta ao seu Contrato Enterprise.  

### <a name="accounts-with-support-subscriptions"></a>Contas com assinaturas de suporte

Se o Contrato Enterprise não tiver uma assinatura de suporte e você adicionar uma conta existente com uma assinatura de suporte no Azure Enterprise Portal, sua assinatura de suporte do MOSA não será transferida automaticamente. Você precisará comprar novamente uma assinatura de suporte no EA do Azure durante o período de carência: até o final do mês subsequente.

## <a name="department-spending-quotas"></a>Cotas de gastos do departamento

Os clientes do EA podem definir ou alterar as cotas de gastos para cada departamento em um registro. O valor da cota de gastos é definido para o termo do pagamento antecipado atual. No final do termo do pagamento antecipado atual, o sistema estenderá a cota de gastos existente para o próximo termo do pagamento antecipado, a menos que os valores sejam atualizados.

O administrador do departamento pode exibir a cota de gastos, mas somente o administrador corporativo pode atualizar o valor da cota. O administrador corporativo e o administrador do departamento receberão notificações assim que a cota atingir 50%, 75%, 90% e 100%.

### <a name="enterprise-administrator-to-set-the-quota"></a>Para o administrador corporativo definir a cota:

 1. Abra o Portal do EA do Azure.
 1. Selecione **Gerenciar** na área de navegação à esquerda.
 1. Selecione a guia **Departamento**.
 1. Selecione o Departamento.
 1. Selecione o símbolo de lápis na seção Detalhes do Departamento ou o símbolo **+ Adicionar Departamento** para adicionar uma cota de gastos juntamente com um novo departamento.
 1. Em Detalhes do Departamento, insira um valor para a cota de gastos na moeda do registro na caixa $ da Cota de Gastos (o valor precisa ser maior que 0).
    - As propriedades Nome do Departamento e Centro de Custo também podem ser editadas nesse momento.
 1. Clique em **Salvar**.

A cota de gastos do departamento agora estará visível no modo de exibição Lista de Departamentos na guia Departamento. No final do pagamento antecipado atual, o Portal do Azure EA manterá as cotas de gastos para o próximo termo do pagamento antecipado.

O valor da cota de departamento é independente do pagamento antecipado do Azure atual, e o valor e os alertas da cota se aplicam somente ao uso interno. A cota de gastos do departamento serve meramente para fins informativos e não impõe limites de gastos.

### <a name="department-administrator-to-view-the-quota"></a>Para o administrador do departamento exibir a cota:

1. Abra o Portal do EA do Azure.
1. Selecione **Gerenciar** na área de navegação à esquerda.
1. Selecione a guia **Departamento** e veja a exibição Lista de Departamentos com as cotas de gastos.

Se você for um cliente indireto, os recursos de custos deverão ser habilitados pelo seu parceiro de canal.

## <a name="enterprise-user-roles"></a>Funções de usuário corporativo

O Portal do EA do Azure ajuda você a administrar seus custos e uso do EA do Azure. Há três funções principais no Portal do EA do Azure:

- Administrador de EA
- Administrador de departamento
- Proprietário da conta

Cada função tem um nível diferente de acesso e de autoridade.

Para saber mais sobre as funções de usuário, confira [Funções de usuário corporativo](./understand-ea-roles.md#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Adicionar uma conta de EA do Azure

A conta do EA do Azure é uma unidade organizacional no Portal do EA do Azure. Ela é usada para administrar assinaturas e também é usada para relatórios. Para acessar e usar os serviços do Azure, é necessário que você crie uma conta ou que uma seja criada para você.

Para obter mais informações sobre as contas do Azure, confira [Adicionar uma conta](#add-an-account).

## <a name="enterprise-devtest-offer"></a>Oferta de Desenvolvimento/Teste Enterprise

Como administrador corporativo do Azure, agora você pode permitir que os proprietários da conta na sua organização criem assinaturas com base na oferta Desenvolvimento/Teste do EA. Para fazer isso, marque a caixa Desenvolvimento/Teste do proprietário da conta no Portal do EA do Azure.

Depois de marcar a caixa Desenvolvimento/Teste, informe o proprietário da conta, de modo que ele possa configurar as assinaturas de Desenvolvimento/Teste do EA necessárias para as respectivas equipes de assinantes de Desenvolvimento/Teste.

Essa oferta permite que os assinantes do Visual Studio executem cargas de trabalho de desenvolvimento e teste no Azure com taxas especiais de Desenvolvimento/Teste. Ela fornece acesso à galeria completa de imagens de Desenvolvimento/Teste, incluindo Windows 8.1 e Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Para configurar a oferta de Desenvolvimento/Teste Enterprise:

1. Entre como administrador corporativo.
1. Selecione **Gerenciar** na área de navegação à esquerda.
1. Selecione a guia **Conta**.
1. Selecione a linha da conta em que deseja habilitar o acesso ao Desenvolvimento/Teste.
1. Selecione o símbolo de lápis à direita da linha.
1. Marque a caixa de seleção Desenvolvimento/Teste.
1. Clique em **Salvar**.

Quando um usuário é adicionado como um proprietário de conta por meio do Portal do EA do Azure, todas as assinaturas do Azure associadas ao proprietário da conta que se baseiam na oferta de Desenvolvimento/Teste do PAYG ou as ofertas de crédito mensal para assinantes do Visual Studio serão convertidas na oferta de Desenvolvimento/Teste do EA. As assinaturas baseadas em outros tipos de oferta, como PAYG, associadas ao Proprietário da Conta serão convertidas em ofertas do Microsoft Azure Enterprise.

No momento, a oferta de Desenvolvimento/Teste não é aplicável aos clientes do Azure Gov.

## <a name="create-a-subscription"></a>Criar uma assinatura

Os proprietários da conta podem exibir e gerenciar assinaturas. Você pode usar assinaturas para dar acesso a ambientes e projetos de desenvolvimento às equipes de sua organização. Por exemplo, teste, produção, desenvolvimento e preparo.

Ao criar assinaturas diferentes para cada ambiente de aplicativo, você ajuda a proteger cada ambiente.

- Você também pode atribuir uma conta de administrador de serviços diferente para cada assinatura.
- Você pode associar assinaturas a qualquer número de serviços.
- O proprietário da conta cria assinaturas e atribui uma conta de administrador de serviços a cada assinatura na conta dele.

### <a name="add-a-subscription"></a>Adicionar uma assinatura

Use as informações a seguir para adicionar uma assinatura.

Na primeira vez que você adicionar uma assinatura à conta, será solicitado que você aceite o MOSA (Contrato de Assinatura do Microsoft Online) e um plano de taxas. Embora eles não sejam aplicáveis a clientes do Contrato Enterprise, o MOSA e o plano de taxas são necessários para criar sua assinatura. O aditamento de registro do Contrato Enterprise do Microsoft Azure substitui os itens acima e a sua relação contratual não é alterada. Quando solicitado, selecione a caixa que indica que você aceita os termos.

_Microsoft Azure Enterprise_ é o nome padrão quando uma assinatura é criada. Você pode alterar o nome para diferenciá-lo das outras assinaturas em seu registro e garantir que seja reconhecido em relatórios no nível corporativo.

Para adicionar uma assinatura:

1. No Azure Enterprise Portal, entre na conta.
1. Selecione a guia **Admin** e, em seguida, selecione **Assinatura** na parte superior da página.
1. Verifique se você está conectado como o proprietário da conta em questão.
1. Selecione **+ Adicionar Assinatura** e, em seguida, selecione **Comprar**.

   Na primeira vez que você adicionar uma assinatura a uma conta, deverá fornecer suas informações de contato. Ao adicionar assinaturas adicionais, suas informações de contato serão adicionadas para você.

1. Selecione **Assinaturas** e, em seguida, selecione a assinatura que você criou.
1. Selecione **Editar Detalhes da Assinatura**.
1. Edite o **Nome da Assinatura** e o **Administrador de Serviços** e, em seguida, selecione a marca de seleção.

   O nome da assinatura aparece nos relatórios. É o nome do projeto associado à assinatura no portal de desenvolvimento.

Novas assinaturas podem levar até 24 horas para aparecer na lista de assinaturas. Depois de ter criado uma assinatura, você pode:

- [Editar detalhes da assinatura](https://portal.azure.com)
- [Gerenciar chaves de assinatura](https://portal.azure.com/#home)

## <a name="delete-subscription"></a>Excluir assinatura

Para excluir uma assinatura na qual você seja o proprietário da conta:

1. Entre no portal do Azure com as credenciais associadas a sua conta.
1. No menu Hub, selecione **Assinaturas**.
1. Na guia de assinaturas no canto superior esquerdo da página, escolha a assinatura que deseja cancelar e selecione **Cancelar Assinatura** para iniciar a guia de cancelamento.
1. Insira o nome da assinatura, escolha um motivo para o cancelamento e selecione o botão **Cancelar Assinatura**.

Só os administradores das contas podem cancelar assinaturas.

Para obter mais informações, confira [O que acontecerá depois que eu cancelar minha assinatura?](cancel-azure-subscription.md#what-happens-after-subscription-cancellation).

## <a name="delete-an-account"></a>Excluir uma conta

A remoção da conta só poderá ser concluída para contas ativas sem assinaturas ativas.

1. No Enterprise Portal, escolha **Gerenciar** no painel de navegação à esquerda.
1. Selecione a guia **Conta**.
1. Na tabela Contas, escolha a conta que você deseja excluir.
1. Selecione o símbolo X à direita da linha Conta.
1. Quando não houver assinaturas ativas na conta, selecione **Sim** na linha Conta para confirmar a remoção da Conta.

## <a name="update-notification-settings"></a>Atualizar configurações de notificação

Os administradores corporativos são registrados automaticamente para receber notificações de uso associadas ao registro deles. Cada administrador corporativo pode alterar o intervalo das notificações individuais ou pode desativá-las por completo.

Os contatos de notificação são mostrados no Portal do EA do Azure na seção **Contato de Notificação**. O gerenciamento de seus contatos de notificação garante que as pessoas certas em sua organização obtenham as notificações do EA do Azure.

Para exibir as configurações de notificações atuais:

1. No Portal do EA do Azure, navegue até **Gerenciar** > **Contato de Notificação**.
2. Endereço de email – o endereço de email associado à conta Microsoft, corporativa ou de estudante do administrador corporativo que recebe as notificações.
3. Frequência de notificação de saldo não cobrado – a frequência que é definida para que as notificações sejam enviadas para cada administrador corporativo individual.

Para adicionar um contato:

1. Selecione **+Adicionar Contato**.
2. Insira o endereço de email e depois confirme-o.
3. Clique em **Salvar**.

O novo contato de notificação é exibido na seção **Contato de Notificação**. Para alterar a frequência de notificação, selecione o contato de notificação e selecione o símbolo de lápis à direita da linha selecionada. Defina a frequência como **diária**, **semanal**, **mensal** ou **nenhuma**.

Você pode suprimir a _data de término do período de cobertura_ e _desabilitar e desprovisionar as notificações de data de término do ciclo de vida se aproximando_. Desabilitar as notificações de ciclo de vida suprime as notificações sobre o período de cobertura e a data de término do contrato.

## <a name="azure-sponsorship-offer"></a>Oferta do Azure Sponsorship

A oferta do Azure Sponsorship é uma conta Microsoft Azure limitada patrocinada. Ela está disponível como um convite por email, somente para clientes limitados selecionados pela Microsoft. Se estiver qualificado para a oferta do Microsoft Azure Sponsorship, você receberá um convite por email para aceitar a oferta para sua ID de conta.

Para saber mais, crie uma [solicitação de suporte para a ativação do patrocínio](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Conversão para autenticação da conta corporativa ou de estudante

Os usuários do Azure Enterprise podem converter um tipo de autenticação de conta Microsoft (MSA ou Live ID) em Conta Corporativa ou de Estudante (que usa o Azure Active Directory).

Para começar:

1. adicione a conta corporativa ou de estudante ao Portal do EA do Azure na função necessária.
1. Se receber erros, é possível que a conta não seja válida no Active Directory.  O Azure usa o nome UPN, que nem sempre é idêntico ao endereço de email.
1. Autentique-se no portal do EA do Azure usando a conta corporativa ou de estudante.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Para converter subscrições de contas Microsoft em contas corporativas ou de estudante:

1. Entre no portal de gerenciamento usando a conta Microsoft que tem as assinaturas.
1. Use a transferência de propriedade da conta para mover para a nova conta.
1. A conta Microsoft já deve estar livre de todas as assinaturas ativas e pode ser excluída.
1. Todas as contas excluídas ficarão no modo de exibição do portal com status inativo para fins de cobrança histórica.  Para filtrá-las no modo de exibição, marque a caixa de seleção para mostrar apenas as contas ativas.

## <a name="account-subscription-ownership-faq"></a>Perguntas frequentes sobre a propriedade da assinatura da conta

Este documento responde às perguntas frequentes relacionadas à propriedade da assinatura da conta.

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>Posso associar minha conta existente do Azure ao registro do EA do Azure?

Sim. Todas as assinaturas do Azure das quais você é o proprietário da conta serão convertidas para o Contrato Enterprise. Estão incluídas assinaturas que usam crédito mensal, como Visual Studio, AzurePass, MPN, BizSpark e muito mais. Você perderá o crédito mensal ao converter essas assinaturas.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Quantos proprietários da conta do Azure você pode ter por assinatura?

Somente um proprietário de conta é permitido por assinatura.  Outras funções podem ser adicionadas por meio do acesso baseado em função ou do IAM (Controle de Acesso) na guia da assinatura no canto superior esquerdo da página do [portal do Azure](https://portal.azure.com).

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>É possível transferir a propriedade de assinatura para outra conta?

Sim, você pode transferir a propriedade de assinatura para uma conta diferente. Por exemplo, se uma Conta A tiver três assinaturas, o administrador corporativo poderá transferir uma assinatura para a Conta B, uma para a Conta C e outra para a Conta D ou pode transferir todas as assinaturas para a Conta E.

Para transferir assinaturas:

1. No Azure Enterprise Portal, selecione **Gerenciar** > **Conta**.
1. Passe o mouse sobre **Conta** na extrema direita para ver as opções **Transferir Propriedade** (ícone de pessoa) e **Transferir Assinatura** (ícone de lista). Essas opções só ficam visíveis para contas ativas.

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Um proprietário de conta do Azure pode ser listado em mais de um departamento?

Não. Um proprietário da conta só pode estar associado a um departamento individual. A política ajuda a garantir o monitoramento preciso e a repartição dos custos e dos gastos associados ao departamento ao qual o proprietário está alinhado no registro do EA no Portal do EA do Azure.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Um proprietário de conta do Azure pode ser listado como um grupo de segurança?

Não, um proprietário de assinatura deve ter uma autenticação exclusiva da MSA (conta Microsoft) ou do Azure AD (Active Directory). Para explicar a sucessão em sua organização, considere criar contas genéricas e usar o Azure AD para gerenciar o acesso à assinatura.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Um usuário individual pode ter várias assinaturas?

Um proprietário de conta do Azure pode criar e gerenciar um número ilimitado de assinaturas.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Como posso acessar/ver todas as assinaturas da minha organização?

Atualmente, isso deve ser feito pela política, ou seja, você precisará exigir que, para cada assinatura criada, sua conta seja adicionada a uma função de assinatura por meio do acesso baseado em função.

### <a name="where-do-i-go-to-create-a-subscription"></a>Onde posso criar uma assinatura?

Antes de criar uma assinatura de oferta do Azure Enterprise (EA), sua conta precisará ser adicionada à função de proprietário da conta pelo administrador do registro do EA no Portal do EA do Azure. Em seguida, você precisará entrar no Portal do EA do Azure para obter o direito de criar assinaturas do tipo de oferta do EA. Recomendamos que a sua primeira assinatura do EA seja criada por meio do link '+ Adicionar Assinatura' na guia de assinaturas no Portal do EA.  No entanto, depois que a conta estiver autorizada, é provável que fique mais fácil criar assinaturas em portal.azure.com na guia de assinaturas no canto superior esquerdo da página. Lá, você poderá criar e renomear sua assinatura em uma única etapa.

### <a name="who-can-create-a-subscription"></a>Quem pode criar uma assinatura?

Para criar uma assinatura do tipo de oferta do Azure Enterprise, você deve estar qualificado na função de proprietário da conta no [portal do EA](https://ea.azure.com).

## <a name="azure-ea-term-glossary"></a>Glossário de termos do EA do Azure

- **Conta**: uma unidade organizacional no Azure Enterprise Portal. Ela é usada para administrar assinaturas e para relatórios.
- **Proprietário da conta**: a pessoa que gerencia assinaturas e administradores de serviço no Azure. Ela pode exibir dados de uso nessa conta e suas assinaturas associadas.
- **Assinatura de aditamento**: uma assinatura coincidente ou de um ano sob o aditamento do registro.
- **Pagamento antecipado**: Pagamento antecipado de um valor monetário anual de serviços do Azure a uma tarifa de Pagamento antecipado reduzida para uso em relação a esse pagamento antecipado.
- **Administrador de departamento**: a pessoa que gerencia departamentos, cria contas e proprietários de contas, exibe detalhes de uso dos departamentos que gerencia e pode exibir custos quando permissões forem concedidas.
- **Número de registro**: um identificador exclusivo fornecido pela Microsoft para identificar o registro específico associado a um Contrato Enterprise.
- **Administrador corporativo**: a pessoa que gerencia departamentos, proprietários de departamentos, contas e proprietários de contas no Azure. Ela tem a capacidade de gerenciar administradores corporativos e exibir dados de uso, quantidades cobradas e encargos não cobrados em todas as contas e assinaturas associadas ao registro corporativo.
- **Contrato Enterprise**: um contrato de licenciamento da Microsoft para clientes com compras centralizadas que desejam padronizar toda a organização com a tecnologia da Microsoft e manter uma infraestrutura de tecnologia da informação com um padrão de software da Microsoft.
- **Registro do contrato Enterprise**: um registro no programa de Contrato Enterprise que fornece produtos da Microsoft em volume com tarifas reduzidas.
- **Conta Microsoft**: um serviço online que permite que sites participantes autentiquem um usuário com um conjunto de credenciais.
- **Aditamento do Registro do Microsoft Azure Enterprise (aditamento do registro)** : um aditamento assinado por uma empresa que fornece acesso ao Azure como parte de seu Registro Enterprise.
- **Azure Enterprise Portal**: o portal usado por nossos clientes empresariais para gerenciar suas contas do Azure e as assinaturas relacionadas.
- **Quantidade de recursos consumidos**: a quantidade de um serviço individual do Azure utilizado em um mês.
- **Administrador de serviços**: a pessoa que acessa e gerencia assinaturas e projetos de desenvolvimento no Azure Enterprise Portal.
- **Assinatura**: representa uma assinatura do Azure Enterprise Portal e é um contêiner de serviços do Azure gerenciados pelo mesmo administrador de serviços.
- **Conta corporativa ou de estudante**: Para organizações que configuraram o Azure Active Directory com federação para a nuvem e todas as contas estão em um locatário.

### <a name="enrollment-statuses"></a>Status de registro

- **Novo**: Esse status é atribuído a um registro criado dentro de 24 horas e será atualizado para um status pendente dentro de 24 horas.
- **Pendente**: o administrador do registro precisa entrar no Azure Enterprise Portal. Depois de conectado, o registro mudará para o status Ativo.
- **Ativa**: o registro está Ativo e as contas e assinaturas podem ser criadas no Azure Enterprise Portal. O registro permanecerá ativo até a data de término do Contrato Enterprise.
- **Termo estendido indefinido**: o status de termo estendido indefinido ocorre após a data de término do Contrato Enterprise. Ele permite que os clientes do EA do Azure que optaram pelo período estendido continuem a usar os serviços do Azure indefinidamente no final do Contrato Enterprise.

   Antes que o registro do EA do Azure atinja a data de término do Contrato Enterprise, o administrador do registro deve decidir dentre as seguintes opções:

  - Renovar o registro adicionando outro Pagamento antecipado do Azure.
  - Transferir para um novo registro.
  - Migrar para o MOSP (Programa de Assinatura do Microsoft Online).
  - Confirmar a desabilitação de todos os serviços associados ao registro.
- **Expirado**: o cliente do EA do Azure é recusado do período estendido e o registro de EA do Azure atingiu a data de término do Contrato Enterprise. O registro expirará e todos os serviços associados serão desabilitados.
- **Transferidos**: registros em que todos os serviços e contas associados foram transferidos para um novo registro são exibidos com o status Transferidos.
  >[!NOTE]
  > Os registros não são transferidos automaticamente se um novo número de registro é gerado na renovação. Você deve incluir o número de registro anterior na sua documentação de renovação para facilitar uma transferência automática.

## <a name="next-steps"></a>Próximas etapas

- Leia sobre como as [reservas de máquina virtual](ea-portal-vm-reservations.md) podem ajudá-lo a economizar dinheiro.
- Se você precisar de ajuda para solucionar problemas do Portal do EA do Azure, confira [Solucionar problemas de acesso ao Portal do EA do Azure](ea-portal-troubleshoot.md).