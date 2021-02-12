---
title: Versões do layout da página
titleSuffix: Azure AD B2C
description: Histórico de versão de layout de página para personalização de interface do usuário em políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 491549933817a127a92180b0e4b455e461e4d3a2
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492239"
---
# <a name="page-layout-versions"></a>Versões do layout da página

Os pacotes de layout de página são atualizados periodicamente para incluir correções e aprimoramentos em seus elementos de página. O log de alterações a seguir especifica as alterações introduzidas em cada versão.

## <a name="self-asserted-page-selfasserted"></a>Página autodeclarada (selfasserted)

**2.1.2**
- Correção do problema de codificação de localização para idiomas como espanhol e francês.

**2.1.1**

- Adicionou um UXString `heading` além de `intro` ser exibido na página como um título. Isso fica oculto por padrão.
- Adicionado suporte para salvar senhas no conjunto de chaves do iCloud.
- Adicionado suporte para usar a política ou o parâmetro QueryString `pageFlavor` para selecionar o layout (clássico, oceanBlue ou slateGray).
- Foram adicionadas isenções na página autodeclarada.
- O foco agora é colocado no primeiro campo editável quando a página é carregada.
- O foco agora é colocado no primeiro campo de erro quando vários campos têm erros.
- O foco agora é colocado no botão ' Alterar ' depois que o código de verificação de email é verificado.

**2.1.0**

- Correções de localização e acessibilidade.

**2.0.0**

- Adicionado suporte para [controles de exibição](display-controls.md) em políticas personalizadas.

**1.2.0**

- Os campos nome de usuário/email e senha agora usam o `form` elemento HTML para permitir que o Edge e o Internet Explorer (IE) salvem corretamente essas informações.
- Foi adicionado um atraso de validação de entrada de usuário configurável para melhorar a experiência do usuário.
-  Correções de acessibilidade
- Corrigido um problema de acessibilidade para que as mensagens de erro agora sejam lidas pelo Narrator. 
- O foco agora é colocado no campo de senha depois que o email é verificado.
- Removido `autofocus` do controle de caixa de seleção. 
- Adicionado suporte para um controle de exibição para a verificação do número de telefone.
- Agora você pode adicionar o `data-preload="true"` atributo [em suas marcas HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content)
  - Carregue arquivos CSS vinculados ao mesmo tempo que o modelo HTML para que ele não fique piscando entre o carregamento dos arquivos.
  - Controle a ordem na qual suas `script` marcas são buscadas e executadas antes do carregamento da página.
- O campo de email é agora `type=email` e teclados móveis fornecerão as sugestões corretas.
- Suporte para tradução do Chrome.
- Suporte adicionado para identidade visual da empresa em páginas de fluxo do usuário.

**1.1.0**

- Removido o alerta de cancelamento
- Classe CSS para elementos error
- Mostrar/ocultar a lógica de erro aprimorada
- CSS padrão removido

**1.0.0**

- Versão inicial

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>Página de inscrição de entrada unificada com link de redefinição de senha (unifiedssp)

**2.1.2**
- Correção do problema de codificação de localização para idiomas como espanhol e francês.
- Permitir o link "esqueceu a senha" para usar como troca de declarações como IDP social.

**2.1.1**
- Adicionou um UXString `heading` além de `intro` ser exibido na página como um título. Isso fica oculto por padrão.
- Adicionado suporte para usar a política ou o parâmetro QueryString `pageFlavor` para selecionar o layout (clássico, oceanBlue ou slateGray).
- Adicionado suporte para salvar senhas no conjunto de chaves do iCloud.
- O foco agora é colocado no primeiro campo de erro quando vários campos têm erros.
- O foco agora é colocado no primeiro campo editável quando a página é carregada.
- Adicionado um novo local para o link de seleção do provedor de declarações `bottomUnderFormClaimsProviderSelections` .
- UXStrings removidos que não são mais usados.

**2.1.0**

- Suporte adicionado para vários links de inscrição.
- Adicionado suporte para validação de entrada de usuário de acordo com as regras de predicado definidas na política.

**1.2.0**

