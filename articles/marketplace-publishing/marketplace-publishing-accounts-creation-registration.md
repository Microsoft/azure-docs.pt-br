---
title: "Criação e registro da conta do editor | Microsoft Docs"
description: "Instruções para criar uma conta de Desenvolvedor da Microsoft para que, mediante aprovação, seja possível vender vários tipos de ofertas no Azure Marketplace."
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5a2fe68d-2967-463f-8af6-42bed07e3eaa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: hascipio
ms.openlocfilehash: 642e4a2d11ef5a92f5ab46bc4872414966b04c0d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-microsoft-developer-account"></a>Criar uma conta de Desenvolvedor da Microsoft
Este artigo fornece uma orientação sobre o processo necessário de criação e registro de uma conta para se tornar um Desenvolvedor da Microsoft aprovado para o Azure Marketplace.

## <a name="1-create-a-microsoft-account"></a>1. Criar uma conta da Microsoft
Para iniciar o processo de publicação, será necessário criar uma conta da Microsoft. Essa conta será usada para fazer o registro no **Centro de Desenvolvimento da Microsoft** e no **Portal de Publicação do Azure**. Você deve ter apenas uma conta da Microsoft para suas ofertas do Azure Marketplace. Ela não deve ser específica a serviços ou ofertas.

O endereço que forma o nome de usuário deve estar no seu domínio e ser controlado por sua equipe de TI. Todas as atividades de publicação relacionadas devem ser feitas por meio dessa conta.

> [!WARNING]
> Palavras como **"Azure"** e **"Microsoft"** não têm suporte para o registro da conta da Microsoft. Evite usar essas palavras para concluir a criação da conta e o processo de registro.
>
>

### <a name="guidelines-for-company-accounts"></a>Diretrizes para contas da empresa
Ao criar uma conta da empresa, siga estas diretrizes se mais de uma pessoa precisar acessar a conta ao efetuar logon com a conta da Microsoft que abriu a conta.

