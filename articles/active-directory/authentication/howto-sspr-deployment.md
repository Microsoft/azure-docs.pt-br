---
title: Considerações de implantação para redefinição de senha self-service do Azure Active Directory
description: Saiba mais sobre as considerações e a estratégia de implantação para a implementação bem-sucedida da redefinição de senha self-service do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55fc4c0b5f3e2bbf57c972ad4a7fd2c9bdd3810e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579188"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Planejar uma implantação de redefinição de senha self-service do Azure Active Directory

> [!IMPORTANT]
> Este plano de implantação oferece orientações e melhores práticas para implantar a SSPR (redefinição de senha self-service) do Azure AD.
>
> **Se você for um usuário final e precisar voltar à sua conta, vá para [https://aka.ms/sspr](https://aka.ms/sspr)**.

A [SSPR (redefinição de senha self-service)](https://www.youtube.com/watch?v=pS3XwfxJrMo) é um recurso do Azure Active Directory (AD) que permite que os usuários redefinam suas senhas sem entrar em contato com a equipe de TI para obter ajuda. Os usuários podem rapidamente se desbloquear e continuar trabalhando independentemente de onde estiverem ou da hora do dia. Ao permitir que os funcionários se desbloqueiem, sua organização pode reduzir o tempo não produtivo e os altos custos de suporte para os problemas mais comuns relacionados a senhas.

O SSPR tem os seguintes recursos principais:

* O self-service permite que os usuários finais redefinam suas senhas expiradas ou não expiradas sem entrar em contato com um administrador ou uma assistência técnica para obter suporte.
* [O Write-back de Senha](./concept-sspr-writeback.md) permite o gerenciamento de senhas locais e a resolução de bloqueio de conta por meio da nuvem.
* Os relatórios de atividade de gerenciamento de senha fornecem aos administradores percepções sobre as atividades de registro e de redefinição de senha que ocorrem em suas organizações.

Este guia de implantação mostra como planejar e testar uma distribuição SSPR.

Para ver rapidamente o SSPR em ação e voltar para entender as considerações adicionais de implantação:

> [!div class="nextstepaction"]
> [Habilitar SSPR (redefinição de senha self-service)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>Saiba mais sobre o SSPR

Saiba mais sobre o SSPR. Consulte [Como funciona: Redefinição de senha self-service do Azure AD](./concept-sspr-howitworks.md).

### <a name="key-benefits"></a>Principais benefícios

Os principais benefícios da habilitação de SSPR são:

* **Gerenciamento de custo**. O SSPR reduz os custos de suporte de TI, permitindo que os usuários redefinam senhas por conta própria. Ele também reduz o custo do tempo perdido devido a senhas perdidas e bloqueios. 

* **Experiência de usuário intuitiva**. Ele fornece um processo de registro de usuário único intuitivo que permite que os usuários redefinam senhas e desbloqueiem contas sob demanda de qualquer dispositivo ou local. O SSPR permite que os usuários voltem ao trabalho com mais rapidez e sejam mais produtivos.

* **Flexibilidade e segurança**. O SSPR permite que as empresas acessem a segurança e a flexibilidade que uma plataforma de nuvem fornece. Os administradores podem alterar as configurações para acomodar novos requisitos de segurança e reverter essas alterações para os usuários sem interromper sua entrada.

* **Auditoria e rastreamento de uso robustos**. Uma organização pode garantir que os sistemas de negócios permaneçam seguros enquanto seus usuários redefinem suas próprias senhas. Os logs de auditoria robustos incluem informações de cada etapa do processo de redefinição de senha. Esses logs estão disponíveis a partir de uma API e permitem que o usuário importe os dados para um sistema de escolha do SIEM (monitoramento de eventos e incidentes de segurança).

### <a name="licensing"></a>Licenciamento

O Azure Active Directory é licenciado por usuário, o que significa que cada usuário requer uma licença apropriada para os recursos que ele usa. Recomendamos o licenciamento baseado em grupo para SSPR. 

Para comparar edições e recursos e habilitar o licenciamento baseado em grupo ou usuário, confira [Requisitos de licenciamento para redefinição de senha self-service do Azure AD](./concept-sspr-licensing.md).

Para obter mais informações sobre preços, consulte [Preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Pré-requisitos

* Um locatário de trabalho do Azure AD com pelo menos uma licença de avaliação habilitada. Se necessário, [crie um gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Uma conta com privilégios de Administrador Global.


### <a name="training-resources"></a>Recursos de treinamento

| Recursos| Link e descrição |
| - | - |
| vídeos| [Capacite seus usuários com melhor escalabilidade de TI](https://youtu.be/g9RpRnylxS8) 
| |[O que é redefinição de senha self-service?](https://youtu.be/hc97Yx5PJiM)|
| |[Como implantar a redefinição de senha self-service](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Como habilitar e configurar o SSPR no Azure AD](https://www.youtube.com/watch?v=rA8TvhNcCvQ)|
| |[Como configurar a redefinição de senha self-service para usuários no Azure AD?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Como [preparar usuários para] registrar [suas] informações de segurança para o Azure Active Directory](https://youtu.be/gXuh0XS18wA) |
| Cursos online|[Gerenciamento de identidades no Microsoft Azure Active Directory](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) Use o SSPR para dar aos seus usuários uma experiência moderna e protegida. Consulte principalmente o módulo "[Gerenciamento de usuários e grupos do Azure Active Directory](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)". |
|Cursos pagos da Pluralsight |[Os problemas de gerenciamento de identidade e acesso](https://www.pluralsight.com/courses/identity-access-management-issues) Saiba mais sobre IAM e os problemas de segurança que você deve conhecer em sua organização. Consulte principalmente o módulo "Outros métodos de autenticação".|
| |[Introdução ao Microsoft Enterprise Mobility Suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Conheça as melhores práticas para estender ativos locais para a nuvem de uma maneira que permita autenticação, autorização, criptografia e uma experiência móvel protegida. Consulte principalmente o módulo "Como configurar recursos avançados do Microsoft Azure Active Directory Premium".
|Tutoriais |[Concluir uma distribuição piloto da redefinição de senha self-service do Azure AD](./tutorial-enable-sspr.md) |
| |[Habilitar o write-back de senha](./tutorial-enable-sspr-writeback.md) |
| |[Redefinição de senha do Azure AD na tela de logon para Windows 10](./howto-sspr-windows.md) |
| Perguntas frequentes|[Perguntas frequentes sobre o gerenciamento de senhas](./active-directory-passwords-faq.md) |


### <a name="solution-architecture"></a>Arquitetura da solução

O exemplo a seguir descreve a arquitetura da solução de redefinição de senha para ambientes híbridos comuns.

![diagrama da arquitetura da solução](./media/howto-sspr-deployment//solutions-architecture.png)

Descrição do fluxo de trabalho

Para redefinir a senha, os usuários acessam o [portal de redefinição de senha](https://aka.ms/sspr). Eles devem verificar o método ou métodos de autenticação registrados anteriormente para provar sua identidade. Se eles redefinirem a senha com êxito, eles iniciarão o processo de redefinição.

* Para usuários somente de nuvem, o SSPR armazena a nova senha no Azure AD. 

* Para usuários híbridos, o SSPR grava novamente a senha para o Active Directory local por meio do serviço Azure AD Connect. 

Observação: Para usuários que têm [PHS (sincronização de hash de senha)](../hybrid/whatis-phs.md) desabilitado, o SSPR armazena as senhas somente no Active Directory local.

### <a name="best-practices"></a>Práticas recomendadas

Você pode ajudar os usuários a se registrarem rapidamente implantando o SSPR com outro aplicativo ou serviço popular na organização. Essa ação vai gerar um grande volume de entradas e orientar o registro.

Antes de implantar o SSPR, você pode optar por determinar o número e o custo médio de cada chamada de redefinição de senha. Você pode usar esses dados após a implantação para mostrar o valor que o SSPR está trazendo para a organização.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Habilitar o registro combinado para SSPR e MFA

A Microsoft recomenda que as organizações habilitem a experiência de registro combinada para SSPR e autenticação multifator. Quando você habilita essa experiência de registro combinada, os usuários só precisam selecionar suas informações de registro uma vez para habilitar os dois recursos.

A experiência de registro combinada não exige que as organizações habilitem o SSPR e a autenticação multifator do Azure AD. O registro combinado fornece às organizações uma melhor experiência do usuário. Para obter mais informações, consulte [Registro de informações de segurança combinadas](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>Planejar o projeto de implantação

Considere suas necessidades organizacionais enquanto determina a estratégia para essa implantação em seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolva os participantes certos

Quando os projetos de tecnologia falham, eles normalmente fazem isso devido a expectativas incompatíveis com o impacto, os resultados e as responsabilidades. Para evitar essas armadilhas, [verifique se você está participando dos stakeholders certos](../fundamentals/active-directory-deployment-plans.md) e que as funções de stakeholder no projeto sejam bem compreendidas ao documentar os stakeholders, a entrada e as responsabilidades no projeto.

#### <a name="required-administrator-roles"></a>Funções de administrador necessárias


| Função corporativa/persona| Função do Azure AD (se necessário) |
| - | - |
| Suporte técnico nível 1| Administrador de senha |
| Suporte técnico nível 2| Administrador de usuários |
| Administrador do SSPR| Administrador global |


### <a name="plan-communications"></a>Planejar comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Você deve se comunicar proativamente com seus usuários, informando como sua experiência será alterada, quando ela será alterada e como obter suporte se eles tiverem problemas. Examine os [Materiais de distribuição de redefinição de senha self-service no centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=56768) para obter ideias sobre como planejar sua estratégia de comunicação do usuário final.

### <a name="plan-a-pilot"></a>Planejar um piloto

Recomendamos que a configuração inicial do SSPR esteja em um ambiente de teste. Comece com um grupo piloto habilitando o SSPR para um subconjunto de usuários em sua organização. Consulte [Melhores práticas para obter um piloto](../fundamentals/active-directory-deployment-plans.md).

Para criar um grupo, consulte como [criar um grupo e adicionar membros no Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md). 

## <a name="plan-configuration"></a>Configuração do plano

As configurações a seguir são necessárias para habilitar o SSPR com os valores recomendados.

| Área | Configuração | Valor |
| --- | --- | --- |
| **Propriedades de SSPR** | Redefinição de senha self-service habilitada | Grupo **selecionado** para o piloto/**Todos** para produção |
| **Métodos de autenticação** | Métodos de autenticação necessários para registro | Sempre 1 mais do que o necessário para redefinir |
|   | Métodos de autenticação necessários para redefinir | Um ou dois |
| **Registro** | Exigir que os usuários se registrem ao entrar | Sim |
|   | Número de dias antes que os usuários precisem reconfirmar suas informações de autenticação | 90 – 180 dias |
| **Notificações** | Notificar os usuários de redefinições de senha | Sim |
|   | Notificar todos os administradores quando outros administradores redefinirem suas próprias senhas | Sim |
| **Personalização** | Personalizar link de assistência técnica | Sim |
|   | URL ou email de assistência técnica personalizado | Site de suporte ou endereço de email |
| **Integração local** | Write-back de senhas para o AD local | Sim |
|   | Permitir que os usuários desbloqueiem a conta sem redefinir a senha | Sim |

### <a name="sspr-properties"></a>Propriedades de SSPR

Ao habilitar o SSPR, escolha um grupo de segurança apropriado no ambiente piloto.

* Para impor o registro SSPR para todos, é recomendável usar a opção **Todos**.
* Caso contrário, selecione o grupo de segurança do Azure AD ou do AD apropriado.

### <a name="authentication-methods"></a>Métodos de autenticação

Quando o SSPR estiver habilitado, os usuários só poderão redefinir sua senha se tiverem dados presentes nos métodos de autenticação que o administrador tiver habilitado. Os métodos incluem telefone, notificação de aplicativo autenticador, perguntas de segurança etc. Para obter mais informações, consulte [O que são métodos de autenticação?](./concept-authentication-methods.md).

Recomendamos as seguintes configurações do método de autenticação:

* Defina os **Métodos de autenticação necessários para registro** em pelo menos um maior que o número necessário para a redefinição. Permitir várias autenticações dá aos usuários flexibilidade quando eles precisam ser redefinidos.

* Defina o **Número de métodos necessários para redefinir** como um nível apropriado para sua organização. Um deles requer o menor conflito, enquanto dois podem aumentar sua postura de segurança. 

Observação: O usuário deve ter os métodos de autenticação configurados em [Diretivas e restrições de senha no Azure Active Directory](./concept-sspr-policy.md).

### <a name="registration-settings"></a>Configurações de registro

Defina **Exigir que os usuários se registrem ao entrar** como **Sim**. Essa configuração exige que os usuários se registrem ao entrar, garantindo que todos os usuários estejam protegidos.

Defina o **Número de dias antes que os usuários sejam solicitados a reconfirmar suas informações de autenticação** entre **90** e **180** dias, a menos que sua organização tenha uma necessidade comercial para um período de tempo menor.

### <a name="notifications-settings"></a>Configurações de notificações

Defina **Notificar os usuários sobre redefinições de senha** e **Notificar todos os administradores quando outros administradores redefinirem suas senhas** como **Sim**. Selecionar **Sim** em ambos aumenta a segurança, garantindo que os usuários saibam quando sua senha é redefinida. Também garante que todos os administradores saibam quando um administrador altera uma senha. Se os usuários ou administradores receberem uma notificação e eles não tiverem iniciado a alteração, eles poderão relatar imediatamente um possível problema de segurança.

### <a name="customization-settings"></a>Configurações de personalização

É fundamental personalizar o email ou a URL da assistência técnica para garantir que os usuários que tenham problemas possam obter ajuda imediatamente. Defina essa opção como um endereço de email comum da assistência técnica ou página da Web com a qual os usuários estão familiarizados. 

Para obter mais informações, consulte [Personalizar a funcionalidade do Azure AD para redefinição de senha self-service](./howto-sspr-customization.md).

### <a name="password-writeback"></a>Write-back de senha

O **write-back de senha** é habilitado com [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) e grava redefinições de senha na nuvem de volta para um diretório local existente em tempo real. Para obter mais informações, consulte [O que é write-back de senha?](./concept-sspr-writeback.md)

Recomendamos as seguintes configurações:

* Verifique se **Write-back de senhas no AD local** está definido como **Sim**. 
* Defina **Permitir que os usuários desbloqueiem a conta sem redefinir a senha** como **Sim**.

Por padrão, o Azure Active Directory desbloqueia contas quando ele executa uma redefinição de senha.

### <a name="administrator-password-setting"></a>Configuração de senha de administrador

As contas de administrador têm permissões elevadas. Os administradores de domínio ou corporativos locais não podem redefinir suas senhas por meio de SSPR. As contas de administrador local têm as seguintes restrições:

* só é possível alterar a senha em seu ambiente local.
* nunca pode usar as perguntas e respostas secretas como um método para redefinir sua senha.

Recomendamos que você não sincronize suas contas de administrador de Active Directory local com o Azure AD.

### <a name="environments-with-multiple-identity-management-systems"></a>Ambientes com vários sistemas de gerenciamento de identidade

Alguns ambientes têm vários sistemas de gerenciamento de identidade. Os gerenciadores de identidade locais, como Oracle AM e SiteMinder, exigem sincronização com o AD para senhas. Você pode fazer isso usando uma ferramenta como o serviço de notificação de alteração de senha (PCNS) com o MIM (Microsoft Identity Manager). Para encontrar informações sobre esse cenário mais complexo, consulte o artigo [Implantar o serviço de notificação de alteração de senha do MIM em um controlador de domínio](/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-testing-and-support"></a>Teste e suporte do plano

Em cada estágio de sua implantação de grupos piloto iniciais por toda a organização, verifique se os resultados são os esperados.

### <a name="plan-testing"></a>Teste de plano

Para garantir que sua implantação funcione conforme o esperado, planeje um conjunto de casos de teste para validar a implementação. Para avaliar os casos de teste, você precisa de um usuário de teste que não seja administrador com uma senha. Se você precisar criar um usuário, consulte [Adicionar novos usuários ao Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).

A tabela a seguir inclui cenários de teste úteis que você pode usar para documentar os resultados esperados de suas organizações com base em suas políticas.
<br>


| Caso comercial| Resultados esperados |
| - | - |
| O portal do SSPR pode ser acessado de dentro da rede corporativa| Determinado pela sua organização |
| O portal do SSPR pode ser acessado de fora da rede corporativa| Determinado pela sua organização |
| Redefinir a senha do usuário do navegador quando o usuário não estiver habilitado para redefinição de senha| O usuário não consegue acessar o fluxo de redefinição de senha |
| Redefinir a senha do usuário do navegador quando o usuário não tiver se registrado para redefinição de senha| O usuário não consegue acessar o fluxo de redefinição de senha |
| O usuário entra quando imposto para fazer o registro de redefinição de senha| Solicita que o usuário registre informações de segurança |
| O usuário entra quando o registro de redefinição de senha é concluído| Solicita que o usuário registre informações de segurança |
| O portal do SSPR é acessível quando o usuário não tem uma licença| Está acessível |
| Redefinir a senha do usuário da tela de bloqueio do dispositivo ingressado no Azure AD do Windows 10 ou híbrido do Azure AD| O usuário pode redefinir a senha |
| Os dados de registro e de uso do SSPR estão disponíveis para os administradores quase em tempo real| Está disponível por meio de logs de auditoria |

Você também pode consultar [Concluir uma reversão do piloto de redefinição de senha self-service do Azure AD](./tutorial-enable-sspr.md). Neste tutorial, você habilitará uma distribuição piloto de SSPR em sua organização e testará usando uma conta que não seja de administrador.

### <a name="plan-support"></a>Planejar suporte

Embora o SSPR normalmente não crie problemas de usuário, é importante preparar a equipe de suporte para lidar com problemas que possam surgir. Embora um administrador possa redefinir a senha para os usuários finais por meio do portal do Azure AD, é melhor ajudar a resolver o problema por meio de um processo de suporte self-service.

Para garantir o sucesso da sua equipe de suporte, é possível criar perguntas frequentes com base em perguntas que você recebe de seus usuários. Veja alguns exemplos:

| Cenários| Descrição |
| - | - |
| O usuário não tem nenhum método de autenticação registrado disponível| Um usuário está tentando redefinir sua senha, mas não tem nenhum dos métodos de autenticação que ele registrou disponível (exemplo: ele deixou seu telefone celular em casa e não pode acessar o e-mail) |
| O usuário não está recebendo um texto ou uma chamada em seu escritório ou telefone celular| Um usuário está tentando verificar sua identidade por meio de texto ou chamada, mas não está recebendo um texto/chamada. |
| O usuário não pode acessar o portal de redefinição de senha| Um usuário deseja redefinir sua senha, mas não está habilitado para redefinição de senha e não pode acessar a página para atualizar senhas. |
| O usuário não pode definir uma nova senha| Um usuário conclui a verificação durante o fluxo de redefinição de senha, mas não pode definir uma nova senha. |
| O usuário não vê um link Redefinir Senha em um dispositivo Windows 10| Um usuário está tentando redefinir a senha da tela de bloqueio do Windows 10, mas o dispositivo não está associado ao Azure AD ou a política de dispositivo do Intune não está habilitada |

### <a name="plan-rollback"></a>Reversão do plano

Para reverter a implantação:

* para um único usuário, remova o usuário do grupo de segurança 

* para um grupo, remova o grupo da configuração do SSPR

* Para todos, desabilite SSPR para o locatário do Azure AD

## <a name="deploy-sspr"></a>Implantar SSPR

Antes de implantar, verifique se você fez o seguinte:

1. Criou e iniciou a execução do [plano de comunicação](#plan-communications).

1. Determinou as [definições de configuração](#plan-configuration) apropriadas.

1. Identificou usuários e grupos para os ambientes [piloto](#plan-a-pilot) e de produção.

1. [Determinou os parâmetros de configuração](#plan-configuration) para registro e self-service.

1. [Configurou o write-back de senha](#password-writeback) caso tenha um ambiente híbrido.


**Agora você está pronto para implantar o SSPR!**

Consulte [Habilitar a redefinição de senha self-service](./tutorial-enable-sspr.md#enable-self-service-password-reset) para obter instruções passo a passo completas sobre como configurar as seguintes áreas.

1. [Métodos de autenticação](./concept-authentication-methods.md)

1. [Configurações de registro](./concept-registration-mfa-sspr-combined.md)

1. [Configurações de notificações](#notifications-settings)

1. [Configurações de personalização](./howto-sspr-customization.md)

1. [Integração local](./tutorial-enable-sspr-writeback.md)

### <a name="enable-sspr-in-windows"></a>Habilitar SSPR no Windows
Para computadores que executam o Windows 7, 8, 8.1 e 10, você pode [permitir que os usuários redefinam sua senha na tela de entrada do Windows](./howto-sspr-windows.md)

## <a name="manage-sspr"></a>Gerenciar SSPR

O Azure AD pode fornecer informações adicionais sobre o desempenho do SSPR por meio de auditorias e relatórios.

### <a name="password-management-activity-reports"></a>Relatórios de atividade de gerenciamento de senhas 

Você pode usar relatórios predefinidos em portal do Azure para medir o desempenho do SSPR. Se você estiver licenciado adequadamente, também poderá criar consultas personalizadas. Para obter mais informações, consulte [Opções de relatório para gerenciamento de senhas do Azure AD](./howto-sspr-reporting.md)

> [!NOTE]
>  Você deve ser um [administrador global](../roles/permissions-reference.md) e deve aceitar que esses dados sejam coletados para sua organização. Para aceitar, você deve visitar a guia Relatório ou os logs de auditoria no portal do Azure pelo menos uma vez. Até lá, os dados não são coletados para sua organização.

Os logs de auditoria para registro e redefinição de senha estão disponíveis por 30 dias. Se a auditoria de segurança dentro de sua empresa exigir maior retenção, os logs precisarão ser exportados e consumidos em uma ferramenta SIEM, como [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk ou ArcSight.

![Captura de tela de relatório do SSPR](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Métodos de autenticação – Uso e insights

O [uso e as informações](./howto-authentication-methods-activity.md) permitem que você entenda como os métodos de autenticação para recursos como o Azure ad MFA e SSPR estão trabalhando em sua organização. Essa funcionalidade de relatório fornece à sua organização os meios para entender quais métodos se registram e como usá-los.

### <a name="troubleshoot"></a>Solucionar problemas

* Consulte [Solucionar problemas de redefinição de senha self-service](./troubleshoot-sspr.md) 

* Siga [Perguntas frequentes sobre gerenciamento de senhas](./active-directory-passwords-faq.md) 

### <a name="helpful-documentation"></a>Documentação útil

* [Quais são os métodos de autenticação?](./concept-authentication-methods.md)

* [Como funciona: Redefinição de senha self-service do Azure AD?](./concept-sspr-howitworks.md)

* [Personalizar a funcionalidade do Azure AD para redefinição de senha self-service](./howto-sspr-customization.md)

* [Políticas e restrições de senha do Active Directory do Azure](./concept-sspr-policy.md)

* [O que é o Write-back de senha?](./concept-sspr-writeback.md)

## <a name="next-steps"></a>Próximas etapas

* Para começar a implantar o SSPR, consulte [Habilitar a redefinição de senha self-service do Azure AD](tutorial-enable-sspr.md)

* [Considere implementar a proteção de senha do Azure AD](./concept-password-ban-bad.md)

* [Considere implementar o bloqueio inteligente do Azure AD](./howto-password-smart-lockout.md)