---
title: Conceder consentimento de administrador em todo o locatário para um aplicativo-Azure AD
description: Saiba como conceder consentimento em todo o locatário a um aplicativo para que os usuários finais não sejam solicitados a fornecer consentimento ao entrar em um aplicativo.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 713b4ed2559e3cd16943af92e68818047e249ef4
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501007"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Conceder consentimento de administrador em todo o locatário para um aplicativo

Saiba como simplificar a experiência do usuário concedendo consentimento de administrador em todo o locatário a um aplicativo. Este artigo fornece as diferentes maneiras de conseguir isso. Os métodos se aplicam a todos os usuários finais do locatário do Azure AD (Azure Active Directory).

Para obter mais informações sobre o consentimento de aplicativos, consulte a [estrutura de consentimento do Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Pré-requisitos

Conceder consentimento de administrador em todo o locatário exige que você entre como [administrador global](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [administrador de aplicativos](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)ou administrador de [aplicativos de nuvem](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).

> [!IMPORTANT]
> Quando um aplicativo recebe o consentimento do administrador em todo o locatário, todos os usuários poderão entrar no aplicativo, a menos que tenham sido configurados para exigir a atribuição de usuário. Para restringir quais usuários podem entrar em um aplicativo, exija a atribuição de usuário e, em seguida, atribua usuários ou grupos ao aplicativo. Para obter mais informações, confira [Métodos para atribuir usuários e grupos](methods-for-assigning-users-and-groups.md).
>
> A função de administrador global é necessária para fornecer consentimento de administrador para a API de Microsoft Graph.
>


> [!WARNING]
> Conceder consentimento de administrador em todo o locatário a um aplicativo concederá o aplicativo e o acesso do Publicador do aplicativo aos dados da sua organização. Examine atentamente as permissões que o aplicativo está solicitando antes de conceder consentimento.
>
> A função de administrador global é necessária para fornecer consentimento de administrador para a API de Microsoft Graph.
>

## <a name="grant-admin-consent-from-the-azure-portal"></a>Conceder consentimento de administrador do portal do Azure

### <a name="grant-admin-consent-in-enterprise-apps"></a>Conceder consentimento de administrador em aplicativos empresariais

Você pode conceder consentimento de administrador em todo o locatário por meio de *aplicativos empresariais* se o aplicativo já tiver sido provisionado em seu locatário. Por exemplo, um aplicativo pode ser provisionado em seu locatário se pelo menos um usuário já tiver consentido o aplicativo. Para obter mais informações, consulte [como e por que os aplicativos são adicionados ao Azure Active Directory](../develop/active-directory-how-applications-are-added.md).

Para conceder consentimento de administrador em todo o locatário para um aplicativo listado em **aplicativos empresariais**:

1. Entre no [portal do Azure](https://portal.azure.com) como um [administrador global](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), um administrador de [aplicativos](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)ou um administrador de [aplicativos na nuvem](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Selecione **Azure Active Directory** **aplicativos empresariais**.
3. Selecione o aplicativo ao qual você deseja conceder consentimento de administrador em todo o locatário.
4. Selecione **permissões** e clique em **conceder consentimento do administrador**.
5. Examine atentamente as permissões que o aplicativo requer.
6. Se você concordar com as permissões que o aplicativo requer, conceda o consentimento. Caso contrário, clique em **Cancelar** ou feche a janela.

### <a name="grant-admin-consent-in-app-registrations"></a>Conceder consentimento de administrador no Registros de aplicativo

Para aplicativos que sua organização desenvolveu ou que estão registrados diretamente em seu locatário do Azure AD, você também pode conceder consentimento de administrador em todo o locatário de **registros de aplicativo** no portal do Azure.

Para conceder consentimento de administrador em todo o locatário do **registros de aplicativo**:

1. Entre no [portal do Azure](https://portal.azure.com) como um [administrador global](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), um administrador de [aplicativos](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)ou um administrador de [aplicativos na nuvem](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Selecione **Azure Active Directory** , em seguida, **registros de aplicativo**.
3. Selecione o aplicativo ao qual você deseja conceder consentimento de administrador em todo o locatário.
4. Selecione **permissões de API** e clique em **conceder consentimento de administrador**.
5. Examine atentamente as permissões que o aplicativo requer.
6. Se você concordar com as permissões que o aplicativo requer, conceda o consentimento. Caso contrário, clique em **Cancelar** ou feche a janela.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Construa a URL para conceder consentimento de administrador em todo o locatário

Ao conceder consentimento de administrador em todo o locatário usando qualquer um dos métodos descritos acima, uma janela é aberta no portal do Azure para solicitar o consentimento do administrador em todo o locatário. Se você souber a ID do cliente (também conhecida como a ID do aplicativo) do aplicativo, poderá criar a mesma URL para conceder o consentimento do administrador em todo o locatário.

A URL de consentimento do administrador em todo o locatário segue o seguinte formato:

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

onde:

* `{client-id}`é a ID do cliente do aplicativo (também conhecida como ID do aplicativo).
* `{tenant-id}`é a ID de locatário da sua organização ou qualquer nome de domínio verificado.

Como sempre, examine atentamente as permissões que um aplicativo solicita antes de conceder consentimento.

## <a name="next-steps"></a>Próximas etapas

[Configurar como os usuários finais concordam com os aplicativos](configure-user-consent.md)

[Configurar o fluxo de trabalho de consentimento do administrador](configure-admin-consent-workflow.md)

[Permissões e consentimento na plataforma de identidade da Microsoft](../develop/active-directory-v2-scopes.md)

[Azure AD no StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
