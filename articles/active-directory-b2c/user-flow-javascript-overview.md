---
title: JavaScript e versões de layout de página-Azure Active Directory B2C | Microsoft Docs
description: Saiba como habilitar o JavaScript e usar versões de layout de página no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0eb5c89387d8bdcf0e0b72c669c42f716ff5fbb3
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227092"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>JavaScript e versões de layout de página no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C fornece um conjunto de conteúdo empacotado que contém HTML, CSS e JavaScript para os elementos da interface do usuário em seus fluxos de usuário e políticas personalizadas. Para habilitar o JavaScript para seus aplicativos, você deve adicionar um elemento à [política personalizada](active-directory-b2c-overview-custom.md) ou habilitá-lo no portal para fluxos de usuário, selecionar um layout de página e usar [b2clogin.com](b2clogin.md) em suas solicitações.

Se você pretende habilitar o código do lado do cliente [JavaScript](javascript-samples.md) , certifique-se de que os elementos nos quais você está baseando seu JavaScript sejam imutáveis. Caso contrário, as alterações podem causar um comportamento inesperado nas páginas do usuário. Para evitar esses problemas, você pode impor o uso de um layout de página e especificar uma versão de layout de página. Isso garante que todas as definições de conteúdo com base no seu JavaScript sejam imutáveis. Mesmo que você não pretenda habilitar o JavaScript, você pode especificar uma versão de layout de página para suas páginas.

## <a name="user-flows"></a>Fluxos de usuário

Nas **Propriedades**de fluxo do usuário, você pode habilitar o JavaScript, que também impõe o uso de um layout de página. Em seguida, você pode definir a versão de layout da página para o fluxo do usuário, conforme descrito na próxima seção.

![Página Propriedades do fluxo de usuário com a configuração Habilitar JavaScript realçada](media/user-flow-javascript-overview/javascript-settings.png)

### <a name="select-a-page-layout-version"></a>Selecionar uma versão de layout de página

Se você habilitar ou não o JavaScript nas propriedades do fluxo de usuário, poderá especificar uma versão de layout de página para suas páginas de fluxo de usuário. Abra o fluxo de usuário e selecione layouts de **página**. Em **nome do layout**, selecione uma página fluxo de usuário e escolha a **versão de layout da página**.

Para obter informações sobre as diferentes versões de layout de página, consulte o [log de alterações de versão](page-layout.md#version-change-log).

![Configurações de layout de página no portal mostrando o menu suspenso versão de layout de página](media/user-flow-javascript-overview/page-layout-version.png)

## <a name="custom-policies"></a>Políticas personalizadas

Para habilitar o JavaScript em políticas personalizadas, adicione o elemento **ScriptExecution** ao elemento **RelyingParty** em seu arquivo de política personalizado. Para obter mais informações, consulte [exemplos de JavaScript para uso em Azure Active Directory B2C](javascript-samples.md).

Se você habilitar ou não o JavaScript em suas políticas personalizadas, poderá especificar uma versão de layout de página para suas páginas. Para obter mais informações sobre como especificar um layout de página, consulte [selecionar um layout de página em Azure Active Directory B2C usando políticas personalizadas](page-layout.md).

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre as diferentes versões de layout de página, consulte a seção **log de alterações de versão** de [selecionar um layout de página em Azure Active Directory B2C usando políticas personalizadas](page-layout.md#version-change-log).

Você pode encontrar exemplos de uso de JavaScript em [exemplos de JavaScript para uso no Azure Active Directory B2C](javascript-samples.md).
