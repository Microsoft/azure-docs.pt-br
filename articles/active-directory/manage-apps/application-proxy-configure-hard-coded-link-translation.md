---
title: Converter links e URLs do Proxy de Aplicativo do Azure AD | Microsoft Docs
description: Saiba como redirecionar links embutidos em código para aplicativos publicados com o proxy de aplicativo do Azure AD.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/15/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44266f7a7485b44ba60a27aadd7422e8d1c3acf4
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259399"
---
# <a name="redirect-hard-coded-links-for-apps-published-with-azure-ad-application-proxy"></a>Redirecionar links embutidos em código para aplicativos publicados com o Azure Proxy de Aplicativo do AD

O Proxy de Aplicativo do Azure AD disponibiliza seus aplicativos locais para usuários remotos ou que estão em seus próprios dispositivos. No entanto, alguns aplicativos foram desenvolvidos com links locais inseridos no HTML. Esses links não funcionam corretamente quando o aplicativo é usado remotamente. Quando você faz com que vários aplicativos locais apontem uns para os outros, os usuários esperam os links para continuar trabalhando quando não estiverem no escritório. 

A melhor maneira de garantir que os links funcionem corretamente dentro e fora de sua rede corporativa é configurar as URLs externas de seus aplicativos para que sejam as mesmas que as URLs internas. Use [domínios personalizados](application-proxy-configure-custom-domain.md) para configurar suas URLs externas para que tenham o nome de domínio corporativo em vez do domínio padrão do proxy de aplicativo.


Se você não pode usar domínios personalizados em seu locatário, há várias outras opções para fornecer essa funcionalidade. Todos esses também são compatíveis com domínios personalizados e entre si, assim você pode configurar domínios personalizados e outras soluções, se necessário.

> [!NOTE]
> A conversão de link não tem suporte para URLs internas embutidas em código geradas por meio de JavaScript.

**Opção 1: usar o Microsoft Edge** – essa solução só será aplicável se você planeja recomendar ou exigir que os usuários acessem o aplicativo por meio do navegador Microsoft Edge. Ele manipulará todas as URLs publicadas. 

**Opção 2: usar a extensão do MyApps** – essa solução exige que os usuários instalem uma extensão de navegador do lado do cliente, mas manipulará todas as URLs publicadas e funciona com navegadores mais populares. 

**Opção 3: usar a configuração de conversão de link** – esta é uma configuração do lado do administrador que é invisível para os usuários. No entanto, ele tratará URLs somente em HTML e CSS.   

Esses três recursos mantêm seus vínculos de trabalho, independentemente de onde os usuários estão. Quando você tem aplicativos que apontam diretamente para portas ou pontos de extremidade internos, pode mapear essas URLs internas para as URLs de Proxy de Aplicativo externas publicadas. 

 
> [!NOTE]
> A última opção é apenas para locatários que, por qualquer motivo, não podem usar domínios personalizados para ter as mesmas URLs internas e externas para seus aplicativos. Antes de habilitar esse recurso, verifique se [domínios personalizados no Proxy de Aplicativo do Azure AD](application-proxy-configure-custom-domain.md) podem funcionar para você. 
> 
> Ou, se o aplicativo que você precisa configurar com a conversão de link é o SharePoint, consulte [Configurar mapeamentos alternativos de acesso para o SharePoint 2013](/SharePoint/administration/configure-alternate-access-mappings) a fim de conhecer outra abordagem de mapeamento de links. 

 
### <a name="option-1-microsoft-edge-integration"></a>Opção 1: integração do Microsoft Edge 

Você pode usar o Microsoft Edge para proteger ainda mais seu aplicativo e conteúdo. Para usar essa solução, você precisa exigir/recomendar que os usuários acessem o aplicativo por meio do Microsoft Edge. Todas as URLs internas publicadas com o proxy de aplicativo serão reconhecidas pelo Edge e redirecionadas para a URL externa correspondente. Isso garante que todas as URLs internas inseridas no código funcionem, e se um usuário for ao navegador e digitar diretamente a URL interna, ela funcione mesmo se o usuário estiver remoto.  

Para saber mais, incluindo como configurar essa opção, consulte a documentação [gerenciar o acesso via Web usando o Edge for Ios e Android com Microsoft Intune](/mem/intune/apps/manage-microsoft-edge) .  

### <a name="option-2-myapps-browser-extension"></a>Opção 2: extensão de navegador MyApps 

Com a extensão de navegador My Apps, todas as URLs internas publicadas com o Proxy de Aplicativo são reconhecidas pelo Managed Browser e redirecionadas para a URL externa correspondente. Isso garante que todas as URLs internas inseridas no código funcionem, e se um usuário for à barra de endereço do navegador e digitar diretamente a URL interna, ela funcione mesmo se o usuário estiver remoto.  

Para usar esse recurso, o usuário deve fazer o download da extensão e estar conectado. Não há nenhuma outra configuração necessária para os administradores ou usuários. 

Para saber mais, incluindo como configurar essa opção, confira a documentação da [extensão do navegador myapps](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) .

### <a name="option-3-link-translation-setting"></a>Opção 3: configuração de conversão de link 

Quando a translação de link está habilitada, o serviço de Proxy de Aplicativo pesquisa o HTML e o CSS para links internos publicados e converte-os para que seus usuários obtenham uma experiência ininterrupta. Usar a extensão de navegador myapps é preferencial para a configuração de conversão de link, pois oferece uma experiência mais eficaz aos usuários.

