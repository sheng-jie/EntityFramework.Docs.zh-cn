---
title: "多个项目的 EF 核心的迁移"
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
ms.technology: entity-framework-core
ms.openlocfilehash: e059deb6c7555a4f6732fe7942855e95b3f9a34c
ms.sourcegitcommit: b467368cc350e6059fdc0949e042a41cb11e61d9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
<a name="using-a-separate-project"></a><span data-ttu-id="35fe9-102">使用一个单独的项目</span><span class="sxs-lookup"><span data-stu-id="35fe9-102">Using a Separate Project</span></span>
========================
<span data-ttu-id="35fe9-103">你可能想要比另一个包含其他程序集中存储迁移你`DbContext`。</span><span class="sxs-lookup"><span data-stu-id="35fe9-103">You may want to store your migrations in a different assembly than the one containing your `DbContext`.</span></span> <span data-ttu-id="35fe9-104">此外可以使用此策略，维护多个组迁移，例如，一个用于开发，另一个版本的升级。</span><span class="sxs-lookup"><span data-stu-id="35fe9-104">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

<span data-ttu-id="35fe9-105">若要执行此操作...</span><span class="sxs-lookup"><span data-stu-id="35fe9-105">To do this...</span></span>

1. <span data-ttu-id="35fe9-106">创建一个新的类库。</span><span class="sxs-lookup"><span data-stu-id="35fe9-106">Create a new class library.</span></span>

2. <span data-ttu-id="35fe9-107">添加到 DbContext 程序集的引用。</span><span class="sxs-lookup"><span data-stu-id="35fe9-107">Add a reference to your DbContext assembly.</span></span>

3. <span data-ttu-id="35fe9-108">将迁移和模型快照文件移动到类库。</span><span class="sxs-lookup"><span data-stu-id="35fe9-108">Move the migrations and model snapshot files to the class library.</span></span>
   * <span data-ttu-id="35fe9-109">如果你尚未添加任何，添加一个到 DbContext 项目，然后将其移。</span><span class="sxs-lookup"><span data-stu-id="35fe9-109">If you haven't added any, add one to the DbContext project then move it.</span></span>

4. <span data-ttu-id="35fe9-110">配置迁移程序集：</span><span class="sxs-lookup"><span data-stu-id="35fe9-110">Configure the migrations assembly:</span></span>

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. <span data-ttu-id="35fe9-111">添加到迁移程序集中的启动程序集的引用。</span><span class="sxs-lookup"><span data-stu-id="35fe9-111">Add a reference to your migrations assembly from the startup assembly.</span></span>
   * <span data-ttu-id="35fe9-112">如果这导致循环依赖关系，更新类库的输出路径：</span><span class="sxs-lookup"><span data-stu-id="35fe9-112">If this causes a circular dependency, update the output path of the class library:</span></span>

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStarupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

<span data-ttu-id="35fe9-113">如果你这样做的所有内容正确，你应能够向项目添加新的迁移。</span><span class="sxs-lookup"><span data-stu-id="35fe9-113">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```
``` Console
dotnet ef migraitons add NewMigration --project MyApp.Migrations
```