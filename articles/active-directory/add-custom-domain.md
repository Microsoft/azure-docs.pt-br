---
title: "Adicionar um domínio personalizado ao Azure AD | Microsoft Docs"
description: "Explica como adicionar um domínio personalizado no Azure Active Directory."
services: active-directory
author: curtand
manager: femila
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: curtand
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: 2ea119b56f467f57b5929dec05bfd645028578ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-add-a-custom-domain-name-to-azure-active-directory"></a>Início rápido: adicionar um nome de domínio personalizado ao Azure Active Directory

Todo diretório do Azure AD vem com um nome de domínio inicial no formato *nomededomínio*.onmicrosoft.com. O nome de domínio inicial não pode ser alterado nem excluído, mas você pode adicionar seu nome de domínio corporativo para o Azure AD também. Por exemplo, sua organização provavelmente tem outros nomes de domínio que são usados para fazer negócios e usuários que entram usando seu nome de domínio corporativo. Adicionar nomes de domínio personalizados ao Azure AD permite que você atribua nomes de usuário no diretório que são familiares para seus usuários, tais como "alice@contoso.com". em vez de "alice@*<domain name>*.onmicrosoft.com". O processo é simples:

1. Adicionar o nome de domínio personalizado ao diretório
2. Adicionar uma entrada DNS para o nome de domínio no registrador de nome de domínio
3. Verifique o nome de domínio personalizado no Azure AD