> [!NOTE]
> Se você estiver usando a opção 2 ou 3, apenas uma delas deverá ser habilitada por vez.

## <a name="how-link-translation-works"></a>Como a translação de link funciona

Após a autenticação, quando o servidor proxy passa os dados do aplicativo para o usuário, o proxy de aplicativo examina o aplicativo em busca de links embutidos em código e os substitui por suas respectivas URLs externas publicadas.

O Proxy de Aplicativo pressupõe que os aplicativos estejam codificados em UTF-8. Se esse não for o caso, especifique o tipo de codificação em um cabeçalho de resposta HTTP, como `Content-Type:text/html;charset=utf-8` .

### <a name="which-links-are-affected"></a>Quais links são afetados?

O recurso de translação de link procura apenas por links que estão nas marcas de código no corpo do aplicativo. O Proxy de Aplicativo tem um recurso separado para converter cookies ou URLs em cabeçalhos. 

Há dois tipos comuns de links internos em aplicativos locais:

- **Links internos relativos** que apontam para um recurso compartilhado em uma estrutura de arquivo local como `/claims/claims.html`. Esses links funcionam automaticamente em aplicativos que são publicados por meio do Proxy de Aplicativo e continuam funcionando com ou sem a conversão de link. 
- **Links internos** embutidos em código para outros aplicativos locais como `http://expenses` ou arquivos publicados como `http://expenses/logo.jpg` . O recurso de conversão de link funciona em links internos embutidos em código e os altera para apontar para as URLs externas que os usuários remotos precisam seguir.

A lista completa de atributos em marcas de código HTML que o proxy de aplicativo dá suporte à conversão de links para o incluem:
* a (href)
* áudio (src)
* base (href)
* botão (formaaction)
* div (dados-plano de fundo, estilo, dados-src)
* Inserir (src)
* formulário (ação)
* quadro (src)
* cabeçalho (perfil)
* HTML (manifesto)
* iframe (longdesc, src)
* img (longdesc, src)
* Input (forma, src, value)
* link (href)
* MenuItem (ícone)
* meta (conteúdo)
* objeto (arquivo morto, dados, codebase)
* script (src)
* origem (src)
* Track (src)
* vídeo (src, pôster)

Além disso, no CSS, o atributo URL também é traduzido.

### <a name="how-do-apps-link-to-each-other"></a>Como aplicativos são vinculados entre si?

A translação de link está habilitada para cada aplicativo, para que você tenha controle sobre a experiência do usuário no nível por aplicativo. Ative a translação de link para um aplicativo quando desejar que os links *desse* aplicativo sejam convertidos, não os links *para* ele. 

Por exemplo, suponha que você tenha três aplicativos publicados por meio do Proxy de Aplicativo que estão vinculados entre si: Benefícios, Despesas e Viagem. Há um quarto aplicativo, Comentários, que não está publicado pelo Proxy de Aplicativo.

Quando você habilita a translação de link para o aplicativo Benefícios, os links para Despesas e Viagem são redirecionados para as URLs externas para esses aplicativos, mas o link para Comentários não é redirecionado porque não há nenhuma URL externa. Os links de Despesas e Viagem para Benefícios não funcionam, pois a translação de link não foi habilitada para esses dois aplicativos.

![Os links de Benefícios para outros aplicativos quando a translação de link está habilitada](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Quais links não são convertidos?

Para melhorar o desempenho e a segurança, alguns links não são convertidos:

- Links que não estão dentro de marcas de código. 
- Links que não estão em HTML ou CSS. 
- Links em formato codificado de URL.
- Links internos abertos de outros programas. Links enviados por email, mensagem instantânea ou incluídos em outros documentos não serão convertidos. Os usuários precisam saber ir para a URL externa.

Se você precisar dar suporte a um desses dois cenários, use as mesmas URLs internas e externas em vez da conversão de link.  

## <a name="enable-link-translation"></a>Habilitar a translação de link

Começar a trabalhar com a translação de link é tão fácil quanto clicar em um botão:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Vá para **Azure Active Directory**  >  **aplicativos empresariais**  >  **todos os aplicativos** > selecione o aplicativo que você deseja gerenciar > **proxy de aplicativo**.
3. Mude **Converter URLs no corpo do aplicativo** para **Sim**.

   ![Selecione Sim para converter URLs no corpo do aplicativo](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Selecione **Salvar** para salvar suas alterações.

Agora, quando os usuários acessarem esse aplicativo, o proxy examinará automaticamente para verificar a existência de URLs internas que foram publicadas por meio do Proxy de Aplicativo no locatário.

## <a name="send-feedback"></a>Enviar comentários

Queremos sua ajuda para fazer esse recurso funcionar para todos os seus aplicativos. Podemos pesquisar mais de 30 marcas em HTML e CSS. Se você tiver um exemplo de links gerados que não estão sendo convertidos, envie um snippet de código para [Comentários de Proxy de Aplicativo](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Próximas etapas
[Usar domínios personalizados com o Azure proxy de aplicativo do AD](application-proxy-configure-custom-domain.md) para ter a mesma URL interna e externa

[Configurar mapeamentos alternativos de acesso para o SharePoint 2013](/SharePoint/administration/configure-alternate-access-mappings)
