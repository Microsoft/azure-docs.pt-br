---
title: Introdução ao servidor do Azure MFA-Azure Active Directory
description: Introdução passo a passo do servidor Azure MFA no local
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
ms.openlocfilehash: d7bd9913cccbe077a4deed9a7c5bfdc601f3dd5e
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742333"
---
# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Introdução ao Servidor de Autenticação Multifator do Azure

<center>

![Introdução ao servidor MFA local](./media/howto-mfaserver-deploy/server2.png)</center>

Esta página aborda uma nova instalação do servidor e sua configuração com o Active Directory local. Se você já tiver o servidor MFA instalado e quiser atualizar, consulte [Atualizar para o Servidor de Autenticação Multifator do Azure mais recente](howto-mfaserver-deploy-upgrade.md). Se você estiver procurando informações sobre como instalar apenas o serviço Web, confira [Implantar o serviço Web de aplicativo móvel do Servidor de Autenticação Multifator](howto-mfaserver-deploy-mobileapp.md).

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferece mais o servidor MFA para novas implantações. Novos clientes que desejam exigir a MFA (autenticação multifator) durante eventos de entrada devem usar a autenticação multifator do Azure AD baseada em nuvem.
>
> Para começar a usar a MFA baseada em nuvem, consulte [tutorial: proteger eventos de entrada de usuário com a autenticação multifator do Azure ad](tutorial-enable-azure-mfa.md).
>
> Os clientes existentes que ativaram o servidor MFA antes de 1º de julho de 2019 podem baixar a versão mais recente, atualizações futuras e gerar credenciais de ativação como de costume.

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
| Software |<li>Windows Server 2016</li><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008/R2 (somente com [ESU](/lifecycle/faq/extended-security-updates) )</li><li>Windows 10</li><li>Windows 8.1, todas as edições</li><li>Windows 8, todas as edições</li><li>Windows 7, todas as edições (somente com [ESU](/lifecycle/faq/extended-security-updates) )</li><li>Microsoft .NET 4.0 Framework</li><li>IIS 7.0 ou superior se estiver instalando o portal do usuário ou o SDK do serviço Web</li> |
| Permissões | Conta de Administrador Corporativo ou de Administrador de Domínio para registrar-se no Active Directory |

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
| 70.37.154.128/25 |255.255.255.128 |70.37.154.129 – 70.37.154.254   |
| 52.251.8.48/28   | 255.255.255.240 | 52.251.8.48 - 52.251.8.63     |
| 52.247.73.160/28 | 255.255.255.240 | 52.247.73.160 - 52.247.73.175 |
| 52.159.5.240/28  | 255.255.255.240 | 52.159.5.240 - 52.159.5.255   |
| 52.159.7.16/28   | 255.255.255.240 | 52.159.7.16 - 52.159.7.31     |
| 52.250.84.176/28 | 255.255.255.240 | 52.250.84.176 - 52.250.84.191 |
| 52.250.85.96/28  | 255.255.255.240 | 52.250.85.96 - 52.250.85.111  |

Se você não estiver usando o recurso de Confirmação de Eventos e os usuários não estiverem usando aplicativos móveis para verificar com dispositivos na rede corporativa, só precisará dos seguintes intervalos:

| Subrede de IP | Máscara de rede | Intervalo IP |
|:---: |:---: |:---: |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72 – 134.170.116.79|
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72 – 134.170.165.79|
| 70.37.154.200/29 |255.255.255.248  |70.37.154.201 – 70.37.154.206  |
| 52.251.8.48/28   | 255.255.255.240 | 52.251.8.48 - 52.251.8.63     |
| 52.247.73.160/28 | 255.255.255.240 | 52.247.73.160 - 52.247.73.175 |
| 52.159.5.240/28  | 255.255.255.240 | 52.159.5.240 - 52.159.5.255   |
| 52.159.7.16/28   | 255.255.255.240 | 52.159.7.16 - 52.159.7.31     |
| 52.250.84.176/28 | 255.255.255.240 | 52.250.84.176 - 52.250.84.191 |
| 52.250.85.96/28  | 255.255.255.240 | 52.250.85.96 - 52.250.85.111  |

## <a name="download-the-mfa-server"></a>Baixar o Servidor MFA

