---
title: Método de autenticação de tokens OATH-Azure Active Directory
description: Saiba mais sobre como usar tokens OATH no Azure Active Directory para ajudar a melhorar e proteger eventos de entrada
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45b3d5155befe005a6af68355426883b63a8c410
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611779"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Métodos de autenticação em tokens Azure Active Directory OATH 

O OATH TOTP (senha de uso único por tempo limitado) é um padrão aberto que especifica os códigos de OTP (senha de uso único) são gerados. O OATH TOTP pode ser implementado usando software ou hardware para gerar os códigos. O Azure AD não dá suporte a HOTP OATH, um padrão diferente de geração de código.

## <a name="oath-software-tokens"></a>Tokens de software OATH

Os tokens de software OATH normalmente são aplicativos como o Microsoft Authenticator e outros aplicativos de autenticação. O Azure AD gera a chave secreta ou semente, que é inserida no aplicativo e usada para gerar cada OTP.

O aplicativo Authenticator gera códigos automaticamente quando configurado para fazer notificações por push para que um usuário tenha um backup, mesmo que seu dispositivo não tenha conectividade. Aplicativos de terceiros que usam OATH TOTP para gerar códigos também podem ser usados.

Alguns tokens de hardware OATH TOTP são programáveis, o que significa que eles não vêm com uma chave secreta ou semente previamente programada. Esses tokens de hardware programáveis podem ser configurados usando a chave secreta ou semente obtida do fluxo de configuração do token de software. Os clientes podem comprar esses tokens do fornecedor que preferirem e usar a chave secreta ou semente no processo de configuração do fornecedor.

## <a name="oath-hardware-tokens-preview"></a>Tokens de hardware OATH (visualização)

O Azure AD dá suporte ao uso de tokens OATH-TOTP SHA-1 que atualizam códigos a cada 30 ou 60 segundos. Os clientes podem adquirir esses tokens do fornecedor que preferirem.

Os tokens de hardware OATH TOTP normalmente vêm com uma chave secreta ou semente previamente programada no token. Essas chaves devem ser inseridas no Azure AD, conforme descrito nas etapas a seguir. As chaves secretas estão limitadas a 128 caracteres, que podem não ser compatíveis com todos os tokens. A chave secreta pode conter apenas os caracteres *de a-z* ou *a-z* e os dígitos *2-7* e deve ser codificada em *Base32*.

Os tokens de hardware OATH TOTP programáveis que podem ser propagados também podem ser configurados com o Azure AD no fluxo de instalação do token de software.

Os tokens de hardware OATH agora são compatíveis como parte de uma versão prévia pública. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![Carregar tokens OATH para a folha de tokens OATH da MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Depois que os tokens são adquiridos, eles devem ser carregados em um formato de arquivo CSV (valores separados por vírgula), incluindo o UPN, o número de série, a chave secreta, o intervalo de tempo, o fabricante e o modelo, conforme mostrado no exemplo a seguir:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,2234567abcdef2234567abcdef,60,Contoso,HardwareKey
```  

> [!NOTE]
> Verifique se você incluiu a linha de cabeçalho no arquivo CSV. Se um UPN tiver uma aspa simples, escape-o com outra aspa simples. Por exemplo, se o UPN for my ' user@domain.com , altere-o para meu ' ' user@domain.com ao carregar o arquivo.

Uma vez formatado corretamente como um arquivo CSV, um administrador global pode entrar no portal do Azure, navegar até **Azure Active Directory > Security > MFA > tokens OATH** e carregar o arquivo CSV resultante.

Dependendo do tamanho do arquivo CSV, ele poderá levar alguns minutos para ser processado. Selecione o botão **Atualizar** para obter o status atual. Se houver erros no arquivo, você poderá baixar um arquivo CSV que lista os erros para que você possa resolvê-los. Os nomes de campo no arquivo CSV baixado são diferentes da versão carregada.  

Depois que os erros forem resolvidos, o administrador poderá ativar cada chave selecionando **Ativar** para o token e inserindo a OTP exibido no token. Você pode ativar um máximo de 200 tokens OATH a cada 5 minutos. 

Os usuários podem ter uma combinação de até cinco tokens de hardware OATH ou aplicativos de autenticação, como o Microsoft Authenticator, configurados para uso a qualquer momento.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como configurar métodos de autenticação usando a [API REST do Microsoft Graph versão beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).
