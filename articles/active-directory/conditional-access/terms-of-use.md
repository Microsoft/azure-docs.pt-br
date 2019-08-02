---
title: Termos de uso - Azure Active Directory | Microsoft Docs
description: Introdução ao uso de termos de uso para apresentar as informações antes de obter acesso a funcionários ou convidados do Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: f891642761a2f692158efbd9111ff96444c4269d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476197"
---
# <a name="azure-active-directory-terms-of-use"></a>Termos de uso do Active Directory do Azure

Os termos de uso do AD do Azure fornece um método simples que as organizações podem usar para apresentar informações aos usuários finais. Essa apresentação faz com que os usuários vejam os avisos de isenção de responsabilidade relevantes para os requisitos de conformidade ou legais. Este artigo descreve como começar com os termos de uso.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Vídeos de visão geral

O vídeo a seguir fornece uma visão geral dos termos de uso.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Para ver outros vídeos, consulte:
- [Como implantar os termos de uso no Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Como distribuir os termos de uso no Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>O que pode fazer com os termos de uso?

Os termos de uso do AD do Azure tem os seguintes recursos:

- Exigir que os funcionários ou convidados aceitar os termos de uso antes de obter acesso.
- Exigir que os funcionários ou convidados aceitar os termos de uso em todos os dispositivos antes de obter acesso.
- Exigir que os funcionários ou convidados aceitar os termos de uso em um agendamento recorrente.
- Exigir que os funcionários ou convidados aceitar os termos de uso antes de registrar informações de segurança na autenticação de multifator do Azure (MFA).
- Exigir que os funcionários aceitar os termos de uso antes de registrar informações de segurança na redefinição de senha de autoatendimento do Azure AD (SSPR).
- Apresentar os termos gerais de uso para todos os usuários na sua organização.
- Apresentar os termos específicos de uso com base em atributos de um usuário (por exemplo médicos, enfermeiras, funcionários locais ou internacionais, usando [grupos dinâmicos](../users-groups-roles/groups-dynamic-membership.md)).
- Apresentar os termos de uso específicos ao acessar a aplicativos do impacto comercial alto, como o Salesforce.
- Apresentar os termos de uso em diferentes idiomas.
- Lista que tem ou não tiver aceitado seus termos de uso.
- Ajudar a atender às normas de privacidade.
- Exiba um log dos termos de uso de atividade para conformidade e auditoria.
- Criar e gerenciar os termos de uso usando [APIs do Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (atualmente em versão prévia).

## <a name="prerequisites"></a>Pré-requisitos

Para usar e configurar os termos de uso do Azure AD, você deve ter:

- Assinatura do Azure AD Premium P1, P2, EMS E3 ou EMS E5.
   - Se você não tiver uma dessas assinaturas, poderá [obter o Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) ou [habilitar a avaliação do Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Uma das seguintes contas de administrador para o diretório que você deseja configurar:
   - Administrador global
   - Administrador de segurança
   - Administrador de acesso condicional

## <a name="terms-of-use-document"></a>Documento Termos de uso

Termos de uso do Azure AD usa o formato PDF para apresentar conteúdo. O arquivo em PDF pode ter qualquer conteúdo, por exemplo, contratos existentes, o que permite que você colete os contratos do usuário final na entrada do usuário. Para dar suporte a usuários em dispositivos móveis, o tamanho de fonte recomendado em PDF é de 24 pontos.

## <a name="add-terms-of-use"></a>Adicionar termos de uso

Depois de preparar os seus termos de uso de documento, use o procedimento a seguir para adicioná-lo.

1. Entre no Azure como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

   ![Acesso condicional – termos de uso de folha](./media/terms-of-use/tou-blade.png)

1. Clique em **Novos termos**.

   ![Novo termo do painel de uso para especificar os termos de uso de configurações](./media/terms-of-use/new-tou.png)

1. No **nome** , digite um nome para os termos de uso que serão usadas no portal do Azure.
1. Na caixa **Nome de exibição**, insira um título que os usuários visualizam ao entrar.
1. Para **termos de uso documento**, navegue até seu finalizados termos de uso em PDF e selecioná-lo.
1. Selecione o idioma para os termos de uso de documento. A opção de idioma permite carregar vários termos de uso, cada um com um idioma diferente. A versão dos termos de uso que um usuário final verá terá base em suas preferências de navegador.
1. Para exigir que os usuários finais exibir os termos de uso antes de aceitá-los, defina **exigem que os usuários expandam os termos de uso** à **em**.
1. Para exigir que os usuários finais aceitar os termos de uso em todos os dispositivos estiverem acessando de, defina **exigir que os usuários em todos os dispositivos de consentimento** ao **em**. Para obter mais informações, consulte [termos de uso por dispositivo](#per-device-terms-of-use).
1. Se deseja expirar os termos de consentimentos de uso em um agendamento, defina **expirar consentimentos** à **em**. Quando definido como On, duas configurações de cronograma adicionais são exibidas.

   ![Expirar consentimentos configurações para definir a data de início, frequência e duração](./media/terms-of-use/expire-consents.png)

1. Usar o **expirar, iniciando em** e **frequência** configurações para especificar o agendamento para termos de expirações de usar. A tabela a seguir mostra o resultado para duas configurações de exemplo:

   | Expirar a partir de | Frequência | Result |
   | --- | --- | --- |
   | Data de hoje  | Mensalmente | A partir de hoje, os usuários devem aceitar os termos de uso e, em seguida, aceitem novamente todos os meses. |
   | Data no futuro  | Mensalmente | A partir de hoje, os usuários devem aceitar os termos de uso. Quando a data futura chegar, os consentimentos irão expirar e depois os usuários devem aceitar novamente a todos os meses.  |

   Por exemplo, se você definir a expiração a partir da data para **1 de janeiro** e a frequência para **Mensal**, segue como ocorreriam as expirações para dois usuários:

   | Usuário | Primeira data de aceitação | Primeira data de expiração | Segunda data de expiração | Terceira data de expiração |
   | --- | --- | --- | --- | --- |
   | Alice | 1 de janeiro | 1 de fevereiro | 1 de março | 1 de abril |
   | Roberto | 15 de janeiro | 1 de fevereiro | 1 de março | 1 de abril |

1. Use o **duração antes de (dias) requer o re-aceitação** configuração para especificar o número de dias antes que o usuário deve aceitem novamente os termos de uso. Isso permite que os usuários sigam seu próprio cronograma. Por exemplo, se você definir a duração como **30** dias, segue como ocorreriam as expirações para dois usuários:

   | Usuário | Primeira data de aceitação | Primeira data de expiração | Segunda data de expiração | Terceira data de expiração |
   | --- | --- | --- | --- | --- |
   | Alice | 1 de janeiro | 31 de janeiro | 2 de março | 1 de abril |
   | Roberto | 15 de janeiro | 14 de fevereiro | 16 de março | 15 de abril |

   É possível usar as configurações **Expirar consentimentos** e **Duração (dias) até exigir a nova aceitação** juntas, mas normalmente você usa uma ou a outra.

1. Sob **acesso condicional**, use o **impor com o modelo de política de acesso condicional** lista para selecionar o modelo para impor os termos de uso.

   ![Lista de lista suspensa de acesso condicional para selecionar um modelo de política](./media/terms-of-use/conditional-access-templates.png)

   | Modelo | DESCRIÇÃO |
   | --- | --- |
   | **Acesso a aplicativos de nuvem para todos os convidados** | Uma política de acesso condicional será criada para todos os convidados e todos os aplicativos de nuvem. Essa política afeta o portal do Azure. Após ela ser criada, talvez seja necessário sair e entrar novamente. |
   | **Acesso a aplicativos de nuvem para todos os usuários** | Uma política de acesso condicional será criada para todos os usuários e todos os aplicativos de nuvem. Essa política afeta o portal do Azure. Após ela ser criada, será necessário sair e entrar novamente. |
   | **Política personalizada** | Selecione os usuários, grupos e aplicativos que serão aplicados a estes termos de uso. |
   | **Criar política de acesso condicional mais tarde** | Estes termos de uso aparecerá na lista de controle de concessão ao criar uma política de acesso condicional. |

   >[!IMPORTANT]
   >Controles de política de acesso condicionais (incluindo termos de uso) não dá suporte à imposição em contas de serviço. É recomendável excluir todas as contas de serviço da política de acesso condicional.

    Políticas de acesso condicional personalizadas permitem termos de uso, para baixo até um aplicativo de nuvem específica ou um grupo de usuários granulares. Para saber mais, confira [Início Rápido: Exigir a aceitação dos termos de uso antes de acessar os aplicativos de nuvem](require-tou.md).

1. Clique em **Criar**.

   Se você tiver selecionado um modelo de acesso condicional personalizado, uma nova tela é exibida que permite que você crie a política de acesso condicional personalizada.

   ![Novo painel de acesso condicional se você escolheu o modelo de política de acesso condicional personalizado](./media/terms-of-use/custom-policy.png)

   Agora você deve ver seus novos termos de uso.

   ![Novos termos de uso listados nos termos da folha de uso](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Exibir relatório de quem aceitou e recusou

A folha Termos de uso mostra uma contagem de usuários que os aceitaram e recusaram. Essas contagens e quem aceitou/recusadas são armazenados durante a vida útil dos termos de uso.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

   ![Termos de folha de uso listando o número de Mostrar usuário aceitaram e recusaram](./media/terms-of-use/view-tou.png)

1. Para termos de uso, clique no número sob **aceito** ou **recusadas** para exibir o estado atual para os usuários.

   ![Termos de uso de painel de consentimentos listando os usuários que aceitaram](./media/terms-of-use/accepted-tou.png)

1. Para exibir o histórico de um usuário individual, clique no botão de reticências ( **...** ) e, em seguida **Exibir histórico**.

   ![Menu de contexto de histórico de exibição para um usuário](./media/terms-of-use/view-history-menu.png)

   No painel de exibição de histórico, você vê um histórico de todos os aceites, recusas e expirações.

   ![Exibir o histórico aceita o painel lista de histórico, recusas e expirações de um usuário](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Exibir logs de auditoria do Azure AD

Se você quiser exibir a atividade adicional, termos de uso do Azure AD inclui logs de auditoria. Cada consentimento do usuário dispara um evento nos logs de auditoria que ficam armazenados durante **30 dias**. Você pode exibir esses logs no portal ou baixá-los como um arquivo .csv.

Para começar a usar os logs de auditoria do Microsoft Azure AD, use o procedimento a seguir:

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de uso.
1. Clique em **Exibir logs de auditoria**.

   ![Termos da folha de uso com o modo de exibição realçada de opção de logs de auditoria](./media/terms-of-use/audit-tou.png)

1. Na tela de logs de auditoria do Azure AD, é possível filtrar as informações usando as listas fornecidas para buscar informações de log de auditoria específico.

   Você também pode clicar em **Download** para baixar as informações em um arquivo .csv para uso local.

   ![Listagem data, a política de destino, iniciada por e a atividade de tela de logs de auditoria do AD do Azure](./media/terms-of-use/audit-logs-tou.png)

   Se você clicar em um log, um painel é exibido com detalhes adicionais da atividade.

   ![Detalhes de atividade para um log mostrando a atividade, status da atividade, iniciada por, direcionar a política](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Quais termos de uso é semelhante para os usuários

Depois de termos de uso é criado e imposta, os usuários, que estão no escopo, verá a tela a seguir durante a entrada.

![Exemplo de termos de uso que aparece quando um usuário entra em](./media/terms-of-use/user-tou.png)

Os usuários podem exibir os termos de uso e, se necessário, use os botões para ampliar e reduzir.

![Exibição dos termos de uso com botões de zoom](./media/terms-of-use/zoom-buttons.png)

A tela a seguir mostra a aparência dos termos de uso em dispositivos móveis.

![Exemplo de termos de uso que aparece quando um usuário faz logon em um dispositivo móvel](./media/terms-of-use/mobile-tou.png)

Os usuários só precisam aceitar os termos de uso de uma vez e não poderão ver os termos de uso novamente em conexões subsequentes.

### <a name="how-users-can-review-their-terms-of-use"></a>Como os usuários podem examinar seus termos de uso

Os usuários podem examinar e ver os termos de uso que eles tiverem aceitado usando o procedimento a seguir.

1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. No canto superior direito, clique em seu nome e selecione **Perfil**.

   ![Site do MyApps com aberto de painel do usuário](./media/terms-of-use/tou14.png)

1. Na página de seu perfil, clique em **Analisar termos de uso**.

   ![Página de perfil para um usuário, mostrando os termos de revisão de link de uso](./media/terms-of-use/tou13a.png)

1. A partir daí, você pode revisar os termos de uso aceitos.

## <a name="edit-terms-of-use-details"></a>Editar os termos de detalhes de uso

Você pode editar alguns detalhes dos termos de uso, mas não é possível modificar um documento existente. O procedimento a seguir descreve como editar os detalhes.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de uso que você deseja editar.
1. Clique em **editar termos**.
1. Em termos de edição do painel de uso, altere o nome, nome de exibição ou exigir que os usuários expandam os valores.

   Se não houver outras configurações que você deseja alterar, como o documento PDF, exigir que os usuários consentir em todos os dispositivos, expirar consentimentos, duração antes reacceptance ou política de acesso condicional, você deve criar novos termos de uso.

   ![Editar os termos de nome de exibição do painel de uso e opções](./media/terms-of-use/edit-tou.png)

1. Clique em **Salvar** para salvar as alterações.

   Depois de salvar as alterações, os usuários terão que aceitar novamente essas edições.

## <a name="add-a-terms-of-use-language"></a>Adicionar termos de uso de linguagem

O procedimento a seguir descreve como adicionar termos de uso de idioma.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de uso que você deseja editar.
1. No painel de detalhes, clique na guia **Idiomas**.

   ![Termos de uso selecionado e mostrando a guia de idiomas no painel de detalhes](./media/terms-of-use/languages-tou.png)

1. Clique em **Adicionar idioma**.
1. Em termos do painel de linguagem do uso de Add, carregar o PDF localizado e selecione o idioma.

   ![Adicionar termos de painel de linguagem de uso com opções para carregar PDFs localizadas](./media/terms-of-use/language-add-tou.png)

1. Clique em **adicionar** para adicionar o idioma.

## <a name="per-device-terms-of-use"></a>Termos de uso por dispositivo

O **exigem o consentimento em todos os dispositivos dos usuários** configuração permite que você exija que os usuários finais aceitar os termos de uso em todos os dispositivos estiverem acessando de. O usuário final precisará ingressar o dispositivo dele no Azure AD. Quando o dispositivo está associado, a ID do dispositivo é usada para impor os termos de uso em cada dispositivo.

Segue uma lista dos softwares e plataformas com suporte.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Outros |
> | --- | --- | --- | --- | --- |
> | **Aplicativo nativo** | Sim | sim | Sim |  |
> | **Microsoft Edge** | Sim | sim | Sim |  |
> | **Internet Explorer** | Sim | sim | Sim |  |
> | **Chrome (com extensão)** | Sim | sim | Sim |  |

Termos de acordo com o dispositivo de uso tem as seguintes restrições:

- Um dispositivo somente pode ser unido a um locatário.
- Um usuário deve ter permissões para ingressar seu dispositivo.
- Não há suporte para o aplicativo de registro do Intune.
- Não há suporte para os usuários de B2B do AD do Azure.

Se o dispositivo do usuário não estiver ingressado, ele receberá uma mensagem de que é necessário ingressar o dispositivo dele. A experiência dele será dependente da plataforma e do software.

### <a name="join-a-windows-10-device"></a>Ingressar um dispositivo com Windows 10

Se um usuário estiver usando o Windows 10 e o Microsoft Edge, ele receberá uma mensagem semelhante à seguinte para [ingressar o dispositivo](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 e Microsoft Edge - mensagem indicando que o dispositivo devem ser registrado](./media/terms-of-use/per-device-win10-edge.png)

Se estiver usando o Chrome, o usuário será solicitado a instalar a [Extensão de Contas do Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="browsers"></a>Navegadores

Se um usuário estiver usando um navegador sem suporte, ele será solicitado a usar um navegador diferente.

![Mensagem indicando que o seu dispositivo deve ser registrado, mas não há suporte para o navegador](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Excluir termos de uso

Você pode excluir o antigos termos de uso usando o procedimento a seguir.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de uso que você deseja remover.
1. Clique em **Excluir termos**.
1. Na mensagem que será exibida perguntando se deseja continuar, clique em **Sim**.

   ![Mensagem que solicita confirmação excluir termos de uso](./media/terms-of-use/delete-tou.png)

   Você não deve ver os termos de uso.

## <a name="deleted-users-and-active-terms-of-use"></a>Usuários excluídos e os termos de uso do Active Directory

Por padrão, um usuário excluído permanece excluído no Azure AD por 30 dias, durante os quais ele pode ser restaurado por um administrador, se necessário. Após 30 dias, esse usuário será excluído permanentemente. Além disso, usando o portal do Azure Active Directory, um Administrador Global pode explicitamente [excluir permanentemente um usuário excluído recentemente](../fundamentals/active-directory-users-restore.md) antes de atingir o período de tempo. Um usuário foi excluído permanentemente, dados subsequentes sobre o que o usuário serão removidos do Active Directory termos de uso. Auditar informações sobre usuários excluídos na trilha de auditoria.

## <a name="policy-changes"></a>Alterações na política

Políticas de acesso condicional entram em vigor imediatamente. Quando isso acontece, o administrador começa a ver uma imagem de "nuvens tristes" ou "problemas de token do Azure AD". O administrador precisa sair e entrar novamente para atender à nova política.

> [!IMPORTANT]
> Os usuários que estão no escopo precisarão sair e entrar novamente para atender a uma nova política se:
>
> - uma política de acesso condicional é habilitada em termos de uso
> - ou se outros termos de uso forem criados

## <a name="b2b-guests-preview"></a>Convidados B2B (versão prévia)

A maioria das organizações tem um processo em vigor para seus funcionários para concordar com os termos de sua organização de instruções de uso e a privacidade. Mas como você pode impor os mesmos consentimentos para convidados B2B (business-to-business) do Azure AD quando eles forem adicionados por meio do SharePoint ou Teams? Usando o acesso condicional e termos de uso, você pode impor uma política diretamente para os usuários convidados de B2B. Durante o fluxo de resgate do convite, o usuário é apresentado com os termos de uso. No momento, esse suporte está na versão prévia.

Termos de uso só serão exibidos quando o usuário tiver uma conta de convidado no Azure AD. SharePoint Online no momento, tem um [experiência de destinatário compartilhamento externa ad-hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) para compartilhar um documento ou uma pasta que não requer que o usuário tenha uma conta de convidado. Nesse caso, os termos de uso não é exibida.

![Incluir usuários e grupos - guia com a opção de todos os usuários de convidado é verificada](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Suporte para aplicativos de nuvem (versão prévia)

Termos de uso podem ser usados para diferentes aplicativos de nuvem, como Proteção de Informações do Azure e Microsoft Intune. No momento, esse suporte está na versão prévia.

### <a name="azure-information-protection"></a>Proteção de Informações do Azure

Você pode configurar uma política de acesso condicional para o aplicativo de proteção de informações do Azure e exigem termos de uso, quando um usuário acessa um documento protegido. Isso vai disparar um termos de uso antes de um usuário que acessa um documento protegido pela primeira vez.

![Painel de aplicativos de nuvem com o aplicativo de proteção de informações do Microsoft Azure selecionado](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Registro do Microsoft Intune

Você pode configurar uma política de acesso condicional para o aplicativo de registro do Microsoft Intune e exigem termos de uso antes do registro de um dispositivo no Intune. Para obter mais informações, leia [Escolher a solução de Termos correta para a postagem no blog da organização](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Painel de aplicativos de nuvem com o Microsoft Intune aplicativo selecionado](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Não há suporte para o aplicativo de registro do Intune para [termos de uso por dispositivo](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: Como ver quando / se um usuário aceitou os termos de uso?**<br />
R: Na folha de Termos de uso, clique no número em **Aceito**. Também é possível exibir ou pesquisar a atividade de aceitação nos logs de auditoria do Azure AD. Para obter mais informações, confira Exibir relatório de quem aceitou e recusou e [Exibir logs de auditoria do Azure AD](#view-azure-ad-audit-logs).

**P: Por quanto tempo as informações ficam armazenadas?**<br />
R: Conta de usuário nos termos do relatório de uso e que aceita/recusadas são armazenadas durante a vida útil dos termos de uso. Os logs de auditoria do Azure AD são armazenados por 30 dias.

**P: Por que vejo logs de auditoria de um número diferente de consentimentos em termos de uso relatório versus o Azure AD?**<br />
R: Os termos de uso de relatório é armazenado para o tempo de vida dos termos de uso, enquanto a auditoria do AD do Azure que os logs são armazenados por 30 dias. Além disso, os termos de uso de relatório só exibe o estado de consentimento dos usuários atuais. Por exemplo, se um usuário recusa e, em seguida, aceita, os termos de uso de relatório somente mostrará que o usuário aceite. Se você precisar ver o histórico, poderá usar os logs de auditoria do Azure AD.

**P: Se eu editar os detalhes para termos de uso, ele requer que os usuários aceitem novamente?**<br />
R: Não, se um administrador editar os detalhes para termos de uso (nome, nome de exibição, exigem que os usuários expandam ou adicionar um idioma), ele não requer que os usuários aceitem novamente os novos termos.

**P: Posso atualizar uma existente de condições de uso de documento?**<br />
R: No momento, você não pode atualizar uma existente de condições de uso de documento. Para alterar os termos de uso de documento, você terá que criar novos termos de uso de instância.

**P: Se forem hiperlinks em termos de usar o documento PDF, os usuários finais conseguirá clicar nelas?**<br />
R: O PDF é renderizado por padrão como JPEG, portanto, os hiperlinks não são clicáveis. Os usuários têm a opção de selecionar **Você está tendo problemas para exibir? Clique aqui**, que renderiza o PDF nativamente onde os hiperlinks têm suporte.

**P: Termos de uso podem dar suporte a vários idiomas?**<br />
R: Sim. Atualmente, há 108 idiomas diferentes, um administrador pode configurar para um único termos de uso. Um administrador pode carregar vários documentos em PDF e marcar esses documentos com um idioma correspondente (até 108). Quando os usuários finais entram, examinamos a preferência de idioma do navegador deles e exibimos o documento correspondente. Se não houver nenhuma correspondência, exibimos o documento padrão, que é o primeiro documento carregado.

**P: Quando é os termos de uso são acionados?**<br />
R: Os termos de uso é disparada durante a experiência de entrada.

**P: Quais aplicativos posso empregar os termos de uso para?**<br />
R: Você pode criar uma política de acesso condicional em aplicativos empresariais usando autenticação moderna. Para obter mais informações, consulte [aplicativos empresariais](./../manage-apps/view-applications-portal.md).

**P: Pode adicionar vários termos de uso para um determinado usuário ou aplicativo?**<br />
R: Sim, com a criação de várias políticas de acesso condicional direcionar esses grupos ou aplicativos. Se um usuário estiver no escopo de vários termos de uso, eles aceitam uma termos de uso por vez.

**P: O que acontece se um usuário recusar os termos de uso?**<br />
R: O usuário é impedido de acessar o aplicativo. O usuário precisa entrar novamente e aceitar os termos para obter acesso.

**P: É possível unaccept termos de uso que anteriormente foi aceita?**<br />
R: Você pode [revisão anteriormente aceitou os termos de uso](#how-users-can-review-their-terms-of-use), mas atualmente não há uma maneira unaccept.

**P: O que acontece se também estiver usando os termos e condições do Intune?**<br />
R: Se você configurou ambos os termos de uso do AD do Azure e [Intune dos termos e condições](/intune/terms-and-conditions-create), o usuário precisará aceitar os dois. Para obter mais informações, consulte a [Escolha da solução de Termos correta para a publicação do seu blog da organização](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**P: Quais pontos de extremidade os termos de uso de serviço pode usar para autenticação?**<br />
R: Termos de uso utiliza os seguintes pontos de extremidade para autenticação: https://tokenprovider.termsofuse.identitygovernance.azure.com e https://account.activedirectory.windowsazure.com. Se sua organização tiver uma lista de permissões de URLs para o registro, você precisará adicionar esses pontos de extremidade para sua lista de permissões, juntamente com os pontos de extremidade do Azure AD para entrar.

## <a name="next-steps"></a>Próximas etapas

- [Início rápido: Exigir a aceitação dos termos de uso antes de acessar os aplicativos de nuvem](require-tou.md)
- [Práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md)
