---
title: Problemas conhecidos do Azure Percept
description: Saiba mais sobre os problemas conhecidos do Azure Percept e suas soluções alternativas
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: reference
ms.date: 03/25/2021
ms.openlocfilehash: 49ac497505930d82a3ab8e90fb3f386cc1741dc7
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605052"
---
# <a name="known-issues"></a>Problemas conhecidos

Se você encontrar algum desses problemas, não será necessário abrir um bug. Se você tiver problemas com qualquer uma das soluções alternativas, abra um problema.

|Área|Descrição do problema|Solução alternativa|
|-------|---------|---------|
| Experiência de integração | Não é possível concluir a experiência de integração, a menos que o Wi-Fi do dispositivo esteja configurado (falha no logon do Azure). | 1. [SSH em seu Azure PERCEPT DK](./how-to-ssh-into-percept-dk.md). <br> 2. identifique e copie o endereço IP da Ethernet do dispositivo. <br> 3. Conecte-se à experiência de integração usando a URL baseada em IP de Ethernet. |
| Experiência de integração | O clique nos links no EULA (contrato de licença), às vezes, não abre uma nova página da Web. | Copie o link e abra-o em uma janela separada do navegador. |
| Experiência de integração | Não é possível trabalhar com a experiência de integração quando conectada a um hotspot móvel Wi-Fi. | Conecte seu dispositivo diretamente ao SoftAP, a uma rede Wi-Fi ou a uma rede pela Ethernet. |
| Wi-Fi | Às vezes, o SoftAP pode desconectar ou desaparecer. | Estamos investigando.  A reinicialização do dispositivo normalmente o levará de volta. |
| Wi-Fi | O botão de hardware que alterna o Wi-Fi SoftAP, às vezes, não funciona. | Continue pressionando o botão ou reinicialize o dispositivo. |
| Wi-Fi | Os usuários poderão ver uma mensagem depois de se conectarem ao Wi-Fi: <br> "Esta Wi-Fi rede usa um padrão de segurança mais antigo". | O SoftAP da devkit usa o algoritmo de criptografia WEP. |
| Wi-Fi | Não é possível conectar-se ao SoftAP do PC com Windows 10 com a seguinte mensagem de erro: <br> "Não é possível conectar-se a esta rede" | Reinicialize o devkit e o computador. |
| Atualização de dispositivo | Os contêineres não são executados após uma atualização OTA. | SSH no dispositivo e reinicie o contêiner de IoT Edge com este comando: `systemctl restart iotedge` . Isso reiniciará todos os contêineres. |
| Atualização de dispositivo | Os usuários podem receber uma mensagem informando que a atualização falhou, mesmo que tenha êxito. | Confirme a atualização navegando até o dispositivo do devkit no Hub IoT e verificando o valor de `swVersion` . Isso é corrigido após a primeira atualização. |
| Atualização de dispositivo | Os usuários podem perder suas configurações de Wi-Fi conexão após a primeira atualização. | Execute a experiência de integração após a atualização para configurar a conexão de Wi-Fi. Isso é corrigido após a primeira atualização. |
| Atualização de dispositivo | Depois de executar uma atualização OTA, os usuários não poderão mais fazer logon via SSH usando contas de usuário criadas anteriormente e novos usuários SSH não poderão ser criados por meio da experiência de integração. Esse problema afeta os sistemas que executam atualizações OTA das seguintes versões de imagem pré-instaladas: 2020.110.114.105 e 2020.109.101.105. | Para recuperar seus perfis de usuário, execute estas etapas após a atualização OTA: <br> [SSH em seu devkit](./how-to-ssh-into-percept-dk.md) usando "root" como o nome de usuário. Se você tiver desabilitado o logon de usuário "raiz" do SSH por meio da experiência de integração, será necessário habilitá-lo novamente. Execute este comando depois de se conectar com êxito: <br> ```mkdir -p /var/custom-configs/home; chmod 755 /var/custom-configs/home``` <br> Para recuperar dados residenciais do usuário anterior, execute o seguinte comando: <br> ```mkdir -p /tmp/prev-rootfs && mount /dev/mmcblk0p3 /tmp/prev-rootfs && [ ! -L /tmp/prev-rootfs/home ] && cp -a /tmp/prev-rootfs/home/* /var/custom-configs/home/. && echo "User home migrated!"; umount /tmp/prev-rootfs``` |
| Atualização de dispositivo | Depois de fazer uma atualização OTA, os grupos de atualização são perdidos. | Atualize a marca do dispositivo seguindo [estas instruções](./how-to-update-over-the-air.md#create-a-device-update-group). |
| Instalador do Pacote de Ferramentas de Desenvolvimento | A instalação opcional do Caffe poderá falhar se o Docker não estiver sendo executado corretamente no sistema. | Verifique se o Docker está instalado e em execução e repita a instalação do Caffe. |
| Instalador do Pacote de Ferramentas de Desenvolvimento | A instalação opcional do CUDA falha em sistemas incompatíveis. | Verifique a compatibilidade do sistema com o CUDA antes de executar o instalador. |
| Docker, rede IoT Edge | Se sua rede interna usar 172. x. x. x, os contêineres do Docker não conseguirão se conectar ao IoT Edge. | Adicione uma seção bip especial ao/etc/Docker/daemon.jsno arquivo como este: `{    "bip": "192.168.168.1/24"}` |
|Azure Percept Studio | Os links "Exibir fluxo" no Azure Percept Studio não abrem uma nova janela mostrando o fluxo da Web do dispositivo. | 1. Abra o [portal do Azure](https://portal.azure.com) e selecione **Hub IOT**. <br> 2. Clique no Hub IoT ao qual seu dispositivo está conectado. <br> 3. Selecione **IOT Edge** em **Gerenciamento de dispositivo automático** na página do Hub IOT. <br> 4. Selecione seu dispositivo na lista. <br> 5. Selecione **definir módulos** na parte superior da página do seu dispositivo. <br> 6. Clique no ícone de Lixeira ao lado de **HostIpModule** para excluir o módulo. <br> 7. para confirmar a ação, clique em **revisar + criar** e em **criar**. <br> 8. Abra o [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) e clique em **dispositivos** no painel de menu à esquerda. <br> 9. Selecione seu dispositivo na lista. <br> 10. na guia **visão** , clique em **Exibir o fluxo do dispositivo**. Seu dispositivo baixará uma nova versão do HostIpModule e abrirá uma guia do navegador com o fluxo da Web do seu dispositivo. |