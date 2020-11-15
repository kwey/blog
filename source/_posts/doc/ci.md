---
title: Gitlab CI/CD管道配置参考
top_img: '/img/hy.jpeg'
cover: '/img/hy.jpeg'
---

###  Gitlab CI/CD管道配置参考


在每个项目中，使用名为.gitlab-ci.yml的yaml文件配置Gitlab CI/CD管道。

.gitlab-ci.yml文件定义了管道的结构和顺序，并确定：

使用Gitlab Runner执行什么。

遇到特殊情况时要做什么决定？例如，当一个进程成功或失败时。

本主题介绍CI/CD管道配置。有关其他CI/CD配置信息，请参阅：

Gitlab CI/CD变量，用于配置管道运行的环境。

Gitlab Runner高级配置，用于配置Gitlab Runner。

我们有配置管道的完整示例：

要快速介绍Gitlab CI，请遵循我们的快速入门指南。

有关示例的集合，请参见Gitlab CI/CD示例。

要查看企业中使用的大型.gitlab-ci.yml文件，请参见gitlab ce的.gitlab-ci.yml文件。
> 如果你有一个镜像存储库，其中有gitlab，你可能需要在项目的Settings > Repository > Pull from a remote repository > Trigger镜像更新管道。
<h3 id="a1">1. 介绍</h3>

管道配置从job开始。jobs是.gitlab-ci.yml文件最基本的元素。

工作包括：

定义了在什么条件下应该执行它们的约束。

具有任意名称的顶级元素，必须至少包含script子句。

不限于可定义的数量。

例如：
``` yml
job1:
  script: "execute-script-for-job1"
job2:
  script: "execute-script-for-job2"
```
上面的示例是最简单的CI/CD配置，有两个独立的job，其中每个job执行不同的命令。当然，命令可以直接执行代码（./configure；make；make install）或者在存储库中运行script（test.sh）。
工作由Runners挑选并在Runner的环境。重要的是，每个job都在运行彼此独立。
<h3 id="a2">2. 验证 .gitlab-ci.yml</h3>

Gitlab CI的每个实例都有一个名为lint的嵌入式调试工具，用于验证.gitlab-ci.yml文件的内容。您可以在您的页面ci/lint下找到lint。项目命名空间。例如，https://gitlab.example.com/gitlab-org/project-123//ci/lint。
<h3 id="a3">3. Unavailable names for jobs </h3>

每个job必须有一个唯一的名称，但有一些保留的关键字不能用作job名称：
``` yml
image
services
stages
types
before_script
after_script
variables
cache
```
<h3 id="a4">4. 使用保留关键字</h3>

如果在使用特定值（例如，true or false）时出现验证错误，请尝试：

--引用它们。

--将它们更改为其他形式。例如，/bin/true。
<h3 id="a5">5. 配置参数</h3>

job定义为定义job行为的参数列表。

下表列出了job的可用参数：

<table class="code">
<tbody>
    <tr>
        <td>Keyword</td>
        <td>描述</td>
    </tr>
    <tr>
        <td>script</td>
        <td>由运行程序执行的shell脚本。</td>
    </tr>
    <tr>
        <td>image</td>
        <td>使用Docker图像。还提供：image:name和image:entrypoint。</td>
    </tr>
    <tr>
        <td>services</td>
        <td>使用Docker服务图像。还提供：services:name、services:alias、services:entrypoint和services:command。</td>
    </tr>
    <tr>
        <td>before_script</td>
        <td>重写在job之前执行的一组命令。</td>
    </tr>
    <tr>
        <td>after_script</td>
        <td>重写在job后执行的一组命令。</td>
    </tr>
    <tr>
        <td>stages</td>
        <td>定义管道中的阶段。</td>
    </tr>
    <tr>
        <td>stage</td>
        <td>定义job阶段（default: test）。</td>
    </tr>
    <tr>
        <td>only</td>
        <td>限制创建 when jobs。还可用：only：refs，only：kubernetes，only：variables，only：changes。</td>
    </tr>
    <tr>
        <td>except</td>
        <td>限制未创建 when jobs。还可用：except:refs，except:kubernetes，except:variables和except:changes。</td>
    </tr>
    <tr>
        <td>tags</td>
        <td>用于选择运行程序的标记列表。</td>
    </tr>
    <tr>
        <td>allow_failure</td>
        <td>允许job失败。失败的job不影响提交状态。</td>
    </tr>
    <tr>
        <td>when</td>
        <td>何时运行job。还可用：when:manual and when:delayed。</td>
    </tr>
    <tr>
        <td>environment</td>
        <td>job部署到的环境的名称。还可用：environment:name, environment:url, environment:on_stop, and environment:action.</td>
    </tr>
    <tr>
        <td>cache</td>
        <td>在后续运行之间应缓存的文件列表。还可用：cache:paths, cache:key, cache:untracked, and cache:policy.。</td>
    </tr>
    <tr>
        <td>artifacts</td>
        <td>成功时要附加到job的文件和目录列表。还可用：artifacts:paths, artifacts:name, artifacts:untracked, artifacts:when, artifacts:expire_in, artifacts:reports, and artifacts:reports:junit。在Gitlab企业版中，这些可用：artifacts:reports:codequality, artifacts:reports:sast, artifacts:reports:dependency_scanning, artifacts:reports:container_scanning, artifacts:reports:dast, artifacts:reports:license_management, artifacts:reports:performance and artifacts:reports:metrics。</td>
    </tr>
    <tr>
        <td>dependencies</td>
        <td>job所依赖的其他job，以便在它们之间传递artifacts。</td>
    </tr>
    <tr>
        <td>coverage</td>
        <td>给定job的代码覆盖率设置。</td>
    </tr>
    <tr>
        <td>retry</td>
        <td>如果出现故障，一个job可以自动重试的时间和次数。</td>
    </tr>
    <tr>
        <td>parallel</td>
        <td>一个job应并行运行多少个实例。</td>
    </tr>
    <tr>
        <td>trigger</td>
        <td>定义下游管道触发器。</td>
    </tr>
    <tr>
        <td>include</td>
        <td>允许此job包含外部yaml文件。还提供：include:local、include:file、include:template和include:remote。</td>
    </tr>
    <tr>
        <td>extends</td>
        <td>此job将从中继承的配置项。</td>
    </tr>
    <tr>
        <td>pages</td>
        <td>上载job的结果以用于Gitlab页面。</td>
    </tr>
    <tr>
        <td>variables</td>
        <td>在job级别定义job变量。</td>
    </tr>
</tbody>
</table>

> NOTE: Note:Parameters types and type are deprecated.(弃用)
<h3 id="a6">6. 设置默认参数</h3>

某些参数可以全局设置为所有job的默认值，使用默认值：关键字。然后，默认参数可以被特定于job的参数覆盖配置。

可以在默认块内定义以下job参数：
``` yml
image
services
before_script
after_script
cache
```

在下面的示例中，ruby:2.5图像被设置为所有图像的默认值。job，rspec 2.6job除外，它使用ruby:2.6映像：
``` yml
default:
  image: ruby:2.5

rspec:
  script: bundle exec rspec

rspec 2.6:
  image: ruby:2.6
  script: bundle exec rspec
```
<h3 id="a7">7. 参数详细信息</h3>

以下是用于配置CI/CD管道的参数的详细说明。
<h4 id="a7-1">7.1. script</h4>

script是job所需的唯一关键字。这是一个shellscript由运行程序执行。例如：
``` yml
job:
  script: "bundle exec rspec"
```

此参数还可以包含使用数组的多个命令：
``` yml
job:
  script:
    - uname -a
    - bundle exec rspec
```
> 有时，script命令需要用单引号或双引号括起来。例如，包含冒号（：）的命令需要用引号括起来，因此yaml解析器知道将整个事件解释为字符串而不是“key:value”对。使用特殊字符时要小心:
:, {, }, [, ], ,, &, *, #, ?, |, -, <, >, =, !, %, @, `.
<h3 id="a7-2">7.2. image</h3>

用于指定要用于job的Docker映像。

用于：

简单定义示例，<a href="https://gitlab.com/help/ci/docker/using_docker_images.md#define-image-and-services-from-gitlab-ciyml" target="_blank" rel="noopener noreferrer">请参见从.gitlab-ci.yml定义图像和服务。</a> 

