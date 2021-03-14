---
title: Perguntas frequentes sobre o gerenciamento de dispositivo do Azure Active Directory | Microsoft Docs
description: Perguntas frequentes sobre o gerenciamento de dispositivos do Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 509d267dcdaef58a9792609a60c8a9401e90e867
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199705"
---
# <a name="azure-active-directory-device-management-faq"></a>Perguntas frequentes sobre o gerenciamento de dispositivos do Azure Active Directory

## <a name="general-faq"></a>Perguntas frequentes gerais

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>P: Registrei o dispositivo recentemente. Por que não consigo ver o dispositivo em minhas informações de usuário na portal do Azure? Ou, por que o proprietário do dispositivo está marcado como N/D para dispositivos ingressados no Azure Active Directory (Azure AD) híbrido?

**R:** Dispositivos Windows 10 que são ingressados no Azure AD híbrido não aparecem em **dispositivos de usuário**.
Use o modo de exibição **Todos os dispositivos** no portal do Azure. Você também pode usar um cmdlet [Get-MsolDevice](/powershell/module/msonline/get-msoldevice) do PowerShell.

Apenas os dispositivos a seguir estão listados sob os **dispositivos do usuário**:

- Todos os dispositivos pessoais que não são ingressados no Azure AD híbrido. 
- Todos os dispositivos não Windows 10 ou Windows Server 2016.
- Todos os dispositivos não Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>P: Como saber qual é o estado de registro do dispositivo do cliente?

**R:** Na portal do Azure, vá para **todos os dispositivos**. Procure o dispositivo usando a identificação do dispositivo. Verifique o valor na coluna de tipo de associação. Às vezes, o dispositivo pode ter sido redefinido ou recriado. Portanto, é essencial verificar também o estado do registro do dispositivo no dispositivo:

