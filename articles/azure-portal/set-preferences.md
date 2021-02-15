---
title: Gerenciar configurações e preferências do portal do Azure
description: Você pode alterar portal do Azure configurações padrão para atender às suas próprias preferências. As configurações incluem tempo limite de sessão inativo, exibição padrão, modo de menu, contraste, tema, notificações e formatos regionais e de idioma
keywords: configurações, tempo limite, idioma, regional
ms.date: 08/05/2020
ms.topic: how-to
ms.openlocfilehash: 5a108f1cd3b5af6655b592e7283192d4d54498ac
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094103"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>Gerenciar configurações e preferências do portal do Azure

Você pode alterar as configurações padrão do portal do Azure para atender às suas próprias preferências. A maioria das configurações está disponível no menu **configurações** no cabeçalho da página global.

![Captura de tela mostrando ícones de cabeçalho de página global com configurações realçadas](./media/set-preferences/header-settings.png)


## <a name="choose-your-default-subscription"></a>Escolha sua assinatura padrão

Você pode alterar a assinatura que é aberta por padrão ao entrar no portal do Azure. Isso será útil se você tiver uma assinatura primária com a qual você trabalha, mas usar outras pessoas ocasionalmente. 

:::image type="content" source="media/set-preferences/filter-subscription-default-view.png" alt-text="Filtrar lista de recursos por assinatura.":::

1. Selecione o ícone de filtro de assinatura e diretório no cabeçalho da página global.

1. Selecione as assinaturas desejadas como assinaturas padrão ao iniciar o Portal. 

    :::image type="content" source="media/set-preferences/default-directory-subscription-filter.png" alt-text="Selecione as assinaturas desejadas como assinaturas padrão ao iniciar o Portal."::: 


## <a name="choose-your-default-view"></a>Escolha o modo de exibição padrão 

Você pode alterar a página que é aberta por padrão ao entrar no portal do Azure.

![Captura de tela mostrando configurações de portal do Azure com exibição padrão realçada](./media/set-preferences/default-view.png)

- A **página inicial** não pode ser personalizada.  Ele exibe atalhos para serviços populares do Azure e lista os recursos que você usou mais recentemente. Também fornecemos links úteis para recursos como Microsoft Learn e o roteiro do Azure.

- Os painéis podem ser personalizados para criar um espaço de trabalho criado apenas para você. Por exemplo, você pode criar um painel com foco no projeto, na tarefa ou na função. Se você selecionar **painel**, sua exibição padrão vai para o painel usado mais recentemente. Para obter mais informações, confira [Criar e compartilhar painéis no portal do Azure](azure-portal-dashboards.md).

## <a name="choose-a-portal-menu-mode"></a>Escolher um modo de menu do portal

O modo padrão do menu do portal controla a quantidade de espaço que o menu do portal ocupa na página.

![Captura de tela que mostra como definir o modo padrão para o menu do Portal.](./media/set-preferences/menu-mode.png)

- Quando o menu do portal estiver no modo de **submenu** , ele ficará oculto até que você precise dele. Selecione o ícone de menu para abrir ou fechar o menu.

- Se você escolher o **modo encaixado** para o menu do portal, ele estará sempre visível. Você pode recolher o menu para oferecer mais espaço de trabalho.

## <a name="choose-a-theme-or-enable-high-contrast"></a>Escolha um tema ou habilite o alto contraste

O tema escolhido afeta as cores de plano de fundo e fonte que aparecem no portal do Azure. Você pode selecionar um dos quatro temas de cor predefinidos. Selecione cada miniatura para encontrar o tema mais adequado para você.

Como alternativa, você pode escolher um dos temas de alto contraste. Os temas de alto contraste tornam a portal do Azure mais fácil de ler para as pessoas que têm um deficiência visual; Eles substituem todas as outras seleções de tema.

![Captura de tela mostrando configurações de portal do Azure com temas realçados](./media/set-preferences/theme.png)

## <a name="enable-or-disable-pop-up-notifications"></a>Habilitar ou desabilitar notificações pop-up