详细使用信息，<a href="https://gitlab.com/help/ci/docker/README.md" target="_blank" rel="noopener noreferrer">请参阅Docker集成文档。</a> 
<h4>image:name</h4>

<a href="https://gitlab.com/help/ci/docker/using_docker_images.md#extended-docker-configuration-options" target="_blank" rel="noopener noreferrer">扩展Docker配置选项。</a> 

有关详细信息，<a href="https://gitlab.com/help/ci/docker/using_docker_images.md#available-settings-for-image" target="_blank" rel="noopener noreferrer">请参阅图像的可用设置。</a> 
<h4>image:entrypoint</h4>

扩展Docker配置选项。

有关详细信息，请参阅图像的可用设置。
<h3 id="a7-3">7.3. services</h3>

用于指定服务Docker映像，链接到映像中指定的基映像。

用于：

简单定义示例，请参见从.gitlab-ci.yml定义图像和服务。</pp>

详细使用信息，请参阅Docker集成文档。

例如服务，<a href="https://gitlab.com/help/ci/services/README.md" target="_blank" rel="noopener noreferrer">请参见Gitlab CI Services。</a> 
<h4>services:name</h4>

扩展Docker配置选项。有关详细信息，<a href="https://gitlab.com/help/ci/docker/using_docker_images.md#available-settings-for-services" target="_blank" rel="noopener noreferrer"> 请参阅“服务的可用设置”。</a>
<h4>services:alias</h4>

扩展Docker配置选项。有关详细信息，请参阅“服务的可用设置”。
<h4>services:entrypoint</h4>

扩展Docker配置选项。有关详细信息，请参阅“服务的可用设置”。
<h4>services:command</h4>

扩展Docker配置选项。有关详细信息，请参阅“服务的可用设置”。
<h3 id="a7-4">7.4. before_script / after_script</h3>

before_script用于定义应在所有命令之前运行的命令job，包括部署job，但在恢复artifacts之后。这可以是一个数组或多行字符串。

after_script用于定义将要运行的命令job，包括失败的job。这必须是一个数组或多行字符串。

在before_script指定的script是：

与主script中指定的script连接。工作级别在script定义之前覆盖全局级别在script定义之前与script定义连接时。

与主scriptscript一起在单个shell中作为一个script执行上下文。

after_script指定的script：

将当前工作目录设置回默认值。

在与script和script之前分开的shell上下文中执行script。

由于上下文分隔，无法查看定义的script所做的更改在“script”或“before_script"，请执行以下操作之一：

在外壳中。例如，script中导出的命令别名和变量script。

在工作树之外（取决于运行者执行者）。例如，由before_script或scriptscript安装的软件。

可以覆盖在script之前和之后定义的全局,如果按job设置：
``` yml
default:
  before_script:
    - global before script

job:
  before_script:
    - execute this instead of global before script
  script:
    - my command
  after_script:
    - execute this after my script
```
<h3 id="a7-5">7.5. stages</h3>

stages用于全局定义job可以使用的阶段

stages的规格允许有灵活的多级管道。stages中元素的顺序定义了job执行的顺序：

同一阶段的job并行运行。

下一阶段的job在上一阶段的job之后运行成功完成。

让我们考虑下面的例子，它定义了3个阶段：
``` yml
stages:
  - build
  - test
  - deploy
```

首先，build的所有job都是并行执行的。

如果build的所有job都成功，则test job将并行执行。

如果test的所有job都成功，则deploy job将并行执行。

如果Deploy的所有job都成功，则提交标记为通过。

如果以前的任何job失败，提交将标记为失败，并且不

执行下一阶段的工作。

还有两个边缘案例值得一提：

如果在.gitlab-ci.yml中没有定义阶段，默认情况下，build, test and deploy用作job的阶段。

如果job未指定阶段，则该job将被分配到test阶段。
<h3 id="a7-6">7.6. stage</h3>

stage是定义单个job，并依赖于全局的stages。它允许将job分组到不同的阶段，并且相同的job阶段是并行执行的（取决于某些条件）。例如：
``` yml
stages:
  - build
  - test
  - deploy

job 1:
  stage: build
  script: make build dependencies

job 2:
  stage: build
  script: make build artifacts

job 3:
  stage: test
  script: make test

job 4:
  stage: deploy
  script: make deploy
```

Using your own Runners

使用自己的运行程序时，默认情况下，Gitlab运行程序一次只运行一个job（请参见运行程序全局设置中的并发标志）。

只有在以下情况下，job才会并行运行：

在不同程序上运行

运行程序的并发设置已更改。
<h3 id="a7-7">7.7. only/except (basic)</h3>

only和except两个参数将job策略设置为限制创建的job：

only定义要为其运行job的分支和标记的名称。

except定义排除的job

有一些规则适用于job策略的使用：

only和except是包容性的。如果只定义only和except在job规范中，引用only由和except。

only和except允许使用正则表达式（支持的regexp语法）。

only和except允许指定用于筛选job的存储库路径分支。

此外，only和except情况允许使用特殊关键字：
<table class="code">
<tbody>
    <tr>
        <td>Value</td>
        <td>描述</td>
    </tr>
    <tr>
        <td>branches</td>
        <td>当管道的Git引用是分支时。</td>
    </tr>
    <tr>
        <td>tags</td>
        <td>当管道的Git引用是标记时。</td>
    </tr>
    <tr>
        <td>api</td>
        <td>当管道被第二个管道API触发时（而不是触发API）。</td>
    </tr>
    <tr>
        <td>external</td>
        <td>使用Gitlab以外的CI服务时。</td>
    </tr>
    <tr>
        <td>pipelines</td>
        <td>对于多项目触发器，使用带有CI_JOB_TOKEN的API创建的。</td>
    </tr>
    <tr>
        <td>pushes</td>
        <td>管道由用户的git push触发。</td>
    </tr>
    <tr>
        <td>schedules</td>
        <td>用于计划的管道。</td>
    </tr>
    <tr>
        <td>triggers</td>
        <td>用于使用触发器标记创建的管道。</td>
    </tr>
    <tr>
        <td>web</td>
        <td>对于使用Gitlab UI中的“运行管道”按钮创建的管道（在项目的管道下）。</td>
    </tr>
    <tr>
        <td>merge_requests</td>
        <td>创建或更新合并请求时（有关合并请求，请参阅管道）。</td>
    </tr>
    <tr>
        <td>chats</td>
        <td>对于使用gitlab chatops命令创建的job。</td>
    </tr>
</tbody>
</table>


在下面的示例中，job将仅对以问题-开头的引用运行，鉴于将跳过所有分支：
``` yml
job:
  # use regexp
  only:
    - /^issue-.*$/
  # use special keyword
  except:
    - branches
```

默认情况下，模式匹配区分大小写。使用i标志修饰符，如/pattern/i使模式不区分大小写：
``` yml
job:
  # use regexp
  only:
    - /^issue-.*$/i
  # use special keyword
  except:
    - branches
```

在本例中，job将仅对标记的引用运行，或者如果通过API触发器或管道计划显式请求生成：
``` yml
job:
  # use special keywords
  only:
    - tags
    - triggers
    - schedules
```

存储库路径只能用于执行父存储库的job，而不能用于分叉：
``` yml
job:
  only:
    - branches@gitlab-org/gitlab-ce
  except:
    - master@gitlab-org/gitlab-ce
    - /^release/.*$/@gitlab-org/gitlab-ce
```

上面的示例将为gitlab-org/gitlab-ce上的所有分支运行job，master和以release/作为前缀的分支除外。

only默认值：''branches'、'tags']

except默认值为空。

例如，
``` yml
job:
  script: echo 'test'
```

转换为：
``` yml
job:
  script: echo 'test'
  only: ['branches', 'tags']
```
<h4>Regular expressions</h4>

因为@用于表示引用存储库路径的开头，匹配正则表达式中包含@字符的引用名称需要使用十六进制字符代码match \x40。

只有标记或分支名称才能与正则表达式匹配。如果给定存储库路径，则始终按字面匹配。

