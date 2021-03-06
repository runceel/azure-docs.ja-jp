---
title: Resource Manager テンプレートでのアクティビティ ログ アラートの作成
description: Azure リソースの作成時に通知を受け取ります。
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2017
ms.author: ancav
ms.component: alerts
ms.openlocfilehash: a1e28f08231ae1fbef3e0d0306e986c1dc9d1d1c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262992"
---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Resource Manager テンプレートでのアクティビティ ログ アラートの作成
この記事では、[Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)を使用してアクティビティ ログ アラートを構成する方法について説明します。 テンプレートを使用すると、自動デプロイ プロセスの一環として、特定のアクティビティ ログ イベント条件に基づいてアクティブ化する多数のアラートを簡単に設定できます。

基本的な手順は次のとおりです。

1. アクティビティ ログ アラートの作成方法が記述された JSON ファイルとしてテンプレートを作成します。

2. [任意のデプロイ方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)を使用してテンプレートをデプロイします。

## <a name="resource-manager-template-for-an-activity-log-alert"></a>アクティビティ ログ アラート用の Resource Manager テンプレート
Resource Manager テンプレートを使用してアクティビティ ログ アラートを作成するには、`microsoft.insights/activityLogAlerts` 型のリソースを作成します。 次に、関連するすべてのプロパティを入力します。 アクティビティ ログ アラートを作成するテンプレートを以下に示します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

アクティビティ ログ アラート テンプレートの例については、[Azure クイックスタート ギャラリー](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights)を参照してください。

> [!NOTE]

> また、[監視] > [[アラート (プレビュー)]](monitoring-overview-unified-alerts.md) の強化されたユーザー エクスペリエンスを使用して、アクティビティ ログ アラート ルールを作成することもできます。 作成方法の詳細については、[こちらの記事](monitoring-activity-log-alerts-new-experience.md)を参照してください。

## <a name="next-steps"></a>次の手順
- [アラート](monitoring-overview-alerts.md)の詳細について学習します。
- [Resource Manager テンプレートを使用してアクション グループ](monitoring-create-action-group-with-resource-manager-template.md)を追加する方法について確認します。
- [アクティビティ ログ アラートを作成して、サブスクリプションで自動スケールのエンジン操作をすべて監視する](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)方法について確認します。
- [アクティビティ ログ アラートを作成して、サブスクリプションで失敗した自動スケールのスケールイン/スケールアウト操作をすべて監視する](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)方法について確認します。
