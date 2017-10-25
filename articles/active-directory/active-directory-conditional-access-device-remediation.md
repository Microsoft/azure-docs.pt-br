---
title: "Você não consegue chegar lá a partir daqui no portal do Azure em um dispositivo do Windows | Microsoft Docs"
description: "Saiba onde não é possível chegar lá a partir daqui e o que você pode verificar o para evitar essa caixa de diálogo."
services: active-directory
keywords: acesso condicional baseado em dispositivo, registro de dispositivo, habilitar registro de dispositivo, registro de dispositivo e MDM
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: d86e47507f7a578e8a0affb690b6d96673b6b69a
ms.contentlocale: pt-br
ms.lasthandoff: 08/29/2017

---
# <a name="you-cant-get-there-from-here-on-a-windows-device"></a>Não é possível chegar lá a partir deste ponto em um dispositivo do Windows

Durante uma tentativa de, por exemplo, acessar a intranet do SharePoint Online de sua organização, você pode encontrar uma página informando que *não é possível ir daqui até lá*. Você está vendo esta página, porque o administrador configurou uma política de acesso condicional que impede o acesso aos recursos de sua organização sob determinadas condições. Embora talvez seja necessário entrar em contato com a assistência técnica ou com o administrador para resolver esse problema, há algumas coisas que você pode experimentar por conta própria primeiro.

Se você estiver usando um dispositivo com **Windows**, verifique o seguinte:

- Você está usando um navegador com suporte?

- Você está executando uma versão com suporte do Windows em seu dispositivo?

- O dispositivo é compatível?






## <a name="supported-browser"></a>Navegador com suporte

Se o administrador tiver configurado uma política de acesso condicional, você só poderá acessar os recursos de sua organização usando um navegador com suporte. Em um dispositivo com Windows, há suporte apenas para o **Internet Explorer** e o **Edge**.

Basta olhar a seção de detalhes da página de erro para identificar facilmente se a falha no acesso a um recurso ocorreu devido à falta de suporte no navegador:

![Mensagem "Você não pode acessar esse lugar daqui" para navegadores sem suporte](./media/active-directory-conditional-access-device-remediation/02.png "Cenário")

A única correção consiste em usar um navegador ao qual o aplicativo dê suporte para sua plataforma de dispositivo. Para obter uma lista completa dos navegadores com suporte, consulte [Navegadores com suporte](active-directory-conditional-access-supported-apps.md).  


## <a name="supported-versions-of-windows"></a>Versões com suporte do Windows

As suposições a seguir devem ser verdadeiras com relação ao sistema operacional Windows em seu dispositivo: 

- Se você estiver executando um sistema operacional Windows para desktop em seu dispositivo, deverá ser o Windows 7 ou posterior.
- Se você estiver executando um sistema operacional Windows Server em seu dispositivo, deverá ser o Windows Server 2008 R2 ou posterior. 


## <a name="compliant-device"></a>Dispositivo em conformidade

O administrador pode ter configurado uma política de acesso condicional que permite o acesso aos recursos de sua organização somente em dispositivos compatíveis. Para ser compatível, seu dispositivo deve fazer parte de seu Active Directory local ou de seu Azure Active Directory.

Basta olhar a seção de detalhes da página de erro para identificar facilmente se a falha no acesso a um recurso ocorreu devido à falta de compatibilidade do dispositivo:
 
![Mensagens "Você não pode acessar esse lugar daqui" para dispositivos não registrados](./media/active-directory-conditional-access-device-remediation/01.png "Cenário")


### <a name="is-your-device-joined-to-an-on-premises-active-directory"></a>Seu dispositivo está associado a um Active Directory local?

**Se o dispositivo estiver associado a um Active Directory local em sua organização:**

1. Verifique se você entrou no Windows usando sua conta corporativa (a conta do Active Directory).
2. Conecte-se à rede corporativa por meio de uma rede virtual privada (VPN) ou DirectAccess.
3. Depois de conectado, pressione a tecla de logotipo do Windows + a tecla L para bloquear a sessão do Windows.
4. Desbloqueie a sessão do Windows inserindo as credenciais de sua conta corporativa.
5. Aguarde um minuto e tente acessar o aplicativo ou o serviço novamente.
6. Se você vir a mesma página, clique em **Mais detalhes** e contate seu administrador com os detalhes.