如果使用正则表达式来匹配标记或分支名称，模式的整个引用名称部分必须是正则表达式，必须用/包围。（在结束/后附加正则表达式标志）因此，问题-/.*/无法匹配所有标记名或分支名从问题开始。
> 使用锚^和$避免正则表达式只匹配标记名或分支名的子字符串。例如，/^issue-.$/等于/^issue-/，而just/issue/也将匹配一个称为严重问题的分支。

Supported only/except regexp syntax

警告：警告：这是Gitlab 11.9.4引入的突破性变化。

在Gitlab 11.9.4中，Gitlab开始内部转换使用的regexp仅限RE2和RE2的参数。

这意味着只有RubyRegexp提供的特性的子集支持。RE2限制了功能集由于计算的复杂性，这意味着一些特性在Gitlab 11.9.4中变得不可用。例如，负数lookaheads。

对于11.9.7到Gitlab 12.0的Gitlab版本，Gitlab提供了一个可以由管理员启用，允许用户使用不安全的regexp语法。这带来了兼容性使用以前允许的语法版本，并允许用户优雅地迁移到新语法。

``` yml
Feature.enable(:allow_unsafe_ruby_regexp)
```
<h3 id="a7-8">7.8. only/except (advanced)高级</h3>

警告：警告：这是一个alpha功能，随时可能更改，恕不另行通知！

Gitlab支持简单和复杂的策略，因此可以使用数组和哈希配置方案。

有四把钥匙：
``` yml
refs
variables
changes
kubernetes
```

If you use multiple keys under only or except, they act as an AND. The logic is:

(any of refs) AND (any of variables) AND (any of changes) AND (if kubernetes is active)
<h4>only:refs/except:refs</h4>

refs策略可以采用与仅简化/例外配置相同的值。

在下面的示例中，只有在为主分支计划管道或运行管道时，才会创建部署job：
``` yml
deploy:
  only:
    refs:
      - master
      - schedules
```
<h4>only:kubernetes/except:kubernetes</h4>

kubernetes策略只接受活动关键字。

在下面的示例中，只有当项目中的kubernetes服务处于活动状态时，才会创建部署job：
``` yml
deploy:
  only:
    kubernetes: active
```
<h4>only:variables/except:variables</h4>

variables关键字用于定义变量表达式。换句话说，您可以使用预定义的变量/project/group或环境范围的变量来定义一个表达式gitlab将要评估，以决定是否应该创建一个job。

使用变量表达式的示例：
``` yml
deploy:
  script: cap staging deploy
  only:
    refs:
      - branches
    variables:
      - $RELEASE == "staging"
      - $STAGING
```

另一个用例是根据提交消息排除job：
``` yml
end-to-end:
  script: rake test:end-to-end
  except:
    variables:
      - $CI_COMMIT_MESSAGE =~ /skip-end-to-end-tests/
```
<a href="https://gitlab.com/help/ci/variables/README.md#environment-variables-expressions" target="_blank" rel="noopener noreferrer">Learn more about variables expressions.</a>
<h4>only:changes/except:changes</h4>

使用changes关键字only或except可以定义是否应基于Git push事件修改的文件创建job。

For example:
``` yml
docker build:
  script: docker build -t my-image:$CI_COMMIT_REF_SLUG .
  only:
    changes:
      - Dockerfile
      - docker/scripts/*
      - dockerfiles/**/*
      - more_scripts/*.{rb,py,sh}
```

在上面的场景中，将多个提交推送到Gitlab时，分支，Gitlab创建并触发Docker构建job，前提是提交包含对以下任一项的更改：

Dockerfile文件。

docker/scripts/directory中的任何文件。

dockerfiles目录中的任何文件和子目录。

more_scripts目录中任何具有rb、py、sh扩展名的文件。

您还可以使用glob模式来匹配repo根目录或repo中的任何目录中的多个文件。例如：
``` yml
test:
  script: npm run test
  only:
    changes:
      - "*.json"
      - "**/*.sql"
```
>在上面的示例中，表达式被双引号括起来，因为它们是全局模式。Gitlab将无法解析带有未封装的glob模式的.gitlab-ci.yml文件。

如果在repo根目录下扩展名为.md的任何文件中检测到更改，则以下示例将跳过CIjob：
``` yml
build:
  script: npm run build
  except:
    changes:
      - "*.md"
```
>警告：警告：在将此功能与新的分支和标记一起使用时，需要注意一些事项。请参阅下面的部分。

使用带有新分支和标记的更改

在将新分支或新标记推送到Gitlab时，，策略的计算结果始终为true，Gitlab将创建job。此功能尚未与合并请求连接，并且，由于Gitlab正在创建管道，用户才能创建合并请求，因此此时目标分支的位置未知。

对合并请求使用更改

对于合并请求的管道，可以根据合并请求中修改的文件定义要创建的job。

For example:
``` yml
docker build service one:
  script: docker build -t my-service-one-image:$CI_COMMIT_REF_SLUG .
  only:
    refs:
      - merge_requests
    changes:
      - Dockerfile
      - service-one/**/*
```

在上面的场景中，如果创建或更新合并请求以更改service one目录或dockerfile中的文件，那么gitlab将创建并触发docker build service onejob。
<h3 id="a7-9">7.9.  tags</h3>

tags用于从允许运行此项目的所有运行者列表中选择特定的运行者。

在注册运行程序期间，可以指定运行程序的tags，例如ruby、postgres、development。

tags允许您使用分配了指定tags的运行者运行job：
``` yml
job:
  tags:
    - ruby
    - postgres
```

上面的规范将确保job由同时定义了ruby和postgres标记的运行程序构建。

标签也是在不同平台上运行不同job的好方法，例如，给定一个带有标签OSX的OSX运行程序和带有标签的Windows运行程序Windows，以下job在各自的平台上运行：
``` yml
windows job:
  stage:
    - build
  tags:
    - windows
  script:
    - echo Hello, %USERNAME%!

osx job:
  stage:
    - build
  tags:
    - osx
  script:
    - echo "Hello, $USER!"
```
<h3 id="a7-10">7.10. allow_failure</h3>

允许失败允许job失败而不影响CI套件的其余部分。默认值为假，手动job除外。

当启用并且job失败时，该job将在用户界面中显示橙色警告。但是，管道的逻辑流会将job视为成功/通过，并且不会被阻塞。

假设所有其他job都成功，则job的阶段及其管道将显示相同的橙色警告。但是，关联的提交将被标记为“通过”，没有警告。

在下面的示例中，job1和job2将并行运行，但如果job1失败，则不会停止下一阶段的运行，因为它标记为allow_failure:true:
``` yml
job1:
  stage: test
  script:
    - execute_script_that_will_fail
  allow_failure: true

job2:
  stage: test
  script:
    - execute_script_that_will_succeed

job3:
  stage: deploy
  script:
    - deploy_to_staging
```
<h3 id="a7-11">7.11. when</h3>

用于实现在失败或失败时运行的job。

when可以设置为以下值之一：

on_success-仅当先前阶段的所有job成功时执行job（或由于标记为“允许失败”而被视为成功）。这是默认设置。

on_failure-仅当先前阶段中的至少一个job失败时才执行job。

always-执行job，而不管先前阶段的job状态如何。

manual-手动执行job（在Gitlab 8.10中添加）。<a href="https://gitlab.com/help/ci/yaml/README.md#whenmanual" target="_blank" rel="noopener noreferrer">阅读下面的手动操作</a> 。

For example:
``` yml
stages:
  - build
  - cleanup_build
  - test
  - deploy
  - cleanup

build_job:
  stage: build
  script:
    - make build

cleanup_build_job:
  stage: cleanup_build
  script:
    - cleanup build when failed
  when: on_failure

test_job:
  stage: test
  script:
    - make test

deploy_job:
  stage: deploy
  script:
    - make deploy
  when: manual

cleanup_job:
  stage: cleanup
  script:
    - cleanup after jobs
  when: always
```

上面的脚本将：

仅当build_job失败时才执行cleanup_build_job。

始终在最后执行cleanup_job而不管成功或失败。

允许您从Gitlab的用户界面手动执行Deploy_job。
<h4>when:manual</h4>