Siga estas etapas para baixar o Servidor de Autenticação Multifator do Azure do portal do Azure:

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferece mais o servidor MFA para novas implantações. Novos clientes que desejarem exigir a MFA (autenticação multifator) de seus usuários devem usar a autenticação multifator do Azure AD baseada em nuvem.
>
> Para começar a usar a MFA baseada em nuvem, consulte [tutorial: proteger eventos de entrada de usuário com a autenticação multifator do Azure ad](tutorial-enable-azure-mfa.md).
>
> Os clientes existentes que ativaram o servidor MFA antes de 1º de julho de 2019 podem baixar a versão mais recente, atualizações futuras e gerar credenciais de ativação como de costume. As etapas a seguir funcionarão apenas se você for um cliente do servidor MFA existente.

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Pesquise *Azure Active Directory* e selecione-o. Selecione **Security**  >  **MFA** de segurança.
3. Em **Gerenciador servidor MFA**, selecione **configurações do servidor**.
4. Selecione **Baixar** e siga as instruções na página de downloads para salvar o instalador. 

   ![Baixar o servidor MFA do portal do Azure](./media/howto-mfaserver-deploy/downloadportal.png)

5. Mantenha essa página aberta, pois vamos referenciá-la depois de executar o instalador.

## <a name="install-and-configure-the-mfa-server"></a>Instalar e configurar o Servidor MFA

Agora que já baixou o servidor, você pode instalá-lo e configurá-lo. Verifique se o servidor em que você está instalando atende aos requisitos listados na seção de planejamento.

1. Clique duas vezes no arquivo executável.
2. Na tela Selecionar Pasta de Instalação, certifique-se de que a pasta esteja correta e clique em **Avançar**.
3. Quando a instalação for concluída, clique em **Concluir**. Isso inicia o assistente de configuração.
4. Na tela de boas-vindas do assistente de configuração, marque **Ignorar o uso do Assistente de configuração de autenticação** e clique em **Avançar**. O assistente fecha e o servidor é iniciado.

   ![Ignorar usando o assistente de configuração de autenticação](./media/howto-mfaserver-deploy/skip2.png)

5. De volta à página de onde você baixou o servidor, clique no botão **Gerar Credenciais de Ativação** . Copie essas informações no Servidor Azure MFA nas caixas fornecidas e clique em **Ativar**.

> [!NOTE]
> Somente os administradores globais podem gerar credenciais de ativação no portal do Azure.

## <a name="send-users-an-email"></a>Enviar um email aos usuários

Para facilitar a distribuição, permita que o Servidor MFA se comunique com seus usuários. O Servidor MFA poderá enviar um email para informá-los se eles forem registrados para verificação em duas etapas.

O email a ser enviado deve ser determinado pelo modo como você configura seus usuários para a verificação em duas etapas. Por exemplo, se você puder importar os números de telefone do diretório da empresa, o email deverá incluir os números de telefone padrão para que os usuários saibam o que esperar. Se você não importar os números de telefone, ou seus usuários forem usar o aplicativo móvel, envie um email que os direcione à conclusão do registro da conta. Inclua um hiperlink para o Portal do Usuário da Autenticação Multifator do Azure no email.

O conteúdo do email vai variar de acordo com o método de autenticação que foi definido para o usuário (ligação telefônica, SMS ou aplicativo móvel). Por exemplo, se o usuário tiver que usar um PIN quando for se autenticar, o email informará qual PIN inicial foi definido para ele. Os usuários são solicitados a mudar o PIN na primeira autenticação.

### <a name="configure-email-and-email-templates"></a>Configurar o email e os modelos de email

Clique no ícone de email à esquerda para definir as configurações para enviar esses emails. Nesta página você pode inserir as informações SMTP do seu servidor de email e enviar um email marcando a caixa de seleção **Enviar emails aos usuários**.

![Configuração de email do Servidor MFA](./media/howto-mfaserver-deploy/email1.png)

Na guia Conteúdo do Email, você verá os diversos modelos de email disponíveis para sua escolha. Dependendo de como você configurou os usuários para usar a autenticação de dois fatores, é possível escolher o modelo mais adequado para as suas necessidades.

![Modelos de email do servidor MFA no console](./media/howto-mfaserver-deploy/email2.png)

