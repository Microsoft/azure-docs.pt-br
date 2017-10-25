---
title: "O que é a inscrição no Azure por autoatendimento? | Microsoft Docs"
description: "Uma visão geral da inscrição por autoatendimento no Azure, como gerenciar o processo de inscrição e como adotar um nome de domínio DNS."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 511088156d3546e2e0f3ac40e72bf2b8e4ae2cb9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-self-service-signup-for-azure"></a>O que é a inscrição no Azure por autoatendimento?
Este tópico explica o processo de inscrição por autoatendimento e como assumir um nome de domínio DNS.  

## <a name="why-use-self-service-signup"></a>Por que usar a inscrição por autoatendimento?
* Obtenha mais rápido os serviços que os clientes desejam.
* Crie ofertas baseadas em email para um serviço.
* Crie fluxos de inscrição baseados em email que permitem aos usuários criar rapidamente identidades usando seus aliases de email de trabalho, fáceis de lembrar.
* Diretórios do Azure não gerenciados podem ser feitos em diretórios gerenciados posteriormente e reutilizados para outros serviços.

## <a name="terms-and-definitions"></a>Termos e definições
* **Inscrição por autoatendimento**: esse é o método pelo qual um usuário se inscreve em um serviço de nuvem e tem uma identidade automaticamente criada para ele no Azure AD (Azure Active Directory), com base no seu domínio de email.
* **Diretório do Azure não gerenciado**: este é o diretório no qual essa identidade é criada. Um diretório não gerenciado é um diretório sem nenhum administrador global.
* **Usuário verificado por email**: Este é um tipo de conta de usuário no AD do Azure. Um usuário que tem uma identidade criada automaticamente após a inscrição para uma oferta de autoatendimento é conhecido como um usuário verificado por email. Um usuário verificadas por email é um membro comum de um diretório marcado com creationmethod=EmailVerified.

## <a name="user-experience"></a>Experiência do usuário
Por exemplo, digamos que um usuário cujo email é Dan@BellowsCollege.com recebe arquivos confidenciais por email. Os arquivos foram protegidos pelo Azure RMS (Azure Rights Management). Mas a organização de Dan, a faculdade Bellows, não se inscreveu para o Azure RMS, nem implantou o RMS do Active Directory. Nesse caso, Dan pode se inscrever para uma assinatura gratuita do RMS para indivíduos, para que possa ler os arquivos protegidos.

Se Dan for o primeiro usuário com um endereço de email de BellowsCollege.com a inscrever-se nessa oferta por autoatendimento, será criado um diretório não gerenciado para BellowsCollege.com no Azure AD. Se outros usuários do domínio BellowsCollege.com tiverem se inscrito nessa oferta ou em outra oferta por autoatendimento semelhante, eles também terão contas de usuário verificadas por email criadas no mesmo diretório não gerenciado no Azure.

## <a name="admin-experience"></a>Experiência de admin
Um administrador que possui o nome de domínio DNS de um diretório do Azure não gerenciado pode assumir ou mesclar o diretório depois de comprovar a propriedade. As próximas seções explicam a experiência de administração mais detalhadamente, mas aqui está um resumo:

* Ao assumir um diretório não gerenciado do Azure, você simplesmente se torna o administrador global do diretório não gerenciado. Isso às vezes é chamado de uma tomada de controle interna.
* Quando você mescla um diretório do Azure não gerenciado, você adiciona o nome de domínio DNS do diretório não gerenciado ao seu diretório gerenciado do Azure e um mapeamento de usuários a recursos é criado, assim os usuários podem continuar acessar os serviços sem interrupção. Isso às vezes é chamado de uma tomada de controle externa.

## <a name="what-gets-created-in-azure-active-directory"></a>O que é criado no Active Directory do Azure?
#### <a name="directory"></a>diretório
* Um diretório do Azure Active Directory é criado para o domínio, sendo um diretório por domínio.
* O diretório do Azure AD não tem nenhum administrador global.

#### <a name="users"></a>Usuários
* Para cada usuário que se inscreve, um objeto de usuário é criado no diretório do Azure AD.
* Cada objeto de usuário é marcado como externo.
* Cada usuário recebe acesso ao serviço para o qual se inscreveu.

### <a name="how-do-i-claim-a-self-service-azure-ad-directory-for-a-domain-i-own"></a>Como reivindico um diretório por autoatendimento do Azure AD para um domínio que possuo?
Você pode reivindicar um diretório de autoatendimento do Azure AD executando a validação de domínio. A validação de domínio prova, por meio de criação de registros DNS, que você possui o domínio.

Há duas maneiras de tomar o controle do DNS de um diretório do Azure AD:

