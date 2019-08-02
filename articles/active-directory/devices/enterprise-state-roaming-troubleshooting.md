---
title: Solucionando problemas de configurações do Enterprise State Roaming no Azure Active Directory | Microsoft Docs
description: Responde a algumas dúvidas que os administradores de TI podem ter sobre as configurações e a sincronização de dados do aplicativo.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tanning
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cceae17b06e8b631dd530b0408008a8222bccbf
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481865"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Solucionando problemas de configurações do Enterprise State Roaming no Azure Active Directory

Este tópico fornece informações sobre como diagnosticar e solucionar problemas com o Enterprise State Roaming, além de fornecer uma lista de problemas conhecidos.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="preliminary-steps-for-troubleshooting"></a>Etapas preliminares para a solução de problemas 

Antes de iniciar a solução de problemas, verifique se o usuário e o dispositivo foram configurados corretamente e se todos os requisitos do Enterprise State Roaming foram atendidos pelo dispositivo e pelo usuário. 

1. O Windows 10, com as atualizações mais recentes e a versão mínima 1511 (compilação de SO 10586 ou posterior), está instalado no dispositivo. 
1. O dispositivo é adicionado ao Azure AD ou ao Azure AD híbrido. Para obter mais informações, consulte [como colocar um dispositivo sob controle do Azure AD](overview.md).
1. Verifique se **Enterprise State Roaming** está habilitado para o locatário no Azure AD conforme descrito em [Para habilitar Enterprise State Roaming](enterprise-state-roaming-enable.md). Você pode habilitar roaming para todos os usuários ou apenas um grupo selecionado de usuários.
1. O usuário já deve ter recebido uma licença do Azure Active Directory Premium.  
1. O dispositivo deve ser reiniciado e o usuário deve entrar novamente para acessar recursos de Enterprise State Roaming.

## <a name="information-to-include-when-you-need-help"></a>Informações a serem incluídas quando precisar de ajuda
Se você não conseguir solucionar seu problema com as orientações abaixo, pode entrar em contato com nossos engenheiros de suporte. Ao entrar em contato com eles, inclua as seguintes informações:

* **Descrição geral do erro**: Há mensagens de erro vistas pelo usuário? Se não havia nenhuma mensagem de erro, descreva o comportamento inesperado observado em detalhes. Quais recursos estão habilitados para sincronização e o que o usuário espera sincronizar? Muitos recursos não estão sendo sincronizados ou apenas um?
* **Usuários afetados** – a sincronização está funcionando/falhando para um usuário ou vários? Quantos dispositivos estão envolvidos por usuário? Nenhum deles está sincronizando ou alguns estão e outros não?
* **Informações sobre o usuário** – qual identidade o usuário está usando para entrar no dispositivo? Como o usuário está entrando no dispositivo? Eles são parte de um grupo de segurança selecionado autorizado a fazer a sincronização? 
* **Informações sobre o dispositivo** – esse dispositivo está adicionado ao Azure AD ou ao domínio? Em que compilação o dispositivo está? Quais são as atualizações mais recentes?
* **Data/ hora/ fuso horário** - Quais eram a data e o horário exatos em que você viu o erro (inclua o fuso horário)?

A inclusão dessas informações nos ajudará a resolver seu problema o mais rápido possível.

## <a name="troubleshooting-and-diagnosing-issues"></a>Solução de problemas e diagnósticos
Esta seção fornece sugestões sobre como solucionar e diagnosticar problemas relacionados ao Enterprise State Roaming.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Verifique a sincronização e a página de configurações "Sincronize suas configurações" 

1. Após associar seu PC com Windows 10 a um domínio que esteja configurado para permitir o Enterprise State Roaming, entre com sua conta do trabalho. Vá para **Configurações** > **Contas** > **Sincronizar suas configurações** e confirme se a sincronização e as configurações individuais estão ativadas e se a parte superior da página Configurações indica que você está sincronizado com sua conta do trabalho. Confirme se a mesma conta também é usada como sua conta de login em **Configurações** > **Contas** > **Suas informações**. 
1. Verifique se a sincronização funciona em várias máquinas fazendo algumas alterações na máquina original, como mover a barra de tarefas para o lado direito ou superior da tela. Observe a alteração ser propagada para o segundo computador em cinco minutos. 

   * Bloquear e desbloquear a tela (Win + L) pode ajudar a disparar uma sincronização.
   * Você deve entrar com a mesma conta nos dois computadores para que a sincronização funcione, pois o Enterprise State Roaming está associado à conta de usuário e não à conta do computador.

**Problema em potencial**: se os controles na página **Configurações** não estiverem disponíveis e você visualizar a mensagem "Alguns recursos do Windows só estarão disponíveis se você estiver usando uma conta Microsoft ou uma conta corporativa". Esse problema pode surgir para dispositivos que tenham sido configurados para serem adicionados ao domínio e registrados no Azure AD, mas o dispositivo ainda não foi autenticado com êxito para o Azure AD. Uma possível causa é que a política do dispositivo deve ser aplicada, mas essa aplicação ocorre de maneira assíncrona e pode ser atrasada por algumas horas. 

