---
title: Plano de implantação - Azure Active Directory de redefinição de senha de autoatendimento
description: Redefinição de estratégia para uma implementação bem-sucedida da senha de autoatendimento do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b566bfc3f6c49f6cb9fe31f166356f6ae351e38
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440928"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>Implantar o Azure de redefinição de senha de autoatendimento do AD

Redefinição de senha de autoatendimento (SSPR) é um recurso do Azure Active Directory que permite que os funcionários redefinam suas senhas sem a necessidade de entrar em contato com a equipe de TI. Os funcionários devem se registrar para ou ser registrados para autoatendimento a redefinição de senha antes de usar o serviço. Durante o registro, o funcionário escolhe um ou mais métodos de autenticação habilitados pela sua organização.

SSPR permite que os funcionários rapidamente obter o desbloqueio e continuar trabalhando, independentemente de onde eles estão ou a hora do dia. Permitindo que os usuários desbloquear a mesmos, sua organização pode reduzir o tempo não produtivas e custos de suporte de alta dos problemas mais comuns relacionados à senha.

Ajude os usuários a se registrar rapidamente com a implantação de SSPR juntamente com outro aplicativo ou serviço em sua organização. Esta ação irá gerar um grande volume de entradas e orientará o registro.

Antes de implantar a SSPR, as organizações querem determinar quantos de redefinição de senha chamadas de suporte técnico relacionados acontecer com o tempo e o custo médio de cada chamada. Eles podem usar essa implantação de postagem de dados para mostrar o valor que SSPR está trazendo para sua organização.  

## <a name="how-sspr-works"></a>Como funciona o SSPR