* tomada de controle interna (o admin descobre um diretório não gerenciado do Azure e deseja transformá-lo em um diretório gerenciado)
* tomada de controle externa (o admin tenta adicionar um novo domínio a seu diretório gerenciado do Azure)

Você talvez esteja interessado em validar que possui um domínio porque está assumindo um diretório não gerenciado após um usuário ter realizado inscrição por autoatendimento, ou então você pode adicionar um novo domínio a um diretório gerenciado existente. Por exemplo, você tem um domínio contoso.com e deseja adicionar um novo domínio chamado contoso.co.uk ou contoso.uk.

## <a name="what-is-domain-takeover"></a>O que é a tomada de controle de domínio?
Esta seção demonstra como validar que você possui um domínio

### <a name="what-is-domain-validation-and-why-is-it-used"></a>O que é validação de domínio e por que ela é usada?
Para executar operações em um diretório, o Azure AD requer que você valide a propriedade do domínio DNS.  A validação do domínio permite a você reivindicar o diretório e promover o diretório por autoatendimento para um diretório gerenciado ou mesclar o diretório por autoatendimento em um diretório gerenciado existente.

## <a name="examples-of-domain-validation"></a>Exemplos de validação de domínio
Há duas maneiras de tomar o controle do DNS de um diretório:

* tomada de controle interna (por exemplo, um administrador descobre um diretório não gerenciado de autoatendimento e deseja transformá-lo em um diretório gerenciado)
* tomada de controle externa (por exemplo, um admin tenta adicionar um novo domínio a um diretório gerenciado)

### <a name="internal-takeover---promote-a-self-service-unmanaged-directory-to-be-a-managed-directory"></a>Tomada de Controle Interna - promover um diretório não gerenciado por autoatendimento para ser um diretório gerenciado
Quando você realiza a tomada de controle interna, o diretório é convertido de não gerenciado em gerenciado. Você precisa concluir a validação de nome de domínio DNS, em que você pode criar um registro MX ou um registro TXT na zona DNS. Essa ação:

* Valida que você possui o domínio
* Torna o diretório gerenciado
* Torna você o administrador global do diretório

Digamos que um administrador de TI na faculdade Bellows descobre que os usuários da faculdade se inscreveram em ofertas por autoatendimento. Como o proprietário registrado do nome DNS BellowsCollege.com, o administrador de TI pode validar a propriedade do nome DNS no Azure e, em seguida, assumir o controle do diretório não gerenciado. O diretório se tornará um diretório gerenciado e a função de administrador de TI é atribuída à função de administrador global para o diretório BellowsCollege.com.

### <a name="external-takeover---merge-a-self-service-directory-into-an-existing-managed-directory"></a>Tomada de Controle Externa - mesclar um diretório por autoatendimento com um diretório gerenciado existente
Em uma tomada de controle externo, você já tem um diretório gerenciado e deseja que todos os usuários e grupos de um diretório não gerenciado participem desse diretório gerenciado, em vez de ter dois diretórios separados.

Como administrador de um diretório gerenciado, você adiciona um domínio que tem um diretório não gerenciado associado a ele.

Por exemplo, digamos que você é um administrador de TI e você já tem um diretório gerenciado para Contoso.com, um nome de domínio registrado em sua organização. Você descobre que os usuários de sua organização fizeram uma inscrição de autoatendimento para uma oferta usando o nome de domínio de email user@contoso.co.uk, que é outro nome de domínio possuído por sua organização. Atualmente, esses usuários têm contas em um diretório não gerenciado para contoso.co.uk.

Você não quer gerenciar dois diretórios separados, portanto, você mescla o diretório não gerenciado para contoso.co.uk com seu diretório gerenciado por TI existente para contoso.com.

A tomada de controle externa segue o mesmo processo de validação de DNS que a tomada de controle interna.  A diferença é: os usuários e serviços são remapeados para o diretório gerenciado por TI.

#### <a name="whats-the-impact-of-performing-an-external-takeover"></a>Qual é o impacto de uma tomada de controle externa?
Com uma tomada de controle externa, um mapeamento de usuários a recursos é criado para que os usuários possam continuar a acessar os serviços sem interrupção. Muitos aplicativos, incluindo o RMS para indivíduos, lidam bem com o mapeamento de usuários a recursos, e os usuários podem continuar a acessar esses serviços sem alterações. Se um aplicativo não lida com eficiência com o mapeamento de usuários a recursos, a tomada de controle externa pode ser explicitamente bloqueada para impedir que usuários tenham uma experiência ruim.

#### <a name="directory-takeover-support-by-service"></a>suporte a tomada de controle do diretório pelo serviço
Atualmente os seguintes serviços dão suporte à tomada de controle:

