---
title: Solucionar problemas de acesso ao Portal do EA do Azure
description: Este artigo descreve alguns problemas comuns que podem ocorrer com um EA (Contrato Enterprise do Azure) no Portal do EA do Azure.
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: troubleshooting
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 5402ee3283e5ae11f43884b4002486e8bdefae2c
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89294400"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>Solucionar problemas de acesso ao Portal do EA do Azure

Este artigo descreve alguns problemas comuns que podem ocorrer com um EA (Contrato Enterprise do Azure). O Portal do EA do Azure é usado para gerenciar os custos e usuários do Contrato Enterprise. Você pode ter esses problemas quando estiver configurando ou atualizando o acesso ao Portal do EA do Azure.

## <a name="issues-adding-an-admin-to-an-enrollment"></a>Problemas ao adicionar um administrador a um registro

Há diferentes tipos de níveis de autenticação para Registros Enterprise. Quando os níveis de autenticação são aplicados incorretamente, você pode ter problemas ao tentar entrar no Portal do EA do Azure.

Você usa o Portal do EA do Azure para permitir acesso a usuários com níveis de autenticação diferentes. Um administrador corporativo pode atualizar o nível de autenticação para atender aos requisitos de segurança da organização dele.

### <a name="authentication-level-types"></a>Tipos de nível de autenticação

- Somente conta Microsoft – para organizações que desejam usar, criar e gerenciar usuários por meio de contas Microsoft.
- Conta corporativa ou de estudante – para organizações que configuraram Active Directory com federação para a nuvem e todas as contas estão em um único locatário.
- Conta corporativa ou de estudante entre locatários – para organizações que configuraram Active Directory com federação para a nuvem e terão contas em vários locatários.
- Conta mista – permite que você adicione usuários com conta Microsoft e/ou com uma conta corporativa ou de estudante.

A primeira conta corporativa ou de estudante adicionada ao registro determina o domínio _padrão_. Para adicionar uma conta corporativa ou de estudante com outro locatário, você precisa alterar o nível de autenticação no registro para autenticação entre locatários.

Para atualizar o nível de autenticação:

1. Entrar no Portal do EA do Azure como um administrador corporativo.
2. Clique em **Gerenciar** no painel de navegação à esquerda.
3. Clique na guia **Registro**.
4. Em **Detalhes do Registro**, selecione **Nível de Autenticação**.
5. Clique no símbolo de lápis.
6. Clique em **Save** (Salvar).