### <a name="verify-the-device-registration-status"></a>Verificar o status de registro do dispositivo

O Enterprise State Roaming requer que o dispositivo seja registrado com o AD do Azure. Embora não seja específico ao Enterprise State Roaming, seguir as instruções abaixo poderá ajudar a confirmar se o Windows 10 Client está registrado, bem como confirmar a impressão digital, a URL das configurações do Azure AD, o status do NGC e outras informações.

1. Abra o prompt de comando sem privilégios elevados. Para fazer isso no Windows, abra o inicializador de Execução (Win + R) e digite “cmd” para abrir.
1. Depois de aberto o prompt de comando, digite “*dsregcmd.exe /status*”.
1. Para o resultado esperado, o valor do campo **AzureAdJoined** deve ser “YES”, o valor do campo **WamDefaultSet** deve ser “YES” e o valor do campo **WamDefaultGUID** deve ser um GUID com “(AzureAd)” no final.

**Problema em potencial**: o valor do campo **WamDefaultSet** e **AzureAdJoined** é "NÃO”, o dispositivo foi associado ao domínio e registrado com o AD do Azure AD e o dispositivo não sincroniza. Se isso estiver sendo exibido, pode ser necessário que o dispositivo aguarde até que a política seja aplicada ou a autenticação do dispositivo falhou ao se conectar ao AD do Azure. O usuário pode precisar aguardar algumas horas para que a política a seja aplicada. Outras etapas de solução de problemas podem incluir repetir o registro automático se desconectando e entrando novamente ou iniciar a tarefa no Agendador de tarefas. Em alguns casos, executar "*dsregcmd.exe /leave*" em uma janela de prompt de comando elevado, reinicializar e tentar se registrar novamente pode ajudar com esse problema.

**Problema em potencial**: o campo para **SettingsUrl** está vazio e o dispositivo não sincroniza. O usuário pode ter feito o último logon no dispositivo antes de o Enterprise State Roaming ser habilitado no portal do Azure Active Directory. Reinicie o dispositivo e faça o logon do usuário. Opcionalmente, no portal, peça para que o administrador de TI navegue até **Azure Active Directory** > **Dispositivos** > **Enterprise State Roaming** desabilitar e reabilitar **os usuários podem sincronizar configurações e dados de aplicativo entre dispositivos**. Uma vez reativado, reinicie o dispositivo e faça o login do usuário. Se isso não resolver o problema, **SettingsUrl** poderá estar vazio, no caso de um certificado de dispositivo inválido. Nesse caso, executar “*dsregcmd.exe /leave*” em uma janela de prompt de comandos com privilégios elevados, reinicializar e tentar se registrar novamente pode ajudar com esse problema.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Enterprise State Roaming e Autenticação Multifator 

