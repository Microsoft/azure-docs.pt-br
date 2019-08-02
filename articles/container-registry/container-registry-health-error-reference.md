---
title: Referência de erro para verificação de integridade-registro de contêiner do Azure
description: Códigos de erro e possíveis soluções para problemas encontrados ao executar o comando AZ ACR check-Health Diagnostic no registro de contêiner do Azure
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 672d446fa8dc27612c7b046cac109bfa4ca5fec5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309369"
---
# <a name="health-check-error-reference"></a>Referência de erro de verificação de integridade

A seguir estão os detalhes sobre os códigos de erro retornados pelo comando [AZ ACR check-Health][az-acr-check-health] . Para cada erro, as soluções possíveis são listadas.

## <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

Esse erro significa que não foi possível encontrar o cliente do Docker para a CLI. Como resultado, as seguintes verificações adicionais não são executadas: localizando a versão do Docker, avaliando o status do daemon do Docker e executando um comando de pull do Docker.

*Soluções potenciais*: Instalar o cliente do Docker; Adicione o caminho do Docker às variáveis do sistema.

## <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

Esse erro significa que o status do daemon do Docker não está disponível ou não foi possível acessá-lo usando a CLI. Como resultado, as operações do Docker ( `docker login` como e `docker pull`) não estão disponíveis por meio da CLI.

*Soluções potenciais*: Reinicie o daemon do Docker ou valide se ele está instalado corretamente.

## <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

Esse erro significa que a CLI não pôde executar o comando `docker --version`.

*Soluções potenciais*: Tente executar o comando manualmente, verifique se você tem a versão mais recente da CLI e investigue a mensagem de erro.

## <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

Esse erro significa que a CLI não pôde extrair uma imagem de exemplo para o seu ambiente.

*Soluções potenciais*: Valide se todos os componentes necessários para efetuar pull de uma imagem estão sendo executados corretamente.

## <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

Esse erro significa que o cliente Helm não pôde ser encontrado pela CLI, que impede outras operações Helm.

*Soluções potenciais*: Verifique se o cliente Helm está instalado e se seu caminho foi adicionado às variáveis de ambiente do sistema.

## <a name="helmversionerror"></a>HELM_VERSION_ERROR

Esse erro significa que a CLI não pôde determinar a versão do Helm instalada. Isso pode acontecer se a versão de CLI do Azure (ou se a versão Helm) que está sendo usada for obsoleta.

*Soluções potenciais*: Atualizar para a versão mais recente do CLI do Azure ou para a versão recomendada do Helm; Execute o comando manualmente e investigue a mensagem de erro.

## <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

Esse erro significa que o DNS do servidor de logon do registro fornecido foi pingado, mas não respondeu, o que significa que ele não está disponível. Isso pode indicar alguns problemas de conectividade. Como alternativa, o registro pode não existir, o usuário pode não ter as permissões no registro (para recuperar seu servidor de logon corretamente) ou o registro de destino está em uma nuvem diferente daquela usada no CLI do Azure.

*Soluções potenciais*: Validar a conectividade; Verifique a ortografia do registro e se o registro existe; Verifique se o usuário tem as permissões corretas e se a nuvem do registro é a mesma que é usada no CLI do Azure.

## <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

Esse erro significa que o ponto de extremidade de desafio para o registro fornecido respondeu com um status HTTP 403 Proibido. Esse erro significa que os usuários não têm acesso ao registro, provavelmente devido a uma configuração de rede virtual.

*Soluções potenciais*: Remova as regras de rede virtual ou adicione o endereço IP do cliente atual à lista de permissões.

## <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

Esse erro significa que o ponto de extremidade de desafio do registro de destino não emitiu um desafio.

*Soluções potenciais*: Tente novamente após algum tempo. Se o erro persistir, abra um problema em https://aka.ms/acr/issues.

## <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Esse erro significa que o ponto de extremidade de desafio do registro de destino emitiu um desafio, mas o registro não dá suporte à autenticação de Azure Active Directory.

*Soluções potenciais*: Experimente uma maneira diferente de autenticar, por exemplo, com credenciais de administrador. Se os usuários precisarem se autenticar usando Azure Active Directory, abra https://aka.ms/acr/issues um problema em.

## <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Esse erro significa que o servidor de logon do registro não respondeu com um token de atualização, portanto, o acesso ao registro de destino foi negado. Esse erro pode ocorrer se o usuário não tiver as permissões corretas no registro ou se as credenciais do usuário para o CLI do Azure estiverem obsoletas.

*Soluções potenciais*: Verifique se o usuário tem as permissões corretas no registro; Execute `az login` para atualizar permissões, tokens e credenciais.

## <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Esse erro significa que o servidor de logon do registro não respondeu com um token de acesso, de modo que o acesso ao registro de destino foi negado. Esse erro pode ocorrer se o usuário não tiver as permissões corretas no registro ou se as credenciais do usuário para o CLI do Azure estiverem obsoletas.

*Soluções potenciais*: Verifique se o usuário tem as permissões corretas no registro; Execute `az login` para atualizar permissões, tokens e credenciais.

## <a name="loginservererror"></a>LOGIN_SERVER_ERROR

Esse erro significa que a CLI não pôde localizar o servidor de logon do registro fornecido e nenhum sufixo padrão foi encontrado para a nuvem atual. Esse erro pode ocorrer se o registro não existir, se o usuário não tiver as permissões corretas no registro, se a nuvem do registro e a nuvem de CLI do Azure atual não corresponderem ou se a versão do CLI do Azure estiver obsoleta.

*Soluções potenciais*: Verifique se a ortografia está correta e se o registro existe; Verifique se o usuário tem as permissões corretas no registro e se as nuvens do registro e o ambiente da CLI correspondem; Atualize CLI do Azure para a versão mais recente.

## <a name="next-steps"></a>Próximas etapas

Para obter opções para verificar a integridade de um registro, consulte [verificar a integridade de um registro de contêiner do Azure](container-registry-check-health.md).

Consulte as [perguntas frequentes](container-registry-faq.md) para perguntas frequentes e outros problemas conhecidos sobre o registro de contêiner do Azure.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
