---
author: erikre
ms.service: multiple
ms.topic: include
ms.date: 11/25/2018
ms.author: erikre
ms.openlocfilehash: 96c4da8465a87fee4c00bfc6177515c94910704a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60444643"
---
Os clientes do Azure podem desbloquear 25.000 e-mails livres por mês. Esses 25.000 emails mensais gratuitos lhe darão acesso a relatórios e análises avançados e a [todas as APIs][all APIs] (Web, SMTP, Evento, Análise e mais). Para obter informações sobre os serviços adicionais fornecidos por SendGrid, visite a página [Soluções do SendGrid][SendGrid Solutions].

### <a name="to-sign-up-for-a-sendgrid-account"></a>Para se inscrever em uma conta do SendGrid
1. Entre no [Portal do Azure][Azure portal].
2. No menu à esquerda, clique em **Criar um recurso**.

    ![command-bar-new][command-bar-new]
3. Clique em **Complementos** e **Entrega de Email SendGrid**.

    ![sendgrid-store][sendgrid-store]
4. Preencha o formulário de inscrição e selecione **Criar**.

    ![sendgrid-create][sendgrid-create]
5. Insira um **Nome** para identificar o serviço SendGrid nas suas configurações do Azure. Os nomes devem ter entre 1 e 100 caracteres e conter somente caracteres alfanuméricos, traços, pontos e caracteres de sublinhado. O nome deve ser exclusivo na sua lista de itens inscritos da Azure Store.
6. Insira e confirme sua **Senha**.
7. Escolha sua **Assinatura**.
8. Crie um novo **Grupo de recursos** ou use um grupo existente.
9. Na seção **Tipo de preço** selecione o plano do SendGrid no qual deseja se inscrever.

    ![sendgrid-pricing][sendgrid-pricing]
10. Insira um **Código de Promoção**, se você tiver um.
11. Insira suas **Informações de Contato**.
12. Examine e aceite os **Termos legais**.
13. Depois de confirmar sua compra você verá um pop-up de **Implantação com êxito** e verá sua conta listada na seção **Todos os recursos**.

    ![all-resources][all-resources]

    Depois de concluir a compra e clicar no botão **Gerenciar** para iniciar o processo de verificação de email, você receberá um email do SendGrid, pedindo para você verificar sua conta. Se você não receber esse email ou tiver problemas para verificar a sua conta, consulte estas perguntas frequentes.

    ![gerenciar][manage]

    **Você só pode enviar até 100 emails por dia antes de verificar sua conta.**

    Para modificar o plano de assinatura ou consultar as configurações de contato do SendGrid, clique no nome do serviço do SendGrid para abrir o painel SendGrid Marketplace.

    ![configurações][settings]

    Para enviar um email usando o SendGrid, você deve fornecer a sua chave de API.

### <a name="to-find-your-sendgrid-api-key"></a>Para encontrar a sua chave de API do SendGrid
1. Clique em **Gerenciar**.

    ![gerenciar][manage]
2. No painel do SendGrid, selecione **Configurações** e, em seguida, **Chaves de API** no menu à esquerda.

    ![api-keys][api-keys]

3. Clique em **Criar chave de API**.

    ![general-api-key][general-api-key]
4. Forneça, pelo menos, o **Nome dessa chave** e forneça acesso completo à **Enviar Email** e selecione **Salvar**.

    ![access][access]
5. Sua API será exibida nesse ponto uma vez. Certifique-se de armazená-la com segurança.

### <a name="to-find-your-sendgrid-credentials"></a>Para localizar suas credenciais do SendGrid
1. Clique no ícone de chave para localizar seu **Nome de usuário**.

    ![chave][key]
2. A senha é a que você escolheu durante a instalação. Você pode selecionar **Alterar senha** ou **Redefinir senha** para fazer alterações.

Para gerenciar suas configurações de entrega de email, clique no **botão Gerenciar**. Você será redirecionado para o painel do SendGrid.

![gerenciar][manage]

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
