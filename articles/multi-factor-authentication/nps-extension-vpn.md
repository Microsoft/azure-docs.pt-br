---
title: "Integração de VPN com o Azure MFA usando a extensão NPS | Microsoft Docs"
description: "Este artigo aborda a integração da infraestrutura da sua VPN com a Autenticação Multifator do Azure usando a extensão do Servidor de Políticas de Rede (NPS) para o Microsoft Azure."
services: active-directory
keywords: "Azure MFA, integrar VPN, Azure Active Directory, extensão do Servidor de Políticas de Rede"
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: kgremban
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: b47e9b321b2fd0d0db9762003531b0fe9f045f07
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-your-vpn-infrastructure-with-azure-multi-factor-authentication-mfa-using-the-network-policy-server-nps-extension-for-azure"></a>Integre a sua infraestrutura de VPN com a Autenticação Multifator do Azure usando a extensão do Servidor de Políticas de Rede (NPS) para o Azure

## <a name="overview"></a>Visão geral

A extensão de Serviço de Políticas de Rede (NPS) para o Azure permite que as organizações protejam a autenticação do cliente RADIUS usando a [Autenticação Multifator do Azure (MFA)](multi-factor-authentication-get-started-server-rdg.md) baseada em nuvem, que fornece a verificação em duas etapas.

Este artigo fornece instruções para a integração da infra-estrutura de NPS com o Azure MFA usando a extensão NPS do Azure para habilitar a verificação em duas etapas segura para os usuários que tentarem se conectar à rede usando uma VPN. 

A Política de Rede e Serviços de Acesso (NPS) permite que as organizações façam o seguinte:

* Especifiquem locais centrais para o gerenciamento e controle de solicitações para especificar quem pode se conectar, as horas do dia durante as quais as conexões são permitidas, a duração das conexões e o nível de segurança que os clientes devem usar para se conectar e assim por diante. Em vez de especificar essas políticas em cada servidor VPN ou Gateway de Área de Trabalho Remota (RD), essas políticas podem ser especificadas uma vez em um local central. O protocolo RADIUS é usado para fornecer a Autenticação, Autorização e Contabilização (AAA) centralizadas. 
* Estabelecer e impor políticas de integridade do cliente de Proteção de Acesso à Rede (NAP) que determinam se os dispositivos têm acesso irrestrito ou restrito aos recursos de rede.
* Fornece um meio para impor a autenticação e autorização para acesso aos comutadores Ethernet e pontos de acesso sem fio compatíveis com 802.1x.    

