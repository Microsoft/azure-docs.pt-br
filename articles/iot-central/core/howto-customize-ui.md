---
title: Personalizar a interface do usuário de IoT Central do Azure | Microsoft Docs
description: Como personalizar o tema e os links de ajuda para seu aplicativo do Azure IoT central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 660f6dfae23331455408ae7127274550a3a4cc3a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90019828"
---
# <a name="customize-the-azure-iot-central-ui"></a>Personalizar a interface do usuário do IoT Central do Azure

Este artigo descreve como, como administrador, você pode personalizar a interface do usuário do seu aplicativo aplicando temas personalizados e modificando os links de ajuda para apontar para seus próprios recursos de ajuda personalizados. 

A captura de tela a seguir mostra uma página usando o tema padrão:

![Tema de IoT Central padrão](./media/howto-customize-ui/standard-ui.png)

A captura de tela a seguir mostra uma página usando uma captura de tela personalizada com os elementos de interface do usuário personalizados realçados:

![Tema de IoT Central personalizado](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Criar tema

Para criar um tema personalizado, navegue até a página **personalizar seu aplicativo** na seção **Administração** :

![Temas de IoT Central](./media/howto-customize-ui/themes.png)

Nesta página, você pode personalizar os seguintes aspectos do seu aplicativo:

### <a name="application-logo"></a>Logotipo do aplicativo

Uma imagem PNG, não maior que 1 MB, com um plano de fundo transparente. Este logotipo é exibido à esquerda na barra de título do aplicativo IoT Central.

Se a sua imagem de logotipo incluir o nome do seu aplicativo, você poderá ocultar o texto do nome do aplicativo. Para obter mais informações, consulte [gerenciar seu aplicativo](howto-administer.md#change-application-name-and-url).

### <a name="browser-icon-favicon"></a>Ícone do navegador (favicon)

Uma imagem PNG, não maior que 32 x 32 pixels, com um plano de fundo transparente. Um navegador da Web pode usar essa imagem na barra de endereços, no histórico, nos indicadores e na guia navegador.

### <a name="browser-colors"></a>Cores do navegador

Você pode alterar a cor do cabeçalho da página e a cor usada para botões de acentuação e outros destaques. Use um valor de cor hexadecimal de seis caracteres no formato `##ff6347` . Para obter mais informações sobre notação de cor de **valor hexadecimal** , consulte [cores HTML](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Você sempre pode reverter para as opções padrão na página **personalizar seu aplicativo** .

### <a name="changes-for-operators"></a>Alterações de operadores

Se um administrador criar um tema personalizado, os operadores e outros usuários do seu aplicativo não poderão mais escolher um tema nas **configurações**.

## <a name="replace-help-links"></a>Substituir links da ajuda

Para fornecer informações de ajuda personalizadas para seus operadores e outros usuários, você pode modificar os links no menu **ajuda** do aplicativo.

Para modificar os links da ajuda, navegue até a página **Personalizar ajuda** na seção **Administração** :

![Personalizar IoT Central links de ajuda](./media/howto-customize-ui/help-links.png)

Você também pode adicionar novas entradas ao menu ajuda e remover as entradas padrão:

![Ajuda personalizada do IoT Central](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Você sempre pode reverter para os links de ajuda padrão na página **Personalizar ajuda** .

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como personalizar a interface do usuário em seu aplicativo IoT Central, aqui estão algumas próximas etapas sugeridas:

- [Administrar seu aplicativo](./howto-administer.md)
- [Adicionar blocos ao dashboard](howto-add-tiles-to-your-dashboard.md)