---
title: Criar uma especificação de modelo com modelos vinculados
description: Saiba como criar uma especificação de modelo com modelos vinculados.
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 49a26bf61c3c66f41761afe293471575e76c4eb9
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936360"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>Tutorial: criar uma especificação de modelo com modelos vinculados (visualização)

Saiba como criar uma [especificação de modelo](template-specs.md) com um [modelo vinculado](linked-templates.md#linked-template). Use as especificações do modelo para compartilhar modelos do ARM com outros usuários em sua organização. Este artigo mostra como criar uma especificação de modelo para empacotar um modelo principal e seus modelos vinculados usando a nova `relativePath` Propriedade do [recurso de implantação](/azure/templates/microsoft.resources/deployments).

## <a name="prerequisites"></a>Pré-requisitos

Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> As especificações de modelo estão atualmente em versão prévia. Para usá-lo, você deve [inscrever-se na versão prévia](https://aka.ms/templateSpecOnboarding).

## <a name="create-linked-templates"></a>Criar modelos vinculados

Crie o modelo principal e o modelo vinculado.

Para vincular um modelo, adicione um [recurso de implantações](/azure/templates/microsoft.resources/deployments) ao seu modelo principal. Na `templateLink` propriedade, especifique o caminho relativo do modelo vinculado de acordo com o caminho do modelo pai.

O modelo vinculado é chamado ** delinkedTemplate.jsem**e é armazenado em uma subpasta chamada **artefatos** no caminho em que o modelo principal está armazenado.  Você pode usar um dos seguintes valores para o relativePath:

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

A `relativePath` propriedade é sempre relativa ao arquivo de modelo em que `relativePath` é declarado, portanto, se houver outro linkedTemplate2.jsem que é chamado de linkedTemplate.jse linkedTemplate2.jsno é armazenado na mesma subpasta de artefatos, o RelativePath especificado em linkedTemplate.jsem é apenas `linkedTemplate2.json` .

1. Crie o modelo principal com o JSON a seguir. Salve o modelo principal como **azuredeploy.jsem** seu computador local. Este tutorial pressupõe que você salvou em um caminho **c:\Templates\linkedTS\azuredeploy.js** , mas pode usar qualquer caminho.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
          }
        }
      },
      "variables": {
        "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Web/serverfarms",
          "apiVersion": "2016-09-01",
          "name": "[variables('appServicePlanName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "B1",
            "tier": "Basic",
            "size": "B1",
            "family": "B",
            "capacity": 1
          },
          "kind": "linux",
          "properties": {
            "perSiteScaling": false,
            "reserved": true,
            "targetWorkerCount": 0,
            "targetWorkerSizeId": 0
          }
        },
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "createStorage",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > O apiVersion de `Microsoft.Resources/deployments` deve ser 2020-06-01 ou posterior.

1. Crie um diretório chamado **artefatos** na pasta em que o modelo principal foi salvo.
1. Crie o modelo vinculado com o seguinte JSON:

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
          ],
          "metadata": {
            "description": "Storage Account type"
          }
        },
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        }
      },
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. Salve o modelo como **linkedTemplate.jsna** pasta **artefatos** .

## <a name="create-template-spec"></a>Criar especificação de modelo

As especificações de modelos são armazenadas em grupos de recursos.  Crie um grupo de recursos e, em seguida, crie uma especificação de modelo com o script a seguir. O nome de especificação do modelo é **Webspec**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -Name webSpec `
  -Version "1.0.0.0" `
  -ResourceGroupName templateSpecRG `
  -Location westus2 `
  -TemplateJsonFile "c:\Templates\linkedTS\azuredeploy.json"
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name templateSpecRG \
  --location westus2

az template-specs create \
  --name webSpec \
  --version "1.0.0.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "c:\Templates\linkedTS\azuredeploy.json"
```

---

Quando terminar, você poderá exibir a especificação do modelo no portal do Azure ou usando o seguinte cmdlet:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az template-specs show --name webSpec --resource-group templateSpecRG --version "1.0.0.0"
```

---

## <a name="deploy-template-spec"></a>Implantar especificação de modelo

Agora você pode implantar a especificação de modelo. Implantar a especificação de modelo é igual à implantação do modelo que a contém, exceto pelo fato de que você passa a ID do recurso da especificação de modelo. Você usa os mesmos comandos de implantação e, se necessário, passa os valores de parâmetro para a especificação de modelo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

id = $(az template-specs show --name webSpec --resource-group templateSpecRG --version "1.0.0.0" --query "id")

az deployment group create \
  --resource-group webRG \
  --template-spec $id
```

> [!NOTE]
> Há um problema conhecido ao obter a ID de especificação do modelo e, em seguida, atribuí-la a uma variável no Windows PowerShell.

---

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como implantar uma especificação de modelo como um modelo vinculado, confira [tutorial: implantar uma especificação de modelo como um modelo vinculado](template-specs-deploy-linked-template.md).
