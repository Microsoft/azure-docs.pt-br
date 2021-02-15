---
title: Habilitar o suporte para Avere vFXT - Azure
description: Saiba como habilitar o carregamento automático de dados de suporte sobre o cluster do avere vFXT para o Azure para ajudar o suporte a fornecer atendimento ao cliente.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: 93b99aa624a21d9312297e4279b1dcf053c79ae3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88272721"
---
# <a name="enable-support-uploads"></a>Habilitar uploads de suporte

O avere vFXT para Azure pode carregar automaticamente os dados de suporte sobre o cluster. Esses carregamentos permitem que a equipe de suporte fornecem o melhor serviço possível para o cliente.

## <a name="steps-to-enable-uploads"></a>Etapas para ativar uploads

Siga as etapas do painel de controle Avere para ativar o suporte. (Leia [acessar o cluster vFXT](avere-vfxt-cluster-gui.md) para saber como abrir o painel de controle.)

1. Navegue até a **configurações** guia na parte superior.
1. Clique no link **Suporte** à esquerda e aceite a política de privacidade.

   ![Captura de tela mostrando o painel de Controle do Avere e uma janela pop-up com o botão Confirmar para aceitar a política de privacidade](media/avere-vfxt-privacy-policy.png)

1. Na página configuração de suporte, abra a seção **informações do cliente** clicando no triângulo à esquerda.
1. Clique no botão **Revalidar informações de carregamento**.
1. Defina o nome de suporte do cluster em **nome de cluster exclusivo**. Verifique se esse nome identifica exclusivamente o cluster para dar suporte à equipe.
1. Marque as caixas de **Monitoramento de Estatísticas**, **Upload de Informações Gerais** e **Upload de Informações de Falhas**.
1. Clique em **Enviar**.

   ![Captura de tela que contém a seção de informações do cliente concluída da página de configurações de suporte](media/avere-vfxt-support-info.png)

1. Clique no triângulo à esquerda de **Secure Proactive Support (SPS)** para expandir a seção.
1. Marque a caixa **habilitar Link do SPS**.
1. Clique em **Enviar**.

   ![Captura de tela que contém a seção Suporte Proativo Seguro na página de configurações de suporte](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Próximas etapas

Se você precisar adicionar um sistema de armazenamento em nuvem local ou existente ao cluster, siga as instruções em [Configurar armazenamento](avere-vfxt-add-storage.md).

Se você estiver pronto para começar a anexação de clientes para o cluster, leia [montar o cluster de vFXT Avere](avere-vfxt-mount-clients.md).
