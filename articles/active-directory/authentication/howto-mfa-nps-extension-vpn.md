---
title: VPN com o Azure AD MFA usando a extensão do NPS-Azure Active Directory
description: Integre sua infraestrutura de VPN com o Azure AD MFA usando a extensão de servidor de políticas de rede para Microsoft Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c4c5c4e632943ebbe68003f663aebbeaab9ebaf
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743438"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-ad-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integre sua infraestrutura de VPN com o Azure AD MFA usando a extensão de servidor de políticas de rede para o Azure

A extensão do servidor de políticas de rede (NPS) para o Azure permite que as organizações protejam a autenticação de cliente do serviço RADIUS (RADIUS) usando a [MFA (autenticação multifator) do Azure ad](howto-mfaserver-nps-rdg.md)baseada em nuvem, que fornece verificação em duas etapas.

Este artigo apresenta instruções para a integração da infraestrutura NPS ao MFA usando a extensão NPS para o Azure. Esse processo permite a verificação em duas etapas segura para os usuários que tentam se conectar à rede usando uma VPN.

Os Serviços de Acesso e Política de Rede permitem às organizações:

* Atribuir um local central para o gerenciamento e o controle de solicitações de rede para especificar:

  * Quem pode se conectar

  * Em que horários do dia as conexões são permitidas

  * A duração das conexões

  * O nível de segurança que os clientes devem usar para se conectar

    Em vez de especificar políticas em cada VPN ou servidor de Gateway de Área de Trabalho Remota, faça isso depois que estiverem em um local central. O protocolo RADIUS é usado para fornecer AAA (Autenticação, Autorização e Contabilização) centralizada.

* Estabelecer e impor políticas de integridade do cliente de Proteção de Acesso à Rede (NAP) que determinam se os dispositivos têm acesso irrestrito ou restrito aos recursos de rede.

* Oferece uma maneira de impor a autenticação e autorização para acesso aos comutadores Ethernet e pontos de acesso sem fio compatíveis com 802.1x.
  Para obter mais informações, consulte [Servidor de Políticas de Rede](/windows-server/networking/technologies/nps/nps-top).

Para aprimorar a segurança e fornecer um alto nível de conformidade, as organizações podem integrar o NPS com a autenticação multifator do Azure AD para garantir que os usuários usem a verificação em duas etapas para se conectar à porta virtual no servidor VPN. Usuários que devem receber acesso devem informar a combinação de nome de usuário e senha e outras informações que eles controlem. Essas informações devem ser confiáveis e não devem ser facilmente duplicadas. Podem incluir um número de telefone celular, um número de telefone fixo ou um aplicativo em um dispositivo móvel.

Antes da disponibilidade da extensão do NPS do Azure, os clientes que quiserem implementar verificação em duas etapas para ambientes NPS e de MFA integrados precisavam configurar e manter um servidor MFA separado em um ambiente local. Esse tipo de autenticação é oferecido pelo Gateway de Área de Trabalho Remota e pelo usando Servidor de Autenticação Multifator do Azure usando RADIUS.

Com a extensão do NPS do Azure, as organizações podem proteger a autenticação de cliente RADIUS implantando uma solução MFA baseada em local ou em nuvem.

## <a name="authentication-flow"></a>Fluxo de autenticação

Quando os usuários conectam-se a uma porta virtual em um servidor VPN, eles devem primeiro se autenticar usando uma variedade de protocolos. Os protocolos permitem o uso de uma combinação de nome de usuário e senha e métodos de autenticação baseados em certificado.

Além da autenticação e da verificação de identidade, os usuários devem ter as permissões apropriadas de discagem. Em implantações simples, essas permissões de discagem que permitem acesso são definidas diretamente em objetos de usuário do Active Directory.

![Guia de discagem em propriedades do usuário de usuários e computadores do Active Directory](./media/howto-mfa-nps-extension-vpn/image1.png)

Em implantações simples, cada servidor VPN concede ou nega o acesso com base nas políticas definidas em cada servidor VPN local.

