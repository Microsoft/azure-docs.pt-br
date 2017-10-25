---
title: "Personalizar a página de entrada do Azure Active Directory | Microsoft Docs"
description: "Saiba como adicionar uma identidade visual à página de entrada do Azure"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 763bc597bdfc40395511cdd9d797e5c7aaad0fdf
ms.openlocfilehash: 6bfce3d4ec243779229cc4f39e1c22149229a66a
ms.contentlocale: pt-br
ms.lasthandoff: 09/06/2017

---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Início rápido: adicionar identidade visual da empresa à sua página de entrada no Azure AD
Para evitar confusão, muitas empresas desejam aplicar uma aparência consistente em todos os sites e serviços que elas gerenciam. O Azure AD (Azure Active Directory) fornece esse recurso permitindo que você personalize a aparência da página de entrada com esquemas de cores personalizados e o logotipo da empresa. A página de entrada é a página que aparece quando você entra no Office 365 ou em outros aplicativos baseados na Web que estejam usando o Azure AD como provedor de identidade. Você interage com essa página para inserir suas credenciais.

> [!NOTE]
> * A identidade visual da empresa estará disponível somente se você atualizar para a edição Premium ou Basic do Azure AD ou tiver uma licença do Office 365. Para saber se um recurso é compatível com seu tipo de licença, verifique a [página de informações de preço do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * As edições Premium e Basic do Active Directory do Azure estão disponíveis para clientes na China usando a instância mundial do Active Directory do Azure. As edições Azure Active Directory Premium e Basic não têm suporte atualmente no serviço Microsoft Azure operado pela 21Vianet na China. Para obter mais informações, entre em contato conosco no [Fórum do Active Directory do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>Personalização da página de entrada

<!--You can customize the following elements on the sign-in page: <attach image>-->

As personalizações de identidade visual da empresa serão exibidas na página de entrada do Azure AD quando os usuários acessarem uma URL específica de locatário, como [*https://outlook.com/contoso.com*](https://outlook.com/contoso.com).

Quando os usuários visitam uma URL genérica como www.office.com, a página de entrada não contém personalizações de identidade visual da empresa porque o sistema não sabe quem é o usuário. A identidade visual da empresa aparecerá depois que os usuários inserirem sua ID de usuário ou selecionarem um bloco do usuário.

> [!NOTE]
> * O nome do domínio deve aparecer como "Ativo" na parte **Domínios** do portal do Azure no qual você configurou a identidade visual. Para saber mais, confira [Adicionar um nome de domínio personalizado](add-custom-domain.md).
> * A identidade visual da página de entrada não é transferida para a página de logon para contas pessoais da Microsoft. Se os funcionários ou parceiros de negócios convidados entrarem com uma conta pessoal da Microsoft, sua página de entrada não reflete a identidade visual da sua organização.


### <a name="banner-logo"></a>Logotipo de faixa 

Descrição | Restrições | Recomendações
------- | ------- | ----------
O logotipo da faixa é exibido na páginas de entrada e do Painel de Acesso.<br>Ele é mostrado na página de entrada depois que a organização do usuário for determinada geralmente, depois que o nome de usuário for inserido.  | JPG ou PNG transparente<br>Altura máxima: 36 px<br>Largura máxima: 245 px | Use o logotipo da sua organização aqui.<br>Use uma imagem transparente. Não suponha que a tela de fundo será branco.<br>Não adicione preenchimento ao redor de seu logotipo na imagem ou seu logotipo parecerá desproporcionalmente pequeno.

### <a name="username-hint"></a>Dica do nome de usuário   
Descrição | Restrições | Recomendações
------- | ------- | ----------
Isso personaliza o texto de dica no campo de nome de usuário. | Texto em Unicode de até 64 caracteres<br>Somente texto sem formatação | Não é recomendável configurar essa opção se você espera que os usuários convidados de fora da sua organização entrem no seu aplicativo.
            
### <a name="sign-in-page-text"></a>Texto da página de entrada   
Descrição | Restrições | Recomendações
------- | ------- | ----------
Essa opção aparece na parte inferior do formulário de entrada e pode ser usada para comunicar informações adicionais, como o número de telefone para o suporte técnico ou uma instrução jurídica. | Texto em Unicode de até 256 caracteres<br>Apenas texto sem formatação (sem links ou marcas HTML)   

### <a name="sign-in-page-image"></a>Imagem da página de entrada  
Descrição | Restrições | Recomendações
------- | ------- | ----------
Essa opção aparece na tela de fundo da página de entrada, ela está ancorada no centro do espaço visível e irá ser dimensionada e cortada para preencher a janela do navegador.    <br>Em telas estreitas, como celulares, a imagem não é mostrada.<br>Uma máscara preta com opacidade de 0.55 será aplicada sobre essa imagem pelo nosso código quando a página for carregada. | JPG ou PNG<br>Dimensões da imagem: 1920 x 1080 px<br>Tamanho do arquivo: &lt; 300 KB | <br>Use imagens que não possuam um foco forte no assunto. O formulário de entrada opaco aparece sobre o centro da imagem e pode abranger qualquer parte da imagem, dependendo do tamanho da janela do navegador.<br>Mantenha o tamanho do arquivo o menor possível para garantir tempos de carregamento rápido. 

### <a name="background-color"></a>Cor da tela de fundo
Descrição | Restrições | Recomendações
------- | ------- | ----------
Essa cor é usada no lugar da imagem da tela de fundo em conexões de baixa largura de banda. | Cor RGB em hexadecimal (exemplo: #FFFFFF | Sugerimos usar a cor primária do logotipo da faixa ou a cor da sua organização.

### <a name="show-option-to-remain-signed-in"></a>Mostrar opção para permanecer conectado
Descrição | Restrições | Recomendações
------- | ------- | ----------
A entrada do Azure AD permite que o usuário permaneça conectado ao fechar e reabrir o navegador. Use isto para ocultar essa opção.<br>Defina como "Não" para ocultar essa opção dos seus usuários. | &nbsp; | Ela não afeta o tempo de vida da sessão.<br>Alguns recursos do SharePoint Online e do Office 2010 dependem da capacidade dos usuários de poderem permanecer conectados. Se você definir essa configuração como oculta, os usuários poderão ver avisos adicionais e inesperados para entrar.

> [!NOTE]
> Todos os elementos são opcionais. Por exemplo, se você especificar um logotipo de faixa sem nenhuma imagem de tela de fundo, a página de entrada mostrará seu logotipo e a imagem de tela de fundo para o site de destino (por exemplo, Office 365).

## <a name="add-company-branding-to-your-directory"></a>Adicionar identidade visual da empresa ao diretório

1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Escolha **Mais serviços**, insira **Usuários e grupos** na caixa de texto e selecione **Enter**.

   ![Abrir o gerenciamento de usuários](./media/customize-branding/user-management.png)
3. Na folha **Usuários e grupos**, selecione **Identidade visual da empresa**.
4. Na folha **Usuários e grupos - Identidade visual da empresa**, selecione o comando **Editar**.

    ![Editar identidade visual personalizada](./media/customize-branding/edit-branding.png)
5. Modifique os elementos que você deseja personalizar. Todos os elementos são opcionais.
6. Clique em **Salvar**.

Pode demorar até uma hora para que apareçam todas as alterações feitas à identidade visual da página de entrada.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Adicionar identidade visual da empresa específica a um idioma ao diretório

1. Entre no [centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que seja um administrador global para o diretório.
2. Escolha **Usuários e grupos** na caixa de texto e então selecione **Enter**.

   ![Abrir o gerenciamento de usuários](./media/customize-branding/user-management.png)
3. Na folha **Usuários e grupos**, selecione **Identidade visual da empresa**.
4. Na folha **Usuários e grupos - identidade visual da empresa**, selecione o comando **Adicionar idioma**.

    ![Adicionar elementos de identidade visual específicos do idioma](./media/customize-branding/add-language.png)
5. Modifique os elementos que você deseja personalizar. Todos os elementos são opcionais.
6. Clique em **Salvar**.

Pode demorar até uma hora para que apareçam todas as alterações feitas à identidade visual da página de entrada.

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você aprendeu a adicionar identidade visual da empresa ao seu diretório do Azure AD. 

Você pode usar o link a seguir para configurar a identidade visual da empresa no Azure AD no portal do Azure.

> [!div class="nextstepaction"]
> [Configurar a identidade visual da empresa](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 
