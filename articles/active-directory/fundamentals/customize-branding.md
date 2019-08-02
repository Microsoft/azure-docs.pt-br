---
title: Adicionar identidade visual à página de entrada de sua organização – Azure Active Directory | Microsoft Docs
description: Instruções sobre como adicionar a identidade visual da organização à página de entrada do Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: lizross
ms.reviewer: kexia
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: bef3960e5c117a131b59a65196214c51e01bd3c8
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204589"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Adicionar identidade visual à página de entrada de sua empresa no Azure Active Directory
Use o logotipo da organização e os esquemas de cores personalizados para fornecer uma aparência consistente nas páginas de entrada do Azure AD (Azure Active Directory). As páginas de entrada aparecem quando os usuários entram nos aplicativos baseados na Web da organização, como o Office 365, que usam o Azure AD como provedor de identidade.

>[!Note]
>Adicionar identidade visual personalizada exige que você use as edições do Azure Active Directory Premium 1, Premium 2 ou Básico, ou que tenha uma licença do Office 365. Para obter mais informações sobre licenciamento e edições, consulte [Inscrever-se no Azure AD Premium](active-directory-get-started-premium.md).<br><br>As edições Azure AD Premium e Básico estão disponíveis para clientes na China por meio da instância mundial do Azure Active Directory. Atualmente, as edições Azure AD Premium e Básico não têm suporte no serviço do Azure operado pela 21Vianet na China. Para obter mais informações, fale conosco usando o [Fórum do Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customize-your-azure-ad-sign-in-page"></a>Personalizar a página de entrada do Azure AD
É possível personalizar as páginas de entrada do Azure AD que aparecem quando os usuários entram nos aplicativos específicos de locatário da organização, como [ *https://outlook.com/contoso.com* ](https://outlook.com/contoso.com), ou ao passar uma variável de domínio como [ *https://passwordreset.microsoftonline.com/?whr=contoso.com* ](https://passwordreset.microsoftonline.com/?whr=contoso.com).

Sua identidade visual personalizada não aparecerá imediatamente quando os usuários vão para sites, como www\.office.com. Em vez disso, o usuário precisa entrar antes que a identidade visual personalizada seja exibida.

> [!NOTE]
> Todos os elementos de identidade visual são opcionais. Por exemplo, se você especificar um logotipo do banner sem imagem de plano de fundo, a página de entrada mostrará o logotipo com uma imagem de plano de fundo padrão do site de destino (por exemplo, Office 365).<br><br>Além disso, a marca da página de entrada não é transferida para contas pessoais da Microsoft. Se os usuários ou convidados de negócios entrarem usando uma conta pessoal da Microsoft, a página de entrada não refletirá a identidade visual da organização.

### <a name="to-customize-your-branding"></a>Para personalizar a identidade visual
1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador Global para o diretório.

2. Selecione **Azure Active Directory**, selecione **Identidade visual da empresa** e, em seguida, selecione **Configurar**.

    ![Contoso - Página de identidade visual da empresa, opção Configurar realçada](media/customize-branding/company-branding-configure-button.png)

3. Na página **Configurar a identidade visual da empresa**, forneça qualquer uma ou todas as informações a seguir.

    >[!Important]
    >Todas as imagens personalizadas adicionadas a essa página têm restrições de tamanho de imagem (pixels) e de tamanho de arquivo (KB). Devido a essas restrições, você provavelmente precisará usar um editor de fotos para criar as imagens do tamanho certo.

    - **Configurações gerais**

        ![Configurar a página de identidade visual da empresa, com as configurações gerais concluídas](media/customize-branding/configure-company-branding-general-settings.png)

        - **Idioma.** O idioma é definido automaticamente como padrão e não pode ser alterado.
        
        - **Imagem de plano de fundo da página de entrada.** Selecione um arquivo de imagem .png ou .jpg para aparecer como plano de fundo nas páginas de entrada. 
        
            A imagem não pode ter mais de 1920x1080 pixels de tamanho e deve ter um tamanho de arquivo inferior a 300 KB.

        - **Logotipo do banner.** Selecione uma versão .png ou .jpg do logotipo para aparecer na página de entrada, depois que o usuário inserir um nome de usuário e na página do portal **Meus Aplicativos**.
            
            A imagem não pode ser mais alto que 60 pixels ou mais de 280 pixels. É recomendável usar uma imagem transparente, já que o plano de fundo pode não ser adequado ao plano de fundo do logotipo. Além disso, é recomendável não adicionar preenchimento ao redor da imagem, pois pode fazer seu logotipo parecer pequeno.

        - **Dica de nome de usuário.** Digite o texto de dica que aparecerá aos usuários, caso não se lembrem do nome de usuário. Esse texto deve ser Unicode, sem links ou código, e não pode exceder 64 caracteres. Se os visitantes entrarem no aplicativo, sugerimos que você não adicione essa dica.

        - **Texto da página de entrada.** Digite o texto que aparece na parte inferior da página de entrada. Você pode usar esse texto para comunicar informações adicionais como o número de telefone do seu suporte técnico ou uma instrução legal. Esse texto deve ser Unicode e não deve exceder 256 caracteres. É recomendável também não incluir links ou marcas HTML.

    - **Configurações avançadas**
            
        ![Configurar a página de identidade visual da empresa, com configurações avançadas concluídas](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Cor do plano de fundo da página de entrada.** Especifique a cor hexadecimal (por exemplo, branco é #FFFFFF) que aparecerá no lugar da sua imagem de plano de fundo em situações de conexão com pouca largura de banda. É recomendável a utilização da cor principal do logotipo do banner ou da cor da organização.

        - **Imagem de logotipo quadrado.** Selecione uma imagem .png (preferencial) ou .jpg do logotipo da organização para ser exibida aos usuários durante o processo de configuração dos novos dispositivos do Windows 10 Enterprise. Essa imagem é usada somente para autenticação do Windows e aparece apenas em locatários que estão usando o [Windows Autopilot]( https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) para implantação ou páginas de entrada de senha em outras experiências do Windows 10. Em alguns casos elas talvez apareçam na caixa de diálogo de consentimento.
        
            A imagem não pode ser maior que 240x240 pixels e deve ter um tamanho de arquivo inferior a 10 KB. É recomendável usar uma imagem transparente, já que o plano de fundo pode não ser adequado ao plano de fundo do logotipo. Além disso, é recomendável não adicionar preenchimento ao redor da imagem, pois pode fazer seu logotipo parecer pequeno.
    
        - **Imagem do logotipo quadrado, tema escuro.** O mesmo que a imagem do logotipo quadrado acima. Essa imagem do logotipo substitui a imagem do logotipo quadrado quando usada com um plano de fundo escuro como, por exemplo, com telas ingressadas do Azure AD do Windows 10 durante a configuração inicial pelo usuário (OOBE).  Se o logotipo ficar com uma boa imagem em planos de fundo branco, azul escuro e preto, não será necessário adicionar essa imagem. 
        
        - **Mostrar opção para permanecer conectado.** Você pode optar por permitir que os usuários permaneçam conectados ao Azure AD até que sejam desconectados explicitamente. Se você escolher **Não**, essa opção ficará oculta e os usuários deverão entrar sempre que o navegador for fechado e reaberto.
        
            >[!Note]
            >Alguns recursos do SharePoint Online e do Office 2010 dependem da capacidade dos usuários de poderem permanecer conectados. Se você definir essa opção como **Não**, os usuários poderão receber prompts adicionais e inesperados de entrada.
   

3. Após adicionar sua identidade visual, selecione **Salvar**.

    Se esse processo criar a primeira configuração de identidade visual personalizada, ele se tornará o padrão para o locatário. Se você tiver configurações adicionais, poderá escolher a configuração padrão.
    
    >[!Important]
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

    ![Contoso - Página de identidade visual da empresa, com a configuração padrão mostrada](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Adicionar a identidade visual personalizada às páginas
Adicione a identidade visual personalizada às páginas, modificando o final da URL com o texto, `?whr=yourdomainname`. Essa modificação funciona em várias páginas, incluindo a página de configuração de MFA (Autenticação Multifator), a página de configuração de SSPR (Autoatendimento de Redefinição de Senha) e a página de entrada.

**Exemplos:**

**URL original:** https://aka.ms/MFASetup<br>
**URL personalizada:** https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com

**URL original:** https://aka.ms/SSPR<br>
**URL personalizada:** https://passwordreset.microsoftonline.com/?whr=contoso.com

 