## <a name="import-users-from-active-directory"></a>Importar usuários do Active Directory

Agora que o servidor está instalado, você deve adicionar usuários. Você pode escolher criá-los manualmente, importar usuários do Active Directory ou configurar a sincronização automática com o Active Directory.

### <a name="manual-import-from-active-directory"></a>Importar do Active Directory manualmente

1. No Servidor Azure MFA, à esquerda, selecione **Usuários**.
2. Na parte inferior, selecione **Importar do Active Directory**.
3. Agora, você pode procurar por usuários individuais ou buscar no diretório do AD UOs que tenham usuários. Nesse caso, nós especificaremos o UO de usuários.
4. Realce todos os usuários à direita e clique em **Importar**. Você deve receber uma mensagem informando que obteve êxito. Feche a janela de importação.

   ![Importação de usuário do servidor MFA de Active Directory](./media/howto-mfaserver-deploy/import2.png)

### <a name="automated-synchronization-with-active-directory"></a>Sincronização automática com o Active Directory

1. No Servidor MFA do Azure, à esquerda, selecione **Integração de Diretórios**.
2. Navegue até a guia **Sincronização**.
3. Na parte inferior, escolha **Adicionar**
4. Na caixa **Adicionar Item de sincronização** que aparece, escolha o domínio, OU **ou** grupo de segurança, configurações, padrões de método e idioma padrão para essa tarefa de sincronização e clique em **Adicionar**.
5. Marque a caixa denominada **Habilitar a sincronização com o Active Directory** e escolha um **intervalo de sincronização** entre um minuto e 24 horas.

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Como o Servidor de Autenticação Multifator do Azure lida com os dados do usuário

Quando você usa o servidor de autenticação multifator (MFA) local, os dados do usuário são armazenados nos servidores locais. Nenhum dado de usuário persistente é armazenado na nuvem. Quando o usuário executa uma autenticação de dois fatores, o servidor MFA envia dados para o serviço de nuvem do Azure MFA para realizar a autenticação. Quando essas solicitações de autenticação são enviadas ao serviço de nuvem, os campos a seguir são enviados na solicitação e nos logs para que eles fiquem disponíveis em relatórios de uso/autenticação do cliente. Alguns dos campos são opcionais; portanto, podem ser habilitados ou desabilitados no Servidor de Autenticação Multifator. A comunicação do servidor MFA para o serviço de nuvem MFA usa saída SSL/TLS pela porta 443. Esses campos são:

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

> [!IMPORTANT]
> A partir de março de 2019, as opções de chamada telefônica não estarão disponíveis para usuários do servidor MFA em locatários do Azure AD gratuitos/de avaliação. As mensagens SMS não são afetadas por essa alteração. A chamada telefônica continuará disponível para os usuários em locatários pagos do Azure AD. Essa alteração afeta apenas os locatários do Azure AD gratuitos/de avaliação.

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

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Como gerenciar protocolos TLS/SSL e conjuntos de codificação

Depois que você tiver feito upgrade ou instalado o MFA Server versão 8.x ou superior, é recomendável que os conjuntos de codificação mais antigos ou mais fracos sejam desabilitados ou removidos, a menos que eles sejam exigidos por sua organização. Você pode obter informações sobre como concluir esta tarefa no artigo [Gerenciar protocolos SSL/TLS e conjuntos de codificação para o AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

## <a name="next-steps"></a>Próximas etapas

- Instale e configure o [Portal do Usuário](howto-mfaserver-deploy-userportal.md) para o autoatendimento.
- Instale e configure o Servidor de MFA do Azure com o [Serviço de Federação do Active Directory](multi-factor-authentication-get-started-adfs.md), [Autenticação RADIUS](howto-mfaserver-dir-radius.md) ou [Autenticação LDAP](howto-mfaserver-dir-ldap.md).
- Instale e configure o [Gateway de Área de Trabalho Remota e o Servidor de Autenticação Multifator do Azure usando RADIUS](howto-mfaserver-nps-rdg.md).
- [Implante o serviço Web de aplicativo móvel servidor de autenticação multifator do Azure](howto-mfaserver-deploy-mobileapp.md).
- [Cenários avançados com a autenticação multifator do Azure e VPNs de](howto-mfaserver-nps-vpn.md)terceiros.