* RMS

Os serviços a seguir em breve darão suporte à tomada de controle:

* PowerBI

Os serviços a seguir não e exigem ações administrativas adicionais para migrar dados de usuário após uma tomada de controle externa.

* SharePoint/OneDrive

## <a name="how-to-perform-a-dns-domain-name-takeover"></a>Como executar uma tomada de controle de nome de domínio DNS
Você tem algumas opções para realizar uma validação de domínio (e uma tomada de controle, se desejar):

1. Portal de Gerenciamento do Azure

   Uma tomada de controle é disparada por uma adição de domínio.  Se já existir um diretório para o domínio, você terá a opção de executar uma tomada de controle externa.

   Entre no Portal do Azure usando suas credenciais.  Navegue até seu diretório existente e, em seguida, até **Adicionar domínio**.
2. Office 365

   Você pode usar as opções na página [Gerenciar domínios](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) no Office 365 para trabalhar com seus domínios e registros DNS. Consulte [Verificar seu domínio no Office 365](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/).
3. Windows PowerShell

   As etapas a seguir são necessárias para executar uma validação usando o Windows PowerShell.

   | Etapa | Cmdlet a usar |
   | --- | --- |
   | Criar um objeto de credencial |Get-Credential |
   | Conecte-se ao AD do Azure |Connect-MsolService |
   | obter uma lista de domínios |Get-MsolDomain |
   | Criar um desafio |Get-MsolDomainVerificationDns |
   | Criar registro de DNS |Fazer isso em seu servidor DNS |
   | Verificar o desafio |Confirm-MsolEmailVerifiedDomain |

Por exemplo:

1. Conecte o Azure AD usando as credenciais utilizadas para responder à oferta de autoatendimento:

        import-module MSOnline
        $msolcred = get-credential
        connect-msolservice -credential $msolcred
2. Obter uma lista de domínios:

    Get-MsolDomain
3. Em seguida, execute o cmdlet Get-MsolDomainVerificationDns para criar um desafio:

    Get-MsolDomainVerificationDns –DomainName *seu_nome_domínio* –Mode DnsTxtRecord

    Por exemplo:

    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
4. Copie o valor (o desafio) que é retornado deste comando.

    Por exemplo:

    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
5. No seu namespace DNS público, crie um registro DNS txt que contenha o valor que você copiou na etapa anterior.

    O nome deste registro é o nome do domínio-pai, então se você criar este registro de recurso usando a função DNS do Windows Server, deixe o nome do registro em branco e apenas cole o valor na caixa de texto
6. Execute o cmdlet Confirm-MsolDomain para verificar o desafio:

    Confirm-MsolEmailVerifiedDomain -DomainName *seu_nome_domínio*

    Por exemplo:

    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com

Um desafio bem-sucedido fará com que você retorne ao prompt sem erros.

## <a name="how-do-i-control-self-service-settings"></a>Como controlar as configurações de autoatendimento?
Os administradores têm dois controles de autoatendimento atualmente. Eles podem controlar:

* Se os usuários podem ou não ingressar no diretório por email.
* Se os usuários podem ou não licenciar a eles próprios para aplicativos e serviços.

### <a name="how-can-i-control-these-capabilities"></a>Como posso controlar esses recursos?
Um administrador pode configurar esses recursos usando esses parâmetros do cmdlet Set-MsolCompanySettings do AD do Azure:

* **AllowEmailVerifiedUsers** controla se um usuário pode criar ou ingressar em um diretório não gerenciado. Se você definir esse parâmetro como $false, nenhum usuário verificado por email poderá ingressar no diretório.
* **AllowAdHocSubscriptions** controla a capacidade dos usuários de executarem a assinatura por autoatendimento. Se você definir esse parâmetro como $false, nenhum usuário poderá realizar inscrição por autoatendimento.

### <a name="how-do-the-controls-work-together"></a>Como os controles funcionam juntos?
Esses dois parâmetros podem ser usados em conjunto para definir um controle mais preciso sobre a inscrição por autoatendimento. Por exemplo, o comando a seguir permitirá que os usuários realizem a inscrição por autoatendimento, mas apenas se os usuários já tiverem uma conta no AD do Azure (em outras palavras, os usuários que precisassem criar uma conta verificada por email não poderiam realizar inscrição por autoatendimento):

    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

O fluxograma a seguir explica todas as combinações diferentes desses parâmetros e as condições resultantes para o diretório e inscrição por autoatendimento.

![][1]

Para obter mais informações e exemplos de como usar esses parâmetros, consulte [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="see-also"></a>Consulte também
* [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview)
* [PowerShell do Azure](/powershell/azure/overview)
* [Referência de Cmdlets do Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
