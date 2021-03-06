---
title: Registrar aplicativos de daemon que chamam APIs da Web-plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo daemon que chama APIs da Web-registro de aplicativo
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 508101ad615dd96559b1c68a61be7c08772545db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885473"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Aplicativo daemon que chama APIs Web-registro de aplicativo

Para um aplicativo daemon, veja o que você precisa saber ao registrar o aplicativo.

## <a name="supported-account-types"></a>Tipos de conta com suporte

Os aplicativos daemon fazem sentido apenas em locatários do Azure AD. Portanto, ao criar o aplicativo, você precisa escolher uma das seguintes opções:

- **Contas somente neste diretório organizacional**. Essa opção é a mais comum porque os aplicativos de daemon são geralmente escritos por desenvolvedores de linha de negócios (LOB).
- **Contas em qualquer diretório organizacional**. Você fará essa escolha se for um ISV que fornece uma ferramenta de utilitário para seus clientes. Você precisará dos administradores de locatário de seus clientes para aprová-lo.

## <a name="authentication---no-reply-uri-needed"></a>Autenticação-nenhum URI de resposta necessário

No caso em que seu aplicativo cliente confidencial usa *apenas* o fluxo de credenciais do cliente, o URI de resposta não precisa ser registrado. Não é necessário para a configuração ou a construção do aplicativo. O fluxo de credenciais do cliente não o utiliza.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Permissões de API-permissões de aplicativo e consentimento de administrador

Um aplicativo daemon pode solicitar permissões de aplicativo somente para APIs (permissões não delegadas). Na página **permissões de API** para o registro do aplicativo, depois de selecionar **Adicionar uma permissão** e escolher a família de API, escolha **permissões de aplicativo**e, em seguida, selecione suas permissões.

![Permissões do aplicativo e consentimento do administrador](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> A API da Web que você deseja chamar precisa definir *permissões de aplicativo (funções de aplicativo)*, permissões não delegadas. Para obter detalhes sobre como expor essa API, consulte [API Web protegida: registro de aplicativo – quando sua API Web é chamada por um aplicativo daemon](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).

Os aplicativos daemon exigem que um administrador de locatário consentisse previamente o aplicativo que chama a API da Web. Os administradores de locatários fornecem esse consentimento na mesma página de **permissão de API** selecionando **conceder consentimento de administrador para *nossa organização* **

Se você for um ISV criando um aplicativo multilocatário, leia a seção [implantação-caso de aplicativos de daemon multilocatário](scenario-daemon-production.md#deployment---multitenant-daemon-apps).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aplicativo daemon-configuração de código do aplicativo](./scenario-daemon-app-configuration.md)
