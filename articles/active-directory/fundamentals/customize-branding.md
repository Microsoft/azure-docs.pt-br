---
title: Adicionar identidade visual à página de entrada da sua organização-Azure AD
description: Instruções sobre como adicionar a identidade visual da organização à página de entrada do Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/24/2020
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b69daf9e9fbbb3f6d6597a8e8349dc8bec61fbf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008767"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Adicionar identidade visual à página de entrada de sua empresa no Azure Active Directory
Use o logotipo da organização e os esquemas de cores personalizados para fornecer uma aparência consistente nas páginas de entrada do Azure AD (Azure Active Directory). Suas páginas de entrada são exibidas quando os usuários entram nos aplicativos baseados na Web da sua organização, como Microsoft 365, que usa o Azure AD como seu provedor de identidade.

>[!NOTE]
>A adição de identidade visual personalizada exige que você tenha licenças Azure Active Directory Premium 1 ou Premium 2. Para obter mais informações sobre licenciamento e edições, consulte [Inscrever-se no Azure AD Premium](active-directory-get-started-premium.md).<br><br>Azure AD Premium edições estão disponíveis para clientes na China usando a instância Mundial do Azure Active Directory. Atualmente, não há suporte para as edições Azure AD Premium no serviço do Azure operado pela 21Vianet na China. Para obter mais informações, fale conosco usando o [Fórum do Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customize-your-azure-ad-sign-in-page"></a>Personalizar a página de entrada do Azure AD
É possível personalizar as páginas de entrada do Azure AD que aparecem quando os usuários entram nos aplicativos específicos de locatário da organização, como `https://outlook.com/contoso.com`, ou ao passar uma variável de domínio como `https://passwordreset.microsoftonline.com/?whr=contoso.com`.

Sua identidade visual personalizada não aparecerá imediatamente quando os usuários acessarem sites como, www \. Office.com. Em vez disso, o usuário precisa entrar antes que a identidade visual personalizada seja exibida. Depois que o usuário tiver entrado, a identidade visual poderá levar 15 minutos ou mais para aparecer. 

> [!NOTE]
> Todos os elementos de identidade visual são opcionais. Por exemplo, se você especificar um logotipo de faixa sem imagem de plano de fundo, a página de entrada mostrará o logotipo com uma imagem de plano de fundo padrão do site de destino (por exemplo, Microsoft 365).<br><br>Além disso, a identidade visual da página de entrada não é transferida para contas pessoais da Microsoft. Se os usuários ou convidados de negócios entrarem usando uma conta pessoal da Microsoft, a página de entrada não refletirá a identidade visual da organização.

### <a name="to-customize-your-branding"></a>Para personalizar a identidade visual
1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador Global para o diretório.

2. Selecione **Azure Active Directory**, selecione **Identidade visual da empresa** e, em seguida, selecione **Configurar**.

    ![Contoso - Página de identidade visual da empresa, opção Configurar realçada](media/customize-branding/company-branding-configure-button.png)

3. Na página **Configurar a identidade visual da empresa**, forneça qualquer uma ou todas as informações a seguir.

    >[!IMPORTANT]
    >Todas as imagens personalizadas adicionadas a essa página têm restrições de tamanho de imagem (pixels) e de tamanho de arquivo (KB). Devido a essas restrições, você provavelmente precisará usar um editor de fotos para criar as imagens do tamanho certo.

    - **Configurações gerais**

        ![Configurar a página de identidade visual da empresa, com as configurações gerais concluídas](media/customize-branding/configure-company-branding-general-settings.png)

        - **Idioma.** O idioma é definido automaticamente como padrão e não pode ser alterado.
        
        - **Imagem de plano de fundo da página de entrada.** Selecione um arquivo de imagem .png ou .jpg para aparecer como plano de fundo nas páginas de entrada. A imagem será ancorada ao centro do navegador e será dimensionada para o tamanho do espaço visível. Você não pode selecionar uma imagem maior que 1920 x 1080 pixels de tamanho ou que tenha um tamanho de arquivo maior que 300 KB.
        
            É recomendável usar imagens sem um foco forte de assunto, por exemplo, uma caixa branca opaca aparece no centro da tela e pode cobrir qualquer parte da imagem, dependendo das dimensões do espaço visível.

        - **Logotipo de faixa.** Selecione uma versão .png ou .jpg do logotipo para aparecer na página de entrada, depois que o usuário inserir um nome de usuário e na página do portal **Meus Aplicativos**.
            
            A imagem não pode ser mais alta que 60 pixels ou maior que 280 pixels, e o arquivo não deve ser maior que 10 KB. É recomendável usar uma imagem transparente, já que o plano de fundo pode não ser adequado ao plano de fundo do logotipo. Além disso, é recomendável não adicionar preenchimento ao redor da imagem, pois pode fazer seu logotipo parecer pequeno. 

        - **Dica de nome de usuário.** Digite o texto de dica que aparecerá aos usuários, caso não se lembrem do nome de usuário. Esse texto deve ser Unicode, sem links ou código, e não pode exceder 64 caracteres. Se os visitantes entrarem no aplicativo, sugerimos que você não adicione essa dica.

        - **Texto e formatação da página de entrada.** Digite o texto que aparece na parte inferior da página de entrada. Você pode usar esse texto para comunicar informações adicionais como o número de telefone do seu suporte técnico ou uma instrução legal. Este texto deve ser Unicode e não exceder 1024 caracteres.

           Você pode personalizar o texto da página de entrada inserido. Para iniciar um novo parágrafo, use a tecla Enter duas vezes. Você também pode alterar a formatação do texto para incluir negrito, itálico, um link de sublinhado ou clicável. Use a sintaxe a seguir para adicionar formatação ao texto: 

          > Hiperlink ```[text](link)``` 
          
          > Negrito: ``` **text** ``` ou ``` __text__ ``` 
          
          > Itálico: ``` *text* ``` ou ``` _text_ ``` 
          
          > Aplicar ``` ++text++ ``` 

    - **Configurações avançadas**
            
        ![Configurar a página de identidade visual da empresa, com configurações avançadas concluídas](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Cor do plano de fundo da página de entrada.** Especifique a cor hexadecimal (por exemplo, branco é #FFFFFF) que aparecerá no lugar da sua imagem de plano de fundo em situações de conexão com pouca largura de banda. É recomendável a utilização da cor principal do logotipo do banner ou da cor da organização.

        - **Imagem do logotipo quadrado.** Selecione uma imagem. png (preferencial) ou. jpg do logotipo da sua organização para aparecer para os usuários durante o processo de instalação para novos dispositivos Windows 10 Enterprise. Essa imagem é usada somente para autenticação do Windows e aparece apenas em locatários que estão usando o [Windows Autopilot]( /windows/deployment/windows-autopilot/windows-10-autopilot) para implantação ou páginas de entrada de senha em outras experiências do Windows 10. Em alguns casos, ele também pode aparecer na caixa de diálogo de consentimento.
        
            A imagem não pode ter mais de com 240x240 pixels de tamanho e deve ter um tamanho de arquivo inferior a 10 KB. É recomendável usar uma imagem transparente, já que o plano de fundo pode não ser adequado ao plano de fundo do logotipo. Além disso, é recomendável não adicionar preenchimento ao redor da imagem, pois pode fazer seu logotipo parecer pequeno.
    
        - **Imagem do logotipo quadrado, tema escuro.** O mesmo que a imagem do logotipo quadrado acima. Essa imagem do logotipo substitui a imagem do logotipo quadrado quando usada com um plano de fundo escuro como, por exemplo, com telas ingressadas do Azure AD do Windows 10 durante a configuração inicial pelo usuário (OOBE).  Se o seu logotipo ficar bom em branco, azul escuro e planos de fundo pretos, você não precisará adicionar essa imagem. 
        
        - **Mostrar opção para permanecer conectado.** Você pode optar por permitir que seus usuários permaneçam conectados ao Azure AD até que explicitamente se desconectem. Se você escolher **não**, essa opção será ocultada e os usuários deverão entrar toda vez que o navegador for fechado e reaberto.

            Esse recurso só está disponível no objeto de identidade visual padrão e não em nenhum objeto específico de idioma. Para saber mais sobre como configurar e solucionar problemas de opção para permanecer conectado, consulte [Configurar o prompt ' permanecer conectado? ' para contas do Azure ad](keep-me-signed-in.md)
        
            >[!NOTE]
            >Alguns recursos do SharePoint Online e do Office 2010 dependem da capacidade dos usuários de poderem permanecer conectados. Se você definir essa opção como **Não**, os usuários poderão receber prompts adicionais e inesperados de entrada.
   

3. Após adicionar sua identidade visual, selecione **Salvar**.

    Se esse processo criar a primeira configuração de identidade visual personalizada, ele se tornará o padrão para o locatário. Se você tiver configurações adicionais, poderá escolher a configuração padrão.
    
    >[!IMPORTANT]
    >Para adicionar mais configurações de identidade visual corporativa ao locatário, você deverá escolher **Novo idioma** na página **Contoso - Identidade visual da empresa**. Isso abrirá a página **Identidade visual da empresa**, na qual você poderá seguir as mesmas etapas acima.

## <a name="update-your-custom-branding"></a>Atualizar a identidade visual personalizada
Após criar a identidade visual personalizada, você poderá retornar e fazer todas as alterações que desejar.

### <a name="to-edit-your-custom-branding"></a>Para editar a identidade visual personalizada
1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador Global para o diretório.

2. Selecione **Azure Active Directory**, selecione **Identidade visual da empresa** e, em seguida, selecione **Configurar**.

    ![Contoso - Página de identidade visual da empresa, com a configuração padrão mostrada](media/customize-branding/company-branding-default-config.png)

3. Na página **Configurar a identidade visual da empresa**, adicione, remova ou altere qualquer informação, com base nas descrições na seção [Personalizar a página de entrada do Azure AD](#customize-your-azure-ad-sign-in-page) deste artigo.

4. Clique em **Salvar**.

   Pode demorar até uma hora para que apareçam todas as alterações feitas à identidade visual da página de entrada.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Adicionar identidade visual da empresa específica a um idioma ao diretório
Não é possível alterar o idioma da configuração original do idioma padrão. No entanto, se você precisar de uma configuração em um idioma diferente, poderá criar uma nova configuração.

### <a name="to-add-a-language-specific-branding-configuration"></a>Para adicionar uma configuração de identidade visual específica do idioma

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador Global para o diretório.

2. Selecione **Azure Active Directory**, selecione **Identidade visual da empresa** e, em seguida, selecione **Novo idioma**.

    ![Contoso - Página de identidade visual da empresa, com a opção Novo idioma realçada](media/customize-branding/company-branding-new-language.png)

3. Na página **Configurar a identidade visual da empresa**, selecione o idioma (por exemplo, francês) e adicione as informações traduzidas, com base nas descrições na seção [Personalizar a página de entrada do Azure AD](#customize-your-azure-ad-sign-in-page) deste artigo.

4. Clique em **Salvar**.

    A página **Contoso – Identidade visual da empresa** é atualizada para mostrar a nova configuração em francês.

    ![Contoso-página de identidade visual da empresa, com a nova configuração de linguagem mostrada](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Adicionar a identidade visual personalizada às páginas
Adicione a identidade visual personalizada às páginas, modificando o final da URL com o texto, `?whr=yourdomainname`. Essa modificação funciona em várias páginas, incluindo a página de configuração de MFA (Autenticação Multifator), a página de configuração de SSPR (Autoatendimento de Redefinição de Senha) e a página de entrada.

**Exemplos:**

**URL original:** https://aka.ms/MFASetup<br>
**URL personalizada:**`https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**URL original:** https://aka.ms/SSPR<br>
**URL personalizada:**`https://passwordreset.microsoftonline.com/?whr=contoso.com`