手动操作是一种特殊类型的job，不能自动执行，需要由用户显式启动。手动操作的一个示例用法是部署到生产环境。可以从管道、job、环境和部署视图启动手动操作。<a href="https://gitlab.com/help/ci/environments.md#configuring-manual-deployments" target="_blank" rel="noopener noreferrer">Read more at the
environments documentation.</a> 

手动操作可以是可选的，也可以是阻塞的。阻止手动操作将在定义此操作的阶段阻止管道的执行。它是当有人通过单击播放按钮执行阻止手动操作时，可以恢复管道的执行。

当管道被阻塞时，如果设置了“管道成功时合并”，则不会合并管道。阻塞的管道也有一个特殊的状态，称为手动。默认情况下，手动操作是非阻塞的。如果要进行手动操作阻止，则必须在.gitlab-ci.yml中的job定义中添加allow_failure:false。

可选的手动操作默认情况下allow_failure: true，其状态不会影响整个管道状态。因此，如果手动操作失败，管道最终会成功。

手动操作被认为是写操作，因此当用户希望触发操作时，将使用受保护分支的权限。换句话说，为了触发分配给正在运行管道的分支的手动操作，用户需要能够合并到此分支。
<h4>when:delayed</h4>

延迟job用于在一段时间后执行脚本。如果希望避免job立即进入挂起状态，则此操作非常有用。

您可以用start_in键设置周期。除非提供了一个单位，否则start_in key的值是以秒为单位的已用时间,并且时间必须小于或等于一小时。例如：
``` yml
10 seconds
30 minutes
1 hour
```

当某个阶段中存在延迟job时，管道将不会进行，直到延迟job完成。这意味着这个关键字也可以用于在不同阶段之间插入延迟。

延迟job的计时器在上一阶段完成后立即启动。与其他类型的job类似，延迟job的计时器将不会启动，除非上一阶段已通过。

下面的示例创建一个名为Timed Rollow 10%的job，该job在上一阶段完成30分钟后执行：
``` yml
timed rollout 10%:
  stage: deploy
  script: echo 'Rolling out 10% ...'
  when: delayed
  start_in: 30 minutes
```

通过单击Unschedule按钮，可以停止延迟job的活动计时器。除非手动执行该job，否则将来永远不会执行该job。

您可以通过单击“播放”按钮立即启动延迟的job。Gitlab Runner将很快选择您的工作并开始工作。
<h3 id="a7-12">7.12. environment</h3>

environment用于定义job部署到特定环境。如果指定了environment，并且该名称下不存在任何环境，则将自动创建一个新的环境。

在其最简单的形式中，environment关键字的定义如下：
``` yml
deploy to production:
  stage: deploy
  script: git push production HEAD:master
  environment:
    name: production
```

在上面的示例中，部署到生产job将标记为对生产环境进行部署。
<h4>environment:name</h4>

The environment name can contain:
``` yml
letters
digits
spaces
-
_
/
$
{
}
```

常见的名称有qa, staging, and production，但是您可以将任何名称用于您的工作流。

除了在environment关键字后定义环境名称，还可以将其定义为单独的值。为此，请在环境下使用name关键字：
``` yml
deploy to production:
  stage: deploy
  script: git push production HEAD:master
  environment:
    name: production
```
<h4>environment:url</h4>

这是一个可选值，设置后，它会在Gitlab中的不同位置显示按钮，单击该按钮会将您带到定义的URL。

在下面的示例中，如果job成功完成，它将在合并请求和环境/部署页面中创建按钮，这些页面将指向https://prod.example.com。
``` yml
deploy to production:
  stage: deploy
  script: git push production HEAD:master
  environment:
    name: production
    url: https://prod.example.com
```
<h4>environment:on_stop</h4>

关闭（停止）环境可以通过在environment下定义的on-stop关键字来实现。它声明了一个不同的job，该job运行的目的是关闭环境。

例如，请阅读environment:action部分。
<h4>environment:action</h4>

action关键字将与on_stop一起使用，并在调用以关闭环境的job中定义。

例如：
``` yml
review_app:
  stage: deploy
  script: make deploy-app
  environment:
    name: review
    on_stop: stop_review_app

stop_review_app:
  stage: deploy
  variables:
    GIT_STRATEGY: none
  script: make delete-app
  when: manual
  environment:
    name: review
    action: stop
```

在上面的示例中，我们设置了review-appjob以部署到review环境中，并且在on-stop下定义了一个新的stop-review-appjob。一旦review_app完成，它将根据“when”下定义的内容触发stop_review_app。在本例中，我们将其设置为手动，以便它需要通过Gitlab的Web界面进行手动操作才能运行。

同样在这个例子中，GIT_STRATEGY被设置为“none”，这样当自动触发stop_review_app时，Gitlab Runner不会在删除分支后尝试check out代码。

stop_review_app需要定义以下关键字：
``` yml
when - reference
environment:name
environment:action
stage应与review_app相同，以便在删除分支时环境自动停止。
```

动态环境

For example:
``` yml
deploy as review app:
  stage: deploy
  script: make deploy
  environment:
    name: review/$CI_COMMIT_REF_NAME
    url: https://$CI_ENVIRONMENT_SLUG.example.com/
```

deploy as review app将标记为deployment，以动态创建review/$CI_COMMIT_REF_NAME环境，其中$CI_COMMIT_REF_NAME是运行程序设置的环境变量。$CI_ENVIRONMENT_SLUG变量基于环境名称，但适合包含在URL中。在这种情况下，如果在名为pow的分支中运行deploy as review app，则可以使用类似https://review pow.example.com/的URL访问此环境。

当然，这意味着承载应用程序的底层服务器配置正确。

常见的用例是为分支创建动态环境，并将它们用作审查应用程序。您可以在https://gitlab.com/gitlab-examples/review-apps-nginx/上看到一个使用review-apps的简单示例。
<h3 id="a7-13">7.13. cache</h3>
<a href="https://gitlab.com/help/ci/caching/index.md" target="_blank" rel="noopener noreferrer">TIP: Learn more:Read how caching works and find out some good practices in the caching dependencies documentation.</a>

cache用于指定应在job之间缓存的文件和目录的列表。只能使用项目工作区内的路径。

如果cache是在job范围之外定义的，则意味着它是全局设置的，所有job都将使用该定义。
<h4>cache:paths</h4>

使用paths指令选择将缓存哪些文件或目录。可以使用通配符跟踪glob模式和filepath.match。

缓存以.apk和.config文件结尾的二进制文件中的所有文件：
``` yml
rspec:
  script: test
  cache:
    paths:
      - binaries/*.apk
      - .config
```

本地定义的缓存覆盖全局定义的选项。以下rspecjob将只缓存二进制文件/：
``` yml
cache:
  paths:
    - my/files

rspec:
  script: test
  cache:
    key: rspec
    paths:
      - binaries/
```
> 请注意，由于缓存是在job之间共享的，如果您对不同的job使用不同的路径，则还应设置不同的cache:key，否则缓存内容会被覆盖。
<h4>cache:key</h4>

由于缓存是在job之间共享的，如果您对不同的job使用不同的路径，则还应设置不同的cache:key，否则缓存内容会被覆盖。

key指令允许您定义job之间缓存的关联性，允许为所有job都有一个缓存、每个job缓存、每个分支缓存。或者其他适合您工作流程的方式。通过这种方式，您可以微调缓存，允许您在不同的job之间甚至不同的分支之间缓存数据。

cache:key变量可以使用任何预定义的变量，如果没有设置默认键，则它只是文字默认值，这意味着默认情况下，从gitlab 9.0开始，每个管道和job之间共享所有内容。
> cache:key变量不能包含/字符，或者编码为%2f的等效URI；也禁止使用仅由点（.，%2e）构成的值。

例如，要启用每个分支缓存：
``` yml
cache:
  key: "$CI_COMMIT_REF_SLUG"
  paths:
    - binaries/
```

如果使用Windows批处理运行shell脚本，则需要将$替换为%：
``` yml
cache:
  key: "%CI_COMMIT_REF_SLUG%"
  paths:
    - binaries/
```
<h4>cache:untracked</h4>

set untracked:true缓存Git存储库中所有未跟踪的文件：
``` yml
rspec:
  script: test
  cache:
    untracked: true
```