> [!Important]
> Importante: para permitir que vários usuários acessem sua conta do Centro de Desenvolvimento, é recomendável usar o Azure Active Directory para atribuir funções a usuários individuais, que podem acessar a conta entrando com as respectivas credenciais individuais do Azure AD. Para obter mais informações, confira [Gerenciar usuários de contas](https://msdn.microsoft.com/en-us/windows/uwp/publish/manage-account-users).

* Crie sua conta da Microsoft usando um endereço de email que pertença ao domínio da empresa, mas não a um único indivíduo; por exemplo, windowsapps@fabrikam.com.
* Limite o acesso a essa conta da Microsoft ao menor número possível de desenvolvedores.
* Configure uma lista de distribuição por email corporativo que inclua cada pessoa que precisar acessar a conta do desenvolvedor e adicione esse endereço de email às informações de segurança. Isso permite que todos os funcionários da lista recebam códigos de segurança quando necessário e gerenciem as informações de segurança da respectiva conta da Microsoft. Se não for possível configurar uma lista de distribuição, o proprietário da conta de email individual precisará estar disponível para acessar e compartilhar o código de segurança quando solicitado (por exemplo, quando novas informações de segurança são adicionadas à conta ou quando ela precisar ser acessada de um novo dispositivo).
* Adicione um número de telefone da empresa que não exija um ramal e possa ser acessado pelos principais membros da equipe.
* De modo geral, os desenvolvedores usam dispositivos confiáveis para entrar na conta de desenvolvedor da empresa. Todos os principais membros da empresa devem ter acesso a esses dispositivos confiáveis. Isso reduzirá a necessidade de códigos de segurança que precisam ser enviados durante o acesso à conta.
* Se precisar permitir acesso à conta de um PC não confiável, limite esse acesso a um máximo de cinco desenvolvedores. De modo ideal, esses desenvolvedores devem acessar a conta de computadores que compartilham o mesmo local geográfico e de rede.
* Com frequência, revise as informações de segurança da empresa em [https://account.live.com/proofs/Manage](https://account.live.com/proofs/Manage) para garantir que tudo esteja atualizado.

Sua conta de desenvolvedor deve ser acessada basicamente de PCs confiáveis. Isso é essencial, pois há um limite para o número de códigos gerados por conta, por semana. Além de garantir uma experiência de logon mais eficiente.

Para obter mais informações sobre diretrizes de conta de desenvolvedor adicional e segurança, clique [aqui](https://msdn.microsoft.com/en-us/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="instructions"></a>Instruções
1. Abra uma nova sessão de Janela anônima do Chrome ou InPrivate do Internet Explorer para garantir que você não esteja conectado a uma conta existente.
2. Registre o email (de acordo com as diretrizes acima, por exemplo, windowsapp@fabrikam.com) como uma conta da Microsoft usando o link [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx). Siga as instruções abaixo.

   1. Ao registrar sua conta como uma conta da Microsoft, você deverá fornecer um número de telefone válido para que o sistema envie um código de verificação de conta como uma mensagem de texto ou uma chamada automática.
   2. Durante o registro de sua conta como uma conta da Microsoft, você precisará fornecer uma identificação de email válida para receber um email automatizado para verificação da conta.
3. Verifique o endereço de email enviado à DL.
4. Agora, você está pronto para usar uma nova conta da Microsoft no Centro de Desenvolvimento da Microsoft.

## <a name="2-register-your-account-in-microsoft-developer-center"></a>2. Registrar sua conta na Central de Desenvolvedores da Microsoft
O Centro de Desenvolvimento da Microsoft é usada para registrar as informações da empresa uma vez. O inscrito deve ser um representante válido da empresa e fornecer suas informações pessoais como uma maneira de validar sua identidade. A pessoa que fizer o registro deverá usar uma conta da Microsoft que seja compartilhada com a empresa **e a mesma conta deve ser usada no Portal de Publicação do Azure.** Você deve verificar se sua empresa ainda não tem uma conta do Centro de Desenvolvimento da Microsoft antes de tentar criar uma. Durante o processo, coletaremos informações sobre o endereço da empresa, a conta bancária e as informações fiscais. Esses dados são geralmente obtidos por meio de contatos de finanças ou negócios.

> [!IMPORTANT]
> Você deve completar os seguintes componentes de perfil do Desenvolvedor para prosseguir pelas diversas fases da criação e implantação da oferta.
>
>

| Perfil do desenvolvedor | Para iniciar o rascunho | Staging | Publicar modelo gratuito e de solução | Publicar comercial |
| --- | --- | --- | --- | --- |
| Registro da empresa |Imprescindível |Imprescindível |Imprescindível |Imprescindível |
| ID do perfil fiscal |Opcional |Opcional |Opcional |Imprescindível |
| Conta bancária |Opcional |Opcional |Opcional |Imprescindível |

> [!NOTE]
> Há suporte para BYOL (Traga sua Própria Licença) apenas para máquinas virtuais, e é considerado uma oferta **gratuita** .
>
>

### <a name="register-your-company-account"></a>Registrar sua conta da empresa
1. Abra uma nova sessão de Janela anônima do Chrome ou InPrivate do Internet Explorer para garantir que você não esteja conectado a uma conta pessoal.
2. Acesse [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure) para registrar-se como um vendedor no Centro de Desenvolvimento. Leia a observação importante a seguir antes de continuar.

   > [!IMPORTANT]
   > Não deixe de registrar como uma conta da Microsoft a identificação de email ou a lista de distribuição (uma lista de distribuição é recomendável para remover a dependência de indivíduos) que você usará para registrar-se no Centro de Desenvolvimento. Caso contrário, faça esse registro usando este [link](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1). Além disso, **não é permitido o uso de qualquer identificação de email do domínio da empresa Microsoft, isto é, @microsoft.com**, para o registro no Centro de Desenvolvimento.
   >
   >

    ![desenho][img-signin]
3. Conclua o assistente "Ajude-nos a proteger sua conta", que vai verificar sua identidade através do número de telefone ou endereço de email.

    ![desenho][img-verify]
4. Na seção "Informações de Registro de Conta", selecione o **País/região da conta** no menu suspenso.

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_04.png)

   > [!WARNING]
   > **Países para “Origem de venda”:** para vender seus serviços no Azure Marketplace, sua entidade registrada precisa ser de um dos países aprovados para "origem de venda". Essa restrição é por razões de pagamento e tributação. Estamos considerando a expansão dessa lista de países em um futuro próximo, portanto, fique atento. Para saber mais, confira as [políticas de participação no Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).
   >
   >
5. Selecione o "Tipo de Conta" como **Empresa** e clique no botão **Avançar**.

   > [!IMPORTANT]
   > Para entender melhor os tipos de conta e qual é o melhor para você, consulte a página [Tipos de conta, locais e tarifas](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)
   >
   >

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_05.png)
6. Insira o **Nome de exibição do publicador**, normalmente o nome de sua empresa.

   > [!TIP]
   > O nome de exibição do editor inserido no Centro de Desenvolvimento não será exibido no Azure Marketplace depois que sua oferta for listada. Mas isso deve ser preenchido para concluir o processo de registro.
   >
   >
7. Insira as **Informações de contato** para a verificação da conta.

   > [!IMPORTANT]
   > Você deve fornecer informações de contato precisas, pois elas serão usadas em nosso processo de verificação para que sua empresa seja aprovada no Centro de Desenvolvimento.
   >
   >
8. Insira as informações de contato do **Aprovador da Empresa**. O aprovador da empresa é a pessoa que pode verificar que você está autorizado a criar uma conta no Centro de Desenvolvimento em nome de sua organização. Quando terminar, clique em **Avançar** para passar para a seção de **Pagamento**.

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_08.png)
9. Insira suas informações de pagamento para pagar por sua conta. Se você tiver um código promocional que cubra o custo do registro, insira-o aqui. Caso contrário, forneça as informações de seu cartão de crédito (ou do PayPal nos mercados com suporte). Quando tiver terminado, clique em **Próximo** para chegar à **"tela de Revisão"**.

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_09.png)
10. Revise as informações de sua conta e confirme que tudo está correto. Em seguida, leia e aceite os termos e condições do [Contrato de Publicador do Microsoft Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=699560). Marque a caixa para indicar que você leu e aceitou os termos.
11. Clique em **Concluir** para confirmar seu registro. Enviaremos uma mensagem de confirmação ao seu endereço de email.
12. Se você estiver planejando publicar somente ofertas gratuitas, clique em **Ir para o Portal de Publicação do Azure Marketplace** e ignore a seção 3 deste documento, [Registrar sua conta no portal de publicação](#3-register-your-account-in-the-publishing-portal).

Se você estiver planejando publicar ofertas comerciais (por exemplo, ofertas de Máquina Virtual com um modelo de cobrança por hora), clique em **Atualizar as informações de sua conta** e preencha as informações bancárias e tributárias em sua conta do Centro de Desenvolvimento.

Se preferir atualizar suas informações fiscais e bancárias posteriormente, vá para a próxima seção (seção 3) deste documento, [Registrar sua conta no portal de Publicação](#3-register-your-account-in-the-publishing-portal), e volte mais tarde usando os links no Portal de Publicação do Azure.

> [!IMPORTANT]
> No caso de ofertas comerciais, você não poderá enviar suas ofertas comerciais por push para produção sem preencher as informações tributárias e da conta bancária.
>
>

Se você preferir atualizar posteriormente suas informações fiscais e bancárias, vá para a seção 3, [Registrar sua conta no portal de publicação](#3-register-your-account-in-the-publishing-portal)e volte mais tarde usando os links no Portal de Publicação do Azure.

### <a name="add-tax-and-banking-information"></a>Adicionar informações sobre impostos e dados bancários
 Se você quiser publicar ofertas comerciais para compra, também será preciso adicionar informações de pagamento e informações sobre impostos, e enviá-las para validação no Centro de Desenvolvimento da Microsoft. Se você publicará apenas ofertas grátis (ou ofertas BYOL), não é necessário adicionar essas informações. Você pode adicioná-las mais tarde, mas leva algum tempo para validar as informações sobre impostos. Se você souber que irá oferecer ofertas comerciais para compra, recomendamos adicioná-las assim que possível.

**Informações bancárias**

1. Entre no [Centro de Desenvolvimento da Microsoft](http://dev.windows.com/registration?accountprogram=azure) com sua conta da Microsoft.
2. Clique em **Conta de pagamento** no menu à esquerda, em **Escolher método de pagamento** clique em **Conta bancária** ou em **PayPal**.

   > [!IMPORTANT]
   > Se você tiver ofertas comerciais que os clientes compram no Marketplace, essa será a conta em que você receberá o pagamento para essas compras.
   >
   >
3. Insira as informações de pagamento e clique em **Salvar** quando estiver satisfeito.

   > [!IMPORTANT]
   > Se você precisar atualizar ou alterar sua conta de pagamento, execute as mesmas etapas acima, substituindo as informações atuais por novas informações. A alteração da conta de pagamento pode atrasar os pagamentos em até um ciclo de pagamento. Esse atraso ocorre porque precisamos confirmar a alteração da conta, como fizemos na primeira configuração da conta de pagamento. Você ainda receberá o valor total após a verificação de sua conta; os pagamentos atrasados do ciclo de pagamento atual serão adicionados ao próximo.
   >
   >
4. Clique em **Avançar**.

**Informações fiscais**

1. Entre no [Centro de Desenvolvimento da Microsoft](http://dev.windows.com/registration?accountprogram=azure) com sua conta da Microsoft (se for necessário).
2. Clique em **Perfil fiscal** no menu à esquerda.
3. Na página **Configure seu formulário de imposto** , selecione o país ou região onde reside de forma permanente e, em seguida, selecione o país ou região onde você mantém cidadania principal. Clique em **Avançar**.
4. Insira seus detalhes fiscais e, em seguida, clique em **Próximo**.

> [!WARNING]
> Você não poderá enviar suas ofertas comerciais por push para produção sem preencher as informações sobre impostos e os dados de conta bancária em sua conta do Centro de Desenvolvimento da Microsoft.
>
>

Se você tiver problemas com o registro no Centro de Desenvolvedores, registre um tíquete de suporte como mostrado abaixo

1. Acesse o link de suporte [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. Na seção **Fale Conosco**, clique no botão **Enviar um incidente** (como é mostrado na captura de tela abaixo)

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgAddTax_02.png)
3. Escolha "Ajuda com o Centro de Desenvolvimento" como **Tipo de problema** e "Publicar e gerenciar aplicativos" como **Categoria**. Depois disso, clique no botão "Iniciar email".

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgAddTax_03.png)
4. Será exibida uma página de logon. Use qualquer logon de conta da Microsoft. Se você não tiver uma conta da Microsoft, crie uma usando este [link](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
5. Preencha os detalhes do problema e envie o tíquete clicando no botão **Enviar** .

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-publishing-portal"></a>3. Registrar sua conta no portal de publicação
O [portal de Publicação](http://publish.windowsazure.com) é usado para publicar e gerenciar suas ofertas.

1. Abra uma nova sessão de Janela anônima do Chrome ou navegação InPrivate do Internet Explorer para garantir que você não esteja conectado a uma conta pessoal.
2. Acesse [http://publish.windowsazure.com](http://publish.windowsazure.com).
3. Se você for um novo usuário e estiver entrando no portal de Publicação pela primeira vez, entre com a mesma identificação de email com o qual sua conta do Centro de Desenvolvimento foi registrada. Dessa forma, sua conta do Centro de Desenvolvimento e sua conta do portal de Publicação serão vinculadas. Posteriormente, você poderá adicionar os outros membros da empresa que estão trabalhando no aplicativo como coadministradores no portal de Publicação seguindo as etapas abaixo.

Se você for adicionado como coadministrador ao portal de Publicação, será possível entrar com sua conta de coadministrador.

> [!TIP]
> As políticas de participação são descritas no [site do Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
>
>

## <a name="4-steps-to-add-a-co-admin-in-the-publishing-portal"></a>4. Etapas para adicionar um coadministrador no portal de Publicação
**Supondo que você seja o administrador** , abaixo estão as etapas para adicionar um coadministrador.

> [!NOTE]
> **No caso de novos usuários** , antes de adicionar um coadministrador ao portal de Publicação, verifique se você criou pelo menos um aplicativo no portal de Publicação. Isso é obrigatório, pois a guia **EDITORES** aparece apenas após a criação de pelo menos um aplicativo no portal de Publicação.
>
>

1. Certifique-se de que a identificação de email do coadministrador seja uma conta da Microsoft (MSA). Caso não seja, registre-a como uma MSA usando este [link](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Certifique-se de que haja pelo menos um aplicativo na conta de administrador antes de tentar adicionar um coadministrador.
3. Depois de concluir as etapas acima, faça logon no portal de Publicação com a identificação de email de coadministrador e faça logoff.
4. Agora, faça logon no portal de Publicação com a identificação de email de administrador.
5. Navegue até Editores -> selecione sua conta -> Administradores -> Adicionar o coadministrador (captura de tela abaixo)

   ![desenho](media/marketplace-publishing-accounts-creation-registration/imgAddAdmin_05.png)

## <a name="5-steps-to-delete-a-co-admin-in-the-publishing-portal"></a>5. Etapas para excluir um coadministrador no portal de Publicação
**Supondo que você seja o administrador** , abaixo estão as etapas para excluir um coadministrador.

1. Faça logon no portal de Publicação com a identificação de email de administrador.
2. Navegue até **Editores** -> selecione sua conta -> **Administradores** -> **Coadministradores**.
3. Clique no botão **X** ao lado do coadministrador que você quer excluir (captura de tela abaixo).

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgDeleteAdmin_03.png)

## <a name="next-steps"></a>Próximas etapas
Agora que sua conta foi criada e registrada, cumpra ou atenda a todos os pré-requisitos não técnicos para publicar sua oferta conferindo [Pré-requisitos não técnicos](marketplace-publishing-pre-requisites.md).

## <a name="see-also"></a>Consulte também
* [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]:marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]:marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]:marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]:marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]:marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]:marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]:marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]:marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]:marketplace-publishing-push-to-staging.md