Em implementações maiores e mais escalonáveis, as políticas que concedem ou negam acesso à VPN são centralizadas em servidores RADIUS. Nesses casos, o servidor VPN atua como um servidor de acesso (cliente RADIUS) que encaminha solicitações de conexão e mensagens de conta para um servidor RADIUS. Para se conectar à porta virtual no servidor VPN, os usuários devem ser autenticados e atender às condições definidas centralmente em servidores RADIUS.

Quando a extensão NPS para o Azure é integrada ao NPS, há um fluxo de autenticação correto, como segue:

1. O servidor de VPN recebe uma solicitação de autenticação de um usuário VPN incluindo um nome de usuário e uma senha para se conectar a um recurso, como uma sessão de Área de Trabalho Remota.
2. O servidor VPN age como um cliente RADIUS e converte a solicitação em uma mensagem de *Solicitação de Acesso* do RADIUS e a envia (com uma senha é criptografada) ao servidor RADIUS no qual a extensão NPS está instalada.
3. A combinação de nome de usuário e senha é verificada no Active Directory. Se o nome de usuário ou a senha estiver incorreto, o servidor RADIUS enviará uma mensagem de *Rejeição de Acesso*.
4. Se todas as condições, conforme especificado nas políticas de rede e solicitação de conexão do NPS, forem atendidas (por exemplo, hora do dia ou restrições de associação de grupo), a extensão do NPS disparará uma solicitação de autenticação secundária com a autenticação multifator do Azure AD.
5. A autenticação multifator do Azure AD se comunica com o Azure Active Directory, recupera os detalhes do usuário e executa a autenticação secundária usando o método configurado pelo usuário (chamada telefônica celular, mensagem de texto ou aplicativo móvel).
6. Quando o desafio do MFA for bem-sucedido, a autenticação multifator do Azure AD comunicará o resultado à extensão do NPS.
7. Após a tentativa de conexão ser autenticada e autorizada, o NPS no qual a extensão está instalada envia uma mensagem de *Aceitação de Acesso* RADIUS ao servidor VPN (cliente RADIUS).
8. O usuário tem acesso à porta virtual no servidor VPN e estabelece um túnel VPN criptografado.

## <a name="prerequisites"></a>Pré-requisitos

Esta seção apresenta detalhes sobre os pré-requisitos que devem ser concluídos para você integrar MFA à VPN. Antes de começar, você deverá ter os seguintes pré-requisitos em vigor:

* Infraestrutura de VPN
* Função de Serviços de Acesso e Política de Rede
* Licença da autenticação multifator do Azure AD
* Software do Windows Server
* Bibliotecas
* Azure AD (Azure Active Directory) sincronizado ao Active Directory local
* ID do GUID do Azure Active Directory

### <a name="vpn-infrastructure"></a>Infraestrutura de VPN

Este artigo pressupõe que você tenha uma infraestrutura VPN funcional usando o Microsoft Windows Server 2016 e que o servidor VPN não esteja configurado no momento para encaminhar solicitações de conexão a um servidor RADIUS. No artigo, você configura a infraestrutura de VPN para usar um servidor RADIUS central.

Se você não tiver uma infraestrutura VPN funcional em vigor, poderá criar uma rapidamente seguindo as orientações apresentadas em vários tutoriais de configuração de VPN disponíveis na Microsoft e em sites de terceiros.

### <a name="the-network-policy-and-access-services-role"></a>A função de Serviços de Acesso e Política de Rede

Serviços de Acesso e Política de Rede fornecem a funcionalidade de servidor e cliente RADIUS. Este artigo pressupõe que você tenha instalado a função de Serviços de Acesso e Política de rede em um servidor membro ou controlador de domínio em seu ambiente. Neste guia, você configura o RADIUS para uma configuração de VPN. Instale a função Serviços de Acesso e Política de Rede em um servidor *que não* o seu servidor VPN.

Para obter informações sobre como instalar o serviço de função Serviços de Acesso e Política de Rede do Windows Server 2012 ou posterior, consulte [Instalar um Servidor de Política de Integridade de NAP](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10)). O NAP foi preterido no Windows Server 2016. Para obter uma descrição das melhores práticas para NPS, incluindo a recomendação para instalar o NPS em um controlador de domínio, consulte [Práticas recomendadas para NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10)).