缓存binaries下的所有Git未跟踪文件和文件：
``` yml
rspec:
  script: test
  cache:
    untracked: true
    paths:
      - binaries/
```
<h4>cache:policy</h4>

缓存job的默认行为是在执行开始时下载文件，并在结束时重新上传文件。这允许为将来的运行保留job所做的任何更改，称为pull-push缓存策略。

如果知道job不会更改缓存文件，可以通过设置policy:pull来跳过上传步骤。通常，这将与早期阶段的普通缓存job成对出现，以确保缓存不时更新：
``` yml
stages:
  - setup
  - test

prepare:
  stage: setup
  cache:
    key: gems
    paths:
      - vendor/bundle
  script:
    - bundle install --deployment

rspec:
  stage: test
  cache:
    key: gems
    paths:
      - vendor/bundle
    policy: pull
  script:
    - bundle exec rspec ...
```

这有助于加快job执行速度并减少缓存服务器上的负载，特别是当您有大量的缓存使用并行执行的job时。

此外，如果有一个job无条件地重新创建缓存而不引用其先前的内容，则可以使用policy: push跳过下载步骤。
<h3 id="a7-14">7.14. artifacts</h3>

artifacts用于指定在job成功、失败或总是失败时应附加到该job的文件和目录列表。

artifacts将在job完成后发送到Gitlab，并可在Gitlab UI中下载。
<a href="https://gitlab.com/help/user/project/pipelines/job_artifacts.md" target="_blank" rel="noopener noreferrer">Read more about artifacts.</a>
<h4>artifacts:paths</h4>

只能使用项目工作区内的路径。可以使用通配符跟踪glob模式和filepath.match。

要在不同job之间传递artifacts，see dependencies.。

发送binaries and .config:下所有文件：
``` yml
artifacts:
  paths:
    - binaries/
    - .config
```

要禁用artifacts传递，请使用dependencies: []
``` yml
job:
  stage: build
  script: make build
  dependencies: []
```

您可能希望只为tags的版本创建artifacts，以避免用临时的artifacts填充构建服务器存储。

仅为tags创建artifacts（default-job不会创建artifacts）：
``` yml
default-job:
  script:
    - mvn test -U
  except:
    - tags

release-job:
  script:
    - mvn package -U
  artifacts:
    paths:
      - target/*.war
  only:
    - tags
```
<h4>artifacts:name</h4>

name指令允许您定义创建的artifacts存档的名称。这样，当您想从Gitlab下载归档文件时，每个归档文件都可以有一个唯一的名称。artifacts:name变量可以使用任何预定义的变量。默认名称为artifacts，下载时将变为artifacts.zip。
> 如果您的分支名称包含正斜杠（例如feature/my feature），建议使用$ci-commit-ref-slug而不是$ci-commit-ref-name来正确命名artifacts。

要使用当前job的名称创建存档，请执行以下操作：
``` yml
job:
  artifacts:
    name: "$CI_JOB_NAME"
    paths:
      - binaries/
```

要使用当前分支或标记的名称（仅包括binaries文件夹）创建存档，请执行以下操作：
``` yml
job:
  artifacts:
    name: "$CI_COMMIT_REF_NAME"
    paths:
      - binaries/
```

要使用当前job的名称和当前分支或标记（仅包括binaries文件夹）创建存档，请执行以下操作：
``` yml
job:
  artifacts:
    name: "$CI_JOB_NAME-$CI_COMMIT_REF_NAME"
    paths:
      - binaries/
```

要创建具有当前阶段名称和分支名称的存档，请执行以下操作：
``` yml
job:
  artifacts:
    name: "$CI_JOB_STAGE-$CI_COMMIT_REF_NAME"
    paths:
      - binaries/
```

如果使用Windows批处理运行shell脚本，则需要将$替换为%：
``` yml
job:
  artifacts:
    name: "%CI_JOB_STAGE%-%CI_COMMIT_REF_NAME%"
    paths:
      - binaries/
```

如果使用Windows PowerShell运行shell脚本，则需要将$替换为$env:：
``` yml
job:
  artifacts:
    name: "$env:CI_JOB_STAGE-$env:CI_COMMIT_REF_NAME"
    paths:
      - binaries/
```
<h4>artifacts:untracked</h4>

artifacts:untracked用于将所有git未跟踪文件添加为artifacts（沿着artifacts:paths中定义的路径）。
> artifacts:untracked忽略存储库的.gitignore文件中的配置。

发送所有git未跟踪文件：
``` yml
artifacts:
  untracked: true
```

发送所有git未跟踪文件和二进制文件：
``` yml
artifacts:
  untracked: true
  paths:
    - binaries/
```
<h4>artifacts:when</h4>

artifacts:when用于在job失败或尽管失败时上载artifacts的时间。

artifacts:when可以设置为以下值之一：

on_success-仅当job成功时上载artifacts。这是默认设置。

on_failure-仅当job失败时才上载artifacts。

always-上载artifacts，不管job状态如何。

仅当job失败时上载项目：
``` yml
job:
  artifacts:
    when: on_failure
```
<h4>artifacts:expire_in</h4>

expire_-in允许您指定artifacts在过期之前应该存在多长时间，从而从它们上传和存储到Gitlab的时间开始删除。如果未定义到期时间，则默认为实例范围的设置。（默认为30天，永远在gitlab.com上）。

您可以使用“job”页上的“保留”按钮来覆盖过期时间并永久保留项目。

过期后，artifacts默认每小时删除一次（通过cronjob），并且不再可访问。

expire_in的值是以秒为单位的已用时间，除非提供了一个单位。可分析值示例：
``` yml
'42'
'3 mins 4 sec'
'2 hrs 20 min'
'2h20min'
'6 mos 1 day'
'47 yrs 6 mos and 4d'
'3 weeks and 2 days'
```

要在上载后1周使项目过期，请执行以下操作：
``` yml
job:
  artifacts:
    expire_in: 1 week
```
<h4>artifacts:reports</h4>

reports关键字用于从job收集测试报告并在Gitlab的UI（合并请求、管道视图）中公开它们。阅读如何将其与JUnit报告一起使用。
> 无论job结果如何（成功或失败），都会收集测试报告。您可以使用artifacts:expire\u来设置其artifacts的到期日期。
> 如果还希望能够浏览报告输出文件，请包含artifacts:paths关键字。
<h4>artifacts:reports:junit</h4>

JUnit报告将JUnit XML文件收集为artifacts。虽然JUnit最初是在爪哇开发的，但是有许多其他第三方端口，如yml、Python、Ruby等。

有关更多详细信息和示例，请参阅JUnit测试报告。下面是从Ruby的rspec测试工具收集JUnit XML文件的示例：
``` yml
rspec:
  stage: test
  script:
  - bundle install
  - rspec --format RspecJunitFormatter --out rspec.xml
  artifacts:
    reports:
      junit: rspec.xml
```

收集到的JUnit报告将作为artifacts上传到Gitlab，并将自动显示在合并请求中。

如果JUnit工具使用导出到多个XML文件，则可以在一个job中指定多个测试报告路径，这些路径将自动连接到一个文件中。使用文件名模式（junit:rspec-*.xml）、文件名数组（junit:[rspec-1.xml、rspec-2.xml、rspec-3.xml]）或其组合（junit:[rspec.xml，测试结果/test-*.xml]）。
<h4>artifacts:reports:codequality (STARTER)</h4>

代码质量报告将代码质量问题收集为artifacts。

收集的代码质量报告将作为artifacts上传到Gitlab，并将自动显示在合并请求中。
<h4>artifacts:reports:sast (ULTIMATE)终极</h4>

sast报告将sast漏洞收集为artifacts。

收集的SAST报告将作为artifacts上传到Gitlab，并将自动显示在合并请求、管道视图中，并为安全仪表盘提供数据。
<h4>artifacts:reports:dependency_scanning (ULTIMATE)终极</h4>

依赖项扫描报告将依赖项扫描漏洞收集为artifacts。

收集到的依赖项扫描报告将作为artifacts上载到Gitlab，并将自动显示在合并请求、管道视图中，并为安全仪表板提供数据。
<h4>artifacts:reports:container_scanning (ULTIMATE)</h4>

