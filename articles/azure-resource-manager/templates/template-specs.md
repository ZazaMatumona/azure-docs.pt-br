---
title: Visão geral das especificações do modelo
description: Descreve como criar especificações de modelo e compartilhá-las com outros usuários em sua organização.
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: a88e799d2298cb21b5196f5aa143e5453c0447c0
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783783"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Especificações do modelo de Azure Resource Manager (versão prévia)

Uma especificação de modelo é um novo tipo de recurso para armazenar um modelo de Azure Resource Manager (modelo ARM) no Azure para implantação posterior. Esse tipo de recurso permite que você compartilhe modelos do ARM com outros usuários em sua organização. Assim como qualquer outro recurso do Azure, você pode usar o RBAC (controle de acesso baseado em função) para compartilhar a especificação do modelo.

**Microsoft. Resources/templateSpecs** é o novo tipo de recurso para as especificações de modelo. Ele consiste em um modelo principal e qualquer número de modelos vinculados. O Azure armazena as especificações de modelo com segurança em grupos de recursos. As especificações de modelo dão suporte ao [controle de versão](#versioning).

Para implantar a especificação do modelo, você usa ferramentas padrão do Azure como o PowerShell, CLI do Azure, portal do Azure, REST e outros SDKs e clientes com suporte. Você usa os mesmos comandos que faria para o modelo.

> [!NOTE]
> As especificações de modelo estão atualmente em versão prévia. Para usá-las, você deve [inscrever-se na lista de espera](https://aka.ms/templateSpecOnboarding).

## <a name="why-use-template-specs"></a>Por que usar as especificações de modelo?

Se, no momento, você tiver seus modelos em um repositório GitHub ou em uma conta de armazenamento, você terá vários desafios ao tentar compartilhar e usar os modelos. Para que um usuário o implante, o modelo deve ser local ou a URL para o modelo deve ser acessível publicamente. Para contornar essa limitação, você pode compartilhar cópias do modelo com usuários que precisam implantá-lo ou abrir o acesso ao repositório ou à conta de armazenamento. Quando os usuários possuem cópias locais de um modelo, essas cópias podem eventualmente divergir do modelo original. Ao tornar um repositório ou uma conta de armazenamento acessível publicamente, você pode permitir que usuários indesejados acessem o modelo.

O benefício de usar as especificações de modelo é que você pode criar modelos canônicos e compartilhá-los com equipes em sua organização. As especificações de modelo são seguras porque estão disponíveis para Azure Resource Manager para implantação, mas não podem ser acessadas por usuários sem permissão de RBAC. Os usuários só precisam de acesso de leitura à especificação do modelo para implantar seu modelo, para que você possa compartilhar o modelo sem permitir que outras pessoas o modifiquem.

Os modelos incluídos em uma especificação de modelo devem ser verificados pelos administradores em sua organização para seguir os requisitos e as diretrizes da organização.

## <a name="create-template-spec"></a>Criar especificação de modelo

O exemplo a seguir mostra um modelo simples para criar uma conta de armazenamento no Azure.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[concat('store', uniquestring(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      }
    }
  ]
}
```

Quando você cria a especificação do modelo, os comandos do PowerShell ou da CLI são passados como o arquivo de modelo principal. Se o modelo principal fizer referência a modelos vinculados, os comandos os encontrarão e os empacotarão para criar a especificação do modelo. Para saber mais, confira [criar uma especificação de modelo com modelos vinculados](#create-a-template-spec-with-linked-templates).

Crie uma especificação de modelo usando:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -Location westus2 -TemplateJsonFile ./mainTemplate.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az template-specs create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

---

Você pode exibir todas as especificações de modelo em sua assinatura usando:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az template-specs list
```

---

Você pode exibir detalhes de uma especificação de modelo, incluindo suas versões com:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az template-specs show \
    --name storageSpec \
    --resource-group templateSpecRG \
    --version "1.0"
```

---

## <a name="deploy-template-spec"></a>Implantar especificação de modelo

Depois de criar a especificação do modelo, os usuários com acesso de **leitura** à especificação do modelo podem implantá-lo. Para obter informações sobre como conceder acesso, consulte [tutorial: conceder a um grupo acesso aos recursos do Azure usando Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

As especificações de modelo podem ser implantadas por meio do portal, do PowerShell, CLI do Azure ou como um modelo vinculado em uma implantação de modelo maior. Os usuários em uma organização podem implantar uma especificação de modelo em qualquer escopo no Azure (grupo de recursos, assinatura, grupo de gerenciamento ou locatário).

Em vez de passar um caminho ou URI para um modelo, você implanta uma especificação de modelo fornecendo sua ID de recurso. A ID do recurso tem o seguinte formato:

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

Observe que a ID do recurso inclui um número de versão para a especificação do modelo.

Por exemplo, você implanta uma especificação de modelo com o comando a seguir.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

Na prática, você normalmente executará `Get-AzTemplateSpec` para obter a ID da especificação do modelo que deseja implantar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = $(az template-specs show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

## <a name="parameters"></a>Parâmetros

A passagem de parâmetros para a especificação do modelo é exatamente como passar parâmetros para um modelo do ARM. Adicione os valores de parâmetro embutidos ou em um arquivo de parâmetro.

Para passar um parâmetro embutido, use:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -StorageAccountType Standard_GRS
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters storageAccountType='Standard_GRS'
```

---

Para criar um arquivo de parâmetro local, use:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "StorageAccountType": {
      "value": "Standard_GRS"
    }
  }
}
```

E, passe esse arquivo de parâmetro com:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -TemplateParameterFile ./mainTemplate.parameters.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters "./mainTemplate.parameters.json"
```