## <a name="add-the-custom-domain-name-to-your-directory"></a>Adicionar o nome de domínio personalizado ao diretório
1. Entre no [Portal do Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que seja um administrador global do diretório.
2. À esquerda, selecione **Nomes de domínio**.
3. Em ***nome do diretório* – Nomes de domínio**, selecione **Adicionar**.
   
   ![Selecionar o comando Adicionar](./media/active-directory-domains-add-azure-portal/add-command.png)
5. Em **Nome de domínio**, insira o nome do seu domínio personalizado na caixa, como "contoso.com" e selecione **Adicionar domínio**. Inclua .com, .net ou outra extensão de nível superior.
6. Em ***nomededomínio*** (ou seja, o nome do novo domínio é o título), colete as informações da entrada DNS que você deve usar posteriormente para verificar o nome de domínio personalizado no Azure AD.
   
   ![obter informações de entrada DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

> [!TIP]
> Se você planeja federar seu AD do Windows Server local com o Azure AD, você precisa selecionar a caixa de seleção **Pretendo configurar esse domínio para logon único com meu Active Directory local** quando você executa a ferramenta Azure AD Connect para sincronizar seus diretórios. Você também precisa registrar o mesmo nome de domínio selecionado para federação com seu diretório local na etapa **domínio do Azure AD** no assistente. Você pode ver como é essa etapa do assistente [nessas instruções](./connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Se você não tiver a ferramenta Azure AD Connect, poderá [baixá-la aqui](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-a-dns-entry-for-the-domain-name-at-the-domain-name-registrar"></a>Adicionar uma entrada DNS para o nome de domínio no registrador de nome de domínio
A próxima etapa para usar o nome de domínio personalizado com o Azure AD é atualizar o arquivo de zona DNS do domínio. O Azure AD pode então verificar se a organização é proprietária do nome de domínio personalizado. Você pode usar o [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) para seus registros em Azure/Office 365/DNS externo dentro do Azure ou adicionar a entrada DNS em [um registrador DNS diferente](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Entre no registrador de nome de domínio para o domínio. Se você não tem acesso para atualizar a entrada DNS, peça à pessoa ou à equipe que tem esse acesso para concluir a etapa 2 e avisá-lo quando ela for concluída.
2. Atualize o arquivo de zona DNS para o domínio adicionando a entrada DNS fornecida pelo AD do Azure. A entrada DNS não altera nenhum comportamento, como o roteamento de emails ou a hospedagem na Web.

## <a name="verify-the-custom-domain-name-in-azure-ad"></a>Verifique o nome de domínio personalizado no Azure AD
Após adicionar a entrada DNS, você está pronto para verificar o nome de domínio com o Azure AD. Um nome de domínio só poderá ser verificado depois que os registros DNS forem propagados. Essa propagação geralmente leva apenas alguns segundos, mas às vezes pode levar uma hora ou mais. Se a verificação não funcionar na primeira vez, tente novamente mais tarde.

1. Entre no [Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que seja um administrador global do diretório.
2. À esquerda, selecione **Nomes de domínio**.
3. Em ***nome-do-diretório* -Nomes de domínio**, selecione o comando **Adicionar nome de domínio**. 
  ![Selecionar o comando Adicionar](./media/active-directory-domains-add-azure-portal/add-command.png)
3. Em ***nome do diretório* - Nomes de domínio**, selecione o nome de domínio não verificado que você deseja verificar.
4. Em ***nomedodomínio*** (ou seja, o nome de domínio selecionado é o título), selecione **Verificar** para concluir a verificação.

Agora você pode [atribuir nomes de usuário que incluam o nome de domínio personalizado](active-directory-users-create-azure-portal.md). Você pode criar contas de usuário baseadas em nuvem ou atualizar informações de conta de usuário locais sincronizadas anteriormente, usando seu nome de domínio personalizado. Você também pode alterar as informações do sufixo de domínio da conta de usuário sincronizada usando o [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) ou a [API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

> [!TIP]
> É possível adicionar até um máximo de 900 nomes de domínio gerenciado. Se você pretende configurar todos os domínios para federação com Active Directory local, será possível adicionar até um máximo de 450 nomes de domínio em cada diretório. Para mais informações, consulte [Nomes de domínio federados e gerenciados](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="troubleshooting"></a>Solucionar problemas
Se você não puder verificar um nome de domínio personalizado, experimente as seguintes etapas de solução de problemas:

1. **Aguarde uma hora**. Os registros DNS precisam ser propagados para que o Azure AD possa verificar o domínio. Esse processo pode levar uma hora ou mais.
2. **Verifique se o registro DNS foi inserido e se está correto**. Conclua essa etapa no site do registrador de nome de domínio do domínio. O Azure AD não pode verificar o nome de domínio se 
  * A entrada DNS não estiver presente no arquivo de zona DNS
  * Não tiver uma correspondência exata com a entrada DNS fornecida pelo Azure AD. 
  
  Se você não tiver acesso para atualizar os registros DNS parado domínio no registrador de nome de domínio, compartilhe a entrada DNS com a pessoa ou equipe em sua organização que tem acesso a isso e peça-lhe que adicione a entrada DNS.
3. **Exclua o nome de domínio de outro diretório no Azure AD**. Um nome de domínio pode ser verificado apenas em um único diretório. Se um nome de domínio for verificado no momento em um diretório diferente, ele não poderá ser verificado em seu novo diretório até que seja excluído no outro. Para saber mais sobre a exclusão de nomes de domínio, leia [Gerenciar nomes de domínio personalizados](active-directory-domains-manage-azure-portal.md).    

## <a name="add-more-custom-domain-names"></a>Adicionar mais nomes de domínio personalizados
Se sua organização usa vários nomes de domínio personalizados, como 'contoso.com' e 'contosobank.com', você pode adicionar até 900 nomes de domínio. As etapas deste artigo podem ajudar você a adicionar cada um dos seus nomes de domínio.

### <a name="learn-more"></a>Saiba mais
[Visão geral conceitual de nomes de domínio personalizados no Azure AD](active-directory-add-domain-concepts.md)

[Gerenciar nomes de domínio personalizados](active-directory-domains-manage-azure-portal.md)

## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido, você aprendeu como adicionar um domínio personalizado ao Azure AD. 

Você pode usar o link a seguir para adicionar um novo domínio personalizado do Azure AD do Portal do Azure.

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/QuickStart) 