![Exemplo mostrando níveis de autenticação ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

As contas Microsoft precisam ter uma ID associada criada em [https://signup.live.com](https://signup.live.com/).

Contas corporativas ou de estudante estão disponíveis para organizações que configuraram um Active Directory com federação e em que todas as contas estão em um único locatário. Os usuários poderão ser adicionados com a autenticação de usuário federado corporativa ou de estudante se o Active Directory interno da empresa for federado.

Se a sua organização não usar a federação do Active Directory, você não poderá usar seu endereço corporativo ou de estudante. Em vez disso, registre ou crie um novo endereço de email e registre-o como uma conta Microsoft.

## <a name="unable-to-access-the-azure-ea-portal"></a>Não é possível acessar o Portal do EA do Azure

Se você receber uma mensagem de erro ao tentar entrar no Portal do EA do Azure, use as seguintes etapas de solução de problemas:

- Verifique se você está usando a URL correta do Portal do EA do Azure, que é https://ea.azure.com.
- Determine se o acesso ao Portal do EA do Azure foi adicionado como uma conta corporativa ou de estudante ou como uma conta Microsoft.
  - Se você estiver usando sua conta corporativa, insira seu email de trabalho e senha. A senha de trabalho é fornecida pela organização. Se tiver problemas com a senha, você poderá verificar com o departamento de TI sobre como redefini-la.
  - Se você estiver usando uma conta Microsoft, insira seu endereço de email e senha da conta Microsoft. Se você tiver esquecido sua senha da conta Microsoft, poderá redefini-la em [https://account.live.com/password/reset](https://account.live.com/password/reset).
- Use uma sessão de navegador em modo incógnito ou particular para entrar, de modo que nenhum cookie ou informações em cache de sessões anteriores ou existentes sejam mantidos. Limpe o cache do navegador e use uma janela em modo incógnito ou particular para abrir https://ea.azure.com.
- Se você receber um erro de _Usuário Inválido_ ao usar uma conta Microsoft, isso talvez se deva a você ter várias contas Microsoft. Aquela que você está tentando usar para entrar não é o endereço de email principal.
Ou, se você receber um erro de _Usuário Inválido_, poderá ser porque o tipo de conta incorreto foi usado quando o usuário foi adicionado ao registro. Por exemplo, uma conta corporativa ou de estudante em vez de uma conta Microsoft. Neste exemplo, você solicita que outro administrador do EA adicione a conta correta ou há necessidade de entrar em contato com o [suporte](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c).
  - Se você precisar verificar o alias principal, vá para [https://account.live.com](https://account.live.com). Em seguida, clique em **Suas informações** e clique em **Gerenciar como você entra na Microsoft**. Siga os avisos para verificar um endereço de email alternativo e obter um código para acessar informações confidenciais. Digite o código de segurança. Selecione **Configurar mais tarde** se você não quiser configurar a autenticação de dois fatores.
  - Você verá a página **Gerencie a forma como você entra na Microsoft**, na qual poderá exibir seus aliases de conta. Verifique se o alias principal é aquele que você está usando para entrar no Portal do EA do Azure. Se não for, você poderá torná-lo seu alias principal. Ou então, em vez disso, você pode usar o alias principal para o Portal do EA do Azure.

## <a name="azure-ea-activation-faq"></a>Perguntas frequentes sobre a ativação do Azure EA

Esta seção do artigo descreve soluções para problemas comuns relacionados à ativação do Azure EA.

### <a name="i-didnt-receive-an-activation-email"></a>Não recebi um email de ativação

Um email de ativação do Portal do EA do Azure é enviado do *waep@microsoft.com* . Se você não tiver recebido um email de ativação, verifique sua pasta de spam ou lixo eletrônico em busca do email. Ele é enviado com o assunto _Convite para exibir/gerenciar o serviço do Microsoft Azure_. Ele é enviado para cada administrador de EA recém-adicionado.

Se tiver certeza de que foi configurado como o administrador do EA, você não precisará aguardar o recebimento do email de ativação para entrar no Portal do EA do Azure. Em vez disso, você pode acessar https://ea.azure.com e entrar com seu endereço de email (conta Microsoft, corporativa ou de estudante) e senha.

### <a name="i-would-like-to-add-a-new-ea-administrator-to-my-enrollment"></a>Eu gostaria de adicionar um novo administrador do EA ao meu registro

Os administradores corporativos existentes podem adicionar um novo administrador corporativo. Se você é o administrador do EA, entre no portal do EA > clique em **Gerenciar** > clique em **+ Adicionar Administrador** no canto superior direito para adicionar um novo administrador do EA. Verifique se você tem o endereço de email e o método de entrada preferencial da pessoa, por exemplo, autenticação por conta corporativa/de estudante ou Microsoft Live ID, para adicionar o usuário.

Se você não é o Administrador do EA, entre em contato com os administradores do EA de sua empresa para solicitar sua adição ao registro. Depois que eles adicionarem você ao registro, você receberá um email de ativação.

No entanto, se os administradores do EA não puderem ajudar, poderemos adicioná-lo em nome deles, mas você precisa nos fornecer:
- o número do registro.
- o endereço de email a ser adicionado e o tipo de autenticação (conta Microsoft, corporativa ou de estudante).
- aprovação por email de um administrador do EA.

Quando você tiver todas as informações necessárias, envie uma solicitação em [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

### <a name="i-would-like-to-update-the-first-ea-admin-on-the-enrollment"></a>Eu quero atualizar o primeiro administrador do EA no registro

O primeiro administrador do EA pode ser atualizado no centro de serviços de licenciamento por volume ao atualizar o contato de aviso e o administrador online no portal. A atualização do portal do EA levará cerca de 24 horas. Após a atualização, o novo administrador do EA receberá o email de ativação.

Se você não tem acesso ao portal do VLSC, ou se o administrador do EA inicial não pode mais gerenciar o registro e não tem acesso ao portal do EA, envie uma solicitação em [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) para solicitar uma atualização e forneça as seguintes informações:
- Número de registro
- Endereço de email a ser adicionado e o tipo de autenticação (conta Microsoft, corporativa ou de estudante)
- Motivo para alterar o administrador de EA inicial
- Aprovação por email de um administrador inicial do EA

### <a name="my-current-ea-admin-is-no-longer-with-the-company"></a>Meu administrador atual do EA não está mais na empresa

Um registro do EA pode ter vários administradores do EA. Você pode entrar em contato com outro administrador do EA para adicionar um novo administrador de EA/proprietário de conta/administrador de departamento. No entanto, se você não sabe ao certo quem é o administrador do EA em sua empresa, ou se não há outro administrador de EA disponível no registro, entre em contato conosco com as seguintes informações:
- Número de registro
- Endereço de email a ser adicionado e o tipo de autenticação (conta Microsoft, corporativa ou de estudante)
- Informação de que o administrador atual do EA não está mais na empresa

Observe que, se houver outros administradores do EA no registro, entraremos em contato com os administradores do EA a fim de solicitar a aprovação para fazer alterações administrativas no registro.

### <a name="my-enrollment-is-showing-in-pending-status-how-do-i-activate-my-enrollment"></a>Meu registro está aparecendo no status pendente. Como ativar meu registro?

Os registros estarão com status pendente se o administrador do EA inicial não tiver feito logon no registro antes. Se você é o administrador do EA, entre no portal do Azure EA. Na página inicial, onde estão todos os seus números de registro, talvez você não veja seu registro pendente. Desmarque a caixa "ativo" no canto superior direito do Portal do EA. Essa ação exibirá o registro pendente. Clique no registro para acessar as informações e, depois de acessar a página Gerenciar do registro, o status será atualizado de Pendente para Ativo.

### <a name="why-is-my-account-stuck-in-pending-status"></a>Por que minha conta travou no status pendente?

Quando novos AOs (proprietários da conta) forem adicionados a um registro pela primeira vez, o status deles será exibido sempre como "pendente". Após receber o email de ativação de boas-vindas, o AO poderá entrar para ativar a própria conta. A entrada atualizará o status da conta de "pendente" para "ativo".

### <a name="i-received-an-error-when-signing-in-to-azure-ea-portal"></a>Recebi um erro ao entrar no portal do Azure EA

Há alguns motivos possíveis para a mensagem de erro no portal do Azure EA durante a entrada. Execute estas etapas de solução de problemas:

 1. Verifique se você está usando a URL correta do portal do EA em [https://ea.azure.com](https://ea.azure.com).
 1. Determine se o acesso ao portal do Azure EA foi adicionado como uma conta corporativa ou de estudante ou como uma Microsoft Live ID. Se você está usando sua conta corporativa, insira seu email e senha corporativos. Se você está usando o Microsoft Live ID, insira seu email do Live ID e a senha do Microsoft Live ID. Se você esqueceu sua senha do Microsoft Live ID, redefina-a em [https://account.live.com/password/reset](https://account.live.com/password/reset).
 1. Recomendamos que use um navegador privado para entrar, de modo que nenhum cookie ou cache de sessões anteriores/existentes sejam mantidos. Limpe o cache e use o modo de navegação particular/janela anônima para abrir [https://ea.azure.com](https://ea.azure.com).
 1. Se você recebe um erro de Usuário Inválido ao usar uma conta Microsoft, talvez tenha várias contas da Microsoft e a que você está usando para tentar entrar não é o alias principal. Para verificar o alias principal, acesse account.live.com:
    - Acesse "Suas Informações" > "Gerenciar seu número de telefone ou seu email de entrada".
    - Siga os avisos na tela para verificar o endereço de email alternativo e obter um código para acessar informações confidenciais.
    - Digite o código de segurança.
    - Se preferir configurar a autenticação em duas etapas mais tarde, selecione "Configurar mais tarde".
    - Você chegará à página "Gerenciar seus aliases de conta", em que você verá os aliases de conta disponíveis. Verifique de novo se o alias principal é aquele que você está usando para entrar no portal do Azure EA. Se não for, você pode torná-lo seu alias principal ou pode usar o alias principal do portal do EA.

Se as etapas de solução de problemas acima falharem, envie uma solicitação para [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) com informações como:
- Os navegadores e versão usados.
- Captura de tela da mensagem de erro.
- URL da página que mostra o erro.  
- Data, hora e fuso horário de quando o erro ocorreu.
- Além disso, será muito útil se você obtiver um arquivo de log. Estas são as etapas para capturar um rastreamento de rede usando as informações abaixo:
  1. Abra o Internet Explorer.
  1. Pressione F12, o que abrirá uma caixa na parte inferior do IE.
  1. Selecione a guia **Rede**.
  1. Clique em **Iniciar captura**.
  1. Execute a ação que está causando o erro.
  1. Quando você receber o erro, clique em **Parar captura**.
  1. Salve o arquivo e inclua as informações na solicitação de suporte.
  1. Verifique se você forneceu o número de registro e o endereço de email na solicitação de suporte.

### <a name="what-is-the-difference-between-a-workschool-account-and-microsoft-account"></a>Qual a diferença entre uma conta corporativa/de estudante e a conta Microsoft?

**Conta Microsoft:** contas que foram associadas ao Live ID no [https://signup.live.com](https://signup.live.com).

**Conta corporativa ou de estudante:** disponível apenas para empresas que configuraram o Active Directory com Federação na Nuvem e com todas as contas em um único locatário. Os usuários poderão ser adicionados com o tipo de autorização corporativo/de estudante se o Active Directory interno da empresa for federado na nuvem.

  Desde setembro de 2016, a Microsoft não permite que emails corporativos ou de estudante sejam registrados como contas da Microsoft. Para saber mais, confira os seguintes materiais: [https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/](https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/).

  Se sua organização não for federada na nuvem, você não poderá usar seu endereço de email corporativo ou de estudante. Em vez disso, registre ou crie um novo endereço de email e registre-o como uma conta Microsoft.

### <a name="i-forgot-my-password-to-azure-ea-portal"></a>Esqueci minha senha para o portal do Azure EA

Se você esqueceu sua senha do Microsoft Live ID, redefina-a em [https://account.live.com/password/reset](https://account.live.com/password/reset).

Se você esqueceu sua senha corporativa, entre em contato com o administrador de TI da sua empresa.

### <a name="i-have-a-valid-work-or-school-account-but-i-cant-add-it-to-the-ea-portal"></a>Tenho uma conta corporativa ou de estudante válida, mas não consigo adicioná-la ao portal do EA

Se você tiver uma conta corporativa ou de estudante em outro locatário, altere o nível de autorização na página de detalhes do registro para "Locatário Cruzado de Conta Corporativa ou de Estudante" e você poderá adicionar a conta.

## <a name="next-steps"></a>Próximas etapas

- Os administradores do Portal do EA do Azure devem ler [Administração do Portal do EA do Azure](ea-portal-administration.md) para aprender sobre tarefas administrativas comuns.
