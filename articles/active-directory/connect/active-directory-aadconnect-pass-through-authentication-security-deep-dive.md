---
title: "Aprofundamento de segurança da Autenticação de Passagem do Azure Active Directory | Microsoft Docs"
description: "Este artigo descreve como a autenticação de passagem do Azure AD (Azure Active Directory) protege suas contas locais."
services: active-directory
keywords: "Autenticação de Passagem do Azure AD Connect, instalar o Active Directory, componentes necessários para o Azure AD, SSO, Logon único"
documentationcenter: 
author: swkrish
manager: femila
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: billmath
ms.openlocfilehash: 7a886cdb0c36008bdb66592a8d3428889739627e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Aprofundamento de segurança da Autenticação de Passagem do Azure Active Directory

Este artigo fornece uma descrição mais detalhada sobre como funciona a Autenticação de passagem. Especificamente, o foco está direcionado aos aspectos de segurança do recurso. Este tópico será de interesse para os administradores de TI e segurança, Diretores de Conformidade e Segurança e, outros profissionais de TI responsáveis pela segurança e conformidade de TI em organizações de pequeno e médio porte ou empresas de grande porte.

Os tópicos abordados incluem:
- Informações técnicas detalhadas sobre como os Agentes de Autenticação são instalados e registrados.
- Informações técnicas detalhadas sobre criptografia de senhas durante a entrada do usuário.
- Segurança dos canais entre os Agentes de Autenticação local e o Azure AD (Azure Active Directory).
- Informações técnicas detalhadas sobre como os Agentes de Autenticação são mantidos operacionalmente seguros.
- Outros tópicos relacionados à segurança.

## <a name="key-security-capabilities"></a>Principais recursos de segurança

Estes são os principais aspectos de segurança desse recuso:
- É criado em uma arquitetura multilocatária segura que fornece isolamento de solicitações de entrada entre locatários.
- Senhas locais nunca são armazenadas na nuvem, em formato nenhum.
- Os Agentes de Autenticação local que escutam e respondem às solicitações de validação de senha, somente fazem conexões de saída dentro de sua rede. Não há necessidade de instalar esses Agentes de Autenticação em uma rede de perímetro (DMZ).
- Apenas as portas padrão (80 e 443) são utilizadas para comunicação de saída dos Agentes de Autenticação para o Azure AD. Não é necessário abrir portas de entrada no seu firewall. 
  - A porta 443 é utilizada para toda a comunicação de saída autenticada
  - A porta 80 é utilizada apenas para baixar as CRLs (Listas de Certificados Revogados) para garantir que nenhum dos certificados utilizados pelo recurso tenha sido revogado.
  - Consulte [aqui](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-prerequisites) para obter a lista completa dos requisitos de rede.
- As senhas fornecidas pelo usuário durante a entrada são criptografadas na nuvem, antes de serem aceitas pelos Agentes de Autenticação local para validação com o Active Directory.
- O canal HTTPS entre o Azure AD e um Agente de Autenticação local é garantido por autenticação mútua.
- Integra-se perfeitamente com os recursos de proteção de nuvem do Azure AD, como políticas de Acesso Condicional (incluindo Autenticação Multifator), Proteção de Identidade e Bloqueio Inteligente.

## <a name="components-involved"></a>Componentes envolvidos

