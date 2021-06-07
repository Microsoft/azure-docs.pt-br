---
title: Aprofundamento de segurança da Autenticação de Passagem do Azure Active Directory | Microsoft Docs
description: Este artigo descreve como a Autenticação de Passagem do Azure Active Directory (Azure AD) protege suas contas locais
services: active-directory
keywords: Autenticação de Passagem do Azure AD Connect, instalar o Active Directory, componentes necessários para o Azure AD, SSO, Logon único
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08a73c2b1be4b17136ba19e7efb71c2b21359fdf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89280138"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Aprofundamento de segurança da Autenticação de Passagem do Azure Active Directory

Este artigo fornece uma descrição mais detalhada sobre como funciona a Autenticação de Passagem do Azure Active Directory (Azure AD). O foco está nos aspectos de segurança do recurso. Este artigo destina-se a administradores de TI e segurança, diretores de conformidade e segurança e outros profissionais de TI, responsáveis pela segurança e conformidade de TI em organizações de pequeno e médio porte ou em grandes empresas.

Os tópicos abordados incluem:
- Informações técnicas detalhadas sobre como instalar e registrar os Agentes de Autenticação.
- Informações técnicas detalhadas sobre criptografia de senhas durante a entrada do usuário.
- A segurança dos canais entre os Agentes de Autenticação locais e o Azure AD.
- Informações técnicas detalhadas sobre como manter os Agentes de Autenticação operacionalmente seguros.
- Outros tópicos relacionados à segurança.

## <a name="key-security-capabilities"></a>Principais recursos de segurança

