---
title: Criar e gerenciar usuários
description: Crie e gerencie usuários de sensores e o console de gerenciamento local. Os usuários podem receber a função de administrador, analista de segurança ou usuário somente leitura.
ms.date: 03/03/2021
ms.topic: article
ms.openlocfilehash: 2afc3cb2b9cfc0ac6b75c98198d9f0965b6dc04c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779011"
---
# <a name="about-defender-for-iot-console-users"></a>Sobre os usuários do console do defender para IoT

Este artigo descreve como criar e gerenciar usuários de sensores e o console de gerenciamento local. As funções de usuário incluem administrador, analista de segurança ou usuário somente leitura. Cada função é associada a um intervalo de permissões para ferramentas para o sensor ou o console de gerenciamento local. As funções foram projetadas para facilitar o acesso granular e seguro ao Azure defender para IoT.

Os recursos também estão disponíveis para acompanhar a atividade do usuário e habilitar a entrada Active Directory.

Por padrão, cada sensor e o console de gerenciamento local são instalados com um *CyberX e* um usuário de suporte. Esses usuários têm acesso a ferramentas avançadas para solução de problemas e configuração. Os usuários administradores devem entrar com essas credenciais de usuário, criar um usuário administrador e, em seguida, criar usuários extras para analistas de segurança e usuários somente leitura.

## <a name="role-based-permissions"></a>Permissões baseadas em função
As seguintes funções de usuário estão disponíveis:

- **Somente leitura**: os usuários somente leitura executam tarefas como exibir alertas e dispositivos no mapa do dispositivo. Esses usuários têm acesso às opções exibidas em **navegação**.

- **Analista de segurança**: os analistas de segurança têm permissões de usuário somente leitura. Eles também podem executar ações em dispositivos, reconhecer alertas e usar ferramentas de investigação. Esses usuários têm acesso às opções exibidas em **navegação** e **análise**.

- **Administrador**: os administradores têm acesso a todas as ferramentas, incluindo a definição de configurações do sistema, a criação e o gerenciamento de usuários e muito mais. Esses usuários têm acesso às opções exibidas em **navegação**, **análise** e **Administração**.

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>Permissões baseadas em função para ferramentas de console de gerenciamento local

Esta seção descreve as permissões disponíveis para administradores, analistas de segurança e usuários somente leitura para o console de gerenciamento local.  

| Permissão | Somente leitura | Analista de segurança | Administrador |
|--|--|--|--|
| Exibir e filtrar o mapa Enterprise | ✓ | ✓ | ✓ |
| Criar um site |  |  | ✓ |
| Gerenciar um site (adicionar e editar zonas) |  |  | ✓ |
| Exibir e filtrar o inventário de dispositivos | ✓ | ✓ | ✓ |
| Exibir e gerenciar alertas: reconhecer, aprender e fixar | ✓ | ✓ | ✓ |
| Gerar relatórios |  | ✓ | ✓ |
| Exibir relatórios de avaliação de risco |  | ✓ | ✓ |
| Definir exclusões de alerta |  | ✓ | ✓ |
| Exibir ou definir grupos de acesso |  |  | ✓ |
| Gerenciar configurações do sistema |  |  | ✓ |
| Gerenciar usuários |  |  | ✓ |
| Enviar dados de alerta para parceiros |  |  | ✓ |
| Gerenciar certificados |  |  | ✓ |
| Tempo limite da sessão quando os usuários não estão ativos | 30 minutos | 30 minutos  | 30 minutos  |

#### <a name="assign-users-to-access-groups"></a>Atribuir usuários a grupos de acesso

Os administradores podem aprimorar o controle de acesso do usuário no defender para IoT atribuindo usuários a *grupos de acesso* específicos. Os grupos de acesso são atribuídos a zonas, sites, regiões e unidades de negócios onde um sensor está localizado. Ao atribuir usuários a grupos de acesso, os administradores têm controle específico sobre onde os usuários gerenciam e analisam as detecções de dispositivos. 

Trabalhar dessa maneira acomoda grandes organizações em que as permissões de usuário podem ser complexas ou determinadas por uma política de segurança organizacional global. Para obter mais informações, consulte [definir o controle de acesso global](how-to-define-global-user-access-control.md).