Para obter detalhes gerais sobre segurança de dados, serviços e operacional do Azure AD, consulte a [Central de Confiabilidade](https://azure.microsoft.com/support/trust-center/). Os seguintes componentes serão envolvidos quando a Autenticação de Passagem for utilizada para a entrada do usuário:
- **STS do Azure AD**: Um Serviço de Token de Segurança (STS) sem estado que processa solicitações de entrada e emite tokens de segurança aos clientes, serviços ou navegadores dos usuários, conforme necessário.
- **Barramento de Serviço do Microsoft Azure**: Fornece comunicação habilitada para nuvem com sistema de mensagens empresarial e comunicação de retransmissões que ajudam a conectar soluções locais com a nuvem.
- **Agente de Autenticação do Microsoft Azure AD Connect (Agente de Autenticação)**: Um componente local que escuta e responde às solicitações de validação de senha.
- **Banco de Dados SQL do Azure**: Contém informações sobre os Agentes de Autenticação do locatário, incluindo as chaves de criptografia e metadados.
- **AD (Active Directory)**: O Active Directory local onde suas contas de usuário (e suas senhas) são armazenadas.

## <a name="installation-and-registration-of-authentication-agents"></a>Instalação e registro dos Agentes de Autenticação

Os Agentes de Autenticação são instalados e registrados com o Azure AD ao [habilitar a Autenticação de Passagem usando o Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature) ou ao [adicionar Agentes de Autenticação adicionais para garantir a alta disponibilidade de solicitações de entrada](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability). Obter um Agente de Autenticação em funcionamento envolve três fases principais:

- Instalação do Agente de Autenticação
- Registro do Agente de Autenticação
- Inicialização do Agente de Autenticação

Cada uma dessas fases será discutida em detalhes nos seguintes tópicos.

### <a name="authentication-agent-installation"></a>Instalação do Agente de Autenticação

Somente os Administradores Globais podem instalar um Agente de Autenticação (utilizando o Azure AD Connect ou autônomo) em um servidor local. A instalação adiciona essas duas novas entradas à lista **Painel de Controle -> Programas -> Programas e Recursos**:
- O próprio aplicativo do Agente de Autenticação. Isso é executado com privilégios de [Serviço de Rede](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx).
- O aplicativo do Atualizador utilizado para atualização automática do Agente de Autenticação. Isso é executado com privilégios de [Sistema Local](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx).


### <a name="authentication-agent-registration"></a>Registro do Agente de Autenticação

Após instalar o Agente de Autenticação, será necessário registrá-lo com o Azure AD. O Azure AD realiza isso atribuindo a cada Agente de Autenticação um certificado de identidade digital exclusivo, o qual poderá ser utilizado para uma comunicação segura com o Azure AD.

O procedimento de registro também associa o Agente de Autenticação com o locatário para que o Azure AD reconheça que esse Agente de Autenticação específico é o único autorizado a tratar solicitações de validação de senha para o locatário. Esse procedimento é repetido para cada novo Agente de Autenticação registrado.

A seguir, é descrito como os Agentes de Autenticação registram-se com o Azure AD:

![Registro de Agente](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Primeiro, o Azure AD solicita a um Administrador Global entrar no Azure AD com suas credenciais. Durante a entrada, o Agente de Autenticação adquire um Token de Acesso que poderá ser utilizado em nome do Administrador Global.
2. Em seguida, o Agente de Autenticação gera um par de chaves – uma chave pública e uma chave privada.
    - Esse par de chaves é gerado utilizando criptografia **RSA de 2048 bits**.
    - A chave privada jamais sairá do servidor local no qual o Agente de Autenticação foi instalado.
3.  O Agente de Autenticação realiza uma solicitação de "registro" para o Azure AD sobre HTTPS com os seguintes componentes incluídos na solicitação:
    - O Token de Acesso adquirido na Etapa 1.
    - A chave pública gerada na Etapa 2.
    - Uma **Solicitação de Assinatura de Certificado** (CSR ou Solicitação de Certificado). Isso é para solicitar um certificado de identidade digital tendo o Azure AD como sua Autoridade de Certificação.
4. O Azure AD valida o Token de Acesso na solicitação de registro e verifica se a solicitação origina-se de um Administrador Global.
5. O Azure AD assina e emite um certificado de identidade digital de volta para o Agente de Autenticação.
    - O certificado é assinado utilizando a **Autoridade de Certificação Raiz do Azure AD**. Note que essa Autoridade de Certificação _não_ está no repositório das **Autoridades de Certificação de Raiz Confiáveis** do Windows.
    - A entidade do certificado (**Nome Diferenciado ou DN**) é definida para o **ID do Locatário**. Este é um GUID que identifica exclusivamente o locatário. Esse escopo utiliza apenas o certificado para uso com o locatário.
6. O Azure AD armazena a chave pública do Agente de Autenticação em um Banco de Dados SQL do Azure, o qual apenas o Azure AD tem acesso.
7. O certificado (emitido na Etapa 5) é armazenado no servidor local no **Repositório de Certificados do Windows** (especificamente no local **[CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE)**) e é utilizado tanto pelo Agente de Autenticação como pelos aplicativos do Updater.

### <a name="authentication-agent-initialization"></a>Inicialização do Agente de Autenticação

Quando o Agente de Autenticação é iniciado, seja pela primeira vez após o registro ou depois de um reinício do servidor, ele precisará de uma maneira para se comunicar de maneira segura com o serviço do Azure AD e começar a aceitar as solicitações de validação de senha.

![Inicialização do Agente](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

A seguir, é descrito como os Agentes de Autenticação são inicializados:



1. O Agente de Autenticação inicia fazendo uma solicitação de "bootstrap" de saída para o Azure AD. 
    - Essa solicitação de bootstrap é realizada por meio da porta 443 e está sobre um canal HTTPS mutuamente autenticado (utilizando o mesmo certificado emitido durante o registro do Agente de Autenticação).
2. O Azure AD responde a essa solicitação de inicialização, fornecendo uma **Chave de Acesso** para uma fila de Barramento de Serviço do Azure que é exclusiva do locatário (identificada pelo ID de locatário).
3. O Agente de Autenticação faz uma conexão HTTPS de saída persistente (sobre a porta 443) para a fila. 
    - Agora, está pronto para recuperar e tratar as solicitações de validação de senha.

Se houver vários Agentes de Autenticação registrados no seu locatário, o procedimento de inicialização garantirá que cada um se conecte à mesma fila do Barramento de Serviço do Azure. 

## <a name="processing-sign-in-requests"></a>Processamento de solicitações de entrada 

O diagrama abaixo mostra como a Autenticação de Passagem processa as solicitações de entrada do usuário.

![Processamento de entrada](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

A Autenticação de Passagem trata uma solicitação de entrada do usuário conforme a seguir: 

1. Um usuário tenta acessar um aplicativo (por exemplo, o Outlook Web App - [https://outlook.office365.com/owa](https://outlook.office365.com/owa).)
2. Se o usuário ainda não tiver conectado, o aplicativo irá redirecionar o navegador para a página de entrada do Azure AD.
3. O serviço de token de segurança do Azure AD responde de volta com a página de entrada do usuário.
4. O usuário insere o nome de usuário e a senha na página de entrada do Azure AD e clica no botão "Entrar".
5. O nome de usuário e a senha são enviados ao serviço de token de segurança do Azure AD em uma solicitação POST HTTPS.
6. O serviço de token de segurança do Azure AD recupera as chaves públicas para todos os Agentes de Autenticação registrados em seu locatário no Banco de Dados SQL do Azure e criptografa a senha, utilizando-os. 
    - Isso produz valores de senha criptografados 'N' para os Agentes de Autenticação 'N' registrados no seu locatário.
7. O serviço de token de segurança do Azure AD coloca a solicitação de validação de senha (o nome de usuário e os valores da senha criptografada) na fila do Barramento de Serviço do Azure específica para seu locatário.
8. Quando os Agentes de Autenticação inicializados estiverem persistentemente conectados à fila do Barramento de Serviço do Azure, um dos Agentes de Autenticação disponível recupera a solicitação de validação de senha.
9. O Agente de Autenticação localiza o valor da senha criptografada que é específico da sua chave pública (utilizando um identificador) e descriptografa utilizando sua chave privada.
10. O Agente de Autenticação tenta validar o nome de usuário e a senha em seu Active Directory local, utilizando a **[API LogonUser do Win32](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx)** (com o parâmetro **dwLogonType** definido para **LOGON32_LOGON_NETWORK**). 
    - Essa é a mesma API utilizada pelos Serviços de Federação do Active Directory (AD FS) para conectar usuários em um cenário de entrada federada.
11. O Agente de Autenticação recebe o resultado do Active Directory (sucesso, nome de usuário ou senha incorreto, senha expirada, usuário bloqueado, e assim por diante).
12. O Agente de Autenticação encaminha o resultado de volta ao serviço de token de segurança do Azure AD através de um canal HTTPS mutuamente autenticado de saída sobre a porta 443. A autenticação mútua utiliza o mesmo certificado anteriormente emitido para o Agente de Autenticação durante o registro.
13. O serviço de token de segurança do Azure AD verifica se esse resultado corresponde à solicitação de entrada específica no locatário.
14. O serviço de token de segurança do Azure AD continua com o procedimento de entrada, conforme configurado. Por exemplo, se a validação de senha fosse bem-sucedida, o usuário poderia ser solicitado para a Autenticação Multifator ou redirecionado de volta ao aplicativo.

## <a name="operational-security-of-authentication-agents"></a>Segurança operacional dos Agentes de Autenticação

Para garantir que a Autenticação de Passagem permaneça operacionalmente segura, o Azure AD renova periodicamente seus certificados. Essas renovações são disparadas a partir do Azure AD e não são controladas pelos próprios Agentes de Autenticação.

![Segurança operacional](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

A seguir, é descrito como um Agente de Autenticação renova sua confiança com o Azure AD:

1. O Agente de Autenticação executa pings do Azure AD periodicamente (durante algumas horas) para verificar se é hora de renovar seu certificado. 
    - Essa verificação é realizada sobre um canal HTTPS mutuamente autenticado (utilizando o mesmo certificado emitido durante o registro).
2. Se o serviço indicar que é hora de renovar, o Agente de Autenticação irá gerar um novo par de chaves: uma chave pública e uma chave privada.
    - Essas chaves são geradas utilizando a criptografia padrão **RSA de 2048 bits**.
    - A chave privada jamais sairá do servidor local.
3. O Agente de Autenticação faz uma solicitação de "renovação de certificado" para o Azure AD sobre HTTPS com os seguintes componentes incluídos na solicitação:
    - O certificado existente (recuperado do local **CERT_SYSTEM_STORE_LOCAL_MACHINE** no Repositório de Certificados do Windows). Não há um Administrador Global envolvido nesse procedimento, portanto, não há nenhum Token de Acesso necessário em nome do Administrador Global.
    - A chave pública gerada na Etapa 2.
    - Uma **Solicitação de Assinatura de Certificado** (CSR ou Solicitação de Certificado). Isso é para solicitar um novo certificado de identidade digital, com o Azure AD como autoridade de certificação.
4. O Azure AD valida o certificado existente no pedido de renovação do certificado. Em seguida, verifica se a solicitação origina-se de um Agente de Autenticação registrado no seu locatário.
5. Se o certificado existente ainda for válido, o Azure AD assina um novo certificado de identidade digital e emite o novo certificado de volta ao Agente de Autenticação. 
6. Se o certificado existente expirou, o Azure AD excluirá o Agente de Autenticação da lista de locatários dos Agentes de Autenticação registrados. Em seguida, um Administrador Global precisará instalar manualmente e registrar um novo Agente de Autenticação.
    - O certificado é assinado utilizando a **Autoridade de Certificação Raiz do Azure AD**.
    - A entidade do certificado (**Nome Diferenciado ou DN**) é definida para o **ID do Locatário**, um GUID que identifica exclusivamente o locatário. Ou seja, o certificado é com escopo para apenas o locatário.
6. O Azure AD armazena a "nova" chave pública do Agente de Autenticação em um Banco de Dados SQL do Azure, o qual somente ele tem acesso. E invalida a chave pública "antiga" associada ao Agente de Autenticação.
7. O novo certificado (que foi emitido na Etapa 5) é armazenado no servidor no **Repositório de certificados do Windows** (especificamente não local **[CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER)**).
    - Quando o procedimento de renovação de confiança ocorrer de modo não interativo (sem a presença do Administrador Global), o Agente de Autenticação não mais terá acesso para atualizar o certificado existente no local **CERT_SYSTEM_STORE_LOCAL_MACHINE**. Note que o próprio certificado no local **CERT_SYSTEM_STORE_LOCAL_MACHINE** não foi removido durante esse procedimento.
8. A partir desse momento, o novo certificado será utilizado para autenticação. Toda renovação subsequente do certificado substituirá o certificado no local **CERT_SYSTEM_STORE_LOCAL_MACHINE**.

## <a name="auto-update-of-authentication-agents"></a>Atualização automática dos Agentes de Autenticação

O aplicativo do Atualizador atualizará automaticamente o Agente de Autenticação quando uma nova versão for lançada. Ele não trata nenhuma solicitação de validação de senha para o locatário. 

O Azure AD hospeda a nova versão do software como um **Pacote do Windows Installer (MSI)** assinado. O MSI é assinado utilizando o [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) com **SHA256** como o algorítimo hash. 

![Atualização automática](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

A seguir, é descrito como um Agente de Autenticação é atualizado automaticamente:

1. O Atualizador executa pings do Azure AD periodicamente (a cada hora) para verificar se existe uma nova versão do Agente de Autenticação disponível. 
    - Essa verificação é realizada sobre um canal HTTPS mutuamente autenticado (utilizando o mesmo certificado emitido durante o registro). O Agente de Autenticação e o Atualizador compartilham o certificado armazenado no servidor.
2. Se uma nova versão estiver disponível, o Azure AD retornará o MSI assinado de volta ao Atualizador.
3. O Atualizador verifica se o MSI foi assinado pela Microsoft.
4. O Atualizador executa o MSI. Essa ação realiza as seguintes etapas (note que o Atualizador é executado com privilégios de [Sistema Local](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)):
    - Para o serviço do Agente de Autenticação.
    - Instala a nova versão do Agente de Autenticação no servidor.
    - Reinicia o serviço do Agente de Autenticação.

>[!NOTE]
>Se você tiver vários Agentes de Autenticação registrados no seu locatário, o Azure AD não renovará seus certificados ou os atualizará ao mesmo tempo. Em vez disso, o Azure AD realizará esse procedimento gradualmente para garantir a alta disponibilidade das solicitações de entrada.


## <a name="next-steps"></a>Próximas etapas
- [**Limitações atuais**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - Saiba quais cenários têm suporte e quais não têm.
- [**Início rápido**](active-directory-aadconnect-pass-through-authentication-quick-start.md) – instale e execute a autenticação de passagem do Azure AD.
- [**Bloqueio Inteligente**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) – configura a capacidade de Bloqueio Inteligente no seu locatário para proteger as contas de usuário.
- [**Como funciona**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) - Aprenda as noções básicas de como a Autenticação de Passagem do Azure AD funciona.
- [**Perguntas frequentes**](active-directory-aadconnect-pass-through-authentication-faq.md) – respostas para perguntas frequentes.
- [**Solução de problemas**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Saiba como resolver problemas comuns do recurso.
- [**SSO contínuo do Azure AD**](active-directory-aadconnect-sso.md) – Saiba mais sobre esse recurso complementar.