- Para dispositivos Windows 10 e Windows Server 2016 ou posterior, execute `dsregcmd.exe /status`.
- Para versões de sistema operacional de nível inferior, execute `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

**R:** Para obter informações sobre solução de problemas, consulte estes artigos:
- [Solução de problemas de dispositivos usando o comando dsregcmd](troubleshoot-device-dsregcmd.md)
- [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos do Windows 10 e do Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)
- [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>P: vejo o registro do dispositivo nas informações do usuário no portal do Azure. E posso ver o estado como registrado no dispositivo. Estou configurado corretamente para usar o acesso condicional?

**R:** O estado de ingresso no dispositivo, mostrado por **DeviceID**, deve corresponder ao estado no Azure AD e atender a quaisquer critérios de avaliação para acesso condicional. Para obter mais informações, consulte [exigir dispositivos gerenciados para acesso ao aplicativo de nuvem com acesso condicional](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>P: por que meus usuários veem uma mensagem de erro dizendo "a sua organização excluiu o dispositivo" ou "sua organização desabilitou o dispositivo" em seus dispositivos Windows 10?

**R:** Em dispositivos Windows 10 ingressados ou registrados com o Azure AD, os usuários recebem um [PRT (token de atualização principal)](concept-primary-refresh-token.md) que habilita o logon único. A validade do PRT é baseada na validade do próprio dispositivo. Os usuários verão essa mensagem se o dispositivo for excluído ou desabilitado no Azure AD sem iniciar a ação do próprio dispositivo. Um dispositivo pode ser excluído ou desabilitado no Azure AD um dos seguintes cenários: 

- O usuário desabilita o dispositivo do portal meus aplicativos. 
- Um administrador (ou usuário) exclui ou desabilita o dispositivo no portal do Azure ou usando o PowerShell
- Somente ingressado no Azure AD híbrido: um administrador remove a UO dos dispositivos fora do escopo de sincronização, resultando na exclusão dos dispositivos do Azure AD
- Atualizando o Azure AD Connect para a versão 1.4. XX. x. [Entendendo Azure ad Connect 1.4. XX. x e desaparecem o dispositivo](../hybrid/reference-connect-device-disappearance.md).


Veja abaixo como essas ações podem ser corrigidas.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>P: desabilitei ou excluí meu dispositivo no portal do Azure ou usando o Windows PowerShell. Mas o estado local no dispositivo diz que ele ainda está registrado. O que devo fazer?

**R:** Esta operação é por design. Nesse caso, o dispositivo não tem acesso aos recursos na nuvem. Os administradores podem executar essa ação para dispositivos obsoletos, perdidos ou roubados para impedir o acesso não autorizado. Se essa ação foi executada sem intenção, você precisará reabilitar ou registrar novamente o dispositivo conforme descrito abaixo

- Se o dispositivo tiver sido desabilitado no Azure AD, um administrador com privilégios suficientes poderá habilitá-lo no portal do AD do Azure  
  > [!NOTE]
  > Se você estiver sincronizando dispositivos usando Azure AD Connect, os dispositivos ingressados no Azure AD híbridos serão reabilitados automaticamente durante o próximo ciclo de sincronização. Portanto, se você precisar desabilitar um dispositivo ingressado no Azure AD híbrido, será necessário desabilitá-lo do seu AD local

 - Se o dispositivo for excluído no Azure AD, você precisará registrar o dispositivo novamente. Para registrar novamente, você deve executar uma ação manual no dispositivo. Consulte abaixo para obter instruções de novo registro com base no estado do dispositivo. 

      Para registrar novamente os dispositivos Windows 10 e Windows Server 2016/2019 ingressados no Azure AD, execute as seguintes etapas:

      1. Abra o prompt de comando como administrador.
      1. Digite `dsregcmd.exe /debug /leave`.
      1. Saia e entre para disparar a tarefa agendada que registra o dispositivo com o Azure AD novamente. 

      Para versões de sistema operacional Windows de nível inferior associadas ao Azure AD híbrido, execute as seguintes etapas:

      1. Abra o prompt de comando como administrador.
      1. Digite `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
      1. Digite `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

      Para dispositivos ingressados no Azure AD dispositivos Windows 10, execute as seguintes etapas:

      1. Abra o prompt de comando como administrador
      1. Insira `dsregcmd /forcerecovery` (Observação: você precisa ser um administrador para executar esta ação).
      1. Clique em "entrar" na caixa de diálogo que é aberta e continue com o processo de entrada.
      1. Saia e entre novamente no dispositivo para concluir a recuperação.

      Para dispositivos Windows 10 registrados no Azure AD, execute as seguintes etapas:

      1. Vá para **configurações**  >  **contas**  >  **acesso corporativo ou de estudante**. 
      1. Selecione a conta e selecione **Desconectar**.
      1. Clique em "+ conectar" e registre o dispositivo novamente ao passar pelo processo de entrada.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>P: por que vejo entradas de dispositivo duplicadas no portal do Azure?

**R:**

- Para o Windows 10 e o Windows Server 2016, tentativas repetidas de desunir e reingressar no mesmo dispositivo podem resultar em entradas duplicadas. 
- Cada usuário do Windows que usar **Adicionar Conta Corporativa ou de Estudante** cria um novo registro do dispositivo com o mesmo nome do dispositivo.
- Para versões do sistema operacional do Windows de nível inferior que são ingressadas no domínio do Azure Directory local, o registro automático cria um novo registro de dispositivo com o mesmo nome do dispositivo para cada usuário de domínio que entra no dispositivo. 
- Uma máquina conectada do Azure AD que foi limpa, reinstalada e reunida com o mesmo nome é exibida como outro registro com o mesmo nome de dispositivo.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>P: o registro de dispositivo do Windows 10 no Azure AD dá suporte a TPMs no modo FIPS?

**R:** O registro de dispositivo do Windows 10 só tem suporte para TPM 2,0 compatível com FIPS e sem suporte para TPM 1,2. Se seus dispositivos tiverem o TPM 1,2 compatível com FIPS, você deverá desabilitá-los antes de prosseguir com o ingresso no Azure AD ou ingressar no Azure AD híbrido. A Microsoft não fornece nenhuma ferramenta para desabilitar o modo FIPS para TPMs, pois depende do fabricante do TPM. Contate o OEM de hardware para obter suporte. 

---

**P: por que um usuário ainda pode acessar recursos de um dispositivo que eu desabilitei na portal do Azure?**

**R:** Demora até uma hora para que uma revogação seja aplicada a partir do momento em que o dispositivo do Azure AD é marcado como desabilitado.

>[!NOTE] 
>Para dispositivos registrados, é recomendável apagar o dispositivo para garantir que os usuários não possam acessar os recursos. Para obter mais informações, confira [O que é o registro de dispositivo?](/mem/intune/user-help/use-managed-devices-to-get-work-done). 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>P: por que há dispositivos marcados como "pendentes" na coluna REGISTRAda na portal do Azure?

**A**: Pending indica que o dispositivo não está registrado. Esse estado indica que um dispositivo foi sincronizado usando o Azure AD Connect de um AD local e está pronto para o registro do dispositivo. Esses dispositivos têm o tipo de junção definido como "ingressado no Azure AD híbrido". Saiba mais sobre [como planejar sua implementação de junção de Azure Active Directory híbrida](hybrid-azuread-join-plan.md).

>[!NOTE]
>Um dispositivo também pode mudar de um estado registrado para "pendente"
>* Se um dispositivo for excluído do Azure AD primeiro e sincronizado novamente a partir de um AD local.
>* Se um dispositivo for removido de um escopo de sincronização no Azure AD Connect e adicionado de volta.
>
>Em ambos os casos, você deve registrar novamente o dispositivo manualmente em cada um desses dispositivos. Para verificar se o dispositivo foi registrado anteriormente, você pode [solucionar problemas de dispositivos usando o comando dsregcmd](troubleshoot-device-dsregcmd.md).

---

### <a name="q-i-cannot-add-more-than-3-azure-ad-user-accounts-under-the-same-user-session-on-a-windows-10-device-why"></a>P: não consigo adicionar mais de três contas de usuário do Azure AD na mesma sessão de usuário em um dispositivo Windows 10, por quê?

**R: o** Azure ad adicionou suporte para várias contas do Azure ad na versão 10 1803 do Windows. No entanto, o Windows 10 restringe o número de contas do Azure AD em um dispositivo para 3 para limitar o tamanho das solicitações de token e habilitar o SSO (logon único confiável). Depois que 3 contas tiverem sido adicionadas, os usuários verão um erro para as contas subsequentes. As informações adicionais sobre o problema na tela de erro fornecem a seguinte mensagem indicando o motivo: "a operação de adição de conta está bloqueada porque o limite da conta foi atingido". 

---
## <a name="azure-ad-join-faq"></a>Perguntas frequentes sobre ingresso no Azure AD

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>P: Como fazer desassociar um dispositivo ingressado no Azure AD localmente no dispositivo?

**R:** Para dispositivos adicionados ao Azure AD puro, verifique se você tem uma conta de administrador local offline ou crie uma. Você não pode entrar com as credenciais de usuário do Azure AD. Em seguida, vá para **configurações**  >  **contas**  >  **acesso corporativo ou de estudante**. Selecione sua conta e, em seguida, **Desconectar**. Siga os prompts e forneça as credenciais de administrador local, quando solicitado. Reinicie o dispositivo para concluir o processo de cancelar a associação.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>P: meus usuários podem entrar em dispositivos ingressados no Azure AD que são excluídos ou desabilitados no Azure AD?

**R:** Sim. O Windows tem uma funcionalidade de armazenamento em cache de nome de usuário e senha que permite aos usuários que se conectaram anteriormente acessarem a área de trabalho rapidamente, mesmo sem conectividade de rede. 

Quando um dispositivo é excluído ou desabilitado no Azure AD, ele não é reconhecido pelo dispositivo do Windows. Portanto, os usuários que se conectaram anteriormente continuam a acessar a área de trabalho com o nome de usuário e a senha armazenados em cache. Mas como o dispositivo é excluído ou desabilitado, os usuários não podem acessar os recursos protegidos pelo acesso condicional baseado em dispositivo. 

Os usuários que não se conectaram anteriormente não podem acessar o dispositivo. Não há nenhum nome de usuário e senha armazenados em cache habilitados para eles. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>P: uma entrada de usuário desabilitada ou excluída de um dispositivo ingressado no Azure AD

**R:** Sim, mas apenas por um período limitado. Quando um usuário é excluído ou desabilitado no Azure AD, ele não é imediatamente conhecido para o dispositivo do Windows. Portanto, os usuários que se conectaram anteriormente podem acessar a área de trabalho com o nome de usuário e a senha armazenados em cache. 

Normalmente, o dispositivo está ciente do estado do usuário em menos de quatro horas. Em seguida, o Windows bloqueia o acesso desses usuários à área de trabalho. Como o usuário é excluído ou desabilitado no Azure AD, todos os seus tokens são revogados. Portanto, eles não podem acessar nenhum recurso. 

Os usuários excluídos ou desabilitados que não se conectaram anteriormente não podem acessar um dispositivo. Não há nenhum nome de usuário e senha armazenados em cache habilitados para eles. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>P: por que meus usuários têm problemas em dispositivos adicionados ao Azure AD depois de alterar seu UPN?

**R:** Atualmente, as alterações de UPN não têm suporte total em dispositivos ingressados no Azure AD. Portanto, a autenticação com o Azure AD falha após o UPN mudar. Como resultado, os usuários têm problemas de SSO e Acesso Condicional em seus dispositivos. Neste momento, os usuários precisam entrar no Windows por meio do bloco "Outro usuário" usando o novo UPN para resolver esse problema. Estamos trabalhando para solucionar esse problema. No entanto, os usuários que entram no Windows Hello para Empresas não enfrentam esse problema. 

Há suporte para alterações de UPN com a atualização do Windows 10 2004. Os usuários em dispositivos com esta atualização não terão problemas depois de alterar seus UPNs

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>P: meus usuários não podem pesquisar impressoras de dispositivos ingressados no Azure AD. Como habilitar a impressão usando esses dispositivos?

**R:** Para implantar impressoras para dispositivos ingressados no Azure AD, consulte [implantar a impressão em nuvem híbrida do Windows Server com pré-autenticação](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Você precisa de um Windows Server local para implantar a impressão de nuvem híbrida. Atualmente, o serviço de impressão baseado em nuvem não está disponível. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>P: Como fazer conectar a um dispositivo remoto ingressado no Azure AD?

**R:** Consulte [conectar-se a um PC ingressado no Azure Active Directory remoto](/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>P: por que meus usuários veem *que você não pode chegar lá daqui*?

**R:** Você configurou certas regras de acesso condicional para exigir um estado de dispositivo específico? Se o dispositivo não atender aos critérios, os usuários são bloqueados e veem essa mensagem. Avalie as regras de política de acesso condicional. Verifique se o dispositivo atende aos critérios para evitar a mensagem.

---

### <a name="q-why-dont-some-of-my-users-get-azure-ad-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>P: por que alguns dos meus usuários obtêm prompts da autenticação multifator do Azure AD em dispositivos ingressados no Azure AD?

**R:** Um usuário pode ingressar ou registrar um dispositivo com o Azure AD usando a autenticação multifator. Em seguida, o próprio dispositivo torna-se um segundo fator confiável para esse usuário. Sempre que o mesmo usuário faz logon no dispositivo e acessa um aplicativo, o Azure AD considera o dispositivo como um segundo fator. Ele permite que o usuário acesse diretamente os aplicativos sem prompts de autenticação multifator adicionais. 

Esse comportamento:

- É aplicável aos dispositivos associados do Azure AD e aos dispositivos registrados do Azure AD, mas não aos dispositivos associados híbridos do Azure AD.
- Não é aplicável a qualquer outro usuário que se conecte a esse dispositivo. Portanto, todos os outros usuários que acessam esse dispositivo serão apresentados a um desafio de autenticação multifator. Só então eles poderão acessar os aplicativos que exigem a autenticação multifator.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>P: por que obtenho um *nome de usuário ou senha é uma mensagem incorreta* para um dispositivo que acabei de ingressar no Azure AD?

**R:** Os motivos comuns para esse cenário são os seguintes:

- Suas credenciais de usuário não são mais válidas.
- O computador não consegue se comunicar com o Azure Active Directory. Verifique se há problemas de conectividade de rede.
- Credenciais federadas requerem que o servidor de federação dê suporte a pontos de extremidade do WS-Trust que estejam habilitados e acessíveis. 
- Você habilitou a autenticação de passagem. Portanto, sua senha temporária precisa ser alterada quando você entrar.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>P: por que vejo o *Ops... ocorreu um erro!* Quando tento o Azure AD ingressar no meu PC?

**R:** Esse erro ocorre quando você configura o registro de Azure Active Directory com o Intune. Certifique-se de que o usuário que tenta fazer o ingresso do Azure AD tenha a licença correta do Intune atribuída. Para obter mais informações, confira [Configurar registro para dispositivos Windows](/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>P: por que minha tentativa de ingresso no Azure AD em um PC falha, embora não tenha informações sobre o erro?

**R:** Uma causa provável é que você entrou no dispositivo usando a conta de administrador interno local. Crie uma conta local diferente antes de usar o ingresso do Azure Active Directory para concluir a configuração. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>P: Quais são os certificados de MS-Organization-P2P-Access presentes em nossos dispositivos Windows 10?

**R:** Os certificados MS-Organization-P2P-Access são emitidos pelo AD do Azure para os dispositivos ingressados no Azure AD e no Azure AD híbrido. Esses certificados são usados para habilitar a confiança entre os dispositivos no mesmo locatário para cenários de área de trabalho remotos. Um certificado é emitido para o dispositivo e o outro é emitido para o usuário. O certificado do dispositivo está presente no `Local Computer\Personal\Certificates` e é válido por um dia. Esse certificado é renovado (emitindo um novo certificado) se o dispositivo ainda estiver ativo no Azure AD. O certificado de usuário está presente no `Current User\Personal\Certificates` e esse certificado também é válido por um dia, mas é emitido sob demanda quando um usuário tenta uma sessão de área de trabalho remota para outro dispositivo ingressado do Azure AD. Ele não é renovado após a expiração. Esses dois certificados são emitidos usando o certificado MS-Organization-P2P-Access presente no `Local Computer\AAD Token Issuer\Certificates`. Esse certificado é emitido pelo Azure AD durante o registro do dispositivo. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>P: Por que vejo vários certificados expirados emitidos pelo MS-Organization-P2P-Access em nossos dispositivos Windows 10? Como exclui-los?

**R:** Houve um problema identificado no Windows 10 versão 1709 e inferior, quando os certificados MS-Organization-P2P-Access expirados da Microsoft continuaram a existir no repositório do computador devido a problemas de criptografia. Os usuários podem enfrentar problemas com a conectividade de rede, se você estiver usando quaisquer clientes VPN (por exemplo, Cisco AnyConnect) que não podem lidar com o grande número de certificados expirados. Esse problema foi corrigido no Windows 10 versão 1803 para excluir automaticamente esses certificados MS-Organization-P2P-Access expirados. Você pode resolver esse problema atualizando seus dispositivos para o Windows 10 versão 1803. Se você não conseguir atualizar, você pode excluir esses certificados sem que seja causado nenhum impacto negativo.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Perguntas frequentes sobre ingresso no Azure AD Híbrido

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>P: Como fazer desassociar um dispositivo ingressado no Azure AD híbrido localmente no dispositivo?

**R:** Para dispositivos ingressados no Azure AD híbrido, certifique-se de desativar o registro automático. A tarefa agendada não registrará o dispositivo novamente. Agora, abra um prompt de comando como administrador e digite `dsregcmd.exe /debug /leave`. Ou execute este comando como um script em vários dispositivos para fazer um cancelamento de associação em massa.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>P: onde posso encontrar informações de solução de problemas para diagnosticar falhas de ingresso no Azure AD híbrido?

**R:** Para obter informações sobre solução de problemas, consulte estes artigos:

- [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos do Windows 10 e do Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)
- [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>P: por que vejo um registro registrado do Azure AD com o meu dispositivo ingressado no Azure AD híbrido do Windows 10 na lista de dispositivos do Azure AD?

**R:** Quando os usuários adicionam suas contas a aplicativos em um dispositivo ingressado no domínio, eles podem ser solicitados **a adicionar conta ao Windows?** Se o usuário escolher **Sim** no prompt, o dispositivo registra com o Azure AD. O tipo de relação de confiança é marcado como registrado no Azure AD. Depois de habilitar o ingresso do Azure AD híbrido na organização, o dispositivo também será incluído no Azure AD híbrido. Em seguida, dois estados de dispositivo aparecem para o mesmo dispositivo. 

O ingresso do Azure AD híbrido tem precedência sobre o estado de registrado pelo Azure AD. Portanto, seu dispositivo é considerado um Azure AD híbrido associado a qualquer autenticação e avaliação de acesso condicional. Você pode excluir com segurança o registro do dispositivo registrado pelo Azure AD no portal do Azure AD. Saiba como [evitar ou limpar esse estado duplo no computador com Windows 10](hybrid-azuread-join-plan.md#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>P: por que meus usuários têm problemas em dispositivos ingressados no Azure AD híbrido do Windows 10 depois de alterar seu UPN?

**R:** Atualmente, as alterações de UPN não têm suporte total com dispositivos ingressados no Azure AD híbrido. Embora os usuários possam entrar dispositivo e acessar seus aplicativos locais, a autenticação com o Azure AD falhará após a alteração de um UPN. Como resultado, os usuários têm problemas de SSO e Acesso Condicional em seus dispositivos. Neste momento, você precisa desassociar o dispositivo do Azure AD (execute "dsregcmd/Leave" com privilégios elevados) e reingresse (ocorre automaticamente) para resolver o problema. Estamos trabalhando para solucionar esse problema. No entanto, os usuários que entram no Windows Hello para Empresas não enfrentam esse problema. 

Há suporte para alterações de UPN com a atualização do Windows 10 2004. Os usuários em dispositivos com esta atualização não terão problemas depois de alterar seus UPNs

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>P: os dispositivos ingressados no Azure AD híbrido do Windows 10 exigem a linha de visão para o controlador de domínio para obter acesso aos recursos de nuvem?

**R:** Não, exceto quando a senha do usuário é alterada. Depois que o ingresso no Azure AD híbrido do Windows 10 for concluído e o usuário tiver entrado pelo menos uma vez, o dispositivo não exigirá a linha de visão do controlador de domínio para acessar os recursos de nuvem. O Windows 10 pode obter logon único para aplicativos do Azure AD de qualquer lugar com uma conexão com a Internet, exceto quando uma senha é alterada. Os usuários que entram com o Windows Hello para empresas continuam a obter logon único para aplicativos do Azure AD mesmo após uma alteração de senha, mesmo que eles não tenham uma linha de visão para seu controlador de domínio. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>P: o que acontece se um usuário altera sua senha e tenta fazer logon em seu dispositivo ingressado no Azure AD híbrido do Windows 10 fora da rede corporativa?

**R:** Se uma senha for alterada fora da rede corporativa (por exemplo, usando o Azure AD SSPR), a entrada do usuário com a nova senha falhará. Para dispositivos ingressados no Azure AD híbridos, Active Directory local é a autoridade principal. Quando um dispositivo não tem uma linha de visão para o controlador de domínio, ele não consegue validar a nova senha. Portanto, o usuário precisa estabelecer conexão com o controlador de domínio (por meio de VPN ou estar na rede corporativa) antes de poder entrar no dispositivo com a nova senha. Caso contrário, eles só podem entrar com sua senha antiga devido à capacidade de entrada armazenada em cache no Windows. No entanto, a senha antiga é invalidada pelo Azure AD durante solicitações de token e, portanto, impede o logon único e falha em qualquer política de acesso condicional com base no dispositivo. Esse problema não ocorrerá se você usar o Windows Hello para empresas. 

---

## <a name="azure-ad-register-faq"></a>Perguntas frequentes sobre o registro do Azure AD

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>P: Como fazer remover um estado registrado do Azure AD de um dispositivo localmente?

**R:** 
- Para dispositivos registrados no Azure AD do Windows 10, acesse **configurações**  >  **contas**  >  **acesso corporativo ou de estudante**. Selecione sua conta e, em seguida, **Desconectar**. O registro de dispositivo é por perfil de usuário no Windows 10.
- Para IOS e Android, você pode usar o registro de dispositivo Microsoft Authenticator **configurações** de aplicativo  >   e selecionar **Cancelar registro de dispositivo**.
- Para o macOS, você pode usar o aplicativo Microsoft Intune Portal da Empresa para cancelar o registro do dispositivo do gerenciamento e remover qualquer registro. 

Para dispositivos Windows 10, esse processo pode ser automatizado com a [ferramenta de remoção de Workplace Join (WPJ)](https://download.microsoft.com/download/8/e/f/8ef13ae0-6aa8-48a2-8697-5b1711134730/WPJCleanUp.zip)

> [!NOTE]
> Essa ferramenta remove todas as contas de SSO no dispositivo. Após essa operação, todos os aplicativos perderão o estado do SSO e o dispositivo terá o registro cancelado das ferramentas de gerenciamento (MDM) e não registrado na nuvem. Na próxima vez que um aplicativo tentar entrar, os usuários serão solicitados a adicionar a conta novamente.

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>P: como posso impedir que os usuários adicionem outras contas de trabalho (registradas no Azure AD) em meus dispositivos corporativos do Windows 10?

**R:** Habilite o registro a seguir para impedir que os usuários adicionem contas de trabalho adicionais ao domínio corporativo ingressado, ingressado no Azure AD ou dispositivos Windows 10 híbridos ingressados no Azure AD. Essa política também pode ser usada para impedir que computadores ingressados no domínio obtenham inadvertidamente o Azure AD registrado com a mesma conta de usuário. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>P: É possível registrar dispositivos Android ou iOS BYOD?

**R:** Sim, mas somente com o serviço de registro de dispositivo do Azure e para clientes híbridos. Ele não é compatível com o serviço de registro de dispositivo local nos Serviços de Federação do Active Directory (AD FS).

---
### <a name="q-how-can-i-register-a-macos-device"></a>P: Como posso registrar um dispositivo macOS?

**R:** Execute as seguintes etapas:

1.    [Criar uma política de conformidade](/intune/compliance-policy-create-mac-os)
1.    [Definir uma política de acesso condicional para dispositivos macOS](../conditional-access/overview.md) 

**Comentários**

- Os usuários incluídos na sua política de acesso condicional precisam de uma [versão com suporte do Office para MacOS](../conditional-access/concept-conditional-access-conditions.md) para acessar recursos. 
- Durante a primeira tentativa de acesso, os usuários são solicitados a registrar o dispositivo usando o portal da empresa.

---
## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [dispositivos registrados no Azure AD](concept-azure-ad-register.md)
- Saiba mais sobre [dispositivos ingressados no Azure AD](concept-azure-ad-join.md)
- Saiba mais sobre [dispositivos ingressados no Azure AD híbrido](concept-azure-ad-join-hybrid.md)