容器扫描报告将容器扫描漏洞收集为artifacts。

收集的容器扫描报告将作为一个artifacts上载到Gitlab，并将自动显示在合并请求、管道视图中，并为安全仪表板提供数据。
<h4>artifacts:reports:dast (ULTIMATE)</h4>

DAST报告将DAST漏洞收集为artifacts。

收集的DAST报告将作为artifacts上传到Gitlab，并将自动显示在合并请求、管道视图中，并为安全仪表盘提供数据。
<h4>artifacts:reports:license_management (ULTIMATE)</h4>

许可证管理报告将许可证作为artifacts收集。

收集的许可证管理报告将作为一个artifacts上载到Gitlab，并将自动显示在合并请求、管道视图中，并为安全仪表盘提供数据。
<h4>artifacts:reports:performance (PREMIUM)</h4>

性能报告将性能指标收集为artifacts。

收集到的性能报告将作为artifacts上传到Gitlab，并将自动显示在合并请求中。
<h4>artifacts:reports:metrics (PREMIUM)</h4>

性能报告将性能指标收集为artifacts。
收集到的性能报告将作为artifacts上传到Gitlab，并将自动显示在合并请求中。
<h4>artifacts:reports:metrics (PREMIUM)</h4>

度量报告将度量收集为artifacts。

收集的度量报告将作为artifacts上传到Gitlab，并将自动显示在合并请求中。
<h3 id="a7-15">7.15. dependencies</h3>

此功能应该与artifacts一起使用，并允许您定义要在不同job之间传递的artifacts。

请注意，默认情况下，来自所有先前阶段的artifacts都会被传递。

若要使用此功能，请在job的上下文中定义依赖项，并传递一个列表，其中列出了应从中下载artifacts的所有先前job。只能从当前job之前执行的阶段定义job。如果从当前阶段或下一阶段定义job，将显示一个错误。

定义空数组将跳过下载该job的任何artifacts。使用依赖项时不考虑上一个job的状态，因此如果它失败或是未运行的手动job，则不会发生错误。

在下面的示例中，我们用artifacts定义了两个job，build:osx和build:linux。执行test:osx时，来自build:osx的artifacts

将在生成上下文中下载和提取。对于test:linux和build:linux中的artifacts也是如此。

由于阶段优先，job部署将从所有以前的job下载项目：
``` yml
build:osx:
  stage: build
  script: make build:osx
  artifacts:
    paths:
      - binaries/

build:linux:
  stage: build
  script: make build:linux
  artifacts:
    paths:
      - binaries/

test:osx:
  stage: test
  script: make test:osx
  dependencies:
    - build:osx

test:linux:
  stage: test
  script: make test:linux
  dependencies:
    - build:linux

deploy:
  stage: deploy
  script: make deploy
```
<h4>When a dependent job will fail</h4>

如果设置为依赖项的job的artifacts已过期或清除，则依赖job将失败。
> 您可以要求管理员翻转此开关并恢复旧行为。
<h3 id="a7-16">7.16. coverage</h3>

Coverage允许您配置如何从job输出中提取代码覆盖率。

正则表达式是此处唯一有效的值类型。因此，为了一致和显式地表示正则表达式字符串，必须使用环绕/。如果你想从字面上匹配特殊字符，你必须转义它们。

A simple example:
``` yml
job1:
  script: rspec
  coverage: '/Code coverage: \d+\.\d+/'
```
<h3 id="a7-17">7.17. retry</h3>

重试”允许您配置一个job在发生故障时将重试多少次。

当一个job失败并配置了重试时，它将被再次处理到retry关键字指定的次数。

如果retry设置为2，并且job在第二次运行（第一次重试）中成功，则不会重试。

再一次。重试值必须是一个正整数，等于或大于0，但小于或等于2（最多两次重试，总共三次）。

在所有故障情况下重试的简单示例：
``` yml
test:
  script: rspec
  retry: 2
```

默认情况下，将在所有失败情况下重试job。要更好地控制重试失败的次数，retry可以是具有以下键的哈希：

max：最大重试次数。

when：失败的案例要重试。

要最多重试两次运行程序系统故障，请执行以下操作：
``` yml
test:
  script: rspec
  retry:
    max: 2
    when: runner_system_failure
```

如果存在除运行程序系统故障以外的其他故障，则不会重试job。

要在多个故障情况下重试，时间也可以是一个故障数组：
``` yml
test:
  script: rspec
  retry:
    max: 2
    when:
      - runner_system_failure
      - stuck_or_timeout_failure
```

Possible values for when are:

always：在出现任何故障时重试（默认）。

unknown_failure：失败原因未知时重试。

script_failure：当脚本失败时重试。

api_failure：在api失败时重试。

stuck_or_timeout_failure：当job卡住或超时时重试。

runner_system_failure：如果运行程序系统故障（例如设置job失败），请重试。

missing_dependency_failure：如果缺少依赖项，请重试。

runner_unsupported：如果运行程序不受支持，请重试。
<h3 id="a7-18">7.18. parallel</h3>

Parallel允许您配置要并行运行的job实例数。该值必须大于或等于两（2）且小于或等于50。

这将创建并行运行的同一job的n个实例。它们按顺序从job_name 1/n到job_name n/n命名。

对于每个job，都会设置CI_NODE_INDEX and CI_NODE_TOTAL environment variables。

一个简单的例子：
``` yml
test:
  script: rspec
  parallel: 5
```
> 跨并行job并行化测试套件。不同的语言有不同的工具来促进这一点。
<h3 id="a7-19">7.19. trigger (PREMIUM)</h3>

trigger允许您定义下游管道trigger。当从trigger定义创建的job由Gitlab启动时，将创建下游管道。
<a href="https://gitlab.com/help/ci/multi_project_pipelines.md#creating-multi-project-pipelines-from-gitlab-ciyml" target="_blank" rel="noopener noreferrer">Learn more about multi-project pipelines.</a>

简单触发器语法

配置下游触发器以使用具有下游项目完整路径的触发器关键字的最简单方法：
``` yml
rspec:
  stage: test
  script: bundle exec rspec

staging:
  stage: deploy
  trigger: my/deployment
```

复杂触发器语法

可以配置Gitlab用于创建下游管道的分支名称：
``` yml
rspec:
  stage: test
  script: bundle exec rspec

staging:
  stage: deploy
  trigger:
    project: my/deployment
    branch: stable
```
<h3 id="a7-20">7.20. include</h3>

使用include关键字，可以允许包含外部yaml文件。include要求外部yaml文件具有扩展名.yml或.yaml，否则将不包括外部文件。

include中定义的文件包括：

--与.gitlab-ci.yml中的合并。

--始终首先评估并与.gitlab-ci.yml的内容合并，而不管include关键字的位置如何。
> 使用合并可使用本地定义自定义和覆盖包含的CI/CD配置。
> 不支持跨include源的不同yaml文件使用yaml别名。您只能引用同一文件中的别名。您可以使用extends关键字，而不是使用yaml锚。

包含支持四个包含方法

``` yml
local
file
template
remote
```

<a href="https://gitlab.com/help/ci/yaml/README.md#include-examples" target="_blank" rel="noopener noreferrer">See usage examples.</a>
> 所有方法包含的.gitlab-ci.yml配置在管道创建时进行评估。配置是一个及时的快照，并持久存在于数据库中。在创建下一个管道之前，对引用的.gitlab-ci.yml配置所做的任何更改都不会反映在gitlab中。
<h4>include:local</h4>

include:local包含来自与.gitlab-ci.yml相同存储库的文件。它是使用相对于根目录的完整路径来引用的（/）。

您只能在配置文件所在的分支上使用Git当前跟踪的文件。换句话说，当使用include:local时，请确保.gitlab-ci.yml和本地文件都在同一个分支上。

所有嵌套的include都将在同一个项目的范围内执行，因此可以使用本地、项目、远程或模板include。
> 不支持通过git子模块路径包含本地文件。

例子：
``` yml
include:
  - local: '/templates/.gitlab-ci-template.yml'
```
<h4>include:file</h4>