### <a name="role-based-permissions-to-sensor-tools"></a>Permissões baseadas em função para ferramentas de sensor

Esta seção descreve as permissões disponíveis para administradores de sensor, analistas de segurança e usuários somente leitura.  

| Permissão | Somente leitura | Analista de segurança | Administrador |
|--|--|--|--|
| Exibir o painel | ✓ | ✓ | ✓ |
| Exibições de zoom do mapa de controle |  |  | ✓ |
| Exibir alertas | ✓ | ✓ | ✓ |
| Gerenciar alertas: reconhecer, aprender e fixar |  | ✓ | ✓ |
| Exibir eventos em uma linha do tempo |  | ✓ | ✓ |
| Autorizar dispositivos, dispositivos de verificação conhecidos, dispositivos de programação |  | ✓ | ✓ |
| Exibir dados de investigação | ✓ | ✓ | ✓ |
| Gerenciar configurações do sistema |  |  | ✓ |
| Gerenciar usuários |  |  | ✓ |
| Servidores DNS para pesquisa inversa |  |  | ✓ |
| Enviar dados de alerta para parceiros |  | ✓ | ✓ |
| Criar comentários de alerta |  | ✓ | ✓ |
| Exibir histórico de alterações de programação | ✓ | ✓ | ✓ |
| Criar regras de alerta personalizadas |  | ✓ | ✓ |
| Gerenciar várias notificações simultaneamente |  | ✓ | ✓ |
| Gerenciar certificados |  |  | ✓ |
| Tempo limite da sessão quando os usuários não estão ativos | 30 minutos | 30 minutos | 30 minutos |

## <a name="define-users"></a>Definir usuários

Esta seção descreve como definir usuários. Os usuários de CyberX, suporte e administrador podem adicionar, remover e atualizar outras definições de usuário.

Para definir um usuário:

1. No painel esquerdo do sensor ou do console de gerenciamento local, selecione **usuários**.
1. Na janela **usuários** , selecione **criar usuário**.
1. No painel **criar usuário** , defina os seguintes parâmetros:

   - **Nome de usuário**: Insira um nome de usuário.
   - **Email**: Insira o endereço de email do usuário.
   - **Nome**: Insira o nome do usuário.
   - **Sobrenome**: Insira o sobrenome do usuário.
   - **Função**: defina a função do usuário. Consulte [permissões baseadas em função](#role-based-permissions).
   - **Grupo de acesso**: se você estiver criando um usuário para o console de gerenciamento local, defina o grupo de acesso do usuário. Consulte [definir o controle de acesso global](how-to-define-global-user-access-control.md).
   - **Senha**: selecione o tipo de usuário da seguinte maneira:
     - **Usuário local**: defina uma senha para o usuário de um sensor ou um console de gerenciamento local. A senha deve incluir pelo menos seis caracteres e deve incluir letras e números.
     - **Active Directory usuário**: você pode permitir que os usuários entrem no sensor ou no console de gerenciamento usando as credenciais do Active Directory. Os grupos de Active Directory definidos podem ser associados a níveis de permissão específicos. Por exemplo, configure um grupo de Active Directory específico e atribua todos os usuários no grupo ao tipo de usuário somente leitura.

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="Gerencie seus usuários.":::

## <a name="user-session-timeout"></a>Tempo limite da sessão de usuário

Se os usuários não estiverem ativos no teclado ou no mouse por um horário específico, eles serão desconectados de sua sessão e deverão entrar novamente.

Quando os usuários não funcionaram com o mouse ou o teclado do console por um período de 30 minutos, uma sessão de saída é forçada.

Esse recurso é habilitado por padrão e na atualização, mas pode ser desabilitado. Além disso, os tempos de contagem de sessão podem ser atualizados. Os tempos de sessão são definidos em segundos. As definições são aplicadas por sensor e pelo console de gerenciamento local.

Uma mensagem de tempo limite de sessão é exibida no console quando o tempo limite de inatividade foi aprovado.

### <a name="control-inactivity-sign-out"></a>Controlar a saída de inatividade

Os usuários administradores podem habilitar e desabilitar a saída de inatividade e ajustar os limites de inatividade.

Para acessar o comando:

1. Entre na CLI para o sensor ou console de gerenciamento local usando o defender para credenciais administrativas de IoT.

1. Digite `sudo nano /var/cyberx/properties/authentication`.

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

Para desabilitar o recurso, altere `infinity_session_expiration = true` para `infinity_session_expiration = false` .

Para atualizar os períodos de contagem de saída, ajuste o `= <number>` valor para o tempo necessário.

## <a name="track-user-activity"></a>Acompanhar a atividade do usuário 

Você pode acompanhar a atividade do usuário na linha do tempo de evento em cada sensor. A linha do tempo exibe o evento ou o dispositivo afetado e a hora e a data em que o usuário realizou a atividade.

Para exibir a atividade do usuário:

1. Entre no sensor.
1. Na linha do tempo do evento, habilite a opção **operações do usuário** . 

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="Exibir a atividade de um usuário.":::

## <a name="integrate-with-active-directory-servers"></a>Integrar com servidores Active Directory 

Configure o sensor ou o console de gerenciamento local para trabalhar com Active Directory. Isso permite que Active Directory usuários acessem os consoles do defender para IoT usando suas credenciais de Active Directory.

Há suporte para dois tipos de autenticação baseada em LDAP:

- **Autenticação completa**: os detalhes do usuário são recuperados do servidor LDAP. Os exemplos são as permissões nome, sobrenome, email e usuário.

- **Usuário confiável**: somente a senha do usuário é recuperada. Outros detalhes do usuário que são recuperados são baseados em usuários definidos no sensor.

### <a name="active-directory-and-defender-for-iot-permissions"></a>Permissões do Active Directory e do defender para IoT

Você pode associar grupos de Active Directory definidos aqui com níveis de permissão específicos. Por exemplo, configure um grupo de Active Directory específico e atribua permissões somente leitura a todos os usuários no grupo.

Para configurar Active Directory:

1. No painel esquerdo, selecione **configurações do sistema**.

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="Exiba as configurações do sistema Active Directory.":::

2. No painel **configurações do sistema** , selecione **Active Directory**.

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="Edite suas configurações de Active Directory.":::

3. Na caixa de diálogo **Editar configuração de Active Directory** , selecione **Active Directory a integração habilitada**  >  . A caixa de diálogo **Editar configuração de Active Directory** expande e agora você pode inserir os parâmetros para configurar Active Directory.

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="Insira os parâmetros a serem configurados Active Directory.":::

    > [!NOTE]
    > - Você deve definir os parâmetros LDAP aqui exatamente como eles aparecem no Active Directory.
    > - Para todos os parâmetros de Active Directory, use somente minúsculas. Use minúsculas mesmo quando as configurações em Active Directory usarem maiúsculas.
    > - Você não pode configurar LDAP e LDAPs para o mesmo domínio. No entanto, você pode usar ambos para domínios diferentes ao mesmo tempo.

4. Defina os parâmetros do servidor Active Directory, da seguinte maneira:

   | Parâmetro de servidor | Descrição |
   |--|--|
   | FQDN do controlador de domínio | Defina o nome de domínio totalmente qualificado (FQDN) exatamente como ele aparece no servidor LDAP. Por exemplo, insira `host1.subdomain.domain.com`. |
   | Porta do controlador de domínio | Defina a porta na qual o LDAP está configurado. |
   | Domínio primário | Defina o nome de domínio (por exemplo, `subdomain.domain.com` ) e o tipo de conexão de acordo com a configuração do LDAP. |
   | Grupos do Active Directory | Insira os nomes de grupo que são definidos em sua configuração de Active Directory no servidor LDAP. |
   | Domínios confiáveis | Para adicionar um domínio confiável, adicione o nome de domínio e o tipo de conexão de um domínio confiável. <br />Você pode configurar domínios confiáveis somente para usuários que foram definidos em usuários. |

#### <a name="activedirectory-groups-for-the-on-premises-management-console"></a>Grupos do ActiveDirectory para o console de gerenciamento local

Se você estiver criando grupos de Active Directory para usuários do console de gerenciamento local, deverá criar uma regra de grupo de acesso para cada grupo de Active Directory. O console de gerenciamento local Active Directory credenciais não funcionarão se uma regra de grupo de acesso não existir para o grupo de usuários Active Directory. Consulte [definir o controle de acesso global](how-to-define-global-user-access-control.md).

1. Selecione **Salvar**.

2. Para adicionar um servidor confiável, selecione **Adicionar servidor** e configurar outro servidor.

## <a name="resetting-passwords"></a>Redefinindo senhas

### <a name="cyberx-or-support-user"></a>CyberX ou usuário de suporte

Somente o **CyberX** e o usuário de **suporte** têm acesso ao recurso de **recuperação de senha** . Se o **CyberX** ou o usuário de **suporte** esqueceu sua senha, ele poderá redefini-la por meio da opção de **recuperação de senha** na página de entrada do defender for IOT.

Para redefinir a senha para um usuário CyberX ou de suporte:

1. Na tela de entrada do defender para IoT, selecione  **recuperação de senha**. A tela **recuperação de senha** é aberta.

1. Selecione **CyberX** ou **suporte** e copie o identificador exclusivo.

1. Navegue até a portal do Azure e selecione **sites e sensores**.  

1. Selecione o ícone **filtro de assinatura** :::image type="icon" source="media/password-recovery-images/subscription-icon.png" border="false":::  na barra de ferramentas superior e selecione a assinatura à qual seu sensor está conectado.

1. Selecione a guia **recuperar senha do console de gerenciamento local** .

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="Selecione o botão recuperar gerenciamento local para baixar o arquivo de recuperação.":::

1. Insira o identificador exclusivo que você recebeu na tela de **recuperação de senha** e selecione **recuperar**. O `password_recovery.zip` arquivo é baixado.

    > [!NOTE]
    > Não altere o arquivo de recuperação de senha. É um arquivo assinado e não funcionará se você violar.

1. Na tela **recuperação de senha** , selecione **carregar**. **A janela carregar arquivo de recuperação de senha** será aberta.

1. Selecione **procurar** para localizar o `password_recovery.zip` arquivo ou arraste o `password_recovery.zip` para a janela.

    > [!NOTE]
    > Uma mensagem de erro pode aparecer indicando que o arquivo é inválido. Para corrigir essa mensagem de erro, verifique se você selecionou a assinatura correta antes de baixar `password_recovery.zip` e baixá-la novamente.  

1. Selecione **Avançar** e seu usuário, e a senha gerada pelo sistema para o console de gerenciamento será exibida.

### <a name="administrator-security-analyst-and-read-only-user"></a>Administrador, analista de segurança e usuário somente leitura

Os analistas de segurança e somente leitura não podem redefinir sua própria senha e precisam entrar em contato com um usuário com as funções administrador, suporte ou CyberX para redefinir a senha. Um usuário administrador deve contatar o **CyberX** ou o usuário de **suporte** para redefinir sua senha.

Para redefinir a senha de um usuário no sensor:

1. Um usuário de administrador, suporte ou função CyberX deve entrar no sensor.

1. Selecione **usuários** no painel esquerdo.

   :::image type="content" source="media/password-recovery-images/sensor-page.png" alt-text="Selecione a opção usuário no painel do lado esquerdo.":::

1. Localize o usuário e selecione **Editar** no menu suspenso **ações** .

   :::image type="content" source="media/password-recovery-images/edit.png" alt-text="Selecione Editar no menu suspenso ações.":::

1. Insira a nova senha nos campos **nova senha** e **confirmar nova senha** .

1. Selecione **Atualizar**.

Para redefinir a senha de um usuário no console de gerenciamento local:

1. Um usuário de administrador, suporte ou função CyberX deve entrar no sensor.

1. Selecione **usuários** no painel esquerdo.

   :::image type="content" source="media/password-recovery-images/console-page.png" alt-text="No painel esquerdo, selecione a opção do usuário.":::

1. Localize o usuário e selecione o ícone Editar :::image type="icon" source="media/password-recovery-images/edit-icon.png" border="false"::: .

1. Insira a nova senha nos campos **nova senha** e **confirmar nova senha** .

1. Selecione **Atualizar**.

## <a name="see-also"></a>Veja também

[Ativar e configurar seu sensor](how-to-activate-and-set-up-your-sensor.md) 
 [Ativar e configurar seu console](how-to-activate-and-set-up-your-on-premises-management-console.md) 
 de gerenciamento local [Acompanhar atividade do sensor](how-to-track-sensor-activity.md)
