---
title: Proteger dados pessoais com controles de acesso e identidade do Azure | Microsoft Docs
description: Usando os controles de acesso e identidade do Azure para ajudar a proteger dados pessoais
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 7c66a95d5a056f59e0f28dba4e0880e72e74dc3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-and-multi-factor-authentication-protect-personal-data-with-identity-and-access-controls"></a>Azure Active Directory e Autenticação Multifator: proteger dados pessoais com controles de acesso e identidade

Este artigo fornece informações e procedimentos que podem ser usados para proteger dados pessoais, usando os serviços e recursos de segurança do Azure Active Directory e da Autenticação multifator.

## <a name="scenario"></a>Cenário

Uma empresa de cruzeiro de grande porte, com sede nos Estados Unidos, está expandindo suas operações para oferecer roteiros nos mares Mediterrâneo, Adriático e Báltico, bem como nas Ilhas Britânicas. Para dar suporte a esses esforços, ela adquiriu várias linhas de cruzeiro menores localizadas na Itália, na Alemanha, na Dinamarca e no Reino Unido. 

A empresa usa o Microsoft Azure para armazenar dados corporativos na nuvem. Eles incluem informações de identificação pessoal, como nomes, endereços, números de telefone e informações de cartão de crédito de sua base global de clientes. Também incluem informações tradicionais de Recursos Humanos, como endereços, números de telefone, números de identificação fiscal e outras informações sobre os funcionários da empresa em todas as localizações. A linha de cruzeiro também mantém um banco de dados grande de membros do programa de recompensa e fidelidade que inclui informações pessoais para acompanhamento das relações com os clientes atuais e anteriores.

Os funcionários corporativos acessam a rede de escritórios remotos da empresa e os agentes de viagem localizados no mundo todo têm acesso a alguns recursos da empresa.

## <a name="problem-statement"></a>Problema declarado

A empresa deve proteger a privacidade dos dados pessoais de clientes e funcionários contra invasores que buscam usar as identidades comprometidas para obter acesso. Ela também deve garantir que o acesso aos dados pessoais por usuários legítimos seja restrito somente àqueles que precisam deles para realizar seus trabalhos.

## <a name="company-goal"></a>Meta da empresa