要在同一Gitlab实例下包含来自另一个私有项目的文件，请使用include:file。使用相对于根目录的完整路径（/）引用此文件。例如：
``` yml
include:
  - project: 'my-group/my-project'
    file: '/templates/.gitlab-ci-template.yml'
```

还可以指定ref，默认值为项目的标题：
``` yml
include:
  - project: 'my-group/my-project'
    ref: master
    file: '/templates/.gitlab-ci-template.yml'

  - project: 'my-group/my-project'
    ref: v1.0.0
    file: '/templates/.gitlab-ci-template.yml'

  - project: 'my-group/my-project'
    ref: 787123b47f14b552955ca2786bc9542ae66fee5b # Git SHA
    file: '/templates/.gitlab-ci-template.yml'
```

所有嵌套的include都将在目标项目的范围内执行，因此可以使用本地（相对于目标项目）、项目、远程或模板include。
<h4>include:template</h4>

include:template可用于包含随Gitlab一起提供的.gitlab-ci.yml模板。

For example:
``` yml
# File sourced from GitLab's template collection
include:
  - template: Auto-DevOps.gitlab-ci.yml
```

所有嵌套的include只能在用户的权限下执行，因此可以使用project、remote或template include。
<h4>include:remote</h4>

nclude:remote可用于包含来自不同位置的文件，使用http/https，使用完整的URL引用。远程文件必须通过简单的GET请求公开访问，因为不支持远程URL中的身份验证架构。例如：
``` yml
include:
  - remote: 'https://gitlab.com/awesome-project/raw/master/.gitlab-ci-template.yml'
```

所有嵌套的include将作为公共用户在没有上下文的情况下执行，因此只允许使用其他远程、公共项目或模板。
<h4>嵌套包含</h4>

嵌套的include允许您组成一组include。总共允许50个。

重复包含被视为配置错误。

包括示例

下面还有一些例子。

单个字符串或多个值的数组，您可以将额外的yaml文件作为单个字符串或多个值的数组包含在内。下面的例子都是有效的。

包含include:local方法的单个字符串：
``` yml
include: '/templates/.after-script-template.yml'
```

包含include方法的数组：
``` yml
include:
  - 'https://gitlab.com/awesome-project/raw/master/.before-script-template.yml'
  - '/templates/.after-script-template.yml'
```

显式指定包含方法的单个字符串：
``` yml
include:
  remote: 'https://gitlab.com/awesome-project/raw/master/.before-script-template.yml'
```

带有include:remote的数组是单个项：
``` yml
include:
  - remote: 'https://gitlab.com/awesome-project/raw/master/.before-script-template.yml'
```

具有多个包含方法的数组已明确指定：
``` yml
include:
  - remote: 'https://gitlab.com/awesome-project/raw/master/.before-script-template.yml'
  - local: '/templates/.after-script-template.yml'
  - template: Auto-DevOps.gitlab-ci.yml
```

数组混合语法：
``` yml
include:
  - 'https://gitlab.com/awesome-project/raw/master/.before-script-template.yml'
  - '/templates/.after-script-template.yml'
  - template: Auto-DevOps.gitlab-ci.yml
  - project: 'my-group/my-project'
    ref: master
    file: '/templates/.gitlab-ci-template.yml'
```
<h4>重新使用前脚本模板</h4>

在下面的示例中，.before-script-template.yml的内容将与.gitlab-ci.yml的内容一起自动获取和评估。

https://gitlab.com/awesome project/raw/master/.before-script-template.yml的内容：
``` yml
before_script:
  - apt-get update -qq && apt-get install -y -qq sqlite3 libsqlite3-dev nodejs
  - gem install bundler --no-document
  - bundle install --jobs $(nproc)  "${FLAGS[@]}"
```

Content of .gitlab-ci.yml:
``` yml
include: 'https://gitlab.com/awesome-project/raw/master/.before-script-template.yml'

rspec:
  script:
    - bundle exec rspec
```

覆盖外部模板值以下示例显示了特定的yaml定义的变量以及在.gitlab-ci.yml中自定义的include文件中生产job的详细信息。


https://company.com/autodevops-template.yml的内容：
``` yml
variables:
  POSTGRES_USER: user
  POSTGRES_PASSWORD: testing_password
  POSTGRES_DB: $CI_ENVIRONMENT_SLUG

production:
  stage: production
  script:
    - install_dependencies
    - deploy
  environment:
    name: production
    url: https://$CI_PROJECT_PATH_SLUG.$KUBE_INGRESS_BASE_DOMAIN
  only:
    - master
```

Content of .gitlab-ci.yml:
``` yml
include: 'https://company.com/autodevops-template.yml'

image: alpine:latest

variables:
  POSTGRES_USER: root
  POSTGRES_PASSWORD: secure_password

stages:
  - build
  - test
  - production

production:
  environment:
    url: https://domain.com
```

在这种情况下，变量postgres-user和postgres-password以及autodevops-template.yml中定义的生产job的环境url已被.gitlab-ci.yml中定义的新值覆盖。

合并允许扩展和重写字典映射，但不能向包含的数组添加或修改项。例如，要向生产job脚本添加其他项，必须重复现有的脚本项：

https://company.com/autodevops-template.yml的内容：
``` yml
production:
  stage: production
  script:
    - install_dependencies
    - deploy
```

Content of .gitlab-ci.yml:
``` yml
include: 'https://company.com/autodevops-template.yml'

stages:
  - production

production:
  script:
    - install_dependencies
    - deploy
    - notify_owner
```

在这种情况下，如果在.gitlab-ci.yml中不重复install_dependencies和deploy，它们将不会成为组合CI配置中生产job脚本的一部分。
<h4>使用嵌套的include</h4>

下面的示例说明如何使用不同方法的组合从不同的源嵌套include。

在本例中，.gitlab-ci.yml包含本地文件/.gitlab-ci/another-config.yml：
``` yml
include:
  - local: /.gitlab-ci/another-config.yml
```

.gitlab ci/another-config.yml包含一个模板和另一个项目的/templates/docker-workflow.yml文件：
``` yml
include:
  - template: Bash.gitlab-ci.yml
  - project: group/my-project
    file: /templates/docker-workflow.yml
```

group/my项目中的/templates/docker-workflow.yml包含group/my项目的两个本地文件：
``` yml
include:
  - local: /templates/docker-build.yml
  - local: /templates/docker-testing.yml
```

group/my项目中的/templates/docker-build.yml添加了一个docker构建job：
``` yml
docker-build:
  script: docker build -t my-image .
```

我们的第二个/templates/docker-test.yml出现在group/my项目中，它添加了一个docker测试job：
``` yml
docker-test:
  script: docker run my-image /run/tests.sh
```
<h3 id="a7-21">7.21. extends</h3>

扩展定义了使用扩展的job将从中继承的项名称。

它是使用yaml锚的替代方法，并且更灵活和易读：
``` yml
.tests:
  script: rake test
  stage: test
  only:
    refs:
      - branches

rspec:
  extends: .tests
  script: rake rspec
  only:
    variables:
      - $RSPEC
```

在上面的示例中，rspecjob继承自.tests模板job。Gitlab将根据这些键执行反向深进。Gitlab想要：

递归地将rspec内容放入.tests。

不去键的值。

这将导致以下job：
``` yml
rspec:
  script: rake rspec
  stage: test
  only:
    refs:
      - branches
    variables:
      - $RSPEC
```
> 请注意，script:rake测试已被script:rake rspec覆盖。

如果您想包括rake测试，请参见之前的脚本和之后的脚本。

.本例中的测试是隐藏的密钥，但也可以从常规job继承。扩展支持多级别继承，但不建议使用三个以上的级别。支持的最大嵌套级别为10。

以下示例具有两个继承级别：
``` yml
.tests:
  only:
    - pushes

.rspec:
  extends: .tests
  script: rake rspec

rspec 1:
  variables:
    RSPEC_SUITE: '1'
  extends: .rspec

rspec 2:
  variables:
    RSPEC_SUITE: '2'
  extends: .rspec

spinach:
  extends: .tests
  script: rake spinach
```