### <a name="is-your-device-not-joined-to-an-on-premises-active-directory"></a>Seu dispositivo não está associado a um Active Directory local?

Se o dispositivo não estiver associado a um Active Directory local e executar o Windows 10, você terá duas opções:

* Executar o Ingresso do Azure AD
* Adicionar sua conta corporativa ou de estudante ao Windows

Para saber mais sobre como essas opções são diferentes, veja [Usando dispositivos Windows 10 em seu local de trabalho](active-directory-azureadjoin-windows10-devices.md).  
Se o seu dispositivo:

- Pertencer à sua organização, execute o Ingresso no Azure AD.
- For um dispositivo pessoal ou um Windows phone, adicione sua conta corporativa de estudante ao Windows 



#### <a name="azure-ad-join-on-windows-10"></a>Ingresso no Azure AD no Windows 10

As etapas para ingressar seu dispositivo no Azure AD estão vinculadas a versão do Windows 10 que está em execução. Para determinar a versão do seu sistema operacional Windows 10, execute o comando **winver**: 

![Versão do Windows](./media/active-directory-conditional-access-device-remediation/03.png )


**Atualização de Aniversário do Windows 10 (Versão 1607):**

1. Abra o aplicativo **Configurações** .
2. Clique em **Contas** > **Acesso corporativo ou de estudante**.
3. Clique em **Conectar**.
4. Clique em **Unir este dispositivo ao Azure AD**.
5. Autentique para sua organização, forneça autenticação multifator, se solicitado, e siga as etapas mostradas.
6. Saia e entre com sua conta corporativa.
7. Tente acessar o aplicativo novamente.

**Atualização do Windows de 10 de novembro de 2015 (Versão 1511):**

1. Abra o aplicativo **Configurações** .
2. Clique em **Sistema** > **Sobre**.
3. Clique em **Ingressar no Azure AD**.
4. Autentique para sua organização, forneça autenticação multifator, se solicitado, e siga as etapas mostradas.
5. Saia e entre com sua conta corporativa (sua conta do Azure AD).
6. Tente acessar o aplicativo novamente.


#### <a name="workplace-join-on-windows-81"></a>Workplace Join para Windows 8.1

Se o dispositivo não estiver unido ao domínio e executar o Windows 8.1, você poderá executar o Workplace Join e registrar-se no Microsoft Intune, execute estas etapas:

1. Abra **Configurações do PC**.
2. Clique em **Rede** > **Local de Trabalho**.
3. Clique em **Ingressar**.
4. Autentique para sua organização, forneça autenticação multifator, se solicitado, e siga as etapas mostradas.
5. Clique em **Ativar**.
6. Tente acessar o aplicativo novamente.



#### <a name="add-your-work-or-school-account-to-windows"></a>Adicionar sua conta corporativa ou de estudante ao Windows 


**Atualização de Aniversário do Windows 10 (Versão 1607):**

1. Abra o aplicativo **Configurações** .
2. Clique em **Contas** > **Acesso corporativo ou de estudante**.
3. Clique em **Conectar**.
4. Autentique para sua organização, forneça autenticação multifator, se solicitado, e siga as etapas mostradas.
5. Tente acessar o aplicativo novamente.


**Atualização do Windows de 10 de novembro de 2015 (Versão 1511):**

1. Abra o aplicativo **Configurações** .
2. Clique em **Contas** > **Suas contas**.
3. Clique em **Adicionar conta corporativa ou de estudante**.
4. Autentique para sua organização, forneça autenticação multifator, se solicitado, e siga as etapas mostradas.
5. Tente acessar o aplicativo novamente.





## <a name="next-steps"></a>Próximas etapas
[Acesso condicional ao Azure Active Directory](active-directory-conditional-access.md)