A meta da empresa é garantir que o acesso a dados pessoais seja estritamente controlado. É essencial que as identidades de usuários com acesso a dados pessoais sejam protegidas pela autenticação forte. Uma política de [privilégio mínimo] (https://en.wikipedia.org/wiki/Principle_of_least_privilege) deve ser imposta, de modo que os usuários legítimos tenham apenas o nível de acesso necessário e nada mais.

## <a name="solutions"></a>Soluções

O Microsoft Azure fornece ferramentas de gerenciamento de identidade e acesso para ajudar as empresas a controlar quem tem acesso aos recursos que contêm dados pessoais.

### <a name="azure-active-directory"></a>Azure Active Directory

O [AAD](https://docs.microsoft.com/azure/active-directory/) (Azure Active Directory) gerencia identidades e controla o acesso ao Azure, bem como outros recursos locais e outros recursos, dados e aplicativos de nuvem. O [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access) ajuda os administradores do Azure a minimizar o número de pessoas que têm acesso a determinadas informações como dados pessoais. Ele permite que eles descubram, restrinjam e monitorem identidades com privilégios e seu acesso a recursos, além de atribuir direitos administrativos JIT (Just-In-Time) temporários aos usuários qualificados. Também fornece informações sobre aqueles que têm privilégios administrativos do AAD.

As atividades envolvidas no uso do AAD PIM incluem:

- Habilitar o Privileged Identity Management no diretório

- Usar o painel de administração do Privileged Identity Management para ver informações importantes rapidamente

- Gerenciar as identidades com privilégios (administradores) adicionando ou removendo os administradores permanentes ou qualificados para cada função

- Definir as configurações de ativação de função

- Ativar funções

- Examinar a atividade de função

#### <a name="how-do-i-enable-aad-pim"></a>Como fazer para habilitar o AAD PIM?

Para começar a usar o PIM no diretório, faça o seguinte:

1. Entre no portal do Azure como administrador global do diretório.

2. Se sua organização tiver mais de um diretório, selecione seu nome de usuário no canto superior direito do portal do Azure. Selecione o diretório em que você usará o Privileged Identity Management do Azure AD.

3. Selecione **Mais serviços** e use a caixa de texto **Filtrar** para pesquisar o Azure AD Privileged Identity Management.

4. Marque **Fixar no painel** e então clique em **Criar**. O aplicativo Privileged Identity Management é aberto.

Depois que o Azure AD Privileged Identity Management estiver configurado, você verá a folha de navegação sempre que abrir o aplicativo.

![](media/protect-personal-data-identity-access-controls/azure-pim.png)

Para obter mais informações e instruções sobre como começar a usar o AAD PIM, consulte [Começar a usar o Azure AD Privileged Identity Management.](https://docs.microsoft.com/active-directory/active-directory-privileged-identity-management-getting-started)

### <a name="azure-role-based-access-control"></a>Controle de Acesso Baseado em Função do Azure

O [RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) (Controle de Acesso Baseado em Função do Azure) ajuda os administradores do Azure a gerenciar o acesso aos recursos do Azure, permitindo a concessão de acesso de acordo com a função atribuída do usuário. Segregue as tarefas dentro de uma equipe e conceda somente a quantidade de acesso que os usuários, os grupos e os aplicativos precisam para realizar seus trabalhos.

O acesso baseado em função pode ser concedido aos usuários que usam o portal do Azure, as ferramentas de Linha de Comando do Azure ou as APIs de Gerenciamento do Azure.

Para obter mais informações sobre os conceitos básicos do RBAC do Azure, consulte [Introdução ao Controle de Acesso Baseado em Função no Portal do Azure](https://docs.microsoft.com/active-directory/role-based-access-control-what-is).

#### <a name="how-do-i-manage-azure-rbac-with-powershell"></a>Como fazer para gerenciar o RBAC do Azure com o PowerShell?

Use os cmdlets do PowerShell para gerenciar o RBAC do Azure, incluindo as seguintes tarefas de gerenciamento:

- Listar funções

- Veja quem tem acesso

- Conceder acesso

- Remover acesso

- Criar uma função personalizada

- Obter ações para um provedor de recursos

- Modificar uma função personalizada

- Excluir uma função personalizada

- Listar funções personalizadas

Para obter instruções sobre como gerenciar o RBAC do Azure com o PowerShell, consulte [Gerenciar o Acesso Baseado em Função com o Azure PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell).

### <a name="azure-multi-factor-authentication"></a>Autenticação Multifator do Azure

O [Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/) (Autenticação Multifator do Azure) é uma solução de verificação em duas etapas que ajuda a proteger o acesso a dados e aplicativos, enquanto atende às exigências do usuário por um processo de conexão simples. Ele fornece autenticação forte por meio de uma variedade de métodos de verificação, incluindo chamada telefônica, mensagem de texto ou verificação de aplicativo móvel.

Para implantar o MFA na nuvem do Azure, você precisa habilitá-lo primeiro e, em seguida, ativar a verificação em duas etapas para os usuários.

#### <a name="how-do-i-enable-azure-to-use-mfa"></a>Como fazer para habilitar o Azure a usar o MFA?

Se os usuários tiverem licenças que incluem a Autenticação Multifator do Azure, nada precisará ser feito para ativar o MFA do Azure. Caso contrário, você precisará criar um provedor de Autenticação Multifator no diretório. Para fazer isso, siga estas etapas:

1. Selecione **Active Directory** no portal clássico do Azure (conectado como administrador).

2. Selecione **Provedores de Autenticação Multifator.**

3. Selecione **Novo** e, em seguida, em **Serviços de Aplicativos**, selecione **Provedor de Autenticação Multifator.**

4. Selecione **Criação Rápida.**

5. Preencha o campo de nome e selecione um modelo de uso (por autenticação ou por usuário habilitado).

6. Designe um diretório ao qual o Provedor de MFA está associado.

7. Selecione o botão **Criar** .

![](media/protect-personal-data-identity-access-controls/quick-create.png)

Para obter mais instruções sobre como gerenciar o Provedor de Autenticação Multifator, consulte [Introdução a um Provedor de Autenticação Multifator do Azure.](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-auth-provider)

#### <a name="how-do-i-turn-on-two-step-verification-for-users"></a>Como fazer para ativar a verificação em duas etapas para os usuários?

Imponha a verificação em duas etapas para todas as entradas ou crie políticas de acesso condicional para exigir uma verificação em duas etapas somente quando condições específicas forem aplicáveis.

A habilitação do MFA do Azure com a alteração de estados do usuário é a abordagem tradicional para exigir a verificação em duas etapas. Todos os usuários habilitados terão o mesmo requisito de executar a verificação em duas etapas sempre que se conectarem. A habilitação de um usuário substitui qualquer política de acesso condicional que possa afetar esse usuário.

A habilitação do MFA do Azure com uma política de acesso condicional é uma abordagem mais flexível para exigir a verificação em duas etapas. Você pode criar políticas de acesso condicional que se aplicam a grupos, bem como a usuários individuais. Grupos de alto risco podem receber mais restrições do que grupos de baixo risco, ou a verificação em duas etapas pode ser exigida apenas para aplicativos de nuvem de alto risco e ignorada para os de baixo risco. No entanto, o acesso condicional um recurso pago do Azure Active Directory.

Para habilitar o MFA alterando o estado do usuário, faça o seguinte:

1. Entre no portal do Azure como administrador.
2. Acesse **Azure Active Directory \> Usuários e grupos \> Todos os usuários**.
3. Selecione **Autenticação Multifator**.
4. Localize o usuário que você deseja habilitar para a MFA do Azure. Talvez seja necessário alterar o modo de exibição na parte superior.
5. Marque a caixa ao lado do nome do usuário.
6. À direita, em etapas rápidas, escolha **Habilitar**.

   ![](media/protect-personal-data-identity-access-controls/quick-create.png)

7. Confirme sua seleção na janela pop-up que é aberta.  Os usuários para os quais o MFA foi habilitado deverão se registrar na próxima vez que se conectarem.

Para habilitar o MFA do Azure com uma política de acesso condicional, faça o seguinte:

1. Entre no portal do Azure como administrador.

2. Acesse **Azure Active Directory \> Acesso condicional**.

3. Selecione **Nova política**.

4. Em **Atribuições**, selecione **Usuários e grupos**. Use as guias **Incluir** e **Excluir** para especificar quais usuários e grupos serão gerenciados pela política.

5. Em **Atribuições**, selecione **Aplicativos de nuvem.** Opte por **incluir Todos os aplicativos de nuvem**.
6.  Em **Controles de acesso**, selecione **Grant**. Escolha **Exigir autenticação multifator**.
7.  Alterne **Habilitar política** para **Ativado** e selecione **Salvar**.

Para obter informações sobre como definir as configurações do MFA do Azure para configurar alertas de fraudes, criar um bypass avulso, usar mensagens de voz personalizadas, configurar o cache, especificar IPs confiáveis, criar senhas de aplicativo, habilitar o reconhecimento do MFA de dispositivos de confiança para os usuários e selecionar métodos de verificação, consulte [Definir as configurações da Autenticação Multifator do Azure.](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)

## <a name="next-steps"></a>Próximas etapas

- [Protegendo o acesso privilegiado no Azure AD](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)

- [Perguntas frequentes sobre a Autenticação Multifator do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-faq)

- [Solução de problemas do Controle de Acesso Baseado em Função](https://docs.microsoft.com/azure/active-directory/role-based-access-control-troubleshooting)

- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