Para obter mais informações, consulte [Servidor de Políticas de Rede (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top). 

Para aumentar a segurança e fornecer um alto nível de conformidade, as organizações podem integrar o NPS com o Azure MFA para garantir que os usuários usem a verificação em duas etapas para poderem se conectar à porta virtual no servidor VPN. Para conceder acesso aos usuários, é necessário que eles forneçam sua combinação de nome de usuário e senha com as informações que o usuário tem em seu controle. Essas informações devem ser confiáveis e não facilmente duplicadas, como um número de telefone celular, o número de telefone fixo, o aplicativo em um dispositivo móvel e assim por diante.

Antes da disponibilidade da extensão do NPS do Azure, os clientes que desejavam implementar a verificação em duas etapas para ambientes integrados de NPS e o Azure MFA tinham que configurar e manter um servidor de MFA separado no ambiente local, conforme documentado no Gateway de Área de Trabalho Remota e Servidor de Autenticação Multifator do Azure usando RADIUS.

A disponibilidade da extensão do NPS para o Azure agora dá às organizações a opção de implantar uma solução MFA local ou uma solução MFA baseada em nuvem para autenticação de cliente RADIUS segura.
 
## <a name="authentication-flow"></a>Fluxo de autenticação
Quando um usuário se conecta a uma porta virtual em um servidor VPN, ele deve primeiro fazer a autenticação usando uma variedade de protocolos que permitem o uso de uma combinação de nome de usuário/senha e métodos de autenticação baseada em certificado. 

Além de autenticação e verificação de identidade, os usuários devem ter as permissões apropriadas de discagem. Em implantações simples, essas permissões de discagem que permitem acesso são definidas diretamente em objetos de usuário do Active Directory. 

 ![Propriedades do Usuário](./media/nps-extension-vpn/image1.png)

Para implantações simples, cada servidor VPN concede ou nega o acesso com base nas políticas definidas em cada servidor VPN local.

Em implementações maiores e mais escalonáveis, as políticas que concedem ou negam acesso à VPN são centralizadas em servidores RADIUS. Nesse caso, o servidor VPN atua como um servidor de acesso (cliente RADIUS) que encaminha solicitações de conexão e mensagens de conta para um servidor RADIUS. Para se conectar à porta virtual no servidor VPN, os usuários devem ser autenticados e atender às condições definidas centralmente em servidores RADIUS. 

Quando a extensão NPS para o Azure é integrada com o NPS, um fluxo de autenticação correto funciona da seguinte maneira:

1. O servidor de VPN recebe uma solicitação de autenticação de um usuário VPN que inclui um nome de usuário e uma senha para se conectar a um recurso, como uma sessão de Área de Trabalho Remota. 
2. O servidor VPN age como um cliente RADIUS e converte a solicitação para uma mensagem de solicitação de acesso RADIUS e envia a mensagem (a senha é criptografada) para o servidor RADIUS (NPS) onde a extensão NPS está instalada. 
3. A combinação de nome de usuário e senha é verificada no Active Directory. Se o nome de usuário / senha está incorreto, o servidor RADIUS envia uma mensagem de rejeição de acesso. 
4. Se todas as condições conforme especificadas na Solicitação de Conexão de NPS e as Políticas de Rede forem atendidas (por exemplo, hora do dia ou restrições de associação a um grupo), a extensão NPS dispara uma solicitação de autenticação secundária com o Azure MFA. 
5. O Azure MFA comunica-se com o Azure Active Directory, recupera os detalhes do usuário e executa a autenticação secundária usando um método de verificação configurado para o usuário (mensagem de texto, aplicativo móvel e assim por diante). 
6. Após o desafio de MFA ter sido concluído com sucesso, o Azure MFA comunica o resultado para a extensão do NPS.
7. Após a tentativa de conexão ser autenticada e autorizada, o servidor NPS onde a extensão está instalada envia uma mensagem de aceitação de acesso RADIUS ao servidor VPN (cliente RADIUS).
8. O usuário tem acesso à porta virtual no servidor VPN e estabelece um túnel VPN criptografado.

## <a name="prerequisites"></a>Pré-requisitos
Esta seção fornece detalhes sobre os pré-requisitos necessários para integrar o Azure MFA com o Gateway de Área de Trabalho Remota. Antes de iniciar, você deverá ter os seguintes pré-requisitos já preparados.

* Infraestrutura de VPN
* Função de Serviços de Acesso (NPS) e Política de Rede
* Licença do Azure MFA
* Software do Windows Server
* Bibliotecas
* Azure AD sincronizado com o AD local 
* ID do GUID do Azure Active Directory

### <a name="vpn-infrastructure"></a>Infraestrutura de VPN
Este artigo pressupõe que você tem uma infraestrutura VPN funcional usando o Microsoft Windows Server 2016 e que o servidor VPN não está atualmente configurado para encaminhar solicitações de conexão a um servidor RADIUS. Você irá configurar a infraestrutura VPN para usar um servidor RADIUS central neste guia.

Se você não tiver uma infraestrutura de trabalho funcional, você pode criar rapidamente essa infraestrutura seguindo as orientações fornecidas em vários tutoriais de configuração VPN que você pode encontrar na Microsoft e em sites de terceiros. 

### <a name="network-policy-and-access-services-nps-role"></a>Função de Serviços de Acesso (NPS) e Política de Rede

O serviço de função NPS fornece a funcionalidade de cliente e servidor RADIUS. Este artigo pressupõe que você instalou a função NPS em um servidor membro ou controlador de domínio em seu ambiente. Você configurará o RADIUS para uma configuração de VPN neste guia. Instalar a função NPS em um servidor _diferente_ do servidor VPN.

Para obter informações sobre como instalar a função NPS do serviço Windows Server 2012 ou posterior, consulte [Instalar um Servidor de Política de Integridade de NAP](https://technet.microsoft.com/library/dd296890.aspx). Política de Acesso à Rede (NAP) foi preterido no Windows Server 2016. Para obter uma descrição de melhores práticas recomendadas para NPS, incluindo a recomendação para instalar o NPS em um controlador de domínio, consulte [Práticas recomendadas para NPS](https://technet.microsoft.com/library/cc771746).

### <a name="licenses"></a>Licenças

É necessário uma licença para o Azure MFA, que está disponível por meio de um Azure AD Premium, Enterprise Mobility + Security (EMS) ou uma assinatura de MFA. Para obter mais informações, consulte [Como obter Autenticação Multifator do Azure](multi-factor-authentication-versions-plans.md). Para fins de teste, você pode usar uma assinatura de avaliação.

### <a name="software"></a>Software

A extensão NPS requer o Windows Server 2008 R2 SP1 ou superior com o serviço de função NPS instalado. Todas as etapas neste guia foram realizadas usando o Windows Server 2016.

### <a name="libraries"></a>Bibliotecas

Essas bibliotecas são instaladas automaticamente com a extensão.

-   [Pacotes redistribuíveis do Visual C++ para Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Módulo Microsoft Azure Active Directory para Windows PowerShell versão 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

O Módulo Microsoft Azure Active Directory para Windows PowerShell é instalado, se ainda não estiver presente, por meio de um script de configuração que é executado como parte do processo de instalação. Não é necessário instalar este módulo antecipadamente, se ele ainda não estiver instalado.

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory sincronizado com o Active Directory local 

Para usar a extensão NPS, os usuários locais devem ser sincronizados com o Azure Active Directory e habilitados para Autenticação Multifator. Este guia pressupõe que os usuários locais são sincronizados com o Azure Active Directory usando o AD Connect. As instruções para habilitar usuários para MFA são fornecidas abaixo.
Para obter informações sobre o Azure AD Connect, consulte [Integrar seus diretórios locais com o Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>ID do GUID do Azure Active Directory 
Para instalar o NPS, você precisa saber o GUID do Azure Active Directory. São fornecidas instruções para localizar o GUID do Azure Active Directory na próxima seção.

## <a name="configure-radius-for-vpn-connections"></a>Configurar RADIUS para conexões VPN

Se você tiver instalado a função de servidor NPS em um servidor membro, você precisa configurar para autenticar e autorizar o cliente VPN que solicita conexões VPN. 

Esta seção pressupõe que você instalou a função de Servidor de Políticas de Rede, mas não o configurou para uso em sua infraestrutura.

>[!NOTE]
>Se você já tiver um servidor VPN de trabalho que usa um servidor RADIUS centralizado para autenticação, você pode ignorar esta seção.
>

### <a name="register-server-in-active-directory"></a>Registrar o Servidor no Active Directory
Para funcionar corretamente nesse cenário, o servidor NPS deve ser registrado no Active Directory.

1. Abra o Gerenciador de Servidor.
2. No Gerenciador do Servidor, clique em **Ferramentas**e, em seguida, clique em **Servidor de Políticas de Rede**. 
3. No console do Servidor de Políticas de Rede, clique com o botão direito em **NPS (Local)** e, em seguida, clique em **Registrar servidor no Active Directory**. Clique em **OK** duas vezes.

 ![Servidor de Políticas de Rede](./media/nps-extension-vpn/image2.png)

4. Deixe o console aberto para o próximo procedimento.

### <a name="use-wizard-to-configure-radius-server"></a>Use o assistente para configurar o servidor RADIUS
Você pode usar um padrão (baseado em assistente) ou a opção de configuração avançada para configurar o servidor RADIUS. Esta seção pressupõe o uso da opção de configuração padrão baseada em assistente.

1. No console do Servidor de Políticas de Rede, clique em **NPS (Local)**.
2. Em Configuração Padrão, selecione **Servidor RADIUS para conexões VPN ou Conexões Discadas**e, em seguida, clique em **Configurar VPN ou Conexão Discada**.

 ![Configurar VPN](./media/nps-extension-vpn/image3.png)

3. Na página Tipo de Conexões de Rede Privada Virtual ou Conexões Discadas, selecione **Conexões de Rede Virtual Privada** e clique em **Seguinte**.

 ![Rede Virtual Privada](./media/nps-extension-vpn/image4.png)

4. Na página Especificar Servidor VPN ou Discado, clique em **Adicionar**.
5. Na caixa de diálogo **Novo cliente RADIUS**, forneça um nome amigável, digite o nome que pode ser resolvido ou o endereço IP do servidor VPN e insira uma senha de segredo compartilhado. Essa senha de segredo compartilhado deve ser longa e complexa. Anote a senha, pois você precisará dela para as etapas na próxima seção.

 ![Novo Cliente RADIUS](./media/nps-extension-vpn/image5.png)

6. Clique em **OK**e, em seguida, **Seguinte**.
7. Sobre o **configurar métodos de autenticação** página, aceite a seleção padrão (autenticação criptografada da Microsoft versão 2 (MS-CHAPv2) ou escolha outra opção e clique em **Seguinte**.

  >[!NOTE]
  >Se você configurar o protocolo EAP (Extensible Authentication), você deve usar o MS CHAPv2 ou PEAP. Não há suporte para nenhum outro tipo de EAP.
 
8. Na página Especificar grupos de usuários, clique em **Adicionar** e selecione um grupo apropriado, se existir um. Caso contrário, deixe a seleção em branco para conceder acesso a todos os usuários.

 ![Especificar Grupos de Usuários](./media/nps-extension-vpn/image7.png)

9. Clique em **Avançar**.
10. Na página Especificar Filtros de IP, clique em **Seguinte**.
11. Na página Especificar Configurações de Criptografia, aceite as configurações padrão e clique em **Seguinte**.

 ![Especificar Criptografia](./media/nps-extension-vpn/image8.png)

12. Na página Especificar um Nome de Realm, deixe o nome em branco, aceite as configurações padrão e clique em **Seguinte**.

 ![Especificar um Nome de Realm](./media/nps-extension-vpn/image9.png)

13. Na página de clientes RADIUS e Novas Conexões de Rede Virtual Privada ou Conexões Discadas, clique em **Concluir**.

 ![Concluir as Conexões](./media/nps-extension-vpn/image10.png)

### <a name="verify-radius-configuration"></a>Verificar a configuração RADIUS
Esta seção fornece detalhes sobre a configuração que você criou usando o assistente.

1. No servidor NPS, no console NPS (Local), expanda Clientes RADIUS e selecione **Clientes RADIUS**.
2. No painel de detalhes, clique com o botão direito no cliente RADIUS criado usando o assistente e, em seguida, clique em **Propriedades**. As propriedades para o cliente RADIUS (o servidor VPN) devem ser semelhantes ao mostrado abaixo.

 ![Propriedades VPN](./media/nps-extension-vpn/image11.png)

3. Clique em **Cancelar**.
4. No servidor VPN, no console do NPS (Local), expanda **Políticas** e selecione **Políticas de Solicitação de Conexão**. Você deve ver a política de Conexões VPN que se parece com a imagem a seguir.

 ![Solicitações de Conexão](./media/nps-extension-vpn/image12.png)

5. Em Políticas, selecione **Políticas de Rede**. Você deve ver uma política de Conexões de Rede Virtual Privada (VPN) que se parece com a imagem a seguir.

 ![Propriedades da Rede](./media/nps-extension-vpn/image13.png)

## <a name="configure-vpn-server-to-use-radius-authentication"></a>Configurar o Servidor VPN para usar a autenticação RADIUS
Nesta seção, você configura o servidor VPN para usar a autenticação RADIUS. Esta seção pressupõe que você tem uma configuração em vigor do servidor VPN, mas não configurou o servidor VPN para usar a autenticação RADIUS. Depois de configurar o servidor VPN, você confirma que sua configuração está funcionando conforme o esperado.

>[!NOTE]
>Se você já tiver um servidor VPN em vigor que usa autenticação RADIUS, você pode ignorar esta seção.
>

### <a name="configure-authentication-provider"></a>Configurar provedor de autenticação
1. No servidor VPN, abra o Gerenciador do Servidor.
2. No Gerenciador do Servidor, clique em **Ferramentas** e, em seguida, **Roteamento e Acesso Remoto**.
3. No console de Roteamento e Acesso Remoto, clique com o botão direito em **\[Nome do servidor\] (local)** e, em seguida, clique em **Propriedades**.

 ![Roteamento e Acesso Remoto](./media/nps-extension-vpn/image14.png)
 
4. Na caixa de diálogo **Propriedades de [Nome do Servidor} (local)**, clique na guia **Segurança**. 
5. Na guia **Segurança**, em Provedor de Autenticação, clique em **Autenticação RADIUS**e, em seguida, **Configurar**.

 ![Autenticação Radius](./media/nps-extension-vpn/image15.png)
 
6. Na caixa de diálogo de Autenticação RADIUS, clique em **Adicionar**.
7. Em Adicionar Servidor RADIUS, no Nome do servidor, adicione o nome ou o endereço IP do servidor RADIUS configurado na seção anterior.
8. Em Segredo compartilhado, clique em **Alterar** e adicione a senha de segredo compartilhado criada e anotada anteriormente.
9. No Tempo limite (segundos), altere o valor para um valor entre **30** e **60**. Isso é necessário para dar tempo suficiente para concluir o segundo fator de autenticação.
 
 ![Adicionar Servidor RADIUS](./media/nps-extension-vpn/image16.png)
 
10. Clique em **OK** até que você feche todas as caixas de diálogo.

### <a name="test-vpn-connectivity"></a>Testar Conectividade VPN
Nesta seção, você pode confirmar se o cliente é autenticado e autorizado pelo servidor RADIUS quando você tentar se conectar à porta virtual da VPN. Esta seção pressupõe que você está usando o Windows 10 como um cliente VPN. 

>[!NOTE]
>Se você já configurou um cliente VPN para se conectar ao servidor VPN e salvou as configurações, você pode ignorar as etapas relacionadas à configuração e salvar um objeto de conexão VPN.
>

1. No computador de cliente VPN, clique em **Iniciar** e, em seguida, **Configurações** (ícone de engrenagem).
2. Em Configurações de Janela, clique em **Rede e Internet**.
3. Clique em **VPN**.
4. Clique em **Adicionar uma conexão VPN**.
5. Em Adicionar uma conexão VPN, especifique o Windows (interno) como o provedor VPN, em seguida, preencha os campos restantes, conforme apropriado e clique **Salvar**. 

 ![Adicionar Conexão VPN](./media/nps-extension-vpn/image17.png)
 
6. Abra a **Central de Rede e Compartilhamento** no painel de controle.
7. Clique em **Alterar as configurações do adaptador**.

 ![Alterar as Configurações do Adaptador](./media/nps-extension-vpn/image18.png)

8. Clique com botão direito em conexão de rede VPN e clique em Propriedades. 

 ![Propriedades da Rede VPN](./media/nps-extension-vpn/image19.png)

9. Na caixa de diálogo de propriedades da VPN, clique na guia **Segurança**. 
10. Na guia Segurança, certifique-se de que apenas **Microsoft CHAP Versão 2 (MS-CHAP v2)** está selecionado e clique em OK.

 ![Permitir Protocolos](./media/nps-extension-vpn/image20.png)

11. Clique com botão direito em conexão VPN e clique em **Conectar**.
12. Na página Configurações, clique em **Conectar**.

Uma conexão bem-sucedida é exibida no log de segurança no servidor RADIUS como ID de Evento 6272, conforme mostrado abaixo.

 ![Propriedades do evento](./media/nps-extension-vpn/image21.png)

## <a name="troubleshoot-guide"></a>Guia de Solução de Problemas
Suponha que sua configuração de VPN estava funcionando antes de você ter configurado o servidor VPN para usar um servidor RADIUS centralizado para autenticação e autorização. Nesse caso, é provável que o problema possa ser causado por um erro de configuração do Servidor RADIUS ou pelo uso de um nome de usuário ou senha inválidos. Por exemplo, se você usar o sufixo UPN alternativo no nome de usuário, a tentativa de logon poderá falhar (você deve usar o mesmo nome de Conta para obter os melhores resultados). 

Para solucionar esses problemas, o ideal é começar analisando os logs de Eventos de segurança no servidor RADIUS. Para economizar tempo procurando eventos, você pode usar a exibição personalizada de Servidor de Acesso e Política de Rede baseado em função no Visualizador de Eventos, conforme mostrado abaixo. O Evento ID 6273 indica os eventos onde o Servidor de Políticas de Rede negou acesso a um usuário. 

 ![Visualizador de Eventos](./media/nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>Configurar Autenticação Multifator
Esta seção fornece instruções para habilitar usuários para MFA e para configurar contas para verificação em duas etapas. 

### <a name="enable-multi-factor-authentication"></a>Habilitar autenticação multifator
Nesta seção, você pode habilitar contas do Azure AD para MFA. Use o **portal clássico** para habilitar usuários para MFA. 

1. Abra um navegador e navegue até [https://manage.windowsazure.com](https://manage.windowsazure.com). 
2. Faça logon como administrador.
3. No portal, no painel de navegação esquerdo, clique em **ACTIVE DIRECTORY**.

 ![Diretório Padrão](./media/nps-extension-vpn/image23.png)

4. Na coluna NOME, clique em **Diretório Padrão** (ou outro diretório, se apropriado).
5. Na página de Início Rápido, clique em **Configurar**.

 ![Configuração Padrão](./media/nps-extension-vpn/image24.png)

6. Na página CONFIGURAR, role para baixo e na seção autenticação multifator, clique em **Gerenciar configurações do serviço**.

 ![Gerenciar Configurações de MFA](./media/nps-extension-vpn/image25.png)
 
7. Na página de autenticação multifator, examine as configurações de serviço padrão e, em seguida, clique em **Usuários**. 

 ![Usuários MFA](./media/nps-extension-vpn/image26.png)
 
8. Na página Usuários, selecione os usuários que você deseja habilitar para MFA e, em seguida, clique em **Habilitar**.

 ![Propriedades](./media/nps-extension-vpn/image27.png)
 
9. Quando solicitado, clique em **Habilitar autenticação multifator**.

 ![Habilitar MFA](./media/nps-extension-vpn/image28.png)
 
10. Clique em **fechar** 
11. Atualize a página. O status de MFA é alterado para Habilitado.

Para saber mais sobre como habilitar usuários para Autenticação Multifator, consulte [Introdução à Autenticação Multifator do Azure na nuvem](multi-factor-authentication-get-started-cloud.md). 

### <a name="configure-accounts-for-two-step-verification"></a>Configurar contas para verificação em duas etapas
Depois que uma conta tiver sido habilitada para MFA, os usuários não podem entrar em recursos controlados pela política de MFA até que eles tenham configurado com sucesso um dispositivo confiável a ser usado para o segundo fator de autenticação que foi autenticado tendo usado a verificação em duas etapas.

Nesta seção, você pode configurar um dispositivo confiável para uso com a verificação em duas etapas. Há várias opções disponíveis para configurá-los, incluindo o seguinte:

* **Aplicativo móvel**. Você pode instalar o aplicativo Microsoft Authenticator em um dispositivo Windows Phone, Android ou iOS. Dependendo das políticas da sua organização, você deve usar o aplicativo de um dos dois modos: Receber notificações de verificações (uma notificação é enviada por push para o dispositivo) ou Usar o código de verificação (é necessário inserir um código de verificação de atualizações a cada 30 segundos). 
* **Chamada telefônica ou SMS de celular**. Você pode receber uma chamada telefônica automática ou mensagem de texto. Com a opção de chamada telefônica, você responde à chamada e pressiona o sinal # para autenticar. Com a opção de mensagem de texto, você pode responder à mensagem de texto ou inserir o código de verificação fornecido na interface de entrada.
* **Ligação para telefone comercial**. Esse processo é igual ao descrito para as chamadas telefônicas automatizadas acima.

Siga estas instruções para configurar um dispositivo para usar o aplicativo móvel para receber notificações por push para verificação.

1. Faça logon em [https://aka.ms/mfasetup](https://aka.ms/mfasetup) ou em qualquer site, como [https://portal.azure.com](https://portal.azure.com), onde seja necessário autenticar usando suas credenciais habilitadas para MFA. 
2. Ao fazer logon com seu nome de usuário e senha, você verá uma tela solicitando que você configure a conta para verificação de segurança adicional.

 ![Segurança Adicional](./media/nps-extension-vpn/image29.png)

3. Clique em **Configurar agora**.
4. Na página de Verificação de segurança adicional, selecione um tipo de contato (telefone de autenticação, telefone comercial ou aplicativo móvel). Em seguida, selecione um país ou região e selecione um método. O método varia de acordo com o tipo de contato que você selecionar. Por exemplo, se você escolher aplicativo móvel, você pode selecionar se deseja receber notificações de verificação ou usar um código de verificação. As etapas a seguir pressupõem que você escolheu **Aplicativo móvel** como o tipo de contato.

 ![Autenticação por Telefone](./media/nps-extension-vpn/image30.png)

5. Selecione Aplicativo móvel, clique em **Receber notificações de verificação**e, em seguida, **Configurar**. 

 ![Verificação do Aplicativo Móvel](./media/nps-extension-vpn/image31.png)
 
6. Se você ainda não tiver feito isso, instale o aplicativo móvel do autenticador em seu dispositivo. 
7. Siga as instruções no aplicativo móvel para verificar o código de barras apresentado ou insira as informações manualmente e, em seguida, clique em **Concluído**.

 ![Configurar Aplicativo Móvel](./media/nps-extension-vpn/image32.png)

8. Na página de Verificação de segurança adicional, clique em **Entrar em contato** e responda à notificação enviada para seu dispositivo.
9. Na página de Verificação de segurança adicional, insira um número de contato caso você perca o acesso ao aplicativo móvel e clique em **Seguinte**.

 ![Número de Telefone Celular](./media/nps-extension-vpn/image33.png)
 
10. Na Verificação de segurança adicional, clique em **Concluído**.

O dispositivo agora está configurado para fornecer um segundo método de verificação. Para obter informações sobre como configurar contas de verificação em duas etapas, consulte [Configurar minha conta para verificação em duas etapas](./end-user/multi-factor-authentication-end-user-first-time.md).

## <a name="install-and-configure-nps-extension"></a>Instalar e configurar a extensão do NPS

Esta seção fornece instruções para configurar a VPN para usar o Azure MFA para autenticação de cliente com o Servidor VPN.

Depois de instalar e configurar a extensão NPS, todas as autenticações de cliente baseadas em RADIUS processadas por este servidor são necessárias para que se possa usar o Azure MFA. Se nem todos os seus usuários VPN estiverem registrados no Azure MFA, você pode configurar outro servidor RADIUS para autenticar os usuários que não estão configurados para usar a MFA. Ou você pode criar uma entrada de registro que permite aos usuários fornecer um segundo fator de autenticação durante o desafio, apenas se eles estiverem registrados no MFA. 

Crie um novo valor de cadeia de caracteres chamado _REQUIRE_USER_MATCH in HKLM\SOFTWARE\Microsoft\AzureMfa_ e defina o valor como TRUE ou FALSE. 

 ![Exigir Correspondência do Usuário](./media/nps-extension-vpn/image34.png)
 
Se o valor for definido como TRUE ou não for definido, todas as solicitações de autenticação estão sujeitas a um desafio de MFA. Se o valor é definido como FALSE, os desafios de MFA são emitidos somente aos usuários que estão registrados no MFA. Use a configuração FALSE somente para testes ou em ambientes de produção durante um período de integração.

### <a name="acquire-azure-active-directory-guid-id"></a>Obter a ID do GUID do Azure Active Directory

Como parte da configuração da extensão NPS, você precisa fornecer credenciais de administrador e a ID do Azure Active Directory para o seu locatário do Azure AD. As etapas a seguir mostram como obter a ID do locatário.

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com) como administrador global do locatário do Azure.
2. Na barra de navegação esquerda, clique no ícone **Azure Active Directory**.
3. Clique em **Propriedades**.
4. Para copiar sua ID de diretório para a área de transferência, selecione o ícone de **Copiar**.
 
 ![ID do Diretório](./media/nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>Instalar a extensão NPS
A extensão NPS deve ser instalada em um servidor que tem a função de Serviços de acesso (NPS) e Política de Rede instalada e que funciona como o servidor RADIUS no seu projeto. Não instale a extensão NPS no servidor de Área de Trabalho Remota.

1. Baixe a extensão NPS em [https://aka.ms/npsmfa](https://aka.ms/npsmfa). 
2. Copie o arquivo de instalação executável (NpsExtnForAzureMfaInstaller.exe) para o servidor NPS.
3. No servidor NPS, clique duas vezes em **NpsExtnForAzureMfaInstaller.exe**. Se solicitado, clique em **Executar**.
4. Na extensão NPS para a caixa de diálogo de Azure MFA, revise os termos de licença de software, marque **Eu concordo com os termos e condições**e clique em **Instalar**.

 ![Extensão NPS](./media/nps-extension-vpn/image36.png)
 
5. Na Extensão NPS para a caixa de diálogo do Azure MFA, clique em **Fechar**.  

 ![Instalação bem-sucedida](./media/nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Configurar certificados para uso com a extensão NPS usando um script do PowerShell
Em seguida, você precisa configurar certificados para uso pela extensão NPS para garantia e comunicações seguras. Os componentes NPS incluem um script do Windows PowerShell que configura um certificado autoassinado para uso com o NPS. 

O script executa as ações a seguir:

* Cria um certificado autoassinado
* Associa a chave pública do certificado à entidade de serviço no Azure AD
* Armazena o certificado no repositório do computador local
* Concede acesso à chave privada do certificado ao Usuário de Rede
* Reinicia o serviço do Servidor de Políticas de Rede

Se você quiser usar seus próprios certificados, você precisa associar o público do seu certificado à entidade de serviço no Azure AD e assim por diante.
Para usar o script, forneça a extensão com suas credenciais de administrador do Azure Active Directory e a ID do locatário do Azure Active Directory que você copiou anteriormente. Execute o script em cada servidor NPS onde você instalou a extensão NPS.

1. Abra um prompt do Windows PowerShell administrativo.
2. No prompt do PowerShell, digite _cd 'c:\Program Files\Microsoft\AzureMfa\Config'_ e pressione **ENTER**.
3. Digite _.\AzureMfsNpsExtnConfigSetup.ps1_ e pressione **ENTER**. 
 * O script verifica se o módulo do Azure Active Directory PowerShell está instalado. Se ele não estiver instalado, o script instala o módulo para você.
 
 ![PowerShell](./media/nps-extension-vpn/image38.png)
 
4. Depois que o script verifica a instalação do módulo do PowerShell, ele exibe a caixa de diálogo de módulo do Azure Active Directory PowerShell. Na caixa de diálogo, insira suas credenciais de administrador do Azure AD e a senha e clique em **Entrar**. 
 
 ![Entrar no PowerShell](./media/nps-extension-vpn/image39.png)
 
5. Quando solicitado, cole a ID do locatário que você copiou para a área de transferência anteriormente e pressione **ENTER**. 

 ![ID do locatário](./media/nps-extension-vpn/image40.png)

6. O script cria um certificado autoassinado e executa outras alterações de configuração. A saída deve ser como a imagem mostrada abaixo.

 ![Certificado Autoassinado](./media/nps-extension-vpn/image41.png)

7. Reinicialize o servidor.
 
### <a name="verify-configuration"></a>Verificar a configuração
Para verificar a configuração, você precisa estabelecer uma nova conexão VPN com o servidor VPN. Ao inserir corretamente suas credenciais para autenticação primária, a conexão VPN aguarda que a autenticação secundária seja bem-sucedida antes que a conexão seja estabelecida, conforme mostrado abaixo. 

 ![Verificar a Configuração](./media/nps-extension-vpn/image42.png)

Se você autenticar com sucesso o método de verificação secundária que você configurou anteriormente no Azure MFA, você está conectado ao recurso. No entanto, se a autenticação secundária não for bem-sucedida, seu acesso aos recursos será negado. 

No exemplo a seguir, o aplicativo do Autenticador em um Windows Phone é usado para fornecer a autenticação secundária.

 ![Verificar a Conta](./media/nps-extension-vpn/image43.png)

Depois de autenticado com sucesso usando o método secundário, você recebe acesso à porta virtual no servidor VPN. No entanto, como você deve usar um método de autenticação secundária usando um aplicativo móvel em um dispositivo confiável, o processo para conectar-se é mais seguro do que seria usando somente uma combinação de nome de usuário e senha.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Exibir logs do Visualizador de Eventos para eventos de logon com sucesso
Para exibir os eventos de logon com sucesso nos logs do Visualizador de Eventos do Windows, você pode emitir o seguinte comando do Windows PowerShell para consultar o log de segurança do Windows no servidor NPS.

Para consultar eventos de logon com sucesso nos logs de Visualizador de eventos de segurança, use o seguinte comando,
* _Get-WinEvent -Logname Security_ | onde {$_.ID -eq '6272'} | FL 

 ![Visualizador de Eventos de Segurança](./media/nps-extension-vpn/image44.png)
 
Você também pode exibir o log de segurança ou o modo de exibição personalizado de Serviços de Acesso e Política de Rede, conforme mostrado abaixo:

 ![Acesso à Política de Rede](./media/nps-extension-vpn/image45.png)

No servidor onde você instalou a extensão NPS para o Azure MFA, você pode encontrar os logs de aplicativo do Visualizador de Eventos específicas para a extensão em **Application and Services Logs\Microsoft\AzureMfa**. 

* _Get-WinEvent -Logname Security_ | onde {$_.ID -eq '6272'} | FL

 ![Número de Eventos](./media/nps-extension-vpn/image46.png)

## <a name="troubleshoot-guide"></a>Guia de Solução de Problemas
Se a configuração não estiver funcionando conforme o esperado, um bom lugar para iniciar a solução de problemas é verificar se o usuário está configurado para usar o Azure MFA. Peça para o usuário se conectar ao [https://portal.azure.com](https://portal.azure.com). Se os usuários forem solicitados a realizar uma verificação secundária e são autenticados com sucesso, você pode eliminar uma configuração incorreta do Azure MFA.

Se o Azure MFA está funcionando para o(s) usuário(s), você deve analisar os logs de eventos relevantes. Isso inclui os Eventos de Segurança, Gateway operacional e logs do Azure MFA que foram discutidos na seção anterior. 

Abaixo está um exemplo de saída do log de segurança mostrando um evento de logon com falha (Evento ID 6273):

 ![Log de Segurança](./media/nps-extension-vpn/image47.png)

Abaixo está um evento relacionado dos logs do AzureMFA:

 ![Logs do Azure MFA](./media/nps-extension-vpn/image48.png)

Para executar opções de solução de problemas avançadas, consulte os arquivos de log de formato do banco de dados NPS onde o serviço NPS está instalado. Esses arquivos de log são criados na pasta _%SystemRoot%\System32\Logs_ como arquivos de texto separado por vírgula. Para obter uma descrição desses arquivos de log, consulte [Interpretar arquivos de log de formato de banco de dados de NPS](https://technet.microsoft.com/library/cc771748.aspx). 

As entradas nesses arquivos de log são difíceis de interpretar sem importá-los para uma planilha ou um banco de dados. Você pode encontrar vários analisadores de IAS on-line para ajudá-lo a interpretar os arquivos de log. Abaixo está a saída de um desses [aplicativos shareware](http://www.deepsoftware.com/iasviewer) que pode ser baixado: 

 ![Aplicativo Shareware](./media/nps-extension-vpn/image49.png)

E, por último, para mais opções de solução de problemas, você pode usar um analisador de protocolo, como o Wireshark ou o [Analisador de Mensagens da Microsoft](https://technet.microsoft.com/library/jj649776.aspx). A imagem a seguir do Wireshark mostra as mensagens RADIUS entre o servidor VPN e o servidor NPS.

 ![Analisador de Mensagens da Microsoft](./media/nps-extension-vpn/image50.png)

Para obter mais informações, consulte [Integrar sua infraestrutura existente do NPS à Autenticação Multifator do Azure ](multi-factor-authentication-nps-extension.md).  

## <a name="next-steps"></a>Próximas etapas
[Como obter a Autenticação Multifator do Azure](multi-factor-authentication-versions-plans.md)

[Gateway de Área de Trabalho Remota e Servidor Azure Multi-Factor Authentication usando RADIUS](multi-factor-authentication-get-started-server-rdg.md)

[Integrar seus diretórios locais no Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md)

