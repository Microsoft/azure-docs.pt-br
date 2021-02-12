---
title: Como exportar um aplicativo de comandos personalizados como um serviço de fala de habilidades remota
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá a exportar comandos personalizados aplicativo como uma habilidade
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: sausin
ms.openlocfilehash: 77ade17803a35491712ec6df70aed9eb7b4883eb
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025867"
---
# <a name="export-custom-commands-application-as-a-remote-skill"></a>Exportar o aplicativo de comandos personalizados como uma habilidade remota

Neste artigo, você aprenderá a exportar um aplicativo de comandos personalizados como uma habilidade remota.

## <a name="prerequisites"></a>Pré-requisitos
> [!div class="checklist"]
> * [Noções básicas sobre habilidade do bot Framework](/azure/bot-service/skills-conceptual)
> * [Noções básicas sobre o manifesto de habilidades](https://aka.ms/speech/cc-skill-manifest)
> * [Como invocar uma habilidade de um bot do bot Framework](/azure/bot-service/skills-about-skill-consumers)
> * Um aplicativo de comandos personalizados existente. Caso você não tenha nenhum aplicativo de comandos personalizados, experimente com- [QuickStart: criar um assistente de voz usando comandos personalizados](quickstart-custom-commands-application.md)

## <a name="custom-commands-as-remote-skills"></a>Comandos personalizados como habilidades remotas
* As habilidades do bot Framework são reutilizáveis para a criação de habilidades de conversação, blocos que abrangem casos de uso de conversação, permitindo que você adicione ampla funcionalidade a um bot em minutos. Para ler mais sobre isso, vá para [habilidade do bot Framework](https://microsoft.github.io/botframework-solutions/overview/skills/).
* Um aplicativo de comandos personalizados pode ser exportado como uma habilidade. Essa habilidade pode ser invocada sobre o protocolo de habilidades remotas de um bot da estrutura de bot.

## <a name="configure-an-application-to-be-exposed-as-a-remote-skill"></a>Configurar um aplicativo para ser exposto como uma habilidade remota

### <a name="application-level-settings"></a>Configurações de nível de aplicativo
1. No painel esquerdo, selecione **configurações**  >  **habilidades remotas**.
1. A opção definir **habilitar habilidades remotas habilitada** para ativado.

### <a name="authentication-to-skills"></a>Autenticação para habilidades
1. Se você quiser habilitar a autenticação, adicione as IDs de aplicativo da Microsoft dos bots do bot Framework que você deseja configurar para chamar o aplicativo de comandos personalizados.
      > [!div class="mx-imgBorder"]
      > ![Adicionar uma ID do MSA às habilidades](media/custom-commands/skill-add-msa-id.png)

1. Se você tiver pelo menos uma entrada adicionada à lista, a autenticação será habilitada no aplicativo e somente os bots permitidos poderão chamar o aplicativo.
> [!TIP]
>  Para desabilitar a autenticação, exclua todas as IDs de aplicativo da Microsoft da lista de permissões. 

 ### <a name="enabledisable-commands-to-be-exposed-as-skills"></a>Habilitar/desabilitar comandos a serem expostos como habilidades

Você tem a opção de escolher quais comandos deseja exportar sobre habilidades remotas.

1. Para expor um comando sobre as habilidades, selecione **habilitar um novo comando** em **habilitar comandos para habilidades**.
1. No menu suspenso, selecione o comando que você pretende adicionar.
1. Selecione **Salvar**.

### <a name="configure-triggering-utterances-for-commands"></a>Configurar o disparo de declarações para comandos
Os comandos personalizados usam as frases de exemplo que são configuradas para os comandos a fim de gerar as habilidades que disparam declarações. Essas **declarações de disparo** serão usadas para gerar o [**manifesto de habilidade**](https://microsoft.github.io/botframework-solutions/skills/handbook/manifest/)da seção do **Dispatcher** .

Como autor, talvez você queira controlar quais das **frases de exemplo** são usadas para gerar o disparador declarações para habilidades.
1. Por padrão, todos os **exemplos de disparo** de um comando serão incluídos no arquivo de manifesto.
1. Se você quiser eliminar explicitamente qualquer exemplo, selecione o ícone **Editar** no comando na seção **comandos habilitados para habilidades** .
    > [!div class="mx-imgBorder"]
    > ![Editar um comando habilitado para habilidades](media/custom-commands/skill-edit-enabled-command.png)

1. Em seguida, nas sentenças de exemplo que você deseja omitir, clique com o **botão direito do mouse em**  >  **desabilitar sentença de exemplo**.
    > [!div class="mx-imgBorder"]
    > ![Desabilitar exemplos](media/custom-commands/skill-disable-example-sentences.png)

1. Selecione **Salvar**.
1. Você observará que não é possível adicionar um novo exemplo nessa janela. Se houver a necessidade de fazer isso, vá para sair da seção Configurações e selecione o comando relevante do acordeão de **comandos** . Neste ponto, você pode adicionar a nova entrada na seção de **sentenças de exemplo** . Essa alteração será refletida automaticamente no valor de configurações de habilidades remotas para o comando.

> [!IMPORTANT]
> Caso as frases de exemplo existentes tenham referências a **cadeia de caracteres >** tipo de dados do catálogo, essas frases serão omitidas automaticamente da lista de habilidades que disparam o declarações. 

## <a name="download-skill-manifest"></a>Baixar o manifesto de habilidades
1. Depois de **publicar** o aplicativo, você pode baixar o arquivo de manifesto de habilidades.
1. Use o manifesto de habilidades para configurar seu bot de consumidor do bot Framework para chamar para a habilidade de comandos personalizados.
> [!IMPORTANT]
> Você deve **publicar** o aplicativo de comandos personalizados para baixar o manifesto de habilidades. </br>
> Além disso, se você tiver feito **alterações** no aplicativo, será necessário publicar o aplicativo novamente para que as alterações mais recentes sejam refletidas no arquivo de manifesto.

> [!NOTE]
> Se você enfrentar quaisquer problemas com a publicação do aplicativo e o erro direcionar às habilidades disparando declarações, verifique novamente a configuração de **comandos habilitados para obter habilidades**. Cada um dos comandos expostos deve ter pelo menos um expressão de gatilho válido.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Atualizar um comando do cliente](./how-to-custom-commands-update-command-from-client.md)