### <a name="azure-ad-mfa-license"></a>Licença do Azure AD MFA

Uma licença é necessária para a autenticação multifator do Azure AD e está disponível por meio de um Azure AD Premium, Enterprise Mobility + Security ou uma licença autônoma de autenticação multifator. As licenças baseadas em consumo para MFA do Azure AD, como licenças por usuário ou por autenticação, não são compatíveis com a extensão do NPS. Para obter mais informações, consulte [como obter a autenticação multifator do Azure ad](concept-mfa-licensing.md). Para fins de teste, você pode usar uma assinatura de avaliação.

### <a name="windows-server-software"></a>Software do Windows Server

A extensão NPS requer o Windows Server 2008 R2 SP1 ou posterior, com a função Serviços de Acesso e Política de Rede instalada. Todas as etapas neste guia foram realizadas usando o Windows Server 2016.

### <a name="libraries"></a>Bibliotecas

As bibliotecas a seguir são instaladas automaticamente com a extensão NPS:

-    [Pacotes redistribuíveis do Visual C++ para Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-    [Módulo Microsoft Azure Active Directory para Windows PowerShell versão 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Se o Módulo do PowerShell do Microsoft Azure Active Directory ainda não estiver presente, ele será instalado com um script de configuração executado como parte do processo de instalação. Não é necessário instalar esse módulo antecipadamente, caso ele ainda não esteja instalado.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Azure Active Directory sincronizado ao Active Directory local

Para usar a extensão do NPS, os usuários locais devem ser sincronizados com o Azure Active Directory e habilitados para MFA. Este guia pressupõe que os usuários locais estejam sincronizados ao Azure Active Directory usando o Azure AD Connect. As instruções para habilitar usuários para MFA são fornecidas abaixo.

Para obter informações sobre o Azure AD Connect, consulte [Integrar seus diretórios locais com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>ID do GUID do Azure Active Directory

Para instalar a extensão NPS, você precisa saber qual é o GUID do Azure Active Directory. São fornecidas instruções para localizar o GUID do Azure Active Directory na próxima seção.

## <a name="configure-radius-for-vpn-connections"></a>Configurar RADIUS para conexões VPN

Se você tiver instalado a função de NPS em um servidor membro, precisará configurar para autenticar e autorizar o cliente VPN que solicite conexões VPN. 

Esta seção pressupõe que você instalou a função de Serviços de Acesso e Políticas de Rede, mas não a configurou para uso em sua infraestrutura.

> [!NOTE]
> Se você já tiver um servidor VPN de trabalho que usa um servidor RADIUS centralizado para autenticação, você pode ignorar esta seção.
>

### <a name="register-server-in-active-directory"></a>Registrar o Servidor no Active Directory

Para funcionar corretamente nesse cenário, o servidor NPS deve ser registrado no Active Directory.

1. Abra o Gerenciador de Servidor.

2. No Gerenciador do Servidor, selecione **Ferramentas** e, em seguida, selecione **Servidor de Políticas de Rede**.

3. No console do Servidor de Políticas de Rede, clique com o botão direito do mouse em **NPS (Local)** e, em seguida, selecione **Registrar servidor no Active Directory**. Selecione **OK** duas vezes.

    ![Opção de menu Registrar o Servidor no Active Directory](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Deixe o console aberto para o próximo procedimento.

### <a name="use-wizard-to-configure-the-radius-server"></a>Usar o assistente para configurar o servidor RADIUS

Você pode usar um padrão (baseado em assistente) ou a opção de configuração avançada para configurar o servidor RADIUS. Esta seção pressupõe que você esteja usando a opção de configuração padrão baseada em assistente.

1. No console do Servidor de Políticas de Rede, clique em **NPS (Local)** .

2. Em **Configuração Padrão**, selecione **Servidor RADIUS para Conexões VPN ou de Conexão Discada** s e, em seguida, selecione **Configurar VPN ou de Conexão Discada**.

    ![Configurar o servidor RADIUS para conexões discadas ou VPN](./media/howto-mfa-nps-extension-vpn/image3.png)

3. Na janela **Selecionar Tipo de Conexões de Rede Virtual Privada ou de Conexão Discada**, selecione **Conexões de Rede Virtual Privada** e, em seguida, selecione **Avançar**.

    ![Configurar conexões de rede virtual privada](./media/howto-mfa-nps-extension-vpn/image4.png)

4. Na janela **Especificar Servidor VPN ou de Conexão Discada**, selecione **Adicionar**.

5. Na janela **Novo cliente RADIUS**, forneça um nome amigável, digite o nome que pode ser resolvido ou o endereço IP do servidor VPN e insira uma senha de segredo compartilhado. Torne a senha de segredo compartilhado longa e complexa. Registre-a, pois você precisará dela na próxima seção.

    ![Criar uma janela do cliente RADIUS](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Selecione **OK** e, em seguida, selecione **Avançar**.

7. Na janela **Configurar Métodos de Autenticação**, aceite a seleção padrão (**Autenticação Criptografada da Microsoft versão 2 [MS-CHAPv2])** ou escolha outra opção e selecione **Avançar**.

    > [!NOTE]
    > Se você configurar o protocolo EAP (Extensible Authentication), deverá usar o protocolo Microsoft CHAPv2 ou o protocolo PEAP. Não há suporte para nenhum outro tipo de EAP.

8. Na janela **Especificar Grupos de Usuários**, selecione **Adicionar** e, em seguida, selecione um grupo apropriado. Se não houver um grupo, deixe a seleção em branco para conceder acesso a todos os usuários.

    ![Especificar a janela Grupos de Usuários para permitir ou negar acesso](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Selecione **Avançar**.

10. Na janela **Especificar Filtros IP**, selecione **Avançar**.

11. Na janela **Especificar Configurações de Criptografia**, aceite as configurações padrão e, em seguida, selecione **Avançar**.

    ![A janela Especificar Configurações de Criptografia](./media/howto-mfa-nps-extension-vpn/image8.png)

12. Na janela **Especificar um Nome de Realm**, deixe em branco o nome de realm, aceite a configuração padrão e, em seguida, selecione **Avançar**.

    ![A janela Especificar Nome de Realm](./media/howto-mfa-nps-extension-vpn/image9.png)

13. Na página **Concluir clientes RADIUS e Novas Conexões de Rede Virtual Privada ou de Conexão Discada**, selecione **Concluir**.

    ![Janela Configuração concluída](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Verificar a configuração de RADIUS

Esta seção fornece detalhes sobre a configuração que você criou usando o assistente.

1. No Servidor de Políticas de Rede, no console do NPS (local), expanda **Clientes RADIUS** e, em seguida, selecione **Clientes RADIUS**.

2. No painel de detalhes, clique com o botão direito do mouse no cliente RADIUS que você criou e, em seguida, selecione **Propriedades**. As propriedades de seu cliente RADIUS (o servidor VPN) devem ser como o mostrado aqui:

    ![Verificar as propriedades e configurações de VPN](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Selecione **Cancelar**.

4. No Servidor de Políticas de Rede, no console do NPS (local), expanda **Políticas** e selecione **Políticas de Solicitação de Conexão**. A política de Conexões VPN é exibida conforme mostra a imagem a seguir:

    ![Política de solicitação de conexão mostrando uma política de conexão VPN](./media/howto-mfa-nps-extension-vpn/image12.png)

5. Em **Políticas**, selecione **Políticas de Rede**. Você deve ver uma política de Conexões de VPN (Rede Virtual Privada) que se parece com a política mostrada na imagem a seguir:

    ![Políticas de rede mostrando a política de conexões de rede virtual privada](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Configurar o servidor de VPN para usar a autenticação RADIUS

Nesta seção, você configura o servidor VPN para usar a autenticação RADIUS. As instruções pressupõem que você tem uma configuração funcional de um servidor VPN, mas não a configurou para usar a autenticação RADIUS. Depois de configurar o servidor VPN, você confirma que sua configuração está funcionando conforme o esperado.

> [!NOTE]
> Se você já tiver um servidor VPN em vigor que usa autenticação RADIUS, você pode ignorar esta seção.
>

### <a name="configure-authentication-provider"></a>Configurar provedor de autenticação

1. No servidor VPN, abra o Gerenciador do Servidor.

2. No Gerenciador do Servidor, selecione **Ferramentas** e, em seguida, selecione **Roteamento e Acesso Remoto**.

3. Na janela **Roteamento e acesso remoto** , clique com o botão direito do mouse em **\<server name> (local)** e selecione **Propriedades**.

4. Na janela **\<server name> Propriedades (local)** , selecione a guia **segurança** .

5. Na guia **Segurança**, em **Provedor de Autenticação**, clique em **Autenticação do RADIUS** e, em seguida, selecione **Configurar**.

    ![Configurar o provedor de autenticação RADIUS](./media/howto-mfa-nps-extension-vpn/image15.png)

6. Na janela **Autenticação do RADIUS**, selecione **Adicionar**.

7. Na janela **Adicionar servidor do RADIUS**, faça o seguinte:

    a. Na caixa **Nome do servidor**, digite o nome ou o endereço IP do servidor RADIUS que você configurou na seção anterior.

    b. Para o **Segredo compartilhado**, selecione **Alterar** e, em seguida, insira a senha de segredo compartilhado que você criou e registrou anteriormente.

    c. Na caixa **Tempo limite (segundos)** , insira o valor **30**.  
    O valor de tempo limite é necessário para dar tempo suficiente para concluir o segundo fator de autenticação. Algumas VPNs ou regiões exigem configurações de tempo limite superiores a 30 segundos para impedir que os usuários recebam várias chamadas telefônicas simultaneamente. Se os usuários tiverem esse problema, aumente o valor de **Tempo limite (segundos)** em incrementos de 30 segundos até que o problema deixe de ocorrer.

    ![Adicionar a janela do Servidor RADIUS configurando o Tempo limite](./media/howto-mfa-nps-extension-vpn/image16.png) 

8. Selecione **OK**.

### <a name="test-vpn-connectivity"></a>Testar Conectividade VPN

Nesta seção, você pode verificar se o cliente VPN é autenticado e autorizado pelo servidor RADIUS quando você tentar se conectar à porta virtual da VPN. As instruções pressupõem que você esteja usando o Windows 10 como um cliente VPN.

> [!NOTE]
> Se você já configurou um cliente VPN para se conectar ao servidor VPN e salvou as configurações, você pode ignorar as etapas relacionadas à configuração e salvar um objeto de conexão VPN.
>

1. No computador cliente de VPN, selecione o botão **Iniciar** e, em seguida, selecione o botão **Configurações**.

2. Na janela **Configurações do Windows**, selecione **Rede e Internet**.

3. Selecione **VPN**.

4. Selecione **Adicionar uma conexão VPN**.

5. Na janela **Adicionar uma conexão VPN**, na caixa **Provedor VPN**, selecione **Windows (interno)** , preencha os campos restantes conforme apropriado e, em seguida, selecione **Salvar**.

    ![A janela "Adicionar uma conexão VPN"](./media/howto-mfa-nps-extension-vpn/image17.png)

6. Vá para o **Painel de Controle** e selecione **Central de Rede e Compartilhamento**.

7. Selecione **Alterar configurações do adaptador**.

    ![Central de Rede e Compartilhamento – Alterar as configurações do adaptador](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Clique com o botão direito do mouse na conexão de rede VPN e, em seguida, selecione **Propriedades**.

9. Na janela Propriedades de VPN, selecione a guia **Segurança**.

10. Na guia **Segurança**, verifique se apenas **Microsoft CHAP Versão 2 (MS-CHAP v2)** está selecionado e selecione **OK**.

    ![A opção "Permitir estes protocolos"](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Clique com o botão direito do mouse na conexão de VPN e, em seguida, selecione **Conectar**.

12. Na janela **Configurações**, selecione **Conectar**.  
    Uma conexão bem-sucedida é exibida no log de segurança no servidor RADIUS como Evento ID 6272, conforme mostrado aqui:

    ![Janela Propriedades de Eventos mostrando uma conexão bem-sucedida](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Solucionar problemas de RADIUS

Suponha que sua configuração de VPN estava funcionando antes de você ter configurado o servidor VPN para usar um servidor RADIUS centralizado para autenticação e autorização. Se a configuração estiver funcionando, é provável que o problema seja causado por um erro de configuração do servidor RADIUS ou pelo uso de um nome de usuário ou senha inválido. Por exemplo, se você usar o sufixo UPN alternativo no nome de usuário, a tentativa de logon poderá falhar. Use o mesmo nome de conta para obter os melhores resultados.

Para solucionar esses problemas, o ideal é começar analisando os logs de Eventos de segurança no servidor RADIUS. Para economizar tempo procurando eventos, você pode usar a exibição personalizada de Servidor de Acesso e Política de Rede com base em função no Visualizador de Eventos, conforme mostrado aqui. "Evento ID 6273" indica eventos em que o NPS negou acesso a um usuário.

![Visualizador de Eventos mostrando eventos NPAS](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Configurar Autenticação Multifator

Para obter ajuda para configurar usuários para autenticação multifator, consulte os artigos [planejando uma implantação da autenticação multifator do Azure ad baseada em nuvem](howto-mfa-getstarted.md#create-conditional-access-policy) e [configurar minha conta para a verificação em duas etapas](../user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>Instalar e configurar a extensão do NPS

Esta seção apresenta instruções para configurar a VPN para usar MFA para autenticação de cliente com o servidor VPN.

> [!NOTE]
> A chave do registro de REQUIRE_USER_MATCH diferencia maiúsculas de minúsculas. Todos os valores devem ser definidos no formato de letras maiúsculas.
>

Depois de instalar e configurar a extensão NPS, todas as autenticações de cliente baseadas em RADIUS processadas por esse servidor são necessárias para que se possa usar MFA. Se todos os seus usuários de VPN não estiverem registrados na autenticação multifator do Azure AD, você poderá fazer o seguinte:

* Configurar outro servidor RADIUS para autenticar os usuários que não estão configurados para usar MFA.

* Crie uma entrada de registro que permita que usuários desafiados forneçam um segundo fator de autenticação se eles estiverem registrados na autenticação multifator do Azure AD.

Crie um novo valor de cadeia de caracteres chamado _REQUIRE_USER_MATCH em HKLM\SOFTWARE\Microsoft\AzureMfa_ e defina o valor como *true* ou *false*.

![A configuração "Exigir Correspondência do Usuário"](./media/howto-mfa-nps-extension-vpn/image34.png)

Se o valor for definido como *true* ou estiver em branco, todas as solicitações de autenticação estarão sujeitas a um desafio de MFA. Se o valor for definido como *false*, os desafios de MFA serão emitidos somente para usuários registrados na autenticação multifator do Azure AD. Use a configuração *false* somente em ambientes de teste ou de produção durante um período de integração.



### <a name="obtain-the-azure-active-directory-tenant-id"></a>Obter a ID de locatário Azure Active Directory

Como parte da configuração da extensão do NPS, você deve fornecer credenciais de administrador e a ID do seu locatário do Azure AD. Para obter a ID do locatário, conclua as seguintes etapas:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador global do locatário do Azure.
1. No menu portal do Azure, selecione **Azure Active Directory** ou pesquise por **Azure Active Directory** em qualquer página e selecione essa opção.
1. Na página **visão geral** , as *informações do locatário* são mostradas. Ao lado da *ID do locatário*, selecione o ícone de **cópia** , conforme mostrado no seguinte exemplo de captura de tela:

   ![Obtendo a ID do locatário do portal do Azure](./media/howto-mfa-nps-extension-vpn/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>Instalar a extensão NPS

A extensão do NPS deve ser instalada em um servidor que tenha a função de Serviços de Acesso e Política de Rede instalada e que funcione como o servidor RADIUS no seu projeto. *Não* instale a extensão NPS no servidor de VPN.

1. Baixe a extensão NPS do [Centro de Download da Microsoft](https://aka.ms/npsmfa).

2. Copie o arquivo executável de instalação (*NpsExtnForAzureMfaInstaller.exe*) para o servidor NPS.

3. No servidor NPS, clique duas vezes em **NpsExtnForAzureMfaInstaller.exe** e, se solicitado, selecione **Executar**.

4. Na janela **de configuração da extensão NPS para o Azure ad MFA** , examine os termos de licença de software, marque a caixa de seleção **eu concordo com os termos e condições de licença** e selecione **instalar**.

    ![A janela "extensão do NPS para a instalação do Azure AD MFA"](./media/howto-mfa-nps-extension-vpn/image36.png)

5. Na janela **de instalação da extensão do NPS para o Azure ad MFA** , selecione **fechar**.  

    ![A janela de confirmação "Instalação Bem-Sucedida"](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Configurar certificados para uso com a extensão do NPS usando um script do PowerShell

Para assegurar comunicações e para segurança, configure certificados para uso pela extensão do NPS. Os componentes NPS incluem um script do Windows PowerShell que configura um certificado autoassinado para uso com o NPS.

O script executa as ações a seguir:

* Cria um certificado autoassinado.
* Associa a chave pública do certificado à entidade de serviço no Azure AD.
* Armazena o certificado no repositório do computador local.
* Concede acesso à chave privada do certificado ao usuário de rede.
* Reinicia o serviço NPS.

Se você quiser usar seus próprios certificados, associe a chave pública do seu certificado à entidade de serviço no Azure AD e assim por diante.

Para usar o script, forneça a extensão com suas credenciais de administrador do Azure Active Directory e a ID do locatário do Azure Active Directory que você copiou anteriormente. A conta deve estar no mesmo locatário do Azure AD para a qual você deseja habilitar a extensão. Execute o script em cada servidor NPS onde você instalou a extensão NPS.

1. Execute o Windows PowerShell como um administrador.

2. No prompt de comando do PowerShell, digite **cd "c:\Program Files\Microsoft\AzureMfa\Config"** e clique em Enter.

3. No próximo prompt de comando, digite **.\AzureMfaNpsExtnConfigSetup.ps1** e selecione Enter. O script verifica se o módulo PowerShell do Azure AD está instalado. Se ele não estiver instalado, o script instala o módulo para você.

    ![Executar o script de configuração AzureMfsNpsExtnConfigSetup.ps1](./media/howto-mfa-nps-extension-vpn/image38.png)

    Se você receber um erro de segurança devido ao protocolo TLS, habilite o TLS 1.2 usando o comando `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` no prompt do PowerShell.
    
    Depois que o script verifica a instalação do módulo do PowerShell, ele exibe a janela de entrada do módulo do PowerShell do Azure Active Directory.

4. Insira suas credenciais de administrador do Azure AD e a senha e, em seguida, selecione **Entrar**.

    ![Autenticar no PowerShell do Azure AD](./media/howto-mfa-nps-extension-vpn/image39.png)

5. No prompt de comando, cole a ID de locatário copiada anteriormente e, em seguida, selecione Enter.

    ![Insira a ID de locatário do Azure AD copiada antes](./media/howto-mfa-nps-extension-vpn/image40.png)

    O script cria um certificado autoassinado e executa outras alterações de configuração. A saída é parecida com a imagem a seguir:

    ![Janela do PowerShell mostrando o certificado autoassinado](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Reinicialize o servidor.

### <a name="verify-the-configuration"></a>Verificar a configuração

Para verificar a configuração, estabeleça uma nova conexão VPN com o servidor VPN. Depois de inserir corretamente suas credenciais para autenticação primária, a conexão VPN aguarda que a autenticação secundária seja bem-sucedida antes que a conexão seja estabelecida, conforme mostrado abaixo.

![A janela VPN de Configurações do Windows](./media/howto-mfa-nps-extension-vpn/image42.png)

Se você se autentica com êxito com o método de verificação secundário configurado anteriormente na MFA do Azure AD, você está conectado ao recurso. No entanto, se a autenticação secundária não for bem-sucedida, seu acesso aos recursos será negado.

No exemplo a seguir, o aplicativo Microsoft Authenticator em um Windows Phone fornece a autenticação secundária:

![Exemplo de prompt de MFA no Windows Phone](./media/howto-mfa-nps-extension-vpn/image43.png)

Depois de ter autenticado com sucesso usando o método secundário, você receberá acesso à porta virtual no servidor VPN. Uma vez que você precisa usar um método de autenticação secundária usando um aplicativo móvel em um dispositivo confiável, o processo de entrada é mais seguro do que seria usando somente uma combinação de nome de usuário e senha.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Exibir logs do Visualizador de Eventos para eventos de entrada bem-sucedidos

Para ver eventos de entrada bem-sucedidos nos logs do Visualizador de Eventos do Windows, consulte o log de Segurança do Windows no servidor NPS digitando o seguinte comando do PowerShell:

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![Visualizador de Eventos de segurança do PowerShell](./media/howto-mfa-nps-extension-vpn/image44.png)

Você também pode exibir o log de segurança ou a exibição personalizada de Serviços de Acesso e Política de Rede conforme mostrado aqui:

![Exemplo de log do Servidor de Políticas de Rede](./media/howto-mfa-nps-extension-vpn/image45.png)

No servidor em que você instalou a extensão NPS para a autenticação multifator do Azure AD, você pode encontrar Visualizador de Eventos logs de aplicativo que são específicos para a extensão em *aplicativos e serviços Logs\Microsoft\AzureMfa*.

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![Exemplo de painel de logs do AuthZ no Visualizador de Eventos](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Guia de Solução de Problemas

Se a configuração não estiver funcionando conforme o esperado, inicie a solução de problemas verificando se o usuário está configurado para usar MFA. Peça ao usuário para conectar-se ao [Portal do Azure](https://portal.azure.com). Se o usuário for solicitado a realizar uma verificação secundária e conseguir fazer a autenticação com sucesso, você poderá eliminar uma configuração incorreta de MFA como um problema.

Se MFA estiver funcionando para o usuário, examine os logs relevantes do Visualizador de Eventos. Os logs incluem o evento de segurança, o gateway operacional e os logs da autenticação multifator do Azure AD que são discutidos na seção anterior.

Um exemplo de log de segurança que exibe um evento de falha na entrada (evento ID 6273) é mostrado aqui:

![Log de segurança mostrando um evento de falha na entrada](./media/howto-mfa-nps-extension-vpn/image47.png)

Um evento relacionado do log da autenticação multifator do Azure AD é mostrado aqui:

![Logs da autenticação multifator do Azure AD](./media/howto-mfa-nps-extension-vpn/image48.png)

Para executar solução de problemas avançada, consulte os arquivos de log de formato do banco de dados NPS no qual o serviço NPS está instalado. Os arquivos de log são criados na pasta _%SystemRoot%\System32\Logs_ como arquivos de texto separado por vírgula. Para obter uma descrição dos arquivos de log, consulte [Interpretar Arquivos de Log de Formato de Banco de Dados do NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10)).

As entradas nesses arquivos de log são difíceis de interpretar, a menos que você as exporte para uma planilha ou um banco de dados. Você pode encontrar muitas ferramentas de análise de IAS (Internet Authentication Service) online para ajudá-lo a interpretar os arquivos de log. A saída de um desses [aplicativos shareware](https://www.deepsoftware.com/iasviewer) que pode ser baixado é mostrada aqui:

![Exemplo de analisador de IAS do aplicativo shareware](./media/howto-mfa-nps-extension-vpn/image49.png)

Para mais solução de problemas, você pode usar um analisador de protocolo, como o Wireshark ou o [Analisador de Mensagens da Microsoft](/message-analyzer/microsoft-message-analyzer-operating-guide). A imagem a seguir do Wireshark mostra as mensagens do RADIUS entre o servidor VPN e o servidor NPS.

![Analisador de Mensagens da Microsoft mostrando tráfego filtrado](./media/howto-mfa-nps-extension-vpn/image50.png)

Para obter mais informações, consulte [integrar sua infraestrutura de NPS existente com a autenticação multifator do Azure ad](howto-mfa-nps-extension.md).

## <a name="next-steps"></a>Próximas etapas

[Obter a autenticação multifator do Azure AD](concept-mfa-licensing.md)

[Gateway de Área de Trabalho Remota e Servidor de Autenticação Multifator do Azure usando RADIUS](howto-mfaserver-nps-rdg.md)

[Integrar seus diretórios locais no Azure Active Directory](../hybrid/whatis-hybrid-identity.md)