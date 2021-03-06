---
title: Registrar aplicativos de página única (SPA) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo de página única (registro de aplicativo)
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: efd51e90bb14f3d97b76eb6ac45b384192bb8da0
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87311561"
---
# <a name="single-page-application-app-registration"></a>Aplicativo de página única: Registro do aplicativo

Para registrar um aplicativo de página única (SPA) na plataforma de identidade da Microsoft, conclua as etapas a seguir. As etapas de registro diferem entre MSAL.js 1,0, que é compatível com o fluxo de concessão implícita, e MSAL.js 2,0, que é compatível com o fluxo de código de autorização com PKCE.

[!INCLUDE [MSAL.js 2.0 and Azure AD B2C temporary incompatibility notice](../../../includes/msal-b2c-cors-compatibility-notice.md)]

## <a name="create-the-app-registration"></a>Criar o registro do aplicativo

Para aplicativos baseados em MSAL.js 1.0 e 2.0, comece concluindo as etapas a seguir para criar o registro inicial do aplicativo.

1. Entre no [portal do Azure](https://portal.azure.com). Se a sua conta tiver acesso a vários locatários, selecione o filtro **Diretório + Assinatura** no menu superior e, em seguida, escolha o locatário que deve conter o registro do aplicativo que você vai criar.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **Gerenciar**, selecione **Registros de aplicativo**.
1. Selecione **Novo registro**, insira um **Nome** para o aplicativo e escolha os **Tipos de conta com suporte** para o aplicativo. **NÃO** insira **URI de redirecionamento**. Para obter uma descrição dos diferentes tipos de conta, confira [Registrar um novo aplicativo usando o portal do Azure](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).
1. Selecione **Registrar** para criar o registro do aplicativo.

Em seguida, configure o registro do aplicativo com um **URI de redirecionamento** para especificar para onde a plataforma de identidade da Microsoft deve redirecionar o cliente juntamente com os tokens de segurança. Use as etapas apropriadas para a versão do MSAL.js que você está usando em seu aplicativo:

- [MSAL.js 2.0 com fluxo de código de autorização](#redirect-uri-msaljs-20-with-auth-code-flow) (recomendado)
- [MSAL.js 1.0 com fluxo implícito](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>URI de redirecionamento: [MSAL.js 2,0 com fluxo de código de autenticação](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)

Siga estas etapas para adicionar um URI de redirecionamento para um aplicativo que usa MSAL.js 2.0 ou posterior. O MSAL.js 2.0+ é compatível com o fluxo de código de autorização com PKCE e CORS em resposta às [restrições de cookie de terceiros do navegador](reference-third-party-cookies-spas.md). O fluxo de concessão implícita não é compatível com o MSAL.js 2.0+.

1. No portal do Azure, selecione o registro de aplicativo que você criou anteriormente em [Criar o registro do aplicativo](#create-the-app-registration).
1. Em **Gerenciar**, selecione **Autenticação** e, em seguida, selecione **Adicionar uma plataforma**.
1. Em **Aplicativos Web**, selecione o bloco **Aplicativo de página única**.
1. Em **URIs de redirecionamento**, insira um [URI de redirecionamento](reply-url.md). **NÃO** marque as caixas de seleção em **Concessão implícita**.
1. Selecione **Configurar** para concluir a adição do URI de redirecionamento.

Você concluiu o registro do seu aplicativo de página única (SPA) e configurou um URI de redirecionamento para o qual o cliente será redirecionado e todos os tokens de segurança serão enviados. Ao configurar o URI de redirecionamento usando o bloco **Aplicativo de página única** no painel **Adicionar uma plataforma**, o registro do aplicativo é configurado para compatibilidade com o fluxo de código de autorização com PKCE e CORS.

Siga o [tutorial](tutorial-v2-javascript-auth-code.md) para obter mais diretrizes.

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>URI de redirecionamento: [MSAL.js 1,0 com fluxo implícito](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)

Siga estas etapas para adicionar um URI de redirecionamento para um aplicativo de página única que usa MSAL.js 1.3 ou anterior e o fluxo de concessão implícita. Os aplicativos que usam MSAL.js 1.3 ou anterior não são compatíveis com o fluxo de código de autorização.

1. No portal do Azure, selecione o registro de aplicativo que você criou anteriormente em [Criar o registro do aplicativo](#create-the-app-registration).
1. Em **Gerenciar**, selecione **Autenticação** e, em seguida, selecione **Adicionar uma plataforma**.
1. Em **Aplicativos Web**, selecione o bloco **Aplicativo de página única**.
1. Em **URIs de redirecionamento**, insira um [URI de redirecionamento](reply-url.md).
1. Habilite o **Fluxo implícito**:
    - Se o seu aplicativo conectar usuários, selecione **Tokens de ID**.
    - Se o seu aplicativo também precisar chamar uma API Web protegida, selecione **Tokens de acesso**. Para obter mais informações sobre esses tipos de token, confira [Tokens de ID](id-tokens.md) e [Tokens de acesso](access-tokens.md).
1. Selecione **Configurar** para concluir a adição do URI de redirecionamento.

Você concluiu o registro do seu aplicativo de página única (SPA) e configurou um URI de redirecionamento para o qual o cliente será redirecionado e todos os tokens de segurança serão enviados. Quando selecionou **Tokens de ID**, **Tokens de acesso** ou as duas opções, você habilitou o fluxo de concessão implícita.

Siga o [tutorial](tutorial-v2-javascript-spa.md) para obter mais diretrizes.

## <a name="note-about-authorization-flows"></a>Observação sobre fluxos de autorização

Por padrão, um registro de aplicativo criado usando a configuração de plataforma de aplicativo de página única habilita o fluxo de código de autorização. Para aproveitar esse fluxo, seu aplicativo deve usar o MSAL.js 2.0 ou posterior.

Como mencionado anteriormente, aplicativos de página única usando MSAL.js 1.3 são restritos ao fluxo de concessão implícita. As atuais [melhores práticas de OAuth 2.0](v2-oauth2-auth-code-flow.md) recomendam o uso do fluxo de código de autorização no lugar do fluxo implícito para SPAs. Ter tokens de atualização com tempo de vida limitado também ajuda o aplicativo a se adaptar às [limitações de privacidade de cookie dos navegadores modernos](reference-third-party-cookies-spas.md), como o Safari ITP.

Se todos os aplicativos de uma página de produção representados por um registro de aplicativo estiverem usando o MSAL.js 2.0 e o fluxo de código de autorização, desmarque as configurações de concessão implícita no painel **Autenticação** do registro do aplicativo, no portal do Azure. Os aplicativos que usam MSAL.js 1.x e o fluxo implícito continuarão a funcionar se você deixar o fluxo implícito habilitado (marcado).

## <a name="next-steps"></a>Próximas etapas

Em seguida, configure o código do aplicativo para usar o registro do aplicativo criado nas etapas anteriores.

> [!div class="nextstepaction"]
> [Configuração de código do aplicativo](scenario-spa-app-configuration.md)