在Gitlab 12.0及更高版本中，也可以使用多个父级进行扩展。用于合并的算法是“最近的作用域获胜”，因此来自最后一个成员的键将始终隐藏在其他级别上定义的任何内容。例如：
``` yml
.only-important:
  only:
    - master
    - stable
  tags:
    - production

.in-docker:
  tags:
    - docker
  image: alpine

rspec:
  extends:
    - .only-important
    - .in-docker
  script:
    - rake rspec
```

这将导致以下RSPECjob：
``` yml
rspec:
  only:
    - master
    - stable
  tags:
    - docker
  image: alpine
  script:
    - rake rspec
```
<h4>Using extends and include together</h4>

扩展配置文件和include的工作。

例如，如果您有本地included.yml文件：
``` yml
.template:
  script:
    - echo Hello!
```

然后，在.gitlab-ci.yml中，您可以这样使用它：
``` yml
include: included.yml

useTemplate:
  image: alpine
  extends: .template
```

这将运行一个名为usetemplate的job，该job运行echo hello！根据.templatejob中的定义，并使用本地job中定义的Alpine Docker图像。
<h3 id="a7-22">7.22. pages</h3>

页面是一项特殊的工作，用于将静态内容上载到Gitlab，该工作可用于为您的网站提供服务。它有一个特殊的语法，所以这两个

必须满足以下要求：

任何静态内容都必须放在public/目录下。

必须定义具有公共/目录路径的项目。

下面的示例只是将所有文件从项目的根目录移动到public/directory。.public解决方案是这样的，cp不会无限循环地将public/复制到自身：
``` yml
pages:
  stage: deploy
  script:
    - mkdir .public
    - cp -r * .public
    - mv .public public
  artifacts:
    paths:
      - public
  only:
    - master
```
<a href="https://gitlab.com/help/user/project/pages/index.md" target="_blank" rel="noopener noreferrer">Read more on GitLab Pages user documentation.</a>
<h3 id="a7-23">7.23. variables</h3>
> 整数（以及字符串）对于变量的名称和值都是合法的。浮动不合法，不能使用。

Gitlab CI/CD允许您在.gitlab-ci.yml中定义变量，然后在job环境中传递这些变量。它们可以全局设置，也可以按job设置。

在job级别上使用variables关键字时，它将覆盖全局yaml变量和预定义变量。

它们存储在Git存储库中，用于存储非敏感项目配置，例如：
``` yml
variables:
  DATABASE_URL: "postgres://postgres@postgres/my_database"
```

这些变量稍后可以在所有执行的命令和脚本中使用。yaml定义的变量也被设置为所有创建的服务容器，从而允许对它们进行微调。

除了用户定义的变量之外，还有运行程序本身设置的变量。一个例子是ci-commit-ref-name，它具有为其构建项目的分支或标记名的值。除了可以在.gitlab-ci.yml中设置的变量外，还可以在gitlab的用户界面中设置所谓的变量。
<a href="https://gitlab.com/help/ci/variables/README.md" target="_blank" rel="noopener noreferrer">Learn more about variables and their priority.</a>
<h4>Git strategy</h4>

您可以在“变量”部分中设置用于获取最新应用程序代码的Git_策略（全局或每个job）。如果未指定，将使用项目设置中的默认值。

有三个可能的值：clone、fetch和none。

克隆是最慢的选项。它为每个job从头克隆存储库，确保项目工作空间始终是原始的。
``` yml
variables:
  GIT_STRATEGY: clone
```

提取速度更快，因为它重新使用项目工作区（如果不存在则返回到克隆）。Git Clean用于撤消上一个job所做的任何更改，Git Fetch用于检索自上一个job运行以来所做的提交。
``` yml
variables:
  GIT_STRATEGY: fetch
```

也没有人会重新使用项目工作区，但会跳过所有Git操作（包括Gitlab Runner的预克隆脚本，如果存在的话）。它主要用于只在artifacts上操作的job（例如部署）。Git存储库数据可能存在，但它肯定是过时的，因此您应该只依赖从缓存或artifacts引入到项目工作区的文件。
``` yml
variables:
  GIT_STRATEGY: none
```
> Kubernetes的执行者不支持Git_策略，但将来可能会支持。有关更新，请参阅支持带kubernetes执行器的git策略的特性建议。
<h4>Git submodule strategy</h4>

Git_子模块_策略变量用于控制在生成前获取代码时是否包含Git子模块/如何包含Git子模块。您可以在“变量”部分中全局或按job设置它们。

有三个可能的值：无、正常和递归：

无表示在获取项目代码时不包括子模块。这是默认值，与v1.10之前的行为匹配。

正常意味着只包括顶级子模块。相当于：
``` yml
git submodule sync
git submodule update --init
```

递归意味着将包括所有子模块（包括子模块的子模块）。此功能需要Git v1.8.1及更高版本。当使用不基于Docker的执行器的Gitlab运行程序时，请确保Git版本满足该要求。相当于：
``` yml
git submodule sync --recursive
git submodule update --init --recursive
```

请注意，要使此功能正常工作，子模块必须（在.gitmodules中）配置为：

公共可访问存储库的HTTP（S）URL，或指向同一Gitlab服务器上另一个存储库的相对路径。请参见Git子模块文档。
<h4>Git checkout</h4>

当git策略设置为clone或fetch以指定是否应运行git签出时，可以使用git签出变量。如果未指定，则默认为true。您可以在“变量”部分中全局或按job设置它们。如果设置为false，则运行程序将：

执行提取时-更新存储库并将工作副本保留在当前版本上，执行克隆时-克隆存储库并将工作副本保留在默认分支上。

将此设置设置为true意味着对于克隆和获取策略，运行程序将签出工作副本到与CI管道相关的修订：
``` yml
variables:
  GIT_STRATEGY: clone
  GIT_CHECKOUT: "false"
script:
  - git checkout -B master origin/master
  - git merge $CI_COMMIT_SHA
```
<h4>Git clean flags</h4>

git-clean-flags变量用于控制签出源后git-clean的默认行为。您可以全局设置它，也可以在变量部分中为每个job设置它。

git_clean_标志接受git clean命令的所有可能选项。

如果指定了git_checkout:false，则禁用git clean。如果git_clean_标志是：

未指定，git clean标志默认为-ffdx。

给定值none，不执行git clean。

例如：
``` yml
variables:
  GIT_CLEAN_FLAGS: -ffdx -e cache/
script:
  - ls -al cache/
```
<h4>Job stages attempts</h4>

您可以设置正在运行的job将尝试执行以下每个阶段的尝试次数：

<table class="code">
<tbody>
    <tr>
        <td>Variable</td>
        <td>Description</td>
    </tr>
    <tr>
        <td>GET_SOURCES_ATTEMPTS</td>
        <td>尝试获取运行job的源的次数</td>
    </tr>
    <tr>
        <td>ARTIFACT_DOWNLOAD_ATTEMPTS</td>
        <td>尝试下载运行job的项目的次数</td>
    </tr>
    <tr>
        <td>RESTORE_CACHE_ATTEMPTS</td>
        <td>尝试还原运行job的缓存的次数</td>
    </tr>
</tbody>
</table>


默认为一次尝试。

Example:
``` yml
variables:
  GET_SOURCES_ATTEMPTS: 3
```
<h4>Shallow cloning</h4>

可以使用git_depth指定提取和克隆的深度。这允许对存储库进行浅层克隆，这可以显著加快对具有大量提交或旧的大型二进制文件的存储库的克隆。价值是

传递给了git fetch和git clone。
> 如果使用深度1并有job队列或重试job，则job可能会失败。

因为Git获取和克隆是基于引用的，例如分支名称，所以运行者不能克隆特定的commit SHA。如果队列中有多个job，或者您正在重试一个旧job，那么要测试的提交需要在克隆的Git历史记录中。为GIT_DEPTH设置的值太小可能导致无法运行这些旧提交。您将在中看到未解决的引用

job日志。然后，您应该重新考虑将GIT_DEPTH更改为更高的值。

当设置GIT_DEPTH时，依赖于Git描述的job可能无法正常工作，因为Git历史只有一部分存在。

要仅获取或克隆最后3个提交：
``` yml
variables:
  GIT_DEPTH: "3"
```

您可以全局设置它，也可以在变量部分中为每个job设置它。

<h3 id="a8">8. 不推荐使用的参数(见原文)</h3>

以下参数已弃用。......