Estes são os principais aspectos de segurança desse recuso:
- Ele foi criado em uma arquitetura multilocatária segura que fornece isolamento de solicitações de entrada entre locatários.
- Senhas locais nunca são armazenadas na nuvem, em formato nenhum.
- Os Agentes de Autenticação locais que escutam e respondem às solicitações de validação de senha fazem conexões de saída apenas dentro de sua rede. Não há necessidade de instalar esses Agentes de Autenticação em uma rede de perímetro (DMZ). Como melhor prática, trate todos os servidores que estão executando Agentes de Autenticação como sistemas de Tier 0 (veja a [referência](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).
- Apenas as portas padrão (80 e 443) são usadas para comunicação de saída dos Agentes de Autenticação para o Azure AD. Você não precisa abrir portas de entrada no firewall. 
  - A porta 443 é usada para todas as comunicações de saída autenticadas.
  - A porta 80 é usada apenas para fazer o download das CRLs (Listas de Certificados Revogados) de modo a garantir que nenhum dos certificados usados pelo recurso tenha sido revogado.
  - Para obter a lista completa dos requisitos de rede, consulte [Azure Active Directory autenticação de passagem: início rápido](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).
- As senhas que os usuários fornecem durante a entrada são criptografadas na nuvem antes que os Agentes de Autenticação locais as aceitem para validação no Active Directory.
- O canal HTTPS entre o Azure AD e o Agente de Autenticação local é protegido usando autenticação mútua.
- Proteja suas contas de usuário trabalhando diretamente com as [Políticas de acesso condicional do Azure AD](../conditional-access/overview.md), incluindo a MFA (Autenticação Multifator), [bloqueando autenticação herdada](../conditional-access/concept-conditional-access-conditions.md) e [filtrando ataques de senha de força bruta](../authentication/howto-password-smart-lockout.md).

## <a name="components-involved"></a>Componentes envolvidos

Para obter detalhes gerais sobre o Azure AD operacional, serviço e segurança de dados, consulte a [central de confiabilidade](https://azure.microsoft.com/support/trust-center/). Os seguintes componentes são envolvidos quando você usa a Autenticação de Passagem para a entrada do usuário:
- **STS do Azure ad**: um STS (serviço de token de segurança) sem estado que processa solicitações de entrada e emite tokens de segurança para os navegadores, clientes ou serviços dos usuários, conforme necessário.
- **Barramento de Serviço do Microsoft Azure**: Fornece comunicação habilitada para nuvem com sistema de mensagens empresarial e comunicação de retransmissões que ajudam a conectar soluções locais com a nuvem.
- **Agente de Autenticação do Azure AD Connect**: um componente local que escuta e responde às solicitações de validação de senha.
- **Banco de Dados SQL do Azure**: contém informações sobre os Agentes de Autenticação do locatário, incluindo as chaves de criptografia e metadados.
- **Active Directory**: o Active Directory local, onde suas contas de usuário e respectivas senhas são armazenadas.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Instalação e registro dos Agentes de Autenticação

Os Agentes de Instalação são instalados e registrados com o Azure AD quando você:
   - [Habilitar a autenticação de passagem por meio do Azure AD Connect](./how-to-connect-pta-quick-start.md#step-2-enable-the-feature)
   - [Adicionar mais Agentes de Autenticação para garantir a alta disponibilidade das solicitações de entrada](./how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) 
   
Obter um Agente de Autenticação em funcionamento envolve três fases principais:

1. Instalação do Agente de Autenticação
2. Registro do Agente de Autenticação
3. Inicialização do Agente de Autenticação

As seções a seguir abordam essas fases detalhadamente.

### <a name="authentication-agent-installation"></a>Instalação do Agente de Autenticação

Somente os administradores globais podem instalar um Agente de Autenticação (usando o Azure AD Connect ou autônomo) em um servidor local. A instalação adiciona duas novas entradas à   >  lista **programas**  >  **e recursos** do painel de controle:
- O próprio aplicativo do Agente de Autenticação. Esse aplicativo é executado com privilégios [NetworkService](/windows/win32/services/networkservice-account).
- O aplicativo do Atualizador, que é usado para atualizar automaticamente o Agente de Autenticação. Esse aplicativo é executado com os privilégios [LocalSystem](/windows/win32/services/localsystem-account).

>[!IMPORTANT]
>Do ponto de vista da segurança, os administradores devem tratar o servidor que executa o agente PTA como se fosse um controlador de domínio.  Os servidores do agente PTA devem ser protegidos ao longo das mesmas linhas, conforme descrito em [protegendo controladores de domínio contra ataques](/windows-server/identity/ad-ds/plan/security-best-practices/securing-domain-controllers-against-attack)

### <a name="authentication-agent-registration"></a>Registro do Agente de Autenticação

Após instalar o Agente de Autenticação, será necessário registrá-lo com o Azure AD. O Azure AD atribui a cada Agente de Autenticação um certificado de identidade digital exclusivo, que poderá ser usado para comunicação segura com o Azure AD.

O procedimento de registro também associa o Agente de Autenticação ao seu locatário. Isso garante que o Azure AD saiba que esse Agente de Autenticação específico é o único autorizado a tratar das solicitações de validação de senha para seu locatário. Esse procedimento é repetido para cada novo Agente de Autenticação registrado.

Os Agentes de Autenticação usam as seguintes etapas para se registrar com o Azure AD:

![Registro do Agente](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. Primeiro, o Azure AD solicita que um administrador global entre no Azure AD com as respectivas credenciais. Durante a entrada, o Agente de Autenticação adquire um token de acesso que pode ser usado em nome do administrador global.
2. Em seguida, o Agente de Autenticação gera um par de chaves: uma chave pública e uma chave privada.
    - Esse par de chaves é gerado usando criptografia RSA de 2048 bits.
    - A chave privada permanece no servidor local em que reside o Agente de Autenticação.
3. O Agente de Autenticação realiza uma solicitação de "registro" para o Azure AD sobre HTTPS com os seguintes componentes incluídos na solicitação:
    - O token de acesso adquirido na etapa 1.
    - A chave pública gerada na etapa 2.
    - Uma Solicitação de Assinatura de Certificado (CSR ou Solicitação de Certificado). Essa solicitação se aplica a um certificado de identidade digital, com o Azure AD como sua autoridade de certificação.
4. O Azure AD valida o token de acesso na solicitação de registro e verifica se a solicitação origina-se de um administrador global.
5. O Azure AD assina e emite um certificado de identidade digital de volta ao Agente de Autenticação.
    - A autoridade de certificação raiz no Azure AD é usado para assinar o certificado. 

      > [!NOTE]
      > Essa autoridade de certificação _não_ está no repositório Autoridades de Certificado Raiz Confiável do Windows.
    - A autoridade de certificação é usada apenas pelo recurso Autenticação de Passagem. A autoridade de certificação é usada apenas para assinar CSRs durante o registro do Agente de Autenticação.
    -  Nenhum outro serviço do Azure AD usa essa autoridade de certificação.
    - A entidade do certificado (Nome Diferenciado ou DN) é definida para sua ID de locatário. Esse DN é um GUID que identifica exclusivamente o locatário. O escopo desse DN é o certificado para uso apenas com seu locatário.
6. O Azure AD armazena a chave pública do agente de autenticação em um banco de dados no banco de dados SQL do Azure, ao qual somente o Azure AD tem acesso.
7. O certificado (emitido na etapa 5) é armazenado no servidor local do repositório de certificados do Windows (especificamente no local [CERT_SYSTEM_STORE_LOCAL_MACHINE](/windows/win32/seccrypto/system-store-locations#CERT_SYSTEM_STORE_LOCAL_MACHINE)). Ele é usado pelo Agente de Autenticação e pelos aplicativos do Atualizador.

### <a name="authentication-agent-initialization"></a>Inicialização do Agente de Autenticação

Quando o Agente de Autenticação é iniciado, seja pela primeira vez após o registro ou depois de um reinício do servidor, ele precisa de uma maneira para se comunicar seguramente com o serviço do Azure AD e iniciar aceitando solicitações de validação de senha.

![Inicialização do Agente](./media/how-to-connect-pta-security-deep-dive/pta2.png)

Veja como os Agentes de Autenticação são inicializados:

1. O Agente de Autenticação faz uma solicitação de inicialização de saída ao Azure AD. 
    - Essa solicitação é feita pela porta 443 e por um canal HTTPS mutuamente autenticado. A solicitação usa o mesmo certificado que foi emitido durante o registro do Agente de Autenticação.
2. O Azure AD responde a essa solicitação fornecendo uma chave de acesso a uma fila do Barramento de Serviço do Azure que é exclusiva do locatário e identificada pela sua ID de locatário.
3. O Agente de Autenticação faz uma conexão HTTPS de saída persistente (sobre a porta 443) para a fila. 
    - O Agente de Autenticação agora está pronto para recuperar e tratar as solicitações de validação de senha.

Se houver vários Agentes de Autenticação registrados no seu locatário, o procedimento de inicialização garantirá que cada um se conecte à mesma fila do Barramento de Serviço. 

## <a name="process-sign-in-requests"></a>Processar solicitações de entrada 

O diagrama a seguir mostra como a Autenticação de Passagem processa as solicitações de entrada do usuário.

![Processar entrada](./media/how-to-connect-pta-security-deep-dive/pta3.png)

A Autenticação de Passagem trata uma solicitação de entrada do usuário conforme a seguir: 

1. Um usuário tenta acessar um aplicativo, por exemplo, [Outlook Web App](https://outlook.office365.com/owa).
2. Se o usuário ainda não tiver conectado, o aplicativo irá redirecionar o navegador para a página de entrada do Azure AD.
3. O serviço STS do Azure AD responde de volta com a página de **entrada do usuário** .
4. O usuário digita seu nome de usuário na página **Login do usuário** e, em seguida, seleciona o botão **Próximo**.
5. O usuário digita sua senha na página **Login do usuário** e, em seguida, seleciona o botão **Login**.
6. O nome de usuário e a senha são enviados ao STS do Azure AD em uma solicitação HTTPS POST.
7. O STS do Azure AD recupera chaves públicas para todos os agentes de autenticação registrados em seu locatário do banco de dados SQL do Azure e criptografa a senha usando-as.
    - Isso produz "N" valores de senha criptografados para "N" Agentes de Autenticação registrados no seu locatário.
8. O STS do Azure AD coloca a solicitação de validação de senha, que consiste no nome de usuário e nos valores de senha criptografada, na fila do Barramento de Serviço específico ao seu locatário.
9. Como os Agentes de Autenticação inicializados são persistentemente conectados à fila do Barramento de Serviço, um dos Agentes de Autenticação disponíveis recupera a solicitação de validação de senha.
10. O Agente de Autenticação localiza o valor da senha criptografada que é específico a sua chave pública, usando um identificador, e o descriptografa usando a respectiva chave privada.
11. O Agente de Autenticação tenta validar o nome de usuário e a senha no Active Directory local usando a [API LogonUser Win32](/windows/win32/api/winbase/nf-winbase-logonusera) com o parâmetro **dwLogonType** definido como **LOGON32_LOGON_NETWORK**. 
    - Essa é a mesma API usada pelos Serviços de Federação do Active Directory (AD FS) para conectar usuários em um cenário de entrada federada.
    - Essa API depende do processo de resolução padrão no Windows Server para localizar o controlador de domínio.
12. O Agente de Autenticação recebe o resultado do Active Directory, como êxito, nome de usuário ou senha incorretos ou senha expirada.

   > [!NOTE]
   > Se o agente de autenticação falhar durante o processo de entrada, a solicitação de entrada inteira será descartada. Não há nenhuma entrega de solicitações de entrada de um agente de autenticação para outro agente de autenticação local. Esses agentes só se comunicam com a nuvem, e não entre si.
   
13. O Agente de Autenticação encaminha o resultado de volta ao serviço de token de segurança do Azure AD através de um canal HTTPS mutuamente autenticado de saída sobre a porta 443. A autenticação mútua usa o certificado anteriormente emitido para o Agente de Autenticação durante o registro.
14. O serviço de token de segurança do Azure AD verifica se esse resultado corresponde à solicitação de entrada específica no locatário.
15. O serviço de token de segurança do Azure AD continua com o procedimento de entrada, conforme configurado. Por exemplo, se a validação de senha fosse bem-sucedida, o usuário poderia ser solicitado para a Multi-Factor Authentication ou redirecionado de volta ao aplicativo.

## <a name="operational-security-of-the-authentication-agents"></a>Segurança operacional dos Agentes de Autenticação

Para garantir que a Autenticação de Passagem permaneça operacionalmente segura, o Azure AD renova periodicamente os certificados dos Agentes de Autenticação. O Azure AD dispara as renovações. As renovações não são administradas pelos próprios Agentes de Autenticação.

![Segurança operacional](./media/how-to-connect-pta-security-deep-dive/pta4.png)

Para renovar a confiança do Agente de Autenticação com o Azure AD:

1. O Agente de Autenticação executa pings do Azure AD periodicamente no intervalo de algumas horas para verificar se é hora de renovar seu certificado. O certificado é renovado 30 dias antes do vencimento.
    - Essa verificação é realizada por um canal HTTPS mutuamente autenticado e usa o mesmo certificado que foi emitido durante o registro.
2. Se o serviço indicar que é hora de renovar, o Agente de Autenticação vai gerar um novo par de chaves: uma chave pública e uma chave privada.
    - Essas chaves são geradas por meio da criptografia padrão RSA de 2048 bits.
    - A chave privada jamais sairá do servidor local.
3. O Agente de Autenticação faz uma solicitação de "renovação de certificado" para o Azure AD sobre HTTPS com os seguintes componentes incluídos na solicitação:
    - O certificado existente que é recuperado do local CERT_SYSTEM_STORE_LOCAL_MACHINE no repositório de certificados do Windows. Não há administrador global envolvido nesse procedimento, portanto, não há token de acesso necessário em nome do administrador global.
    - A chave pública gerada na etapa 2.
    - Uma Solicitação de Assinatura de Certificado (CSR ou Solicitação de Certificado). Essa solicitação se aplica a um novo certificado de identidade digital, com o Azure AD como sua autoridade de certificação.
4. O Azure AD valida o certificado existente no pedido de renovação do certificado. Em seguida, verifica se a solicitação origina-se de um Agente de Autenticação registrado no seu locatário.
5. Se o certificado existente ainda for válido, o Azure AD assina um novo certificado de identidade digital e emite o novo certificado de volta ao Agente de Autenticação. 
6. Se o certificado existente expirou, o Azure AD excluirá o Agente de Autenticação da lista de locatários dos Agentes de Autenticação registrados. Em seguida, um administrador global precisará instalar manualmente e registrar um novo Agente de Autenticação.
    - Use a autoridade de certificação raiz do Azure AD para assinar o certificado.
    - Defina a entidade do certificado (Nome Diferenciado ou DN) para sua ID de locatário, um GUID que identifica exclusivamente seu locatário. O escopo do DN é o certificado apenas para seu locatário.
6. O Azure AD armazena a nova chave pública do agente de autenticação em um banco de dados no banco de dados SQL do Azure ao qual apenas ele tem acesso. Ele também invalida a chave pública antiga associada ao Agente de Autenticação.
7. O novo certificado (emitido na etapa 5) é armazenado no servidor no repositório de certificados do Windows (especificamente não local [CERT_SYSTEM_STORE_CURRENT_USER](/windows/win32/seccrypto/system-store-locations#CERT_SYSTEM_STORE_CURRENT_USER)).
    - Como o procedimento de renovação de confiança ocorrer de modo não interativo (sem a presença do administrador global), o Agente de Autenticação não terá mais acesso para atualizar o certificado existente no local CERT_SYSTEM_STORE_LOCAL_MACHINE. 
    
   > [!NOTE]
   > Esse procedimento não remove o certificado em si do local CERT_SYSTEM_STORE_LOCAL_MACHINE.
8. A partir desse momento, o novo certificado será utilizado para autenticação. Toda renovação subsequente do certificado substituirá o certificado no local CERT_SYSTEM_STORE_LOCAL_MACHINE.

## <a name="auto-update-of-the-authentication-agents"></a>Atualização automática dos Agentes de Autenticação

O aplicativo atualizador atualiza automaticamente o agente de autenticação quando uma nova versão (com correções de bugs ou aprimoramentos de desempenho) é lançada. O aplicativo do atualizador não trata nenhuma solicitação de validação de senha para seu locatário.

O Azure AD hospeda a nova versão do software como um **pacote de Windows Installer assinado (MSI)**. O MSI é assinado usando o [Microsoft Authenticode](/previous-versions/windows/internet-explorer/ie-developer/platform-apis/ms537359(v=vs.85)) com SHA256 como o algoritmo hash. 

![Atualização automática](./media/how-to-connect-pta-security-deep-dive/pta5.png)

Para atualizar automaticamente um Agente de Autenticação:

1. O aplicativo do Atualizador executa pings do Azure AD a cada hora para verificar se existe uma nova versão do Agente de Autenticação disponível. 
    - Essa verificação é realizada por um canal HTTPS mutuamente autenticado usando o mesmo certificado que foi emitido durante o registro. O Agente de Autenticação e o Atualizador compartilham o certificado armazenado no servidor.
2. Se uma nova versão estiver disponível, o Azure AD retornará o MSI assinado de volta ao Atualizador.
3. O Atualizador verifica se o MSI foi assinado pela Microsoft.
4. O Atualizador executa o MSI. Essa ação envolve as seguintes etapas:

   > [!NOTE]
   > O Atualizador é executado com privilégios de [Sistema Local](/windows/win32/services/localsystem-account).

    - Para o serviço do Agente de Autenticação
    - Instala a nova versão do Agente de Autenticação no servidor
    - Reinicia o serviço do Agente de Autenticação

>[!NOTE]
>Se você tiver vários Agentes de Autenticação registrados no seu locatário, o Azure AD não renovará seus certificados ou os atualizará ao mesmo tempo. Em vez disso, o Azure AD faz isso, um de cada vez, para garantir a alta disponibilidade de solicitações de entrada.
>


## <a name="next-steps"></a>Próximas etapas
- [Limitações atuais](how-to-connect-pta-current-limitations.md): saiba quais cenários têm suporte e quais não têm.
- [Início rápido](how-to-connect-pta-quick-start.md): colocar em funcionamento a autenticação de passagem do Azure AD.
- [Migrar do AD FS para Autenticação de Passagem](https://aka.ms/adfstoptadpdownload) – um guia detalhado para migrar do AD FS (ou outras tecnologias de federação) para Autenticação de Passagem.
- [Bloqueio Inteligente](../authentication/howto-password-smart-lockout.md): configure a capacidade de Bloqueio Inteligente no seu locatário para proteger as contas de usuário.
- [Como funciona](how-to-connect-pta-how-it-works.md): conheça as noções básicas de como funciona a Autenticação de Passagem do Azure AD.
- [Perguntas frequentes](how-to-connect-pta-faq.md): encontre respostas para perguntas frequentes.
- [Solução de problemas](tshoot-connect-pass-through-authentication.md): saiba como resolver problemas comuns com o recurso de Autenticação de Passagem.
- [SSO contínuo do Azure AD](how-to-connect-sso.md): saiba mais sobre esse recurso complementar.