1. Quando um usuário tenta redefinir uma senha, eles devem verificar seus métodos para provar sua identidade ou método de autenticação registrado anteriormente.
1. Em seguida, o usuário insere uma nova senha.
   1. Para usuários somente na nuvem, a nova senha é armazenada no Azure Active Directory. Para obter mais informações, consulte o artigo [funciona como a SSPR](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work).
   1. Para usuários híbridos, a senha é gravada para o Active Directory local por meio do serviço do Azure AD Connect. Para obter mais informações, consulte o artigo [o que é o write-back de senha](concept-sspr-writeback.md#how-password-writeback-works).

## <a name="licensing-considerations"></a>Considerações sobre licenciamento

O Azure Active Directory é uma licença por usuário que significa que cada usuário deve ter uma licença apropriada para os recursos que utilizem.

- Autoatendimento redefinição de senha para usuários somente na nuvem está disponível com o Azure AD básico ou superior.
- Autoatendimento redefinição de senha com write-back de local para ambientes híbridos exige o Azure AD Premium P1 ou acima.

Para obter mais informações sobre o licenciamento do podem ser encontradas no [do Active Directory do Azure, página de preços](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>Habilitar o registro combinado para SSPR e MFA

A Microsoft recomenda que as organizações habilitar a experiência de registro combinado para SSPR e a autenticação multifator. Quando você habilitar essa experiência de registro combinado, os usuários só precisam selecionar suas informações de registro de uma vez para habilitar os dois recursos.

![Registro de informações de segurança combinada](./media/howto-sspr-deployment/combined-security-info.png)

A experiência de registro combinado não exige que as organizações para habilitar SSPR e autenticação multifator do Azure usar. A experiência de registro combinado fornece às organizações que uma melhor experiência de usuário em comparação com os componentes individuais tradicionais. Para obter mais informações sobre registro combinado e como habilitar, podem ser encontradas no artigo [combinado de registro de informações de segurança (visualização)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-configuration"></a>Planeje a configuração

As configurações a seguir são necessárias para habilitar SSPR, juntamente com os valores recomendados.

| Área | Configuração | Value |
| --- | --- | --- |
| **Propriedades SSPR** | A redefinição de senha de autoatendimento habilitada | **Selecionado** grupo piloto / **todos os** para produção |
| **Métodos de autenticação** | Métodos de autenticação necessários para registrar | 1 sempre mais que o necessário para a redefinição |
|   | Métodos de autenticação necessários para a redefinição | Um ou dois |
| **Registro** | Exigir que os usuários se registrem ao entrar | Sim |
|   | Número de dias antes que os usuários são solicitados a reconfirmar suas informações de autenticação | 90 – 180 dias |
| **Notificações** | Notificar os usuários de redefinições de senha | Sim |
|   | Notificar todos os administradores quando outros administradores redefinirem suas próprias senhas | Sim |
| **Personalização** | Personalizar o link de assistência técnica | Sim |
|   | Email de assistência técnica personalizada ou uma URL | Endereço de email ou site de suporte |
| **Integração local** | Senhas write-back para o local AD | Sim |
|   | Permitir aos usuários desbloquear conta sem redefinir senha | Sim |

### <a name="sspr-properties-recommendations"></a>Recomendações de propriedades SSPR

Ao habilitar a redefinição de senha de autoatendimento, escolha um grupo de segurança a ser usado durante o piloto.

Quando você planeja iniciar o serviço de forma mais ampla, é recomendável usar a opção All para impor a SSPR para todos na organização. Se você não pode definir a todos, selecione adequadas do Azure AD ou grupo AD sincronizados ao AD do Azure.

### <a name="authentication-methods"></a>Métodos de autenticação

Defina métodos de autenticação necessários para registrar a pelo menos um mais do que o número necessário para redefinir. Permitindo que vários dá aos usuários flexibilidade de quando for preciso redefinir.

Definir **número de métodos necessários para redefinir** para um nível apropriado para sua organização. Uma requer o atrito de pelo menos, enquanto dois podem aumentar sua postura de segurança.

Ver [quais são os métodos de autenticação](concept-authentication-methods.md) para obter informações detalhadas sobre quais autenticação métodos estão disponíveis para SSPR, perguntas de segurança predefinidos e como criar perguntas de segurança personalizado.

### <a name="registration-settings"></a>Configurações de registro

Definir **exigir que os usuários se registrem ao entrar** à **Sim**. Essa configuração significa que os usuários são forçados a se registrem ao entrar, garantindo que todos os usuários estejam protegidos.

Definir **número de dias antes que os usuários são solicitados a reconfirmar suas informações de autenticação** para entre **90** e **180** dias, a menos que sua organização tiver um negócio necessidade para um período mais curto.

### <a name="notifications-settings"></a>Configurações de notificações

Configurar ambos os **notificar os usuários sobre as redefinições de senha** e o **notificar todos os administradores quando outros administradores redefinirem suas senhas** para **Sim**. Selecionando **Sim** em ambos os aumenta a segurança, garantindo que os usuários estejam cientes quando sua senha foi redefinida, e que todos os administradores estão cientes quando um administrador altera uma senha. Se os usuários ou administradores recebem uma notificação e eles não iniciou a alteração, eles imediatamente podem relatar uma possível violação de segurança.

### <a name="customization"></a>Personalização

É fundamental para personalizar o **helpdesk URL ou email** para garantir que os usuários que enfrentam problemas rapidamente podem obter ajuda. Defina essa opção como um endereço de email de assistência técnica comum ou página da web que os usuários estão familiarizados.

### <a name="on-premises-integration"></a>Integração local

Se você tiver um ambiente híbrido, certifique-se de que **senhas Write-back para o local AD** é definido como **Sim**. Defina também os permitir que os usuários para desbloquear a conta sem redefinir a senha como Sim, porque ele oferece mais flexibilidade.

### <a name="changingresetting-passwords-of-administrators"></a>Alterando/redefinindo senhas de administradores

Contas de administrador são especiais com permissões elevadas. Para protegê-los, as seguintes restrições se aplicam à alteração de senhas de administradores:

- Os administradores de empresa no local ou administradores de domínio não é possível redefinir sua senha por meio de SSPR. Eles só podem alterar a senha em seu ambiente local. Dessa forma, é recomendável não sincronize local contas de administrador do AD para o Azure AD.
- Um administrador não pode usar o segredo de perguntas e respostas como um método para redefinir a senha.

### <a name="environments-with-multiple-identity-management-systems"></a>Ambientes com vários sistemas de gerenciamento de identidade

Se houver várias identidade com sistemas de gerenciamento em um ambiente, como gerentes de identidade local, como Oracle AM, SiteMinder, ou outros sistemas, em seguida, as senhas escritas para o Active Directory talvez precise ser sincronizados com os outros sistemas usando uma ferramenta como o Password Change Notification Service (PCNS) com o Microsoft Identity Manager (MIM). Para obter informações sobre esse cenário mais complexo, consulte o artigo [implantar o serviço de notificação de alteração de senha de MIM em um controlador de domínio](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-deployment-and-support-for-sspr"></a>Planejar a implantação e suporte para SSPR

### <a name="engage-the-right-stakeholders"></a>Entre em contato com os participantes à direita

Quando os projetos de tecnologia falham, eles normalmente fazem isso devido a expectativas incompatíveis no impacto, resultados e responsabilidades. Para evitar essas armadilhas, certifique-se de que você está participando os participantes certos e que as funções de participantes no projeto são bem compreendidas por documentar as partes interessadas e sua entrada de projeto e a responsabilidade.

### <a name="communications-plan"></a>Plano de comunicações

A comunicação é crítica para o sucesso de qualquer novo serviço. Se comunicar proativamente com seus usuários como usar o serviço e o que podem fazer para obter ajuda se algo não funcionar conforme o esperado. Examine os [materiais de distribuição no Centro de download da Microsoft de redefinição de senha de autoatendimento](https://www.microsoft.com/download/details.aspx?id=56768) para ter ideias sobre como planejar sua estratégia de comunicação do usuário final.

### <a name="testing-plan"></a>Plano de testes

Para garantir que a implantação funciona conforme o esperado, você deve planejar um conjunto de casos de teste que você usará para validar a implementação. A tabela a seguir inclui alguns cenários de teste útil que você pode usar para suas organizações esperado de resultados com base em suas políticas de documento.

| Caso comercial | Resultado esperado |
| --- | --- |
| Portal do SSPR é acessível de dentro da rede corporativa | Determinado pela sua organização |
| Portal do SSPR é acessível de fora da rede corporativa | Determinado pela sua organização |
| Redefinir a senha de usuário do navegador quando o usuário não está habilitado para redefinição de senha | Usuário não é capaz de acessar o fluxo de redefinição de senha |
| Redefinir a senha de usuário do navegador quando o usuário não registrado para redefinição de senha | Usuário não é capaz de acessar o fluxo de redefinição de senha |
| Usuário se autentica ao registro de redefinição de senha é imposta | Usuário é solicitado a registrar informações de segurança |
| Sinais de usuário no registro de redefinição de senha de quando foi concluídas | Usuário não será solicitado a registrar informações de segurança |
| Portal SSPR é acessível quando o usuário não tem uma licença | Pode ser acessado |
| Redefinir a senha de usuário da tela de bloqueio de dispositivo Windows 10 AADJ ou H + AADJ depois que o usuário tiver registrado | Usuário pode redefinir a senha |
| Os dados de uso e de registro do SSPR estão disponíveis para administradores em tempo real | Está disponível por meio dos logs de auditoria |

### <a name="support-plan"></a>Plano de suporte

Enquanto o SSPR não cria problemas de usuário normalmente, é importante ter suporte equipe preparados para lidar com problemas que podem surgir.

Embora um administrador pode alterar ou redefinir a senha para usuários finais por meio do portal do Azure AD, é melhor ajudar a resolver o problema por meio de um processo de suporte de autoatendimento.

Na seção guia operacional deste documento, criar uma lista de casos de suporte e suas causas prováveis e criar um guia para a resolução.

### <a name="auditing-and-reporting"></a>Auditoria e relatórios

Após a implantação, muitas organizações desejam saber como ou se a SSPR (redefinição de senha de autoatendimento) realmente está sendo usada. O recurso de relatório do Azure Active Directory (Azure AD) o ajuda a responder perguntas usando relatórios predefinidos.

Logs de auditoria de registro e redefinição de senha estão disponíveis por 30 dias. Portanto, se a auditoria de segurança dentro de uma empresa requer maior retenção, os logs precisam ser exportada e consumidos, como em uma ferramenta do SIEM [Sentinel Azure](../../sentinel/connect-azure-active-directory.md), Splunk ou ArcSight.

Em uma tabela, como a mostrada abaixo, documente o agendamento de backup, o sistema e as partes responsáveis. Talvez não seja necessário separar a auditoria e relatórios de backups, mas você deve ter um backup separado da qual você pode recuperar de um problema.

|   | Frequência de download | Sistema de destino | Parte responsável |
| --- | --- | --- | --- |
| Auditoria de backup |   |   |   |
| Relatórios de backup |   |   |   |
| Backup de recuperação de desastre |   |   |   |

## <a name="implementation"></a>Implementação

Implementação ocorre em três estágios:

- Configurar usuários e licenças
- Configurar o Azure AD SSPR para autoatendimento e registro
- Configurar o Azure AD Connect para write-back de senha

### <a name="communicate-the-change"></a>Comunicar as alterações

Começar a implementação do plano de comunicações desenvolvidos na fase de planejamento.

### <a name="ensure-groups-are-created-and-populated"></a>Certifique-se de grupos são criados e preenchidos

A seção de métodos de autenticação de senha de planejamento de referência e verifique se o grupo (s) para a implementação piloto ou de produção está disponíveis e todos os usuários apropriados são adicionados aos grupos.

### <a name="apply-licenses"></a>Aplicar licenças

Os grupos que você pretende implementar devem ter o Azure AD licença premium atribuída a eles. Você pode atribuir licenças diretamente ao grupo, ou você pode usar políticas de licença existentes, como por meio do PowerShell ou o licenciamento baseado em grupo.

Informações sobre como atribuir licenças a grupos de usuários que podem ser encontradas no artigo [atribuir licenças aos usuários pela associação de grupo no Azure Active Directory](../users-groups-roles/licensing-groups-assign.md).

### <a name="configure-sspr"></a>Configurar o SSPR

#### <a name="enable-groups-for-sspr"></a>Habilitar grupos para SSPR

1. Acesse o portal do Azure com uma conta de administrador.
1. Selecione todos os serviços, na caixa de filtro, digite o Azure Active Directory e, em seguida, selecione Azure Active Directory.
1. Na folha do Active Directory, selecione a redefinição de senha.
1. No painel Propriedades, selecione selecionados. Se você quiser que todos os usuários habilitados, selecionar tudo.
1. A senha padrão folha política de redefinição, digite o nome do primeiro grupo, selecioná-la, em seguida, clique em Selecionar na parte inferior da tela e selecione Salvar na parte superior da tela.
1. Repita esse processo para cada grupo.

#### <a name="configure-the-authentication-methods"></a>Configurar os métodos de autenticação

Fazer referência a seu planejamento da seção de planejamento de métodos de autenticação de senha deste documento.

1. Selecione o registro, exigir que usuário se registrem ao entrar, selecione Sim e, em seguida, defina o número de dias antes da expiração em e, em seguida, selecione Salvar.
1. Selecione notificação e configurar por seu plano e, em seguida, selecione Salvar.
1. Marque a personalização e configurar por seu plano e, em seguida, selecione Salvar.
1. Selecione integração local, configure por seu plano e, em seguida, selecione Salvar.

### <a name="enable-sspr-in-windows"></a>Habilitar SSPR no Windows

Dispositivos Windows 10 executando a versão 1803 ou maior que ambos ingressados no Azure AD ou Azure AD híbrido pode redefinir sua senha na tela de logon do Windows. Informações e etapas para configurar esse recurso podem ser encontradas no artigo [redefinição na tela de logon de senha do AD do Azure](tutorial-sspr-windows.md)

### <a name="configure-password-writeback"></a>Configurar o write-back de senha

Etapas para configurar o write-back de senha para sua organização pode ser encontrada no artigo [instruções: Configurar o write-back de senha](howto-sspr-writeback.md).

## <a name="manage-sspr"></a>Gerenciar SSPR

Funções necessárias para gerenciar os recursos associados com a redefinição de senha de autoatendimento.

| Função/persona de negócios | Função do AD do Azure (se necessário) |
| :---: | :---: |
| Suporte técnico de nível 1 | Administrador de senha |
| Suporte técnico de nível 2 | Administrador de usuários |
| Administrador SSPR | Administrador global |

### <a name="support-scenarios"></a>Cenários de suporte

Para habilitar o sucesso da equipe de suporte, você pode criar perguntas Frequentes com base em perguntas recebidos dos usuários. A tabela a seguir contém cenários comuns de suporte.

| Cenários | DESCRIÇÃO |
| --- | --- |
| O usuário não tem nenhum método de autenticação registrados disponíveis | Um usuário está tentando redefinir sua senha, mas não tem nenhum dos métodos de autenticação que eles registrados disponível (exemplo: eles à esquerda de seu telefone celular em casa e não pode acessar o email) |
| Usuário não está recebendo um texto ou chamar em seu telefone comercial ou celular | Um usuário está tentando verificar sua identidade por meio de texto ou chamada, mas não está recebendo uma chamada de texto /. |
| Usuário não pode acessar o portal de redefinição de senha | Um usuário deseja redefinir sua senha, mas não está habilitado para redefinição de senha e, portanto, não é possível acessar a página para atualizar as senhas. |
| Usuário não é possível definir uma nova senha | Um usuário conclui a verificação durante o fluxo de redefinição de senha, mas não é possível definir uma nova senha. |
| Usuário não verá um link de redefinição de senha em um dispositivo Windows 10 | Um usuário está tentando redefinir a senha da tela de bloqueio do Windows 10, mas o dispositivo é ou não ingressado no Azure AD ou a política de dispositivo do Intune não está habilitada |

Talvez você queira incluir informações como o seguinte para solução de problemas adicionais.

- Quais grupos são habilitados para SSPR.
- Quais métodos de autenticação são configurados.
- As políticas de acesso relacionadas em ou da rede corporativa.
- Etapas para cenários comuns de solução de problemas.

Você também pode consultar a documentação online sobre como solucionar problemas de senha de autoatendimento de redefinição para entender as etapas de solução de problemas gerais para os cenários mais comuns de SSPR.

## <a name="next-steps"></a>Próximas etapas

- [Considere a implementação de proteção por senha do Azure AD](concept-password-ban-bad.md)

- [Considere a implementação de bloqueio inteligente do Azure AD](howto-password-smart-lockout.md)
