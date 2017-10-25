---
title: "Integração do Gateway de Área de Trabalho Remota com a extensão do Azure MFA NPS | Microsoft Docs"
description: "Este artigo aborda a integração da infraestrutura do seu Gateway de Área de Trabalho Remota com a Autenticação Multifator do Azure usando a extensão do Servidor de Políticas de Rede (NPS) para o Microsoft Azure."
services: active-directory
keywords: "Azure MFA, integrar o Gateway de Área de Trabalho Remota, Azure Active Directory, extensão do Servidor de Políticas de Rede"
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
ms.openlocfilehash: 6ff9a341b31e5005949dcc0ecb2591060269846e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
#  <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrar a infraestrutura do seu Gateway de Área de Trabalho Remota usando a extensão do Servidor de Políticas de Rede (NPS) e o Azure AD

Este artigo fornece os detalhes para integração da infraestrutura do seu Gateway de Área de Trabalho Remota com a Autenticação Multifator do Azure (MFA) usando a extensão do Servidor de Políticas de Rede (NPS) para o Microsoft Azure. 

A extensão de Serviço de Políticas de Rede (NPS) para o Azure permite que os clientes protejam a autenticação do serviço RADIUS usando a [Autenticação Multifator do Azure (MFA)](multi-factor-authentication.md) baseada em nuvem. Essa solução fornece uma verificação em duas etapas para adicionar uma segunda camada de segurança para logons de usuário e transações.

Este artigo fornece instruções passo a passo para integrar a infraestrutura do NPS com a Autenticação Multifator do Azure usando a extensão do NPS do Azure. Isso permite uma verificação segura para os usuários que tentarem fazer logon em um Gateway de Área de Trabalho Remota. 

A Política de Rede e Serviços de Acesso (NPS) permite que as organizações façam o seguinte:
* Defina locais centrais para o gerenciamento e controle de solicitações de rede especificando quem pode se conectar, as horas do dia durante as quais as conexões são permitidas, a duração das conexões e o nível de segurança que os clientes devem usar para se conectar e assim por diante. Em vez de especificar essas políticas em cada servidor VPN ou Gateway de Área de Trabalho Remota (RD), essas políticas podem ser especificadas uma vez em um local central. O protocolo RADIUS fornece a Autenticação, Autorização e Contabilização (AAA) centralizadas. 
* Estabelecer e impor políticas de integridade do cliente de Proteção de Acesso à Rede (NAP) que determinam se os dispositivos têm acesso irrestrito ou restrito aos recursos de rede.
* Fornece um meio para impor a autenticação e autorização para acesso aos comutadores Ethernet e pontos de acesso sem fio compatíveis com 802.1x.    

Normalmente, as organizações usam o NPS (RADIUS) para simplificar e centralizar o gerenciamento de políticas de VPN. No entanto, muitas organizações também usam o NPS para simplificar e centralizar o gerenciamento das Políticas de Autorização de Conexão de Área de Trabalho da Área de Trabalho Remota (RD CAPs). 

As organizações também podem integrar o NPS com o Azure MFA para aumentar a segurança e fornecer um alto nível de conformidade. Isso ajuda a garantir que os usuários estabeleçam a verificação em duas etapas para fazer logon no Gateway de Área de Trabalho Remota. Para conceder acesso aos usuários, é necessário que eles forneçam sua combinação de nome de usuário e senha com as informações que o usuário tem em seu controle. Essas informações devem ser confiáveis e não facilmente duplicadas, como um número de telefone celular, o número de telefone fixo, o aplicativo em um dispositivo móvel e assim por diante.

Antes da disponibilidade da extensão do NPS do Azure, os clientes que desejavam implementar a verificação em duas etapas para ambientes integrados de NPS e o Azure MFA tinham que configurar e manter um servidor de MFA separado no ambiente local, conforme documentado no [Gateway de Área de Trabalho Remota e Servidor de Autenticação Multifator do Azure usando RADIUS](multi-factor-authentication-get-started-server-rdg.md).

A disponibilidade da extensão do NPS para o Azure agora dá às organizações a opção de implantar uma solução MFA local ou uma solução MFA baseada em nuvem para autenticação de cliente RADIUS segura.

