---
title: Converter links e URLs do Proxy de Aplicativo do Azure AD | Microsoft Docs
description: "Cobre as noções básicas sobre os conectores do Proxy de Aplicativo do Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 57218346d236b376d2227e0ffaea6c6dd5ebe855
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Redirecione os links inseridos no código para aplicativos publicados com o Proxy de Aplicativo do Azure AD

O Proxy de Aplicativo do Azure AD disponibiliza seus aplicativos locais para usuários remotos ou que estão em seus próprios dispositivos. No entanto, alguns aplicativos foram desenvolvidos com links locais inseridos no HTML. Esses links não funcionam corretamente quando o aplicativo é usado remotamente. Quando você faz com que vários aplicativos locais apontem uns para os outros, os usuários esperam os links para continuar trabalhando quando não estiverem no escritório. 

A melhor maneira de garantir que os links funcionem corretamente dentro e fora de sua rede corporativa é configurar as URLs externas de seus aplicativos para que sejam as mesmas que as URLs internas. Use [domínios personalizados](active-directory-application-proxy-custom-domains.md) para configurar suas URLs externas para que tenham o nome de domínio corporativo em vez do domínio padrão do proxy de aplicativo.

Se não é possível usar domínios personalizados em seu locatário, o recurso de conversão de link do Proxy de Aplicativo mantém seus links funcionando, independentemente de onde os usuários estão. Quando você tem aplicativos que apontam diretamente para portas ou pontos de extremidade internos, pode mapear essas URLs internas para as URLs de Proxy de Aplicativo externas publicadas. Quando a conversão de link está habilitada, o Proxy de Aplicativo pesquisa em HTML, CSS e seleciona marcas de JavaScript para os links internos publicados. Então, o serviço do Proxy de Aplicativo as converte para que os usuários obtenham uma experiência sem interrupções.

>[!NOTE]
>O recurso de conversão de link é para locatários que, por qualquer motivo, não podem usar domínios personalizados para ter as mesmas URLs internas e externas para seus aplicativos. Antes de habilitar esse recurso, verifique se [domínios personalizados no Proxy de Aplicativo do Azure AD](active-directory-application-proxy-custom-domains.md) podem funcionar para você.
>
>Ou, se o aplicativo que você precisa configurar com a conversão de link é o SharePoint, consulte [Configurar mapeamentos alternativos de acesso para o SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx) a fim de conhecer outra abordagem de mapeamento de links.

## <a name="how-link-translation-works"></a>Como a translação de link funciona

Após a autenticação, quando o servidor proxy transmite os dados de aplicativo para o usuário, o Proxy de Aplicativo examina o aplicativo em relação a links inseridos no código e os substitui por suas respectivas URLs externas publicadas.

O Proxy de Aplicativo pressupõe que os aplicativos estejam codificados em UTF-8. Se esse não for o caso, especifique o tipo de codificação em um cabeçalho de resposta HTTP, como `Content-Type:text/html;charset=utf-8`.

### <a name="which-links-are-affected"></a>Quais links são afetados?

O recurso de translação de link procura apenas por links que estão nas marcas de código no corpo do aplicativo. O Proxy de Aplicativo tem um recurso separado para converter cookies ou URLs em cabeçalhos. 

Há dois tipos comuns de links internos em aplicativos locais:

- **Links internos relativos** que apontam para um recurso compartilhado em uma estrutura de arquivo local como `/claims/claims.html`. Esses links funcionam automaticamente em aplicativos que são publicados por meio do Proxy de Aplicativo e continuam funcionando com ou sem a conversão de link. 
- **Links internos inseridos no código** para outros aplicativos de locais como `http://expenses` ou arquivos publicados como `http://expenses/logo.jpg`. O recurso de translação de link funciona em links internos inseridos no código e os altera para apontar para as URLs externas de que os usuários remotos precisam para avançar.

### <a name="how-do-apps-link-to-each-other"></a>Como aplicativos são vinculados entre si?

A translação de link está habilitada para cada aplicativo, para que você tenha controle sobre a experiência do usuário no nível por aplicativo. Ative a translação de link para um aplicativo quando desejar que os links *desse* aplicativo sejam convertidos, não os links *para* ele. 

Por exemplo, suponha que você tenha três aplicativos publicados por meio do Proxy de Aplicativo que estão vinculados entre si: Benefícios, Despesas e Viagem. Há um quarto aplicativo, Comentários, que não está publicado pelo Proxy de Aplicativo.

Quando você habilita a translação de link para o aplicativo Benefícios, os links para Despesas e Viagem são redirecionados para as URLs externas para esses aplicativos, mas o link para Comentários não é redirecionado porque não há nenhuma URL externa. Os links de Despesas e Viagem para Benefícios não funcionam, pois a translação de link não foi habilitada para esses dois aplicativos.

![Os links de Benefícios para outros aplicativos quando a translação de link está habilitada](./media/application-proxy-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Quais links não são convertidos?

Para melhorar o desempenho e a segurança, alguns links não são convertidos:

- Links que não estão dentro de marcas de código. 
- Links que não estão em HTML, CSS ou JavaScript. 
- Links internos abertos de outros programas. Links enviados por email, mensagem instantânea ou incluídos em outros documentos não serão convertidos. Os usuários precisam saber ir para a URL externa.

Se você precisar dar suporte a um desses dois cenários, use as mesmas URLs internas e externas em vez da conversão de link.  

## <a name="enable-link-translation"></a>Habilitar a translação de link

Começar a trabalhar com a translação de link é tão fácil quanto clicar em um botão:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Vá para **Azure Active Directory** > **Aplicativos empresariais** > **Todos os aplicativos** > selecione o aplicativo que deseja gerenciar > **Proxy de aplicativo**.
3. Mude **Converter URLs no corpo do aplicativo** para **Sim**.

   ![Selecione Sim para converter URLs no corpo do aplicativo](./media/application-proxy-link-translation/select_yes.png).
4. Selecione **Salvar** para salvar suas alterações.

Agora, quando os usuários acessarem esse aplicativo, o proxy examinará automaticamente para verificar a existência de URLs internas que foram publicadas por meio do Proxy de Aplicativo no locatário.

## <a name="send-feedback"></a>Enviar comentários

Queremos sua ajuda para fazer esse recurso funcionar para todos os seus aplicativos. Pesquisamos mais de 30 marcas em HTML e CSS e estamos considerando para quais casos do JavaScript dar suporte. Se você tiver um exemplo de links gerados que não estão sendo convertidos, envie um trecho de código para [Comentários de Proxy de Aplicativo](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Próximas etapas
[Usar domínios personalizados com o Proxy de Aplicativo do Azure AD](active-directory-application-proxy-custom-domains.md) para ter a mesma URL interna e externa

[Configurar mapeamentos alternativos de acesso para o SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