Em determinadas condições, o Roaming de Estado de Empresa poderá não sincronizar dados se a Autenticação Multifator do Azure estiver configurada. Para obter mais detalhes sobre esses sintomas, consulte o documento de suporte [KB3193683](https://support.microsoft.com/kb/3193683). 

**Problema em potencial**: Se o dispositivo está configurado para exigir Autenticação Multifator no portal do Azure Active Directory, você pode não conseguir sincronizar configurações ao entrar em um dispositivo Windows 10 usando uma senha. Esse tipo de configuração de Autenticação Multifator destina-se a proteger uma conta de administrador do Azure. Os usuários administradores talvez ainda possam executar a sincronização entrando em seus dispositivos Windows 10 com o PIN do Microsoft Passport for Work ou concluindo a Autenticação Multifator durante o acesso a outros serviços do Azure, como o Office 365.

**Problema em potencial**: Sincronização poderá falhar se o administrador configurar a política de acesso do Active Directory Federation Services multi-factor autenticação condicional e o token de acesso do dispositivo expira. Certifique-se de entrar e sair usando o PIN do Microsoft Passport for Work ou conclua a Autenticação Multifator durante o acesso a outros serviços do Azure, como o Office 365.

### <a name="event-viewer"></a>Visualizador de Eventos

Para a solução de problemas avançada, o Visualizador de Eventos pode ser usado para localizar erros específicos. Eles estão documentados na tabela abaixo. Os eventos podem ser encontrados em Visualizador de Eventos > Logs de Aplicativos e Serviços > **Microsoft** > **Windows** > **SettingSync-Azure** e para problemas com a sincronização relacionados à identidade **Microsoft** > **Windows** > **AAD**.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>A sincronização não funciona em dispositivos que possuem aplicativos com sideloaded usando o software do MDM

Afeta os dispositivos que executam a Atualização de Aniversário do Windows 10 (versão 1607). No Visualizador de Eventos, em logs do Azure SettingSync, a ID de evento 6013 com erro 80070259 é vista com frequência.

**Ação recomendada**  
Verifique se o cliente v1607 do Windows 10 tem a atualização cumulativa de 23 de agosto de 2016 ([KB3176934](https://support.microsoft.com/kb/3176934) compilação do sistema operacional 14393.82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Os favoritos do Internet Explorer não sincronizam

Afeta os dispositivos que executam a atualização de novembro do Windows 10 (versão 1511).

**Ação recomendada**  
Verifique se o cliente v1511 do Windows 10 tem a atualização cumulativa de julho de 2016 ([KB3172985](https://support.microsoft.com/kb/3172985) compilação do sistema operacional 10586.494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>O tema não está sincronizando, assim como os dados protegidos com a Proteção de Informações do Windows 

Para impedir a perda de dados, os dados protegidos com a [Proteção de Informações do Windows](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) não serão sincronizados por meio do Enterprise State Roaming em dispositivos que usam a Atualização de Aniversário do Windows 10.

**Ação recomendada**  
Nenhuma. Atualizações futuras do Windows poderão resolver esse problema.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>As configurações de data, hora e região não sincronizam no dispositivo associado ao domínio 
  
Dispositivos associados ao domínio não passarão pela sincronização para a configuração de data, hora e região: horário automático. O uso do horário automático pode substituir as configurações de data, hora e região e fazer com que essas configurações não sincronizem. 

**Ação recomendada**  
Nenhuma. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>O UAC é ativado ao sincronizar senhas

Afeta os dispositivos que executam a atualização de novembro do Windows 10 (versão 1511) com uma NIC sem fio que está configurada para sincronizar senhas.

**Ação recomendada**  
Verifique se o cliente v1511 do Windows 10 tem a atualização cumulativa ([KB3140743](https://support.microsoft.com/kb/3140743) compilação do sistema operacional 10586.494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>A sincronização não funciona em dispositivos que usam cartões inteligentes para login

Se você tentar entrar em seu dispositivo Windows usando um cartão inteligente ou um cartão inteligente Virtual, a sincronização das configurações vai parar de funcionar.     

**Ação recomendada**  
Nenhuma. Atualizações futuras do Windows poderão resolver esse problema.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>O dispositivo associado ao domínio não está sincronizando após deixar a rede corporativa     

Dispositivos associados ao domínio no AD do Azure podem apresentar falhas de sincronização se o dispositivo estiver fora por longos períodos de tempo, e podem não concluir a autenticação de domínio.

**Ação recomendada**  
Conecte o computador a uma rede corporativa para que a sincronização possa ser retomada.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>O dispositivo Ingressado no Azure AD não está sincronizando e o usuário tem um nome UPN com letras maiúsculas e minúsculas.

Se o usuário tiver um nome UPN com letras maiúsculas e minúsculas (por exemplo, UserName em vez de username) e o usuário estiver em um dispositivo Ingressado no Azure AD que foi atualizado do Windows 10 Build 10586 para 14393, o dispositivo do usuário poderá falhar ao sincronizar. 

**Ação recomendada**  
O usuário precisará remover o dispositivo do domínio e reingressá-lo na nuvem. Para fazer isso, faça logon como o usuário Administrador Local e remova o dispositivo acessando **Configurações** > **Sistema** > **Sobre** e selecione “Gerenciar ou desconectar do trabalho ou da escola”. Limpe os arquivos abaixo e ingresse o dispositivo no Azure AD novamente em **Configurações** > **Sistema** > **Sobre** e selecionando “Conectar ao Trabalho ou à Escola”. Continue ingressando o dispositivo no Azure Active Directory e conclua o fluxo.

Na etapa de limpeza, limpe os seguintes arquivos:
- Settings.dat em `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Todos os arquivos na pasta `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>ID do evento 6065: 80070533 Este usuário não consegue entrar porque esta conta está desabilitada no momento  

No Visualizador de Eventos nos logs de SettingSync/Debug, esse erro pode ser visto quando as credenciais do usuário expiram. Além disso, ele pode ocorrer quando o locatário não provisiona o AzureRMS automaticamente. 

**Ação recomendada**  
No primeiro caso, solicite ao usuário que atualize suas credenciais e faça logon no dispositivo com as novas credenciais. Para resolver o problema do AzureRMS, continue com as etapas listadas em [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>ID do evento 1098: Erro: falha na operação do agente de token 0xCAA5001C  

No Visualizador de Eventos em AAD/logs operacionais, esse erro pode ser visto com o evento 1104: A chamada ao plugin da PA na Nuvem do AAD Obter token retornou o erro: 0xC000005F. Esse problema ocorre se estiverem faltando permissões ou atributos de propriedade.  

**Ação recomendada**  
Prossiga com as etapas listadas em [KB3196528](https://support.microsoft.com/kb/3196528).  

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral, confira [visão geral do Enterprise State Roaming](enterprise-state-roaming-overview.md).
