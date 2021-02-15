---
author: georgewallace
ms.service: multiple
ms.topic: include
ms.date: 11/25/2018
ms.author: gwallace
ms.openlocfilehash: 5bc00f4de95d22eec71f9b1b2504b00f506232dc
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99213745"
---
### <a name="to-sign-up-for-a-sendgrid-account"></a>Para se inscrever em uma conta do SendGrid
1. Entre no [portal do Azure][Azure portal].
2. No menu portal do Azure ou no home page, selecione **criar um recurso**.

    ![Captura de tela do menu portal do Azure com a opção criar um recurso selecionada.][command-bar-new]
3. Procure e selecione **SendGrid**.

    ![Captura da tela do portal do Azure Marketplace mostrando "SendGr" na caixa de pesquisa e SendGrid selecionados nos resultados da pesquisa.][sendgrid-store]
4. Preencha o formulário de inscrição e selecione **Criar**.

    ![Captura de tela da caixa de diálogo criar uma nova conta do SendGrid com os campos nome, senha, assinatura e grupo de recursos preenchidos.][sendgrid-create]
5. Insira um **Nome** para identificar o serviço SendGrid nas suas configurações do Azure. Os nomes devem ter entre 1 e 100 caracteres e conter somente caracteres alfanuméricos, traços, pontos e caracteres de sublinhado. O nome deve ser exclusivo na sua lista de itens inscritos da Azure Store.
6. Insira e confirme sua **Senha**.
7. Escolha sua **Assinatura**.
8. Crie um novo **Grupo de recursos** ou use um grupo existente.
9. Na seção **Tipo de preço** selecione o plano do SendGrid no qual deseja se inscrever.

    ![Captura de tela da caixa de diálogo criar uma nova conta do SendGrid com a seção escolher o tipo de preço aberta][sendgrid-pricing]
10. Insira um **Código de Promoção**, se você tiver um.
11. Insira suas **informações de contato**.
12. Examine e aceite os **termos legais**.
13. Depois de confirmar sua compra, você verá um pop-up de **implantação com êxito** e verá sua conta listada.

    ![Captura de tela da página contas do SendGrid mostrando a nova conta ContosoSendGrid listada.][all-resources]

    Depois de concluir a compra e clicar no botão **Gerenciar** para iniciar o processo de verificação de email, você receberá um email do SendGrid, pedindo para você verificar sua conta. Se você não receber este email ou tiver problemas para verificar sua conta, consulte nossas perguntas frequentes.

    ![Captura de tela da página da conta do ContosoSendGrid com o botão gerenciar realçado.][manage]

    **Você só pode enviar até 100 emails por dia antes de verificar sua conta.**

    Para modificar o plano de assinatura ou consultar as configurações de contato do SendGrid, clique no nome do serviço do SendGrid para abrir o painel SendGrid Marketplace.

    ![Captura de tela mostrando que a página de configurações da conta ContosoSendGrid é aberta selecionando todas as configurações na página conta do ContosoSendGrid.][settings]

    Para enviar um email usando o SendGrid, você deve fornecer a sua chave de API.

### <a name="to-find-your-sendgrid-api-key"></a>Para encontrar a sua chave de API do SendGrid
1. Clique em **Gerenciar**.

    ![Captura de tela da página da conta do ContosoSendGrid com o botão gerenciar realçado.][manage]
2. No painel do SendGrid, selecione **Configurações** e, em seguida, **Chaves de API** no menu à esquerda.

    ![Captura de tela do painel do SendGrid com as configurações lista suspensa abertas e chaves de API selecionadas.][api-keys]

3. Clique em **Criar chave de API**.

    ![Captura da tela de chaves de API com o botão Criar chave de API selecionado.][general-api-key]
4. Forneça, pelo menos, o **Nome dessa chave** e forneça acesso completo à **Enviar Email** e selecione **Salvar**.

    ![Instantâneo da tela Adicionar nova chave de API geral com envio de email definido para acesso completo, envios agendados definido como sem acesso e o botão salvar realçado.][access]
5. Sua API será exibida nesse ponto uma vez. Certifique-se de armazená-la com segurança.

### <a name="to-find-your-sendgrid-credentials"></a>Para localizar suas credenciais do SendGrid
1. Clique no ícone de chave para localizar seu **Nome de usuário**.

    ![Captura de tela da página da conta do ContosoSendGrid com o ícone de chave realçado.][key]
2. A senha é a que você escolheu durante a instalação. Você pode selecionar **Alterar senha** ou **Redefinir senha** para fazer alterações.

Para gerenciar suas configurações de entrega de email, clique no **botão Gerenciar**. Você será redirecionado para o painel do SendGrid.

![Captura de tela da página da conta do ContosoSendGrid com o botão gerenciar realçado.][manage]

Para obter mais informações sobre o envio de email por meio do SendGrid, visite a [Visão Geral da API Email][Email API Overview].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/new-addon.png
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid-store.png
[sendgrid-create]: ./media/sendgrid-sign-up/sendgrid-create.png
[sendgrid-pricing]: ./media/sendgrid-sign-up/sendgrid-pricing.png
[all-resources]: ./media/sendgrid-sign-up/all-resources.png
[manage]: ./media/sendgrid-sign-up/manage.png
[settings]: ./media/sendgrid-sign-up/settings.png
[api-keys]: ./media/sendgrid-sign-up/api-keys.png
[general-api-key]: ./media/sendgrid-sign-up/general-api-key.png
[access]: ./media/sendgrid-sign-up/access.png
[key]: ./media/sendgrid-sign-up/key.png

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure portal]: https://portal.azure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