## <a name="authentication-flow"></a>Fluxo de autenticação

Para os usuários terem acesso aos recursos de rede por meio de um Gateway de Área de Trabalho Remota, eles devem atender às condições especificadas na Política de Autorização de Conexão de Área de Trabalho Remota (RD CAP) e uma Política de Autorização de Recursos de Área de Trabalho Remota (RD RAP). As RD CAPs especificam quem possui autorização para conectar-se a Gateways de Área de Trabalho Remota. As RD RAPs especificam os recursos de rede, como áreas de trabalho remotas ou aplicativos remotos, aos quais o usuário tem permissão para se conectar através do Gateway de Área de Trabalho Remota. 

Um Gateway de Área de Trabalho Remota pode ser configurado para usar um repositório política central para RD CAPs. As RD RAPs não podem usar uma política central, pois elas são processadas no Gateway de Área de Trabalho Remota. Um exemplo de um Gateway de Área de Trabalho Remota configurada para usar um repositório de política central para RD CAPs é um cliente RADIUS para outro servidor NPS que serve como o repositório de políticas central.

Quando a extensão do NPS para o Azure é integrada com o NPS e o Gateway de Área de Trabalho Remota, um fluxo de autenticação correto funciona da seguinte maneira:

1. O servidor de Gateway de Área de Trabalho Remota recebe uma solicitação de autenticação de um usuário de área de trabalho remota para se conectar a um recurso, como uma sessão de Área de Trabalho Remota. O servidor de Gateway de Área de Trabalho Remota age como um cliente RADIUS e converte a solicitação para uma mensagem de solicitação de acesso RADIUS e envia a mensagem para o servidor RADIUS (NPS) onde a extensão do NPS está instalada. 
2. A combinação de nome de usuário e senha é verificada no Active Directory e o usuário é autenticado.
3. Se todas as condições conforme especificadas na Solicitação de Conexão de NPS e as Políticas de Rede forem atendidas (por exemplo, hora do dia ou restrições de associação a um grupo), a extensão NPS dispara uma solicitação de autenticação secundária com o Azure MFA. 
4. O Azure MFA comunica-se com o Azure AD, recupera os detalhes do usuário e executa a autenticação secundária usando um método de verificação configurado para o usuário (mensagem de texto, aplicativo móvel e assim por diante). 
5. Após o desafio de MFA ter sido concluído com sucesso, o Azure MFA comunica o resultado para a extensão do NPS.
6. O servidor NPS onde a extensão está instalada envia uma mensagem de aceitação de acesso RADIUS para a política de RD CAP ao servidor de Gateway de Área de Trabalho Remota.
7. O usuário tem acesso ao recurso de rede solicitado por meio do Gateway de Área de Trabalho Remota.

## <a name="prerequisites"></a>Pré-requisitos
Esta seção fornece detalhes sobre os pré-requisitos necessários para integrar o Azure MFA com o Gateway de Área de Trabalho Remota. Antes de iniciar, você deverá ter os seguintes pré-requisitos já preparados.  

* Infraestrutura de Serviços de Área de Trabalho Remota (RDS)
* Licença do Azure MFA
* Software do Windows Server
* Função de Serviços de Acesso (NPS) e Política de Rede
* Azure AD sincronizado com o AD local 
* ID do GUID do Azure Active Directory

### <a name="remote-desktop-services-rds-infrastructure"></a>Infraestrutura de Serviços de Área de Trabalho Remota (RDS)
Você deve ter uma infraestrutura de Serviços de Área de Trabalho Remota (RDS) em vigor. Se você não tiver, você pode criar essa infraestrutura rapidamente no Azure usando o seguinte modelo de início rápido: [Criar implantação da Coleção de Sessão de Área de Trabalho Remota](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment). 

Se você quiser criar manualmente uma infraestrutura de RDS local rapidamente para fins de teste, siga as etapas para implantar uma. 
**Saiba mais**: [Implantar RDS com início rápido do Azure](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) e [Implantação de infraestrutura de RDS básica](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure). 

