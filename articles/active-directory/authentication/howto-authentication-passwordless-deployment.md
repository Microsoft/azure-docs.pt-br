---
title: Planejar uma implantação de autenticação sem senha com o Azure AD
description: Saiba como planejar e implantar uma implementação de autenticação com senha Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: justinha
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: a786907c5c954aa45de266b6d92dd47867a8445d
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743608"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Planejar uma implantação de autenticação com senha no Azure Active Directory

> [!NOTE]
> Para criar uma versão offline desse plano de implantação, use a funcionalidade imprimir para PDF do navegador.

A maioria dos ataques cibernéticos começa com um nome de usuário e senha comprometidos. As organizações tentam combater a ameaça exigindo que os usuários usem uma das seguintes abordagens:

- Senhas longas
- Senhas complexas
- Alterações frequentes de senha
- Autenticação Multifator (MFA)

A pesquisa da Microsoft [mostra](https://aka.ms/passwordguidance) que esses esforços irritam os usuários e impulsionam os custos de suporte. Para obter mais informações, consulte [seu PA $ $Word não importa](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

### <a name="benefits-of-passwordless-authentication"></a>Benefícios da autenticação com senha

- **Maior segurança**. Reduza o risco de ataques de phishing e de irrigação de senha removendo senhas como uma superfície de ataque.
-  **Melhor experiência do usuário**. Forneça aos usuários uma maneira conveniente de acessar dados de qualquer lugar. Forneça acesso fácil a aplicativos e serviços, como Outlook, OneDrive ou Office, enquanto estiver móvel.
-  **Ideias robustas**. Obter informações sobre a atividade de usuários sem senha com log e auditoria robustos.

Com senha, a senha é substituída por algo que você tem, além de algo que você conhece. Por exemplo, o Windows Hello para empresas pode usar um gesto biométrico como uma face ou impressão digital ou um PIN específico do dispositivo que não é transmitido por uma rede.

## <a name="passwordless-authentication-methods"></a>Métodos de autenticação com senha
A Microsoft oferece três opções de autenticação com senha que abrangem muitos cenários. Esses métodos podem ser usados em conjunto:

- O [Windows Hello para empresas](./concept-authentication-passwordless.md) é melhor para os usuários em seus computadores Windows dedicados.
- A entrada de chave de segurança com [chaves de segurança FIDO2](./concept-authentication-passwordless.md) é especialmente útil para usuários que se conectam a computadores compartilhados, como quiosques, em situações em que o uso de telefones é restrito e para identidades altamente privilegiadas.
- A entrada pelo telefone com o [aplicativo Microsoft Authenticator](./concept-authentication-passwordless.md) é útil para fornecer uma opção sem senha aos usuários com dispositivos móveis. O aplicativo autenticador transforma qualquer telefone iOS ou Android em uma credencial forte e sem senha, permitindo que os usuários entrem em qualquer plataforma ou navegador. Os usuários entram obtendo uma notificação para seu telefone, correspondendo a um número exibido na tela para aquele em seu telefone e, em seguida, usando seus dados biométricos ou PIN para confirmar.

### <a name="passwordless-authentication-scenarios"></a>Cenários de autenticação com senha

Os métodos de autenticação com senha da Microsoft permitem diferentes cenários. Considere suas necessidades organizacionais, pré-requisitos e os recursos de cada método de autenticação para selecionar sua estratégia de autenticação com senha. Recomendamos que todas as organizações que usam dispositivos Windows 10 usem o Windows Hello para empresas. Em seguida, adicione uma entrada do telefone (com o aplicativo Microsoft Authenticator) ou chaves de segurança para cenários adicionais.

| Cenário | Autenticação por telefone | Chaves de segurança | Windows Hello para Empresas |
| --- | --- | --- | --- |
| **Entrada do computador**: <br> Do dispositivo Windows 10 atribuído | **Não** | **Sim** <br> Com biométrica, PIN | **Sim**<br>com reconhecimento biométrico e PIN |
| **Entrada do computador**: <br> Do dispositivo Windows 10 compartilhado | **Não** | **Sim** <br> Com biométrica, PIN  | **Não** |
| **Entrada do aplicativo Web**: <br>de um computador dedicado ao usuário | **Sim** | **Sim** <br> O logon único fornecido para os aplicativos está habilitado pela entrada do computador | **Sim**<br> O logon único fornecido para os aplicativos está habilitado pela entrada do computador |
| **Entrada do aplicativo Web**: <br> de um dispositivo móvel ou não Windows | **Sim** | **Não** | **Não** |
| **Entrada do computador**: <br> Computador não Windows | **Não** | **Não** | **Não** |

Para obter informações sobre como selecionar o melhor método para sua organização, consulte [decidindo um método com senha](./concept-authentication-passwordless.md#choose-a-passwordless-method).

## <a name="prerequisites"></a>Pré-requisitos

As organizações devem atender aos seguintes pré-requisitos antes de iniciar uma implantação com senha:

| Pré-requisito | Aplicativo autenticador | Chaves de segurança do FIDO2 |
| --- | --- | --- |
| O [registro combinado para a autenticação multifator do Azure AD e a SSPR (redefinição de senha de autoatendimento)](howto-registration-mfa-sspr-combined.md) está habilitado | √ | √ |
| [Os usuários podem executar a autenticação multifator do Azure AD](howto-mfa-getstarted.md) | √ | √ |
| [Os usuários se registraram para a autenticação multifator do Azure AD e o SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Os usuários registraram seus dispositivos móveis em Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 versão 1809 ou superior usando um navegador com suporte como Microsoft Edge ou Mozilla Firefox <br> (versão 67 ou superior). <br> *A Microsoft recomenda a versão 1903 ou superior para suporte nativo*. |   | √ |
| Chaves de segurança FIDO2 compatíveis. Verifique se você está usando um dispositivo de segurança FIDO2 [testado e verificado pela Microsoft](./concept-authentication-passwordless.md) ou outro dispositivo de segurança FIDO2 compatível. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Pré-requisitos para o Windows Hello para empresas

Os pré-requisitos para o Windows Hello são altamente dependentes se você estiver implantando em uma configuração local, híbrida ou somente na nuvem. Para obter mais informações, consulte a [lista completa de pré-requisitos para o Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-ad-multi-factor-authentication"></a>Autenticação multifator do Azure AD

Os usuários registram seu método com senha como parte do fluxo de registro da autenticação multifator do Azure AD. A autenticação multifator com um nome de usuário e senha junto com outro método registrado pode ser usada como um fallback, caso eles não possam usar seu telefone ou chave de segurança em alguns cenários.

### <a name="licensing"></a>Licenciamento 
Não há nenhum custo adicional para autenticação sem senha, embora alguns pré-requisitos possam exigir uma assinatura premium. Para obter informações detalhadas sobre recursos e licenciamento na [página de licenciamento do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). 

## <a name="develop-a-plan"></a>Desenvolver um plano

Considere suas necessidades de negócios e os casos de uso para cada método de autenticação. Em seguida, selecione o método que melhor atenda às suas necessidades.

### <a name="use-cases"></a>Casos de uso

A tabela a seguir descreve os casos de uso a serem implementados durante este projeto.

| Área | Descrição |
| --- | --- |
| **Acesso** | A entrada sem senha está disponível em um dispositivo corporativo ou pessoal dentro ou fora da rede corporativa. |
| **Auditoria** | Os dados de uso estão disponíveis para que os administradores sejam auditados quase em tempo real. <br> Os dados de uso são baixados em sistemas corporativos pelo menos a cada 29 dias ou a ferramenta SIEM é usada. |
| **Governança** | O ciclo de vida das atribuições de usuário ao método de autenticação apropriado e aos grupos associados é definido e monitorado. |
| **Segurança** | O acesso ao método de autenticação apropriado é controlado por meio de atribuições de usuário e grupo. <br> Somente usuários autorizados podem usar a entrada sem senha. |
| **Desempenho** | As linhas do tempo de propagação de atribuição de acesso são documentadas e monitoradas. <br> As horas de entrada são medidas para facilitar o uso. |
| **Experiência do Usuário** | Os usuários estão cientes da compatibilidade com a mobilidade. <br> Os usuários podem configurar a entrada sem senha do aplicativo autenticador. |
| **Suporte** | Os usuários estão cientes de como encontrar suporte para problemas de entrada sem senha. |

### <a name="engage-the-right-stakeholders"></a>Envolva os participantes certos

Quando os projetos de tecnologia falham, normalmente é devido a expectativas incompatíveis com o impacto, os resultados e as responsabilidades. Para evitar essas armadilhas, [Verifique se você está participando dos participantes certos](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) e se as funções de Stakeholder no projeto são bem compreendidas.

### <a name="plan-communications"></a>Planejar comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique-se proativamente como a experiência dos usuários será alterada, quando ele será alterado e como obter suporte se eles tiverem problemas.

Suas comunicações com os usuários finais devem incluir as seguintes informações:

- [Habilitando a experiência de registro de segurança combinada](howto-authentication-passwordless-phone.md)
- [Baixando o aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md)
- [Registrando no aplicativo Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Entrar com seu telefone](../user-help/user-help-auth-app-sign-in.md)

A Microsoft fornece [modelos de comunicação](https://aka.ms/mfatemplates)de autenticação multifator, [modelos de comunicação](https://www.microsoft.com/download/details.aspx?id=56768)Self-Service SSPR (redefinição de senha) e [documentação do usuário final](../user-help/security-info-setup-signin.md) para ajudar a rascunhar suas comunicações. Você pode enviar usuários para [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) a fim de que se registrem diretamente, selecionando os links de **Informações de Segurança** nessa página.

### <a name="plan-to-pilot"></a>Planejar para o piloto

Ao implantar a autenticação com senha, você deve primeiro habilitar um ou mais grupos pilotos. Você pode [criar grupos](../fundamentals/active-directory-groups-create-azure-portal.md) especificamente para essa finalidade. Adicione os usuários que farão parte do piloto para os grupos. Em seguida, habilite novos métodos de autenticação com senha para os grupos selecionados.

Os grupos podem ser sincronizados de um diretório local ou do Azure AD. Quando estiver satisfeito com os resultados do seu piloto, você poderá alternar a autenticação sem senha para todos os usuários.

Consulte [práticas recomendadas para um piloto](../fundamentals/active-directory-deployment-plans.md) na página planos de implantação.

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Planejar a autenticação sem senha com o aplicativo Microsoft Authenticator

O aplicativo Microsoft Authenticator é um download gratuito do Google Play ou da Apple App Store. [Saiba mais sobre como baixar o aplicativo Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Fazer com que os usuários baixem o aplicativo Microsoft Authenticator. e siga as instruções para habilitar a entrada pelo telefone. 

Ele transforma qualquer telefone iOS ou Android em uma credencial forte e com senha. Os usuários entram em qualquer plataforma ou navegador, obtendo uma notificação para seu telefone, correspondendo a um número exibido na tela para aquele em seu telefone e, em seguida, usando a biometria ou um PIN para confirmar. [Veja detalhes sobre como o aplicativo Microsoft Authenticator funciona](./concept-authentication-passwordless.md#microsoft-authenticator-app).

![entre com o aplicativo autenticador](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Considerações técnicas para o aplicativo Microsoft Authenticator

**Integração de AD FS** – quando um usuário habilita a Microsoft Authenticator credencial com senha, a autenticação para esse usuário assume o envio de uma notificação para aprovação. Os usuários em um locatário híbrido são impedidos de serem direcionados para o ADFS para entrada, a menos que selecionem "usar sua senha em vez disso". Esse processo também ignora qualquer política de acesso condicional local e fluxos de autenticação de passagem. No entanto, se um *login_hint* for especificado, o usuário será encaminhado para o ADFS e ignorará a opção de usar a credencial sem senha.

**Servidor de autenticação multifator do Azure ad** – usuários finais habilitados para autenticação multifator por meio do servidor Azure MFA local de uma organização podem criar e usar uma única credencial de entrada de telefone sem senha. Se o usuário tentar atualizar várias instalações (5 ou mais) do Microsoft Authenticator com a credencial, essa alteração poderá resultar em um erro.

**Registro de dispositivo** -para usar o aplicativo autenticador para autenticação com senha, o dispositivo deve ser registrado no locatário do Azure AD e não pode ser um dispositivo compartilhado. Um dispositivo só pode ser registrado em um único locatário. Esse limite significa que apenas uma conta corporativa ou de estudante tem suporte para a entrada pelo telefone usando o aplicativo autenticador.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>Planejar a autenticação sem senha com as chaves de segurança do FIDO2
Há três tipos de implantações de entrada sem senha disponíveis com chaves de segurança:

-    Azure Active Directory aplicativos Web em um navegador com suporte
-    Azure Active Directory dispositivos Windows 10 associados
-    Dispositivos Windows 10 ingressados Azure Active Directory híbridos (visualização)
     -    Fornece acesso a recursos baseados em nuvem e locais. Para obter mais informações sobre o acesso a recursos locais, consulte [SSO para recursos locais usando chaves FIDOP2](./howto-authentication-passwordless-security-key-on-premises.md)

Você deve habilitar **as chaves de segurança FIDO2 compatíveis**. A Microsoft anunciou as [principais parcerias com os principais fornecedores de FIDO2](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493).

**Para os aplicativos Web do Azure AD e dispositivos ingressados no Windows do Azure ad**:

-    Windows 10 versão 1809 ou superior usando um navegador com suporte como Microsoft Edge ou Mozilla Firefox (versão 67 ou superior). 
-    O Windows 10 versão 1809 dá suporte à entrada FIDO2 e pode exigir que o software do fabricante da chave FIDO2 seja implantado. Recomendamos que você use a versão 1903 ou posterior. 

**Para dispositivos ingressados no domínio do Azure Active Directory híbrido**: 
-    Windows 10 versão 2004 ou posterior
-    Servidores de domínio totalmente corrigidos que executam o Windows Server 2016 ou 2019.
-    Versão mais recente do Azure AD Connect

Para obter uma lista completa dos requisitos, consulte [Habilitar entrada de chave de segurança sem senha para dispositivos Windows 10 com Azure Active Directory](./howto-authentication-passwordless-security-key-windows.md#requirements).


### <a name="security-key-life-cycle"></a>Ciclo de vida da chave de segurança

As chaves de segurança habilitam o acesso aos seus recursos e você deve planejar o gerenciamento desses dispositivos físicos.

1. **Distribuição de chaves**: planeje como provisionar chaves para sua organização. Você pode ter um processo de provisionamento centralizado ou permitir que os usuários finais adquiram chaves compatíveis com FIDO 2,0.
1. **Ativação de chave**: os usuários finais devem ativar a chave de segurança automaticamente. Os usuários finais registram suas chaves de segurança em [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) e habilitam o segundo fator (PIN ou biométrica) no primeiro uso.
1. **Desabilitando uma chave**: embora a funcionalidade de chave de segurança esteja no estágio de visualização, não há como um administrador remover uma chave de uma conta de usuário. O usuário deve removê-lo. Se uma chave for perdida ou roubada:
   1. Remova o usuário de qualquer grupo habilitado para autenticação com senha.
   1. Verifique se eles removeram a chave como um método de autenticação.
   1. Emitir uma nova chave. **Substituição de chave**: os usuários podem habilitar duas chaves de segurança ao mesmo tempo. Ao substituir uma chave de segurança, verifique se o usuário também removeu a chave que está sendo substituída.

### <a name="enable-windows-10-support"></a>Habilitar o suporte do Windows 10

Habilitar a entrada do Windows 10 usando chaves de segurança FIDO2 requer a habilitação da funcionalidade do provedor de credenciais no Windows 10. Escolha uma destas opções:

- [Habilitar provedor de credenciais com o Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - A implantação do Intune é a opção recomendada.
- [Habilitar o provedor de credenciais com um pacote de provisionamento](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Se a implantação do Intune não for possível, os administradores deverão implantar um pacote em cada computador para habilitar a funcionalidade do provedor de credenciais. A instalação do pacote pode ser executada por uma das seguintes opções:
      - Política de Grupo ou Configuration Manager
      - Instalação local em um computador com Windows 10
- [Habilitar provedor de credenciais com Política de Grupo](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - Somente com suporte para dispositivos ingressados no Azure AD híbrido.

#### <a name="enable-on-premises-integration"></a>Habilitar a integração local

Para habilitar o acesso a recursos locais, siga as etapas para [habilitar a entrada de chave de segurança sem senha para recursos locais (versão prévia)](howto-authentication-passwordless-security-key-on-premises.md).

> [!IMPORTANT]
> Essas etapas também devem ser concluídas para qualquer dispositivo ingressado no Azure AD híbrido para utilizar as chaves de segurança do FIDO2 para entrar no Windows 10.

### <a name="register-security-keys"></a>Registrar chaves de segurança

Os usuários devem registrar sua chave de segurança em cada um de seus Azure Active Directory máquinas com Windows 10 Unidas.

Para obter mais informações, consulte [registro de usuário e gerenciamento de chaves de segurança FIDO2](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).


## <a name="plan-auditing-security-and-testing"></a>Planejar a auditoria, a segurança e os testes
Planejar a auditoria que atenda às suas estruturas organizacionais e de conformidade é uma parte essencial da sua implantação.

### <a name="auditing-passwordless"></a>Auditando com senha

O Azure AD tem relatórios que fornecem informações técnicas e de negócios. Seus proprietários de aplicativos técnicos e de negócios assumem a propriedade e consomem esses relatórios com base nos requisitos da sua organização.

A seção métodos de **autenticação** no portal de Azure Active Directory é onde os administradores podem habilitar e gerenciar configurações para credenciais com senha.

O Azure AD adiciona entradas aos logs de auditoria quando:

- Um administrador faz alterações na seção métodos de autenticação.
- Um usuário faz qualquer tipo de alteração em suas credenciais dentro de Azure Active Directory.

A tabela a seguir fornece alguns exemplos de cenários de relatório típicos:

|   | gerenciar riscos | Aumentar a produtividade | Governança e conformidade |
| --- | --- | --- | --- |
| **Tipos de relatório** | Métodos de autenticação – usuários registrados para o registro de segurança combinado | Métodos de autenticação – usuários registrados para notificação de aplicativo | Entradas: revise quem está acessando o locatário e como |
| **Ações potenciais** | Usuários de destino ainda não registrados | Impulsionar a adoção de Microsoft Authenticator aplicativo ou chaves de segurança | Revogar o acesso ou impor políticas de segurança adicionais para administradores |

O **Azure ad mantém a maioria dos dados de auditoria por 30 dias** e disponibiliza os dados por meio do portal de administração do Azure ou da API para que você faça o download em seus sistemas de análise. Se você precisar de mais tempo de retenção, exportação e consumo de logs em uma ferramenta SIEM, como [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk ou a lógica do sumido. [Saiba mais sobre como exibir seus relatórios de acesso e uso](../reports-monitoring/overview-reports.md).

Os usuários podem registrar e gerenciar suas credenciais navegando até [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Esse link direciona os usuários para a experiência de gerenciamento de credenciais do usuário final que foi habilitada por meio da experiência combinada de registro de autenticação SSPR/multifator. O Azure AD registra o registro de dispositivos de segurança FIDO2 e altera os métodos de autenticação por usuários.

### <a name="plan-security"></a>Segurança do plano
Como parte desse plano de distribuição, a Microsoft recomenda que a autenticação com senha seja habilitada para todas as contas de administrador privilegiadas.

Quando os usuários habilitam ou desabilitam a conta em uma chave de segurança, ou redefinem o segundo fator para a chave de segurança em seus computadores com Windows 10, uma entrada é adicionada ao log de segurança e está sob as seguintes IDs de evento: *4670* e *5382*.

### <a name="plan-testing"></a>Teste de plano

Em cada estágio de sua implantação conforme os cenários e a adoção de teste, verifique se os resultados são os esperados.

#### <a name="testing-the-microsoft-authenticator-app"></a>Testando o aplicativo Microsoft Authenticator

Veja a seguir exemplos de casos de teste para autenticação sem senha com o aplicativo Microsoft Authenticator:

| Cenário | Resultados esperados |
| --- | --- |
| O usuário pode registrar Microsoft Authenticator aplicativo | O usuário pode registrar o aplicativo do aka.ms/mysecurityinfo |
| O usuário pode habilitar a entrada pelo telefone | Entrada pelo telefone configurada para a conta corporativa |
| O usuário pode acessar um aplicativo com entrada no telefone | O usuário passa pelo fluxo de entrada do telefone e atinge o aplicativo. |
| Testar o registro de entrada no telefone desligando Microsoft Authenticator entrada sem senha na tela métodos de autenticação no portal Azure Active Directory | Os usuários anteriormente habilitados não podem usar a entrada sem senha no Microsoft Authenticator. |
| Removendo a entrada pelo telefone do aplicativo Microsoft Authenticator | A conta de trabalho não está mais disponível no Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Testando chaves de segurança

Veja a seguir exemplos de casos de teste para autenticação sem senha com chaves de segurança.

**Conexão FIDO sem senha para Azure Active Directory dispositivos Windows 10 associados**

| Cenário | Resultados esperados |
| --- | --- |
| O usuário pode registrar o dispositivo FIDO2 (1809) | O usuário pode registrar o dispositivo FIDO2 usando as configurações > contas > opções de entrada > chave de segurança |
| O usuário pode redefinir o dispositivo FIDO2 (1809) | O usuário pode redefinir o dispositivo FIDO2 usando o software do fabricante |
| O usuário pode entrar com o dispositivo FIDO2 (1809) | O usuário pode selecionar a chave de segurança na janela de entrada e entrar com êxito. |
| O usuário pode registrar o dispositivo FIDO2 (1903) | O usuário pode registrar o dispositivo FIDO2 em configurações > contas > opções de entrada > chave de segurança |
| O usuário pode redefinir o dispositivo FIDO2 (1903) | O usuário pode redefinir o dispositivo FIDO2 nas configurações > contas > opções de entrada > chave de segurança |
| O usuário pode entrar com o dispositivo FIDO2 (1903) | O usuário pode selecionar a chave de segurança na janela de entrada e entrar com êxito. |

**FIDO sem senha para aplicativos Web do Azure AD**

| Cenário | Resultados esperados |
| --- | --- |
| O usuário pode registrar o dispositivo FIDO2 em aka.ms/mysecurityinfo usando o Microsoft Edge | O registro deve ter sucesso |
| O usuário pode registrar o dispositivo FIDO2 em aka.ms/mysecurityinfo usando o Firefox | O registro deve ter sucesso |
| O usuário pode entrar no OneDrive online usando o dispositivo FIDO2 usando o Microsoft Edge | A entrada deve ter sucesso |
| O usuário pode entrar no OneDrive online usando o dispositivo FIDO2 usando o Firefox | A entrada deve ter sucesso |
| Teste revertendo o registro de dispositivo FIDO2 desativando as chaves de segurança FIDO2 na janela método de autenticação no portal de Azure Active Directory | Os usuários serão solicitados a entrar usando sua chave de segurança. Os usuários entrarão com êxito e um erro será exibido: "a política da empresa requer que você use um método diferente para entrar". Os usuários devem ser capazes de selecionar um método diferente e entrar com êxito. Feche a janela e entre novamente para verificar se elas não veem a mesma mensagem de erro. |

### <a name="plan-for-rollback"></a>Planejar a reversão

Embora a autenticação sem senha seja um recurso leve com impacto mínimo sobre os usuários finais, pode ser necessário reverter.

Reverter requer que o administrador entre no portal de Azure Active Directory, selecione os métodos de autenticação forte desejados e altere a opção Habilitar para **não**. Esse processo desativa a funcionalidade de senha para todos os usuários.

Usuários que já registraram dispositivos de segurança FIDO2 são solicitados a usar o dispositivo de segurança na próxima entrada e, em seguida, ver o seguinte erro:

![Escolha uma maneira diferente de entrar](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>Implantar e solucionar problemas de autenticação com senha

Siga as etapas alinhadas ao método escolhido abaixo.

### <a name="required-administrative-roles"></a>Funções administrativas necessárias

| Função do Azure AD | Descrição |
| --- | --- |
| Administrador global|Função menos privilegiada capaz de implementar a experiência de registro combinada. |
| Administrador de Autenticação | Função menos privilegiada capaz de implementar e gerenciar métodos de autenticação. |
| Usuário | Função com privilégios mínimos para configurar o aplicativo autenticador no dispositivo ou para registrar o dispositivo de chave de segurança para entrada na Web ou no Windows 10. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Implantar a entrada pelo telefone com o aplicativo Microsoft Authenticator

Siga as etapas no artigo habilitar a [entrada sem senha com o aplicativo Microsoft Authenticator](howto-authentication-passwordless-phone.md) para habilitar o aplicativo Microsoft Authenticator como um método de autenticação sem senha em sua organização.

### <a name="deploy-fido2-security-key-sign-in"></a>Implantar entrada de chave de segurança FIDO2

Siga as etapas descritas no artigo [habilitar a entrada de chave de segurança sem senha para o Azure ad](howto-authentication-passwordless-security-key.md) para habilitar as chaves de segurança FIDO2 como métodos de autenticação sem senha.

### <a name="troubleshoot-phone-sign-in"></a>Solucionar problemas de entrada pelo telefone

| Cenário | Solução |
| --- | --- |
| O usuário não pode executar o registro combinado. | Verifique se o [registro combinado](concept-registration-mfa-sspr-combined.md) está habilitado. |
| O usuário não pode habilitar o aplicativo autenticador de entrada do telefone. | Verifique se o usuário está no escopo para implantação. |
| O usuário não está no escopo para autenticação sem senha, mas é apresentado com a opção de entrada sem senha, que não pode ser concluída. | Esse cenário ocorre quando o usuário habilitou a entrada pelo telefone no aplicativo antes da criação da política. <br> *Para habilitar a entrada*: Adicione o usuário ao escopo de usuários habilitados para entrada sem senha. <br> *Para bloquear a entrada*: faça com que o usuário remova a credencial desse aplicativo. |

### <a name="troubleshoot-security-key-sign-in"></a>Solucionar problemas de entrada de chave de segurança

| Cenário | Solução |
| --- | --- |
| O usuário não pode executar o registro combinado. | Verifique se o [registro combinado](concept-registration-mfa-sspr-combined.md) está habilitado. |
| O usuário não pode adicionar uma chave de segurança em suas [configurações de segurança](https://aka.ms/mysecurityinfo). | Verifique se [as chaves de segurança](howto-authentication-passwordless-security-key.md) estão habilitadas. |
| O usuário não pode adicionar a chave de segurança nas opções de entrada do Windows 10. | [Verifique se as chaves de segurança para entrar no Windows](./concept-authentication-passwordless.md) |
| **Mensagem de erro**: detectamos que este navegador ou sistema operacional não dá suporte a chaves de segurança FIDO2. | Os dispositivos de segurança FIDO2 sem senha só podem ser registrados em navegadores com suporte (Microsoft Edge, Firefox versão 67) no Windows 10 versão 1809 ou superior. |
| **Mensagem de erro**: a política da sua empresa requer que você use um método diferente para entrar. | Não se esqueça de que as chaves de segurança estão habilitadas no locatário. |
| O usuário não pode gerenciar minha chave de segurança no Windows 10 versão 1809 | A versão 1809 requer que você use o software de gerenciamento de chaves de segurança fornecido pelo fornecedor de chave FIDO2. Contate o fornecedor para obter suporte. |
| Acho que minha chave de segurança do FIDO2 pode ser defeituosa – como posso testá-la. | Navegue até [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/) , insira as credenciais para uma conta de teste, conecte a chave de segurança suspeita, selecione o **+** botão na parte superior direita da tela, clique em criar e passe pelo processo de criação. Se esse cenário falhar, o dispositivo poderá estar com defeito. |

## <a name="next-steps"></a>Próximas etapas

- [Habilitar chaves de segurança sem senha para entrar no Azure AD](howto-authentication-passwordless-security-key.md)
- [Habilitar a entrada sem senha com o aplicativo Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Saiba mais sobre o uso de métodos de autenticação & insights](howto-authentication-methods-usage-insights.md)