---

## <a name="create-a-template-spec-with-linked-templates"></a>Criar uma especificação de modelo com modelos vinculados

Se o modelo principal para a especificação do modelo fizer referência a modelos vinculados, os comandos do PowerShell e da CLI poderão localizar e empacotar automaticamente os modelos vinculados da unidade local. Você não precisa configurar manualmente as contas de armazenamento ou repositórios para hospedar as especificações do modelo-tudo é independente no recurso de especificação do modelo.

O exemplo a seguir consiste em um modelo principal com dois modelos vinculados. O exemplo é apenas um trecho do modelo. Observe que ele usa uma propriedade chamada `relativePath` para vincular a outros modelos. Você deve usar `apiVersion` `2020-06-01` o ou posterior para o recurso de implantações.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/webapp.json"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/database.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

Quando o comando do PowerShell ou da CLI para criar a especificação do modelo é executado para o exemplo anterior, o comando localiza três arquivos: o modelo principal, o modelo de aplicativo Web ( `webapp.json` ) e o modelo de banco de dados ( `database.json` )-e os empacota para a especificação do modelo.

Para obter mais informações, consulte [tutorial: criar uma especificação de modelo com modelos vinculados](template-specs-create-linked.md).

## <a name="deploy-template-spec-as-a-linked-template"></a>Implantar a especificação do modelo como um modelo vinculado

Depois de criar uma especificação de modelo, é fácil reutilizá-la de um modelo ARM ou outra especificação de modelo. Você vincula a uma especificação de modelo adicionando sua ID de recurso ao seu modelo. A especificação do modelo vinculado é implantada automaticamente quando você implanta o modelo principal. Esse comportamento permite desenvolver especificações de modelo modulares e reutilizá-las conforme necessário.

Por exemplo, você pode criar uma especificação de modelo que implanta recursos de rede e outra especificação de modelo que implanta recursos de armazenamento. Em modelos ARM, você vincula a essas duas especificações de modelo sempre que precisar configurar recursos de rede ou de armazenamento.

O exemplo a seguir é semelhante ao exemplo anterior, mas você usa a `id` propriedade para vincular a uma especificação de modelo em vez da `relativePath` Propriedade a ser vinculada a um modelo local. Use `2020-06-01` para a versão da API para o recurso de implantações. No exemplo, as especificações de modelo estão em um grupo de recursos chamado **templateSpecsRG**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "networkingDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0')]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
        }
      }
    }
  ],
  "outputs": {}
}
```

Para obter mais informações sobre como vincular especificações de modelo, consulte [tutorial: implantar uma especificação de modelo como um modelo vinculado](template-specs-deploy-linked-template.md).

## <a name="versioning"></a>Controle de versão

Ao criar uma especificação de modelo, você fornece um número de versão para ela. À medida que você itera o código do modelo, pode atualizar uma versão existente (para hotfixes) ou publicar uma nova versão. A versão é uma cadeia de caracteres de texto. Você pode optar por seguir qualquer sistema de controle de versão, incluindo controle de versão semântico. Os usuários da especificação do modelo podem fornecer o número de versão que desejam usar ao implantá-lo.

## <a name="next-steps"></a>Próximas etapas

* Para criar e implantar uma especificação de modelo, consulte [início rápido: criar e implantar especificação de modelo](quickstart-create-template-specs.md).

* Para obter mais informações sobre como vincular modelos em especificações de modelo, consulte [tutorial: criar uma especificação de modelo com modelos vinculados](template-specs-create-linked.md).

* Para obter mais informações sobre como implantar uma especificação de modelo como um modelo vinculado, consulte [tutorial: implantar uma especificação de modelo como um modelo vinculado](template-specs-deploy-linked-template.md).