### <a name="licenses"></a>Licenças
É necessário uma licença para o Azure MFA, que está disponível por meio de um Azure AD Premium, Enterprise Mobility + Security (EMS) ou uma assinatura de MFA. Para obter mais informações, consulte [Como obter Autenticação Multifator do Azure](multi-factor-authentication-versions-plans.md). Para fins de teste, você pode usar uma assinatura de avaliação.

### <a name="software"></a>Software
A extensão NPS requer o Windows Server 2008 R2 SP1 ou superior com o serviço de função NPS instalado. Todas as etapas nesta seção foram realizadas usando o Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Função de Serviços de Acesso (NPS) e Política de Rede
O serviço de função NPS fornece o servidor RADIUS e a funcionalidade do cliente, bem como o serviço de integridade de Política de Acesso de Rede. Essa função deve ser instalada em pelo menos dois computadores na sua infraestrutura: O Gateway de Área de Trabalho Remota e outro servidor membro ou controlador de domínio. Por padrão, a função já está presente no computador configurado como o Gateway de Área de Trabalho Remota.  Você deve também instalar a função NPS em pelo menos um outro computador, como um controlador de domínio ou servidor membro.

Para obter informações sobre como instalar a função do NPS do serviço Windows Server 2012 ou anterior, consulte [Instalar um Servidor de Política de Integridade de NAP](https://technet.microsoft.com/library/dd296890.aspx). Para obter uma descrição de melhores práticas recomendadas para NPS, incluindo a recomendação para instalar o NPS em um controlador de domínio, consulte [Práticas recomendadas para NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory sincronizado com o Active Directory local 
Para usar a extensão do NPS, os usuários locais devem ser sincronizados com o Azure AD e habilitados para MFA. Esta seção pressupõe que os usuários locais são sincronizados com o Azure AD usando o AD Connect. Para obter informações sobre o Azure AD Connect, consulte [Integrar seus diretórios locais com o Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>ID do GUID do Azure Active Directory
Para instalar o NPS, você precisa saber o GUID do Azure AD. As instruções para localizar o GUID do Azure AD são fornecidas abaixo.

## <a name="configure-multi-factor-authentication"></a>Configurar Autenticação Multifator 
Esta seção fornece instruções para integração do Azure MFA com o Gateway de Área de Trabalho Remota. Como administrador, você deve configurar o serviço Azure MFA antes que os usuários possam registrar seus dispositivos multifatores ou aplicativos.

Siga as etapas em [Guia de introdução à Autenticação Multifator do Azure na nuvem](multi-factor-authentication-get-started-cloud.md) para habilitar a MFA para os usuários do Azure AD. 

### <a name="configure-accounts-for-two-step-verification"></a>Configurar contas para verificação em duas etapas
Depois que uma conta tiver sido habilitada para MFA, você não pode entrar em recursos controlados pela política de MFA até que você tenha configurado com êxito um dispositivo confiável a ser usado para o segundo fator de autenticação que foi autenticado usando a verificação em duas etapas.

Siga as etapas em [O que a Autenticação Multifator do Azure significa para mim?](./end-user/multi-factor-authentication-end-user.md) para compreender e configurar corretamente os dispositivos para MFA com sua conta de usuário.

## <a name="install-and-configure-nps-extension"></a>Instalar e configurar a extensão do NPS
Esta seção fornece instruções para configurar a infraestrutura do RDS para usar o Azure MFA para autenticação de cliente com o Gateway de Área de Trabalho Remota.

### <a name="acquire-azure-active-directory-guid-id"></a>Obter a ID do GUID do Azure Active Directory

Como parte da configuração da extensão do NPS, você precisa fornecer credenciais de administrador e a ID do Azure AD para o seu locatário do Azure AD. As próximas etapas mostram como obter o ID do locatário.

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador global do locatário do Azure.
2. Na barra de navegação esquerda, selecione o ícone **Azure Active Directory**.
3. Selecione **Propriedades**.
4. Na folha Propriedades, ao lado de ID do diretório, clique no ícone **Copiar**, conforme mostrado abaixo, para copiar a ID para a área de transferência.

 ![Propriedades](./media/nps-extension-remote-desktop-gateway/image1.png)

### <a name="install-the-nps-extension"></a>Instalar a extensão NPS
Instale a extensão NPS em um servidor que tem a função de Serviços de Acesso (NPS) e Política de Rede instalada. Isso funciona como o servidor RADIUS no seu projeto. 

>[!Important]
> Certifique-se de que você não instalou a extensão NPS no servidor de Gateway de Área de Trabalho Remota.
> 

1. Instalar a [extensão NPS](https://aka.ms/npsmfa). 
2. Copie o arquivo de instalação executável (NpsExtnForAzureMfaInstaller.exe) para o servidor NPS.
3. No servidor NPS, clique duas vezes em **NpsExtnForAzureMfaInstaller.exe**. Se solicitado, clique em **Executar**.
4. Na extensão NPS para a caixa de diálogo de Azure MFA, revise os termos de licença de software, marque **Eu concordo com os termos e condições**e clique em **Instalar**.
 
  ![Instalação do Azure MFA](./media/nps-extension-remote-desktop-gateway/image2.png)

5. Na extensão NPS para a caixa de diálogo de Azure MFA, clique em Fechar. 

  ![Extensão NPS para o Azure MFA](./media/nps-extension-remote-desktop-gateway/image3.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Configurar certificados para uso com a extensão NPS usando um script do PowerShell
Em seguida, você precisa configurar certificados para uso pela extensão NPS para garantia e comunicações seguras. Os componentes NPS incluem um script do Windows PowerShell que configura um certificado autoassinado para uso com o NPS. 

O script executa as ações a seguir:

* Cria um certificado autoassinado
* Associa a chave pública do certificado à entidade de serviço no Azure AD
* Armazena o certificado no repositório do computador local
* Concede acesso à chave privada do certificado ao usuário de rede
* Reinicia o serviço do Servidor de Políticas de Rede

Se você quiser usar seus próprios certificados, você precisa associar o público do seu certificado à entidade de serviço no Azure AD e assim por diante.

Para usar o script, forneça a extensão com suas credenciais de administrador do Azure AD e a ID de locatário do Azure AD que você copiou anteriormente. Execute o script em cada servidor NPS onde você instalou a extensão NPS. Faremos o seguinte:

1. Abra um prompt do Windows PowerShell administrativo.
2. No prompt do PowerShell, digite **cd 'c:\Program Files\Microsoft\AzureMfa\Config'** e pressione **ENTER**.
3. Digite _.\AzureMfsNpsExtnConfigSetup.ps1_ e pressione **ENTER**. O script verifica se o módulo do Azure Active Directory PowerShell está instalado. Se não estiver instalado, o script instala o módulo para você.

  ![Azure AD PowerShell](./media/nps-extension-remote-desktop-gateway/image4.png)
  
4. Depois que o script verifica a instalação do módulo do PowerShell, ele exibe a caixa de diálogo de módulo do Azure Active Directory PowerShell. Na caixa de diálogo, insira suas credenciais de administrador do Azure AD e a senha e clique em **Entrar**.

  ![Abra a conta do PowerShell](./media/nps-extension-remote-desktop-gateway/image5.png)

5. Quando solicitado, cole a ID do locatário que você copiou para a área de transferência anteriormente e pressione **ENTER**.

  ![Inserir a ID do locatário](./media/nps-extension-remote-desktop-gateway/image6.png)

6. O script cria um certificado autoassinado e executa outras alterações de configuração. A saída deve ser como a imagem abaixo.

  ![Certificado autoassinado](./media/nps-extension-remote-desktop-gateway/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Configurar componentes NPS no Gateway de Área de Trabalho Remota
Nesta seção, você pode configurar as diretivas de autorização de conexão de Gateway de Área de Trabalho Remota e outras configurações de RADIUS.

O fluxo de autenticação requer que as mensagens RADIUS sejam trocadas entre o Gateway de Área de Trabalho Remota e o servidor NPS onde o servidor NPS está instalado. Isso significa que você deve configurar as configurações do cliente RADIUS no Gateway de Área de Trabalho Remota e no servidor NPS onde a extensão NPS está instalada. 

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Configurar políticas de autorização de conexão de Gateway de Área de Trabalho Remota para usar o repositório central
As políticas de autorização de conexão de Área de Trabalho Remota (RD CAPs) especificam os requisitos para se conectar ao servidor de Gateway de Área de Trabalho Remota. As RD CAPs podem ser armazenadas localmente (padrão) ou podem ser armazenadas em um repositório de RD CAP central que está executando o NPS. Para configurar a integração do Azure MFA com RDS, você precisa especificar o uso de um repositório central.

1. No servidor de Gateway de Área de Trabalho Remota, abra **Gerenciador do Servidor**. 
2. No menu, clique em **Ferramentas**, aponte para **Serviços de Área de Trabalho Remota** e, em seguida, clique em **Gerenciador de Gateway de Área de Trabalho Remota**.

  ![Serviços da Área de Trabalho Remota](./media/nps-extension-remote-desktop-gateway/image8.png)

3. No Gerenciador de Gateway de Área de Trabalho Remota, clique com o botão direito em **\[Nome do servidor\] (Local)** e clique em **Propriedades**.

  ![Nome do Servidor](./media/nps-extension-remote-desktop-gateway/image9.png)

4. Na caixa de diálogo Propriedades, selecione a guia do Repositório **RD CAP**.
5. Acesse a guia Repositório RD CAP, selecione **Servidor central executando NPS**. 
6. No campo **Insira um nome ou endereço IP do servidor que executa o NPS**, digite o endereço IP ou o nome do servidor onde você instalou a extensão NPS.

  ![Insira o Nome ou o Endereço IP](./media/nps-extension-remote-desktop-gateway/image10.png)
  
7. Clique em **Adicionar**.
8. Na caixa de diálogo **Segredo compartilhado**, digite um segredo compartilhado e, em seguida, clique em **OK**. Certifique-se de registrar esse segredo compartilhado e armazene o registro de forma segura.

 >[!NOTE]
 >O segredo compartilhado é usado para estabelecer confiança entre os clientes e os servidores RADIUS. Crie uma senha longa e complexa.
 >

 ![Segredo compartilhado](./media/nps-extension-remote-desktop-gateway/image11.png)

9. Clique em **OK** para fechar a caixa de diálogo.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Configurar o valor de tempo limite do RADIUS no NPS de Gateway de Área de Trabalho Remota
Para garantir que haja tempo para validar as credenciais do usuário, executar a verificação em duas etapas, receber respostas e responder a mensagens RADIUS, é necessário ajustar o valor de tempo limite do RADIUS.

1. No servidor de Gateway de Área de Trabalho Remota, no Gerenciador do Servidor, clique em **Ferramentas**e, em seguida, clique em **Servidor de Políticas de Rede**. 
2. No console **NPS (Local)**, expanda **Clientes e Servidores RADIUS** e selecione **Servidor RADIUS remoto**.

 ![Servidor RADIUS remoto](./media/nps-extension-remote-desktop-gateway/image12.png)

3. No painel de detalhes, clique duas vezes em **GRUPO DE SERVIDORES GATEWAY TS**.

 >[!NOTE]
 >Este grupo de servidores RADIUS foi criado quando você configurou o servidor central para políticas NPS. O Gateway de Área de Trabalho Remota encaminha mensagens RADIUS para este servidor ou grupo de servidores, se houver mais de um no grupo.
 >

4. Na caixa de diálogo **Propriedades de GRUPO DE SERVIDORES GATEWAY TS**, selecione o endereço IP ou nome do servidor NPS configurado para armazenar RD CAPs e, em seguida, clique em **Editar**. 

 ![Grupo de servidores Gateway TS](./media/nps-extension-remote-desktop-gateway/image13.png)

5. Na caixa de diálogo **Editar Servidor RADIUS**, selecione a guia **Balanceamento de Carga**.
6. Na guia **Balanceamento de Carga** no campo **Número de segundos sem resposta antes que uma solicitação seja descartada**, altere o valor padrão de 3 para um valor entre 30 e 60 segundos.
7. No campo **Número de segundos entre as solicitações quando o servidor é identificado como indisponível**, altere o valor padrão de 30 segundos para um valor que seja igual ou maior que o valor especificado na etapa anterior.

 ![Editar servidor RADIUS](./media/nps-extension-remote-desktop-gateway/image14.png)

8.  Clique em OK duas vezes para fechar a caixa de diálogo.

### <a name="verify-connection-request-policies"></a>Verifique as Políticas de Solicitação de Conexão 
Por padrão, quando você configurar o Gateway de Área de Trabalho Remota para usar um repositório central de política para políticas de autorização de conexão, o Gateway de Área de Trabalho Remota está configurado para encaminhar solicitações de CAP para o servidor NPS. O servidor NPS com a extensão Azure MFA instalada, processa a solicitação de acesso RADIUS. As etapas a seguir mostram como verificar a política de solicitação de conexão padrão. 

1. No Gateway de Área de Trabalho Remota, no console do NPS (Local), expanda **Políticas** e selecione **Políticas de Solicitação de Conexão**.
2. Clique com o botão direito em **Políticas de Solicitação de Conexão** e clique duas vezes em **POLÍTICA DE AUTORIZAÇÃO DE GATEWAY TS**.
3. Na caixa de diálogo **Propriedades da POLÍTICA DE AUTORIZAÇÃO DE GATEWAY TS**, clique na guia **Configurações**.
4. Na guia **Configurações**, em Encaminhamento de Solicitação de Conexão, clique em **Autenticação**. O cliente RADIUS está configurado para encaminhar solicitações para autenticação.

 ![Configurações de Autenticação](./media/nps-extension-remote-desktop-gateway/image15.png)
 
5. Clique em **Cancelar**. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Configurar o NPS no servidor onde a extensão NPS está instalada
O servidor NPS onde a extensão NPS está instalada deve ser capaz de trocar mensagens RADIUS com o servidor NPS no Gateway de Área de Trabalho Remota. Para habilitar a troca de mensagens, você precisa configurar os componentes do NPS no servidor onde o serviço de extensão NPS está instalado. 

### <a name="register-server-in-active-directory"></a>Registrar o Servidor no Active Directory
Para funcionar corretamente nesse cenário, o servidor NPS deve ser registrado no Active Directory.

1. Abra o **Gerenciador do Servidor**.
2. No Gerenciador do Servidor, clique em **Ferramentas**e, em seguida, clique em **Servidor de Políticas de Rede**. 
3. No console do Servidor de Políticas de Rede, clique com o botão direito em **NPS (Local)** e, em seguida, clique em **Registrar servidor no Active Directory**. 
4. Clique em **OK** duas vezes.

 ![Registrar servidor no AD](./media/nps-extension-remote-desktop-gateway/image16.png)

5. Deixe o console aberto para o próximo procedimento.

### <a name="create-and-configure-radius-client"></a>Criar e configurar o cliente RADIUS 
O Gateway de Área de Trabalho Remota deve ser configurado como um cliente RADIUS para o servidor NPS. 

1. No servidor NPS onde a extensão NPS estiver instalada, no console **NPS (Local)**, clique com botão direito em **Clientes RADIUS** e clique em **Novo**.

 ![Novos clientes RADIUS](./media/nps-extension-remote-desktop-gateway/image17.png)

2. Na caixa de diálogo **Novo cliente RADIUS**, forneça um nome amigável, como _Gateway_e o endereço IP ou nome DNS do servidor de Gateway de Área de Trabalho Remota. 
3. Nos campos **Segredo compartilhado** e **Confirmar segredo compartilhado**, insira o mesmo segredo usado anteriormente.

 ![Nome e Endereço](./media/nps-extension-remote-desktop-gateway/image18.png)

4. Clique em **OK** para fechar a caixa de diálogo de Novo cliente RADIUS.

### <a name="configure-network-policy"></a>Configurar Política de Rede
Lembre-se de que o servidor NPS com a extensão Azure MFA é o repositório de política central designado para a Política de Autorização de Conexão (CAP). Portanto, você precisa implementar uma CAP no servidor NPS para autorizar solicitações de conexão válidas.  

1. No console do NPS (Local), expanda **Políticas** e clique em **Políticas de Rede**.
2. Clique com botão direito em **Conexões com outros servidores de acesso** e clique em **Duplicar política**. 

 ![Duplicar Política](./media/nps-extension-remote-desktop-gateway/image19.png)

3. Clique com o botão direito em **Cópia de Conexões para outros servidores de acesso** e clique em **Propriedades**.

 ![Propriedades da Rede](./media/nps-extension-remote-desktop-gateway/image20.png)

4. Na caixa de diálogo **Cópia de Conexões para outros servidores de acesso**, no Nome da Política, insira um nome adequado, como **RDG_CAP**. Marque **Política Habilitada** e selecione **Conceder acesso**. Opcionalmente, em Tipo de acesso à rede, selecione **Gateway de Área de Trabalho Remota**, ou você pode deixá-lo como **Não especificado**.

 ![Cópia de Conexões](./media/nps-extension-remote-desktop-gateway/image21.png)

5.  Clique na guia **Restrições** e marque **Permitem que os clientes se conectem sem negociar um método de autenticação**.

 ![Permitir que os clientes se conectem](./media/nps-extension-remote-desktop-gateway/image22.png)

6. Opcionalmente, clique na guia **Condições** e adicione as condições que devem ser atendidas para que a conexão seja autorizada, por exemplo, associação em um grupo específico do Windows.

 ![Condições](./media/nps-extension-remote-desktop-gateway/image23.png)

7. Clique em **OK**. Quando solicitado para exibir o tópico da Ajuda correspondente, clique em **Não**.
8. Certifique-se de que a nova política está no topo da lista, que a política está habilitada, e que ela concede acesso.

 ![Políticas de Rede](./media/nps-extension-remote-desktop-gateway/image24.png)

## <a name="verify-configuration"></a>Verificar a configuração
Para verificar a configuração, você precisa fazer logon no Gateway de Área de Trabalho Remota com um cliente RDP adequado. Certifique-se de usar uma conta que é permitida por suas Políticas de Autorização de Conexão e está habilitada para o Azure MFA. 

Você pode usar a página **Acesso via Web à Área de Trabalho Remota**, conforme mostrado abaixo.

![Acesso via Web da Área de Trabalho Remota](./media/nps-extension-remote-desktop-gateway/image25.png)

Ao inserir corretamente as suas credenciais para autenticação primária, a caixa de diálogo Conexão de Área de Trabalho Remota mostra o status de Iniciando conexão remota, conforme mostrado abaixo. 

Se você autenticar com sucesso o método de autenticação secundário que você configurou anteriormente no Azure MFA, você está conectado ao recurso. No entanto, se a autenticação secundária não for bem-sucedida, seu acesso aos recursos será negado. 

![Iniciar conexão remota](./media/nps-extension-remote-desktop-gateway/image26.png)

No exemplo a seguir, o aplicativo do Autenticador em um Windows Phone é usado para fornecer a autenticação secundária.

![Contas](./media/nps-extension-remote-desktop-gateway/image27.png)

Depois de autenticado com sucesso usando o método de autenticação secundária, você é conectado ao Gateway de Área de Trabalho Remota normalmente. No entanto, como você deve usar um método de autenticação secundária usando um aplicativo móvel em um dispositivo confiável, o processo para conectar-se é mais seguro dessa forma quando comparado com outras opções.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Exibir logs do Visualizador de Eventos para eventos de logon com sucesso
Para exibir os eventos de logon com sucesso nos logs do Visualizador de Eventos do Windows, você pode emitir o seguinte comando do Windows PowerShell para consultar os logs de Serviços de Terminal do Windows e a Segurança do Windows.

Para consultar eventos de entrada bem-sucedida nos logs operacionais do Gateway _(Event Viewer\Applications and Services Logs\Microsoft\Windows\TerminalServices-Gateway\Operational_, use os seguintes comandos:

* _Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational_ | onde {$_.ID -eq '300'} | FL 
* Este comando exibe os eventos do Windows que mostram que o usuário atende aos requisitos da política de autorização de recursos (RD RAPS) e foi concedido acesso.

![Exibir Visualizador de Eventos](./media/nps-extension-remote-desktop-gateway/image28.png)

* _Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational_ | onde {$_.ID -eq '200'} | FL
* Este comando exibe os eventos que aparecem quando o usuário atende aos requisitos de política de autorização de conexão.

![Autorização de conexão](./media/nps-extension-remote-desktop-gateway/image29.png)

Você também pode exibir esse log e o filtro nas IDs de evento, 300 e 200. Para consultar eventos de logon com sucesso nos logs de Visualizador de eventos de segurança, use o seguinte comando:

* _Get-WinEvent -Logname Security_ | onde {$_.ID -eq '6272'} | FL 
* Este comando pode ser executado no NPS central ou no Servidor de Gateway de Área de Trabalho Remota. 

![Eventos de logon com sucesso](./media/nps-extension-remote-desktop-gateway/image30.png)

Você também pode exibir o log de segurança ou o modo de exibição personalizado de Serviços de Acesso e Política de Rede, conforme mostrado abaixo:

![Serviços de Acesso (NPS) e Política de Rede](./media/nps-extension-remote-desktop-gateway/image31.png)

No servidor onde você instalou a extensão NPS para o Azure MFA, você pode encontrar os logs de aplicativo do Visualizador de Eventos específicas para a extensão em _Application and Services Logs\Microsoft\AzureMfa_. 

![Logs de aplicativo do Visualizador de Eventos](./media/nps-extension-remote-desktop-gateway/image32.png)

## <a name="troubleshoot-guide"></a>Guia de Solução de Problemas

Se a configuração não estiver funcionando conforme o esperado, o primeiro lugar para iniciar a solução de problemas é verificar se o usuário está configurado para usar o Azure MFA. Peça ao usuário para conectar-se ao [Portal do Azure](https://portal.azure.com). Se os usuários são solicitados para verificação secundária e são autenticados com sucesso, você pode eliminar uma configuração incorreta do Azure MFA.

Se o Azure MFA está funcionando para o(s) usuário(s), você deve analisar os logs de eventos relevantes. Isso inclui os Eventos de Segurança, Gateway operacional e logs do Azure MFA que foram discutidos na seção anterior. 

Abaixo está um exemplo de saída do log de segurança mostrando um evento de logon com falha (Evento ID 6273).

![Eventos de logon com falha](./media/nps-extension-remote-desktop-gateway/image33.png)

Abaixo está um evento relacionado dos logs do AzureMFA:

![Log do Azure MFA](./media/nps-extension-remote-desktop-gateway/image34.png)

Para executar opções de solução de problemas avançadas, consulte os arquivos de log de formato do banco de dados NPS onde o serviço NPS está instalado. Esses arquivos de log são criados na pasta _%SystemRoot%\System32\Logs_ como arquivos de texto separado por vírgula. 

Para obter uma descrição desses arquivos de log, consulte [Interpretar arquivos de log de formato de banco de dados de NPS](https://technet.microsoft.com/library/cc771748.aspx). As entradas nesses arquivos de log podem ser difícil de interpretar sem importá-los para uma planilha ou um banco de dados. Você pode encontrar vários analisadores de IAS on-line para ajudá-lo a interpretar os arquivos de log. 

A imagem abaixo mostra a saída de um desses [aplicativos shareware](http://www.deepsoftware.com/iasviewer) que pode ser baixado. 

![Aplicativo shareware](./media/nps-extension-remote-desktop-gateway/image35.png)

E, por último, para mais opções de solução de problemas, você pode usar um analisador de protocolo, como o [Analisador de Mensagens da Microsoft](https://technet.microsoft.com/library/jj649776.aspx). 

A imagem abaixo do Analisador de Mensagens da Microsoft mostra o tráfego de rede filtrado no protocolo RADIUS que contém o nome de usuário **Contoso\AliceC**.

![Analisador de Mensagens da Microsoft](./media/nps-extension-remote-desktop-gateway/image36.png)

## <a name="next-steps"></a>Próximas etapas
[Como obter a Autenticação Multifator do Azure](multi-factor-authentication-versions-plans.md)

[Gateway de Área de Trabalho Remota e Servidor Azure Multi-Factor Authentication usando RADIUS](multi-factor-authentication-get-started-server-rdg.md)

[Integrar seus diretórios locais no Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md)
