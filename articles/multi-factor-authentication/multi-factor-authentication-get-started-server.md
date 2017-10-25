---
title: "Introdução ao Servidor de Autenticação Multifator do Azure | Microsoft Docs"
description: "Esta é a página de autenticação da Autenticação Multifator que descreve como começar com o Servidor Azure MFA."
services: multi-factor-authentication
keywords: "servidor de autenticação, página de ativação de aplicativo de autenticação multifator do azure, download de servidor de autenticação"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: e94120e4-ed77-44b8-84e4-1c5f7e186a6b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/30/2017
ms.author: joflore
ms.reviewer: alexwe
ms.custom: it-pro
ms.openlocfilehash: df847c370817c0702163b5e22c35c7e4f1d3cfee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Introdução ao Servidor de Autenticação Multifator do Azure

<center>![MFA local](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Agora que determinamos que o Servidor de Autenticação Multifator do Azure local será utilizado, vamos continuar. Esta página aborda uma nova instalação do servidor e sua configuração com o Active Directory local. Se você já tiver o servidor MFA instalado e quiser atualizar, consulte [Atualizar para o Servidor de Autenticação Multifator do Azure mais recente](multi-factor-authentication-server-upgrade.md). Se você estiver procurando informações sobre como instalar apenas o serviço Web, confira [Implantar o serviço Web de aplicativo móvel do Servidor de Autenticação Multifator](multi-factor-authentication-get-started-server-webservice.md).

## <a name="plan-your-deployment"></a>Planejar sua implantação

Antes de baixar o Servidor de Autenticação Multifator do Azure, pense em quais são seus requisitos de alta disponibilidade e carga. Use essas informações para decidir como e onde implantar.

Uma boa diretriz para a quantidade de memória necessária é o número de usuários que devem autenticar regularmente.

| Usuários | RAM |
| ----- | --- |
| 1-10,000 | 4 GB |
| 10,001-50,000 | 8 GB |
| 50,001-100,000 | 12 GB |
| 100,000-200,001 | 16 GB |
| 200,001+ | 32 GB |

Você precisa configurar vários servidores para alta disponibilidade ou balanceamento de carga? Há várias maneiras de definir essa configuração com o servidor MFA do Azure. Quando você instala seu primeiro servidor MFA do Azure, ele se torna o mestre. Outros servidores adicionais se tornam subordinados e sincronizam automaticamente os usuários e a configuração com o mestre. Em seguida, você pode configurar um servidor primário e ter o resto atuando como backup, ou pode configurar o balanceamento de carga entre todos os servidores.

Quando um servidor MFA do Azure mestre fica offline, os servidores subordinados ainda podem processar solicitações de verificação em duas etapas. No entanto, você não pode adicionar novos usuários e os usuários existentes não podem atualizar suas configurações até que o mestre fique novamente online ou um subordinado seja promovido.

### <a name="prepare-your-environment"></a>Prepare o seu ambiente

Verifique se o servidor que você está usando para a Autenticação Multifator do Azure atende aos seguintes requisitos:

| Requisitos do Servidor de Autenticação Multifator do Azure | Descrição |
|:--- |:--- |
| Hardware |<li>200 MB de espaço em disco rígido</li><li>processador compatível com x32 ou x64</li><li>1 GB ou mais de RAM</li> |
| Software |<li>Windows Server 2016</li><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2</li><li>Windows Server 2008 SP1, SP2</li><li>Windows Server 2003 R2</li><li>Windows Server 2003 SP1, SP2</li><li>Windows 10</li><li>Windows 8.1, todas as edições</li><li>Windows 8, todas as edições</li><li>Windows 7, todas as edições</li><li>Windows Vista, todas as edições, SP1, SP2</li><li>Microsoft .NET 4.0 Framework</li><li>IIS 7.0 ou superior se estiver instalando o portal do usuário ou o SDK do serviço Web</li> |

### <a name="azure-mfa-server-components"></a>Componentes do servidor MFA do Azure

Há três componentes Web que perfazem o servidor MFA do Azure:

* SDK do serviço Web: permite a comunicação com outros componentes e é instalado no servidor de aplicativos do MFA do Azure
* Portal do Usuário: é um site do IIS que permite aos usuários se registrar na MFA (Autenticação Multifator do Azure) e manter suas contas.
* Serviço de aplicativo Web móvel: permite usar um aplicativo móvel como o aplicativo Microsoft Authenticator para verificação em duas etapas.

Todos os três componentes podem ser instalados no mesmo servidor se ele estiver voltado para a Internet. Na divisão dos componentes, o SDK do serviço Web está instalado no servidor de aplicativos do MFA do Azure e o portal do usuário e o Serviço Web dos Aplicativos Móveis estão instalados em um servidor voltado para a Internet.

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Requisitos de firewall do Servidor de Autenticação Multifator do Azure

Cada servidor MFA deve ser capaz de se comunicar na porta 443 de saída para os seguintes endereços:

* https://pfd.phonefactor.net
* https://pfd2.phonefactor.net
* https://css.phonefactor.net

Se os firewalls de saída forem restritos na porta 443, abra os seguintes intervalos de endereços IP:

| Subrede de IP | Máscara de rede | Intervalo IP |
|:---: |:---: |:---: |
| 134.170.116.0/25 |255.255.255.128 |134.170.116.1 – 134.170.116.126 |
| 134.170.165.0/25 |255.255.255.128 |134.170.165.1 – 134.170.165.126 |
| 70.37.154.128/25 |255.255.255.128 |70.37.154.129 – 70.37.154.254 |

Se você não estiver usando o recurso de Confirmação de Eventos e os usuários não estiverem usando aplicativos móveis para verificar com dispositivos na rede corporativa, só precisará dos seguintes intervalos:

| Subrede de IP | Máscara de rede | Intervalo IP |
|:---: |:---: |:---: |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72 – 134.170.116.79 |
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72 – 134.170.165.79 |
| 70.37.154.200/29 |255.255.255.248 |70.37.154.201 – 70.37.154.206 |

## <a name="download-the-azure-multi-factor-authentication-server"></a>Baixar o Servidor de Autenticação Multifator do Azure

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Selecione **Active Directory**à esquerda
3. Clique em **Usuários e grupos**
4. Clique em **Todos os usuários**
5. Clique em **Autenticação Multifator**
6. Na seção **autenticação multifator**, selecione **configurações de serviço**

   ![Página de configurações do serviço](./media/multi-factor-authentication-get-started-server/servicesettings.png)

6. Na página das configurações de serviços, na parte inferior da tela, clique em **Ir para o portal**. Uma nova página é aberta.
7. Clique em **Downloads**.
8. Clique no link **Baixar** e salve o instalador.

   ![Baixar o Servidor MFA](./media/multi-factor-authentication-get-started-server/download4.png)

9. Mantenha essa página aberta, pois vamos referenciá-la depois de executar o instalador.

## <a name="install-and-configure-the-azure-multi-factor-authentication-server"></a>Instalar e configurar o Servidor de Autenticação Multifator do Azure

Agora que já baixou o servidor, você pode instalá-lo e configurá-lo. Verifique se o servidor em que você está instalando atende aos requisitos listados na seção de planejamento.

1. Clique duas vezes no arquivo executável.
2. Na tela Selecionar Pasta de Instalação, certifique-se de que a pasta esteja correta e clique em **Avançar**.
3. Quando a instalação for concluída, clique em **Concluir**.  Isso inicia o assistente de configuração.
4. Na tela de boas-vindas do assistente de configuração, marque **Ignorar o uso do Assistente de configuração de autenticação** e clique em **Avançar**.  O assistente fecha e o servidor é iniciado.

   ![Nuvem](./media/multi-factor-authentication-get-started-server/skip2.png)

5. De volta à página de onde baixamos o servidor, clique no botão **Gerar Credenciais de Ativação** . Copie essas informações no Servidor Azure MFA nas caixas fornecidas e clique em **Ativar**.

## <a name="send-users-an-email"></a>Enviar um email aos usuários

Para facilitar a distribuição, permita que o Servidor MFA se comunique com seus usuários. O Servidor MFA poderá enviar um email para informá-los se eles forem registrados para verificação em duas etapas.

O email a ser enviado deve ser determinado pelo modo como você configura seus usuários para a verificação em duas etapas. Por exemplo, se você puder importar os números de telefone do diretório da empresa, o email deverá incluir os números de telefone padrão para que os usuários saibam o que esperar. Se você não importar os números de telefone, ou seus usuários forem usar o aplicativo móvel, envie um email que os direcione à conclusão do registro da conta. Inclua um hiperlink para o Portal do Usuário da Autenticação Multifator do Azure no email.

O conteúdo do email vai variar de acordo com o método de autenticação que foi definido para o usuário (ligação telefônica, SMS ou aplicativo móvel).  Por exemplo, se o usuário tiver que usar um PIN quando for se autenticar, o email informará qual PIN inicial foi definido para ele.  Os usuários são solicitados a mudar o PIN na primeira autenticação.

### <a name="configure-email-and-email-templates"></a>Configurar o email e os modelos de email

Clique no ícone de email à esquerda para definir as configurações para enviar esses emails. Nesta página você pode inserir as informações SMTP do seu servidor de email e enviar um email marcando a caixa de seleção **Enviar emails aos usuários**.

![Configuração de email do Servidor MFA](./media/multi-factor-authentication-get-started-server/email1.png)

Na guia Conteúdo do Email, você verá os diversos modelos de email disponíveis para sua escolha. Dependendo de como você configurou os usuários para usar a autenticação de dois fatores, é possível escolher o modelo mais adequado para as suas necessidades.

![Modelos de email do Servidor MFA](./media/multi-factor-authentication-get-started-server/email2.png)

## <a name="import-users-from-active-directory"></a>Importar usuários do Active Directory

Agora que o servidor está instalado, você deve adicionar usuários. Você pode escolher criá-los manualmente, importar usuários do Active Directory ou configurar a sincronização automática com o Active Directory.

### <a name="manual-import-from-active-directory"></a>Importar do Active Directory manualmente

1. No Servidor Azure MFA, à esquerda, selecione **Usuários**.
2. Na parte inferior, selecione **Importar do Active Directory**.
3. Agora, você pode procurar por usuários individuais ou buscar no diretório do AD UOs que tenham usuários.  Nesse caso, nós especificaremos o UO de usuários.
4. Realce todos os usuários à direita e clique em **Importar**.  Você deve receber uma mensagem informando que obteve êxito.  Feche a janela de importação.

   ![Importação de usuários do Servidor MFA](./media/multi-factor-authentication-get-started-server/import2.png)

### <a name="automated-synchronization-with-active-directory"></a>Sincronização automática com o Active Directory

1. No Servidor MFA do Azure, à esquerda, selecione **Integração de Diretórios**.
2. Navegue até a guia **Sincronização**.
3. Na parte inferior, escolha **Adicionar**
4. Na caixa **Adicionar Item de sincronização** que aparece, escolha o domínio, OU **ou** grupo de segurança, configurações, padrões de método e idioma padrão para essa tarefa de sincronização e clique em **Adicionar** .
5. Marque a caixa denominada **Habilitar a sincronização com o Active Directory** e escolha um **intervalo de sincronização** entre um minuto e 24 horas.

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Como o Servidor de Autenticação Multifator do Azure lida com os dados do usuário

Quando você usa o servidor MFA (Autenticação Multifator) local, os dados do usuário são armazenados nos servidores locais. Nenhum dado de usuário persistente é armazenado na nuvem. Quando o usuário executa uma autenticação de dois fatores, o servidor MFA envia dados para o serviço de nuvem do Azure MFA para realizar a autenticação. Quando essas solicitações de autenticação são enviadas ao serviço de nuvem, os campos a seguir são enviados na solicitação e nos logs para que eles fiquem disponíveis em relatórios de uso/autenticação do cliente. Alguns dos campos são opcionais; portanto, podem ser habilitados ou desabilitados no Servidor de Autenticação Multifator. A comunicação do servidor MFA para o serviço de nuvem MFA usa saída SSL/TLS pela porta 443. Esses campos são:

* ID exclusiva: nome de usuário ou ID interna do servidor MFA
* Nome e sobrenome (opcional)
* Endereço de email (opcional)
* Número de telefone - ao fazer uma chamada de voz ou SMS de autenticação
* Token de dispositivo: ao fazer autenticação de aplicativos móveis
* Modo de autenticação
* Resultado da autenticação
* Nome do servidor MFA
* Servidor IP MFA
* Cliente IP: se disponível

Além dos campos acima, o resultado da autenticação (êxito/negação) e o motivo de uma possível recusa também são armazenados com os dados de autenticação e ficam disponíveis por meio de relatórios de autenticação/uso.

## <a name="back-up-and-restore-azure-mfa-server"></a>Fazer backup e restaurar o Servidor MFA do Azure

Ter certeza de que você tem um bom backup é uma etapa importante em qualquer sistema.

Para fazer backup do Servidor MFA do Azure, verifique se você tem uma cópia da pasta **C:\Arquivos de Programas\Servidor de Autenticação Multifator\Dados.**, incluindo o arquivo **PhoneFactor.pfdata**. 

Caso seja necessário fazer uma restauração, conclua as seguintes etapas:

1. Reinstale o Servidor MFA do Azure em um novo servidor.
2. Ative o novo servidor MFA do Azure.
3. Pare o serviço **MultiFactorAuth**.
4. Substitua o **PhoneFactor.pfdata** pela cópia de backup.
5. Inicie o serviço **MultiFactorAuth**.

O novo servidor está agora configurado e em execução com a configuração e os usuário de backup originais.

## <a name="next-steps"></a>Próximas etapas

- Instale e configure o [Portal do Usuário](multi-factor-authentication-get-started-portal.md) para o autoatendimento.
- Instale e configure o Servidor de MFA do Azure com o [Serviço de Federação do Active Directory](multi-factor-authentication-get-started-adfs.md), [Autenticação RADIUS](multi-factor-authentication-get-started-server-radius.md) ou [Autenticação LDAP](multi-factor-authentication-get-started-server-ldap.md).
- Instale e configure o [Gateway de Área de Trabalho Remota e o Servidor de Autenticação Multifator do Azure usando RADIUS](multi-factor-authentication-get-started-server-rdg.md).
- [Implante o serviço Web do aplicativo móvel do Servidor de Autenticação Multifator do Azure](multi-factor-authentication-get-started-server-webservice.md).
- [Cenários avançados com a Autenticação Multifator do Azure e VPNs de terceiros](multi-factor-authentication-advanced-vpn-configurations.md).