As notificações são mensagens do sistema relacionadas à sessão atual. Eles fornecem informações como seu saldo de crédito atual, quando os recursos que você acabou de criar ficam disponíveis ou confirmar sua última ação, por exemplo. Quando as notificações pop-up são ativadas, as mensagens são exibidas brevemente no canto superior da tela. 

Para habilitar ou desabilitar notificações pop-up, marque ou desmarque **habilitar notificações pop-up**.

![Captura de tela mostrando configurações de portal do Azure com notificações pop-up realçadas](./media/set-preferences/popup-notifications.png)

Para ler todas as notificações recebidas durante a sessão atual, selecione **notificações** no cabeçalho global.

![Captura de tela mostrando portal do Azure cabeçalho global com notificações realçadas](./media/set-preferences/read-notifications.png)

Se você quiser ler notificações de sessões anteriores, Procure eventos no log de atividades. Para obter mais informações, consulte [Exibir o log de atividades](../azure-monitor/platform/activity-log.md#view-the-activity-log). 

## <a name="change-the-inactivity-timeout-setting"></a>Alterar a configuração de tempo limite de inatividade

A configuração de tempo limite de inatividade ajuda a proteger recursos contra acesso não autorizado se você se esquecer de proteger sua estação de trabalho. Depois de ficar ocioso por algum tempo, você será automaticamente desconectado da sessão de portal do Azure. Como um indivíduo, você pode alterar a configuração de tempo limite para você mesmo. Se você for um administrador, poderá defini-lo no nível do diretório para todos os usuários no diretório.

### <a name="change-your-individual-timeout-setting-user"></a>Alterar a configuração de tempo limite individual (usuário)

Selecione a lista suspensa em **conectar-me quando inativo**. Escolha a duração após a qual sua sessão de portal do Azure será desconectada se você estiver ocioso.

![Captura de tela mostrando configurações do portal com configurações de tempo limite inativo realçadas](./media/set-preferences/inactive-signout-user.png)

A alteração é salva automaticamente. Se você estiver ocioso, sua sessão de portal do Azure será desconectada após a duração definida.

Se o administrador tiver habilitado uma política de tempo limite de inatividade, você ainda poderá definir o seu próprio, desde que seja menor que a configuração no nível do diretório. Selecione **substituir a política de tempo limite de inatividade de diretório** e defina um intervalo de tempo.

![Captura de tela mostrando as configurações do portal com substituir a configuração da política de tempo limite de inatividade do diretório](./media/set-preferences/inactive-signout-override.png)

### <a name="change-the-directory-timeout-setting-admin"></a>Alterar a configuração de tempo limite do diretório (admin)

Os administradores na [função de administrador global](../active-directory/roles/permissions-reference.md#global-administrator) podem impor o tempo ocioso máximo antes que uma sessão seja desconectada. A configuração de tempo limite de inatividade se aplica ao nível do diretório. A configuração entra em vigor para novas sessões. Ele não será aplicado imediatamente a nenhum usuário que já esteja conectado. Para obter mais informações sobre diretórios, consulte [Active Directory Domain Services visão geral](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

Se você for um administrador global e quiser impor uma configuração de tempo limite de ociosidade para todos os usuários da portal do Azure, siga estas etapas:

1. Selecione o texto do link **Configurar o tempo limite do nível do diretório**.

    ![Captura de tela mostrando as configurações do portal com texto do link realçado](./media/set-preferences/settings-admin.png)

1. Na página **Configurar tempo limite de inatividade de nível de diretório** , selecione **habilitar tempo limite de ociosidade de nível de diretório para a Portal do Azure** ativar a configuração.

1. Em seguida, insira as **horas** e os **minutos** para o tempo máximo que um usuário pode ficar ocioso antes de sua sessão ser desconectada automaticamente.

1. Selecione **Aplicar**.

    ![Captura de tela mostrando a página para definir o tempo limite de inatividade no nível do diretório](./media/set-preferences/configure.png)

Para confirmar se a política de tempo limite de inatividade está definida corretamente, selecione **notificações** no cabeçalho da página global. Verifique se uma notificação de êxito está listada.

![Captura de tela mostrando mensagem de notificação com êxito para tempo limite de inatividade no nível do diretório](./media/set-preferences/confirmation.png)

## <a name="restore-default-settings"></a>Restaurar configurações padrão

Se você fez alterações nas configurações de portal do Azure e deseja descartá-las, selecione **Restaurar configurações padrão**. As alterações feitas nas configurações do Portal serão perdidas. Essa opção não afeta as personalizações do painel.

![Captura de tela mostrando a restauração das configurações padrão](./media/set-preferences/useful-links-restore-defaults.png)

## <a name="export-user-settings"></a>Exportar configurações do usuário

As informações sobre suas configurações personalizadas são armazenadas no Azure. Você pode exportar os seguintes dados de usuário:

* Painéis privados no portal do Azure
* Configurações do usuário, como assinaturas ou diretórios favoritos e o último diretório conectado
* Temas e outras configurações personalizadas do portal

É uma boa ideia exportar e revisar suas configurações se você planeja excluí-las. Recriar painéis ou refazer configurações pode ser demorado.

Para exportar as configurações do portal, selecione **exportar todas as configurações**.

![Captura de tela mostrando a exportação de configurações](./media/set-preferences/useful-links-export-settings.png)

A exportação de configurações cria um arquivo *. JSON* que contém as configurações do usuário, como seu tema de cores, favoritos e painéis privados. Devido à natureza dinâmica das configurações do usuário e ao risco de dados corrompidos, você não pode importar as configurações do arquivo *. JSON* .

## <a name="delete-user-settings-and-dashboards"></a>Excluir configurações e painéis do usuário

As informações sobre suas configurações personalizadas são armazenadas no Azure. Você pode excluir os seguintes dados de usuário:

* Painéis privados no portal do Azure
* Configurações do usuário, como assinaturas ou diretórios favoritos e o último diretório conectado
* Temas e outras configurações personalizadas do portal

É uma boa ideia exportar e revisar suas configurações antes de excluí-las. Recriar painéis ou refazer configurações personalizadas pode ser demorado.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

Para excluir as configurações do portal, selecione **excluir todas as configurações e painéis particulares**.

![Captura de tela mostrando a exclusão das configurações](./media/set-preferences/useful-links-delete-settings.png)

## <a name="change-language-and-regional-settings"></a>Alterar idioma e configurações regionais

Há duas configurações que controlam como o texto no portal do Azure é exibido: 
- A configuração de **idioma** controla o idioma que você vê para texto na portal do Azure. 

- O **formato regional** controla a maneira como as datas, a hora, os números e a moeda são mostrados.

Para alterar o idioma usado no portal do Azure, use o menu suspenso para selecionar na lista de idiomas disponíveis.

A seleção de formato regional muda para exibir opções regionais somente para o idioma selecionado. Para alterar a seleção automática, use a lista suspensa para escolher o formato regional desejado.

Por exemplo, se você selecionar inglês como seu idioma e, em seguida, selecionar Estados Unidos como o formato regional, a moeda será mostrada em dólares americanos. Se você selecionar inglês como o idioma e, em seguida, selecionar Europa como o formato regional, a moeda será mostrada em euros.

Selecione **aplicar** para atualizar suas configurações de idioma e formato regional.

   ![Captura de tela mostrando configurações de idioma e formato regional](./media/set-preferences/language.png)

>[!NOTE]
>Essas configurações regionais e de idioma afetam apenas o portal do Azure. Links de documentação que abrem em uma nova guia ou janela usam as configurações de idioma do navegador para determinar o idioma a ser exibido.
>

## <a name="next-steps"></a>Próximas etapas

- [Atalhos de teclado no portal do Azure](azure-portal-keyboard-shortcuts.md)
- [Navegadores e dispositivos com suporte](azure-portal-supported-browsers-devices.md)
- [Adicionar, remover e reorganizar favoritos](azure-portal-add-remove-sort-favorites.md)
- [Criar e compartilhar painéis personalizados](azure-portal-dashboards.md)
- [Série de vídeos explicativos sobre o portal do Azure](azure-portal-video-series.md)
