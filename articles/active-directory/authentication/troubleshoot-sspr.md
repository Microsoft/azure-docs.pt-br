---
title: Solucionar problemas de redefinição de senha de autoatendimento – Azure Active Directory
description: Saiba como solucionar problemas comuns e etapas de resolução para redefinição de senha de autoatendimento no Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1dee21182349108c44f9d498417d3b760ac4913
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103600855"
---
# <a name="troubleshoot-self-service-password-reset-in-azure-active-directory"></a>Solucionar problemas de redefinição de senha de autoatendimento no Azure Active Directory

Azure Active Directory (Azure AD) autoatendimento de redefinição de senha (SSPR) permite que os usuários redefinam suas senhas na nuvem.

Se você tiver problemas com o SSPR, as seguintes etapas de solução de problemas e erros comuns podem ajudar. Você também pode assistir a esse breve vídeo sobre [como ele resolve as seis mensagens de erro de usuário final SSPR mais comuns](https://www.youtube.com/watch?v=9RPrNVLzT8I&list=PL3ZTgFEc7LyuS8615yo39LtXR7j1GCerW&index=1).

Se você não conseguir encontrar a resposta para o problema, [nossas equipes de suporte estarão sempre disponíveis](#contact-microsoft-support) para ajudá-lo.

## <a name="sspr-configuration-in-the-azure-portal"></a>Configuração do SSPR no portal do Azure

Se você tiver problemas para ver ou configurar as opções de SSPR no portal do Azure, examine as seguintes etapas de solução de problemas:

### <a name="i-dont-see-the-password-reset-section-under-azure-ad-in-the-azure-portal"></a>Não consigo ver a seção **Redefinição de Senha** em Azure AD no portal do Azure.

Você não verá se a opção de menu de **redefinição de senha** se não tiver uma licença do Azure ad atribuída ao administrador que executa a operação.

Para atribuir uma licença à conta de administrador em questão, siga as etapas para [atribuir, verificar e resolver problemas com licenças](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="i-dont-see-a-particular-configuration-option"></a>Não consigo ver determinada opção de configuração.

Muitos elementos da interface do usuário ficam ocultos até que sejam necessários. Verifique se a opção está habilitada antes de procurar as opções de configuração específicas.

### <a name="i-dont-see-the-on-premises-integration-tab"></a>Não consigo ver a guia **Integração local**.

O Write-back de senha local só será visível se você tiver baixado Azure AD Connect e tiver configurado o recurso.

Para obter mais informações, consulte [introdução ao Azure ad Connect](../hybrid/how-to-connect-install-express.md).

## <a name="sspr-reporting"></a>Relatórios do SSPR

Se você tiver problemas com os relatórios do SSPR na portal do Azure, examine as seguintes etapas de solução de problemas:

### <a name="i-dont-see-any-password-management-activity-types-in-the-self-service-password-management-audit-event-category"></a>Não vejo nenhum tipo de atividade de gerenciamento de senhas na categoria de evento de auditoria de **Gerenciamento de senhas de autoatendimento** .

Isso poderá ocorrer se você não tiver uma licença do Azure AD atribuída ao administrador que realiza a operação.

Para atribuir uma licença à conta de administrador em questão, siga as etapas para [atribuir, verificar e resolver problemas com licenças](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="user-registrations-show-multiple-times"></a>Os registros do usuário aparecem várias vezes.

Quando um usuário registra, no momento, registramos cada dado individual registrado como um evento separado.

Se você quiser agregar esses dados e ter maior flexibilidade em como exibi-los, poderá baixar o relatório e abrir os dados como uma tabela dinâmica no Excel.

## <a name="sspr-registration-portal"></a>Portal de registro do SSPR

Se os usuários tiverem problemas de registro para o SSPR, examine as seguintes etapas de solução de problemas:

### <a name="the-directory-isnt-enabled-for-password-reset-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>O diretório não está habilitado para redefinição de senha. O usuário pode ver um erro que informa "seu administrador não habilitou você a usar esse recurso".

Você pode habilitar o SSPR para todos os usuários, nenhum usuário ou para grupos de usuários selecionados. No momento, só é possível habilitar um grupo do Azure AD para SSPR usando o portal do Azure. Como parte de uma implantação mais ampla da SSPR, há suporte para grupos aninhados. Verifique se os usuários nos grupos que você escolher têm as licenças apropriadas atribuídas.

No portal do Azure, altere a configuração **habilitada de redefinição de senha de autoatendimento** para *selecionado* ou *todos* e, em seguida, selecione **salvar**.

### <a name="the-user-doesnt-have-an-azure-ad-license-assigned-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>O usuário não tem uma licença do Azure AD atribuída. O usuário pode ver um erro que informa "seu administrador não habilitou você a usar esse recurso".

No momento, só é possível habilitar um grupo do Azure AD para SSPR usando o portal do Azure. Como parte de uma implantação mais ampla da SSPR, há suporte para grupos aninhados. Verifique se os usuários nos grupos que você escolher têm as licenças apropriadas atribuídas. Examine a etapa anterior de solução de problemas para habilitar o SSPR conforme necessário.

Examine também as etapas de solução de problemas para garantir que o administrador que executa as opções de configuração tenha uma licença atribuída. Para atribuir uma licença à conta de administrador em questão, siga as etapas para [atribuir, verificar e resolver problemas com licenças](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="theres-an-error-processing-the-request"></a>Há um erro ao processar a solicitação.

Erros de registro SSPR genéricos podem ser causados por muitos problemas, mas geralmente esse erro é causado por uma interrupção de serviço ou um problema de configuração. Se você continuar a ver esse erro genérico ao tentar novamente o processo de registro SSPR, [entre em contato com o suporte da Microsoft](#contact-microsoft-support) para obter assistência adicional.

## <a name="sspr-usage"></a>Uso de SSPR

Se você ou seus usuários tiverem problemas ao usar o SSPR, examine os seguintes cenários de solução de problemas e etapas de resolução:

| Erro | Solução |
| --- | --- |
| O diretório não está habilitado para redefinição de senha. | No portal do Azure, altere a configuração **habilitada de redefinição de senha de autoatendimento** para *selecionado* ou *todos* e, em seguida, selecione **salvar**. |
| O usuário não tem uma licença do Azure AD atribuída. | Isso pode acontecer se você não tiver uma licença do Azure AD atribuída ao usuário desejado. Para atribuir uma licença à conta de administrador em questão, siga as etapas para [atribuir, verificar e resolver problemas com licenças](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses). |
| O diretório está habilitado para redefinição de senha, mas o usuário tem informações de autenticação ausentes ou incorretas. | Verifique se o usuário formou corretamente os dados de contato no arquivo no diretório. Para obter mais informações, consulte [Dados usados pelo autoatendimento de redefinição de senha do Azure AD](howto-sspr-authenticationdata.md). |
| O diretório está habilitado para redefinição de senha, mas o usuário tem somente uma parte dos dados de contato no arquivo quando a política está definida para exigir dois métodos de verificação. | Certifique-se de que o usuário tenha pelo menos dois métodos de contato configurados corretamente. Um exemplo é ter um número de telefone celular *e* um número de telefone comercial. |
| O diretório está habilitado para redefinição de senha e o usuário está configurado corretamente, mas não pode ser contatado. | Isso pode ser o resultado de um erro de serviço temporário ou de dados de contato incorretos que não podem ser detectados corretamente. <br> <br> Se o usuário aguardar 10 segundos, será exibido um link para "tentar novamente" e "Contate o administrador". Se o usuário selecionar "tentar novamente", ele tentará novamente a chamada. Se o usuário selecionar "entre em contato com seu administrador", ele enviará um email de formulário aos administradores solicitando que uma redefinição de senha seja executada para essa conta de usuário. |
| O usuário nunca recebe a chamada telefônica ou redefinição de senha de SMS. | Isso pode ser o resultado de um número de telefone incorreto no diretório. Verifique se o número de telefone está no formato "+ 1 4251234567". <br> <br>A redefinição de senha não dá suporte a extensões, mesmo se você especificar uma no diretório. As extensões são removidas antes que a chamada seja feita. Use um número sem uma extensão ou integre a extensão ao número de telefone em sua PBX (troca de Branch privada). |
| O usuário nunca recebe o email de redefinição de senha. | A causa mais comum desse problema é que a mensagem foi rejeitada por um filtro de spam. Verifique o email no spam, na pasta Lixo eletrônico ou nos itens de email excluídos. <br> <br> Além disso, verifique se o usuário verifica a conta de email correta como registrada com SSPR. |
| Defini uma política de redefinição de senha, mas quando uma conta de administrador usa a redefinição de senha, essa política não é aplicada. | A Microsoft gerencia e controla a política de redefinição de senha do administrador para garantir o nível mais alto de segurança. |
| O usuário foi impedido de tentar a redefinição de senha muitas vezes em um mesmo dia. | Um mecanismo de limitação automática é usado para impedir que os usuários tentem redefinir suas senhas muitas vezes em um curto período de tempo. A limitação ocorre nos seguintes cenários: <br><ul><li>O usuário tenta validar um número de telefone cinco vezes em uma hora.</li><li>O usuário tenta usar o portão de perguntas de segurança cinco vezes em uma hora.</li><li>O usuário tenta redefinir uma senha para a mesma conta de usuário cinco vezes em uma hora.</li></ul>Se um usuário encontrar esse problema, ele deverá aguardar 24 horas após a última tentativa. O usuário poderá, então, redefinir a senha. |
| O usuário vê um erro ao validar seu número de telefone. | Esse erro ocorre quando o número de telefone digitado não corresponde ao número de telefone no arquivo. Verifique se o usuário está inserindo o número de telefone completo, incluindo o código de área e país, ao tentar usar um método baseado em telefone para a redefinição de senha. |
| Há um erro ao processar a solicitação. | Erros de registro SSPR genéricos podem ser causados por muitos problemas, mas geralmente esse erro é causado por uma interrupção de serviço ou um problema de configuração. Se você continuar a ver esse erro genérico ao tentar novamente o processo de registro SSPR, [entre em contato com o suporte da Microsoft](#contact-microsoft-support) para obter assistência adicional. |
| Violação de política local | A senha não atende à política de senha de Active Directory local. O usuário deve definir uma senha que atenda aos requisitos de complexidade ou de força. |
| A senha não está em conformidade com a política difusa | A senha que foi usada aparece na [lista de senhas banidas](./concept-password-ban-bad.md#how-are-passwords-evaluated) e não pode ser usada. O usuário deve definir uma senha que atenda ou exceda a política de lista de senhas banidas. |

## <a name="sspr-errors-that-a-user-might-see"></a>Erros de SSPR que um usuário pode ver

Os seguintes erros e detalhes técnicos podem ser mostrados para um usuário como parte do processo SSPR. Geralmente, o erro não é algo que ele pode resolver sozinho, pois o recurso SSPR precisa ser habilitado, configurado ou registrado para sua conta.

Use as informações a seguir para entender o problema e o que precisa ser corrigido no locatário do Azure AD ou na conta de usuário individual.

| Erro | Detalhes | Detalhes técnicos |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Desculpe, você não pode redefinir sua senha neste momento porque o administrador desabilitou a redefinição de senha para sua organização. Não há mais ações para solucionar esta situação. Entre em contato com seu administrador e solicite a habilitação deste recurso.<br /><br />Para saber mais, confira [Preciso de ajuda, esqueci a minha senha do Azure AD](../user-help/active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions). | SSPR_0009: Detectamos que a redefinição de senha não foi habilitada pelo administrador. Entre em contato com o seu administrador e solicite a habilitação da redefinição de senha para a sua organização. |
| WritebackNotEnabled = 10 |Desculpe, você não pode redefinir sua senha neste momento porque o administrador não habilitou um serviço necessário para sua organização. Não há mais ações para solucionar esta situação. Entre em contato com seu administrador e peça para verificar a configuração da sua organização.<br /><br />Para saber mais sobre o serviço necessário, confira [Como configurar o write-back de senha](./tutorial-enable-sspr-writeback.md). | SSPR_0010: Detectamos que o write-back de senha não foi habilitado. Entre em contato com o seu administrador e solicite a habilitação do write-back de senha. |
| SsprNotEnabledInUserPolicy = 11 | Desculpe, você não pode redefinir sua senha neste momento porque o administrador não configurou a redefinição de senha para sua organização. Não há mais ações para solucionar esta situação. Entre em contato com o seu administrador e solicite a configuração da redefinição de senha.<br /><br />Para saber mais sobre a configuração da redefinição de senha, consulte [Início rápido: Redefinição de senha self-service do Azure AD](./tutorial-enable-sspr.md). | SSPR_0011: Sua organização não tem uma política de redefinição de senha definida. Entre em contato com seu administrador e solicite a implantação de uma política de redefinição de senha. |
| UserNotLicensed = 12 | Desculpe, você não pode redefinir sua senha neste momento porque as licenças necessárias estão ausentes da sua organização. Não há mais ações para solucionar esta situação. Entre em contato com seu administrador e solicite que verifique sua atribuição de licença.<br /><br />Para saber mais sobre requisitos de licenciamento, confira os [Requisitos de licenciamento para redefinição da senha de autoatendimento do Azure AD](./concept-sspr-licensing.md). | SSPR_0012: Sua organização não tem as licenças obrigatórias necessárias para realizar a redefinição de senha. Entre em contato com seu administrador e solicite a revisão das atribuições de licença. |
| UserNotMemberOfScopedAccessGroup = 13 | Desculpe, você não pode redefinir sua senha neste momento porque o administrador não configurou sua conta para usar a redefinição de senha. Não há mais ações para solucionar esta situação. Entre em contato com seu administrador e solicite a configuração da sua conta para redefinição de senha.<br /><br />Para saber mais sobre a configuração de conta para redefinição de senha, confira [Como executar a redefinição de senha para usuários](./howto-sspr-deployment.md). | SSPR_0013: Você não é membro de um grupo habilitado para a redefinição de senha. Entre em contato com o administrador e peça para ser adicionado ao grupo. |
| UserNotProperlyConfigured = 14 | Infelizmente, você não pode redefinir sua senha no momento porque as informações necessárias estão ausentes em sua conta. Não há mais ações para solucionar esta situação. Entre em contato com o seu administrador e solicite a redefinição da sua senha. Depois de ter acesso à sua conta novamente, você precisará registrar as informações necessárias.<br /><br />Para registrar informações, siga as etapas no artigo [Registro de redefinição de senha de autoatendimento](../user-help/active-directory-passwords-reset-register.md). | SSPR_0014: São necessárias informações de segurança adicionais para redefinir a senha. Para continuar, entre em contato com o seu administrador e solicite a redefinição da sua senha. Depois de acessar sua conta, registre as informações de segurança adicionais em https://aka.ms/ssprsetup. O administrador pode adicionar informações de segurança adicionais à sua conta seguindo as etapas em [Configuração e leitura de dados de autenticação para redefinição de senha](howto-sspr-authenticationdata.md). |
| OnPremisesAdminActionRequired = 29 | Desculpe, não é possível redefinir sua senha no momento devido a um problema com a configuração de redefinição de senha da sua organização. Não há mais ações para solucionar esta situação. Entre em contato com seu administrador e solicite uma investigação. <br /><br />Ou<br /><br />Não é possível redefinir sua senha neste momento devido a um problema com a configuração de redefinição de senha da sua organização. Não há nenhuma ação adicional que você possa executar para resolver esse problema. Entre em contato com seu administrador e solicite uma investigação.<br /><br />Para saber mais sobre o problema, confira [Solucionar problemas de write-back de senha](troubleshoot-sspr-writeback.md). | SSPR_0029: Não é possível redefinir a senha devido a um erro na configuração local. Entre em contato com seu administrador e solicite uma investigação. |
| OnPremisesConnectivityError = 30 | Desculpe, não é possível redefinir sua senha no momento devido a problemas de conectividade para sua organização. Não há nenhuma ação a ser executada no momento, mas o problema pode ser resolvido se você tentar novamente mais tarde. Se o problema persistir, entre em contato com o seu administrador e solicite uma investigação.<br /><br />Para saber mais sobre problemas de conectividade, confira [Solução de problemas de conectividade de write-back de senha](troubleshoot-sspr-writeback.md). | SSPR_0030: Não é possível redefinir a senha devido a uma conexão fraca com o ambiente local. Entre em contato com seu administrador e solicite uma investigação.|

## <a name="azure-ad-forums"></a>Fóruns do Azure AD

Se você tiver perguntas gerais sobre o Azure AD e a redefinição de senha de autoatendimento, você poderá pedir à comunidade ajuda na [página do Microsoft Q&uma pergunta para Azure Active Directory](/answers/topics/azure-active-directory.html). Os membros da comunidade incluem engenheiros, gerentes de produto, MVPs e colegas profissionais de TI.

## <a name="contact-microsoft-support"></a>Contatar Suporte da Microsoft

Caso não encontre a resposta para um problema, nossas equipes de suporte sempre estarão disponíveis para fornecer assistência adicional.

Para uma assistência adequada, solicitamos que você forneça o máximo de detalhes possíveis ao abrir um caso. Esses detalhes incluem o seguinte:

* **Descrição geral do erro**: Qual é o erro? Qual foi o comportamento observado? Como podemos reproduzir o erro? Forneça o máximo de detalhes possível.
* **Página**: Em qual página você estava quando observou o erro? Se possível, inclua a URL e uma captura de tela da página.
* **Código de suporte**: Qual foi o código de suporte gerado quando o usuário viu o erro?
   * Para encontrar o código, reproduza o erro, selecione o link **Código de Suporte** na parte inferior da tela e envie o GUID resultante ao engenheiro de suporte.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="O código de suporte está localizado na parte inferior direita da janela do navegador da Web.":::

  * Se você estiver em uma página sem um código de suporte na parte inferior, selecione F12 para o SID e o CID e envie esses dois resultados para o engenheiro de suporte.
* **Data, hora e fuso horário**: Inclua a data e a hora exatas *com o fuso horário* em que ocorreu o erro.
* **ID de Usuário**: Qual usuário viu o erro? Um exemplo é *user\@contoso.com*.
   * Trata-se de um usuário federado?
   * Trata-se de um usuário de autenticação de passagem?
   * Um usuário sincronizado com hash de senha?
   * Ou de um usuário somente de nuvem?
* **Licenciamento**: O usuário tem uma licença do Azure AD atribuída?
* **Log de eventos do aplicativo**: Se estiver usando o write-back de senha e o erro estiver na infraestrutura local, inclua uma cópia compactada do log de eventos do aplicativo por meio do servidor do Azure AD Connect.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o SSPR, veja [como ele funciona: redefinição de senha de autoatendimento do Azure ad](concept-sspr-howitworks.md) ou [como o Write-back de redefinição de senha de autoatendimento funciona no Azure ad?](concept-sspr-writeback.md).