- Os campos nome de usuário/email e senha agora usam o `form` elemento HTML para permitir que o Edge e o Internet Explorer (IE) salvem corretamente essas informações.
-  Correções de acessibilidade
- Agora você pode adicionar o `data-preload="true"` atributo [em suas marcas HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content) para controlar a ordem de carregamento para CSS e JavaScript.
  - Carregue arquivos CSS vinculados ao mesmo tempo que o modelo HTML para que ele não fique piscando entre o carregamento dos arquivos.
  - Controle a ordem na qual suas `script` marcas são buscadas e executadas antes do carregamento da página.
- O campo de email é agora `type=email` e teclados móveis fornecerão as sugestões corretas.
- Suporte para tradução do Chrome.
- Suporte adicionado para identidade visual do locatário em páginas de fluxo do usuário.

**1.1.0**

- Controle KMSI (Mantenha-me conectado) adicionado

**1.0.0**

- Versão inicial

## <a name="mfa-page-multifactor"></a>Página MFA (multifator)

**1.2.2**
- Corrigido um problema com o preenchimento automático do código de verificação ao usar o iOS.
- Corrigido um problema com o redirecionamento de um token para a terceira parte confiável da WebView do Android. 
- Adicionou um UXString `heading` além de `intro` ser exibido na página como um título. Isso fica oculto por padrão.  
- Adicionado suporte para usar a política ou o parâmetro QueryString `pageFlavor` para selecionar o layout (clássico, oceanBlue ou slateGray).

**1.2.1**

- Correções de acessibilidade em modelos padrão

**1.2.0**

-  Correções de acessibilidade
- Agora você pode adicionar o `data-preload="true"` atributo [em suas marcas HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content) para controlar a ordem de carregamento para CSS e JavaScript.
  - Carregue arquivos CSS vinculados ao mesmo tempo que o modelo HTML para que ele não fique piscando entre o carregamento dos arquivos.
  - Controle a ordem na qual suas `script` marcas são buscadas e executadas antes do carregamento da página.
- O campo de email agora `type=email` e teclados móveis fornecerão as sugestões corretas
- Suporte para tradução do Chrome.
- Suporte adicionado para identidade visual do locatário em páginas de fluxo do usuário.

**1.1.0**

- Botão ' confirmar código ' removido
- O campo de entrada para o código agora só usa a entrada de até seis (6) caracteres
- A página tentará automaticamente verificar o código inserido quando um código de seis dígitos for inserido, sem qualquer botão que precise ser clicado
- Se o código estiver errado, o campo de entrada será limpo automaticamente
- Depois de três (3) tentativas com um código incorreto, o B2C envia um erro de volta para a terceira parte confiável
-  Correções de acessibilidade
- CSS padrão removido

**1.0.0**

- Versão inicial

## <a name="exception-page-globalexception"></a>Página de exceção (globalexception)

**1.2.0**

-  Correções de acessibilidade
- Agora você pode adicionar o `data-preload="true"` atributo [em suas marcas HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content) para controlar a ordem de carregamento para CSS e JavaScript.
  - Carregue arquivos CSS vinculados ao mesmo tempo que o modelo HTML para que ele não fique piscando entre o carregamento dos arquivos.
  - Controle a ordem na qual suas `script` marcas são buscadas e executadas antes do carregamento da página.
- O campo de email agora `type=email` e teclados móveis fornecerão as sugestões corretas
- Suporte para tradução do Chrome

**1.1.0**

- Correção de acessibilidade
- Removeu a mensagem padrão quando não há contato da política
- CSS padrão removido

**1.0.0**

- Versão inicial

## <a name="other-pages-providerselection-claimsconsent-unifiedssd"></a>Outras páginas (ProviderSelection, ClaimsConsent, UnifiedSSD)

**1.2.0**

-  Correções de acessibilidade
- Agora você pode adicionar o `data-preload="true"` atributo [em suas marcas HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content) para controlar a ordem de carregamento para CSS e JavaScript.
  - Carregue arquivos CSS vinculados ao mesmo tempo que o modelo HTML para que ele não fique piscando entre o carregamento dos arquivos.
  - Controle a ordem na qual suas `script` marcas são buscadas e executadas antes do carregamento da página.
- O campo de email agora `type=email` e teclados móveis fornecerão as sugestões corretas
- Suporte para tradução do Chrome

**1.0.0**

- Versão inicial

## <a name="next-steps"></a>Próximas etapas

Para obter detalhes sobre como personalizar a interface do usuário de seus aplicativos em políticas personalizadas, consulte [Personalizar a interface do usuário do seu aplicativo usando uma política personalizada](customize-ui-with-html.md).
