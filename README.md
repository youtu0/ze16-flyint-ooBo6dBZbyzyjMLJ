
GitHub 的 workflow 和 action 存在着一些注意事项，包括 workflow 的 yaml 配置，action 的脚本编写，以及对应的 branch 的保护设置，总结如下，以供参考


## Workflow


### on.issues.types


如果需要判断 label，不需要指定 `opened`，只需要指定 `labeled`，因为即使 label 是新建时设置的，也会触发 `labeled`


### permissions


如果需要 checkout 当前 repo，需要添加 `contents: write`，否则会有权限问题


### jobs.check.steps.env


如果需要使用 GitHub API，需要添加 `GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}`，作为环境变量


## Action


### List repository issues


[API](https://github.com) 不仅返回 issues，也会返回 prs，默认 30 条每页，可以指定 `labels` 来过滤


### List pull requests


[API](https://github.com) 返回所有 pull requests，默认 30 条每页，可以通过 `per_page` 和 `page` 参数做分页处理，例如：



```
const prs = [];
for (let page = 1; ; page++) {
  const { data } = await octokit.rest.pulls.list({
    ...context.repo,
    base: `refs/heads/main`,
    state: 'open',
    per_page: PER_PAGE,
    page,
  });
  prs.push(...data);
  if (data.length < PER_PAGE) {
    break;
  }
}

```

### Get commit status


[API](https://github.com):[westworld加速器](https://xbsj9.com) 列出所有 contexts 对应的 state，可以过滤当前的 context


### Create commit status


[API](https://github.com) 用于创建 commit 的 status，可指定 `state`，`context` 和 `description`（作为运行结果显示）


## Setting


### Require status checks


需要在 Branch protection rule 中的 Protect matching branches 下，勾选 Require status checks to pass before merging


### Add required checks


需要将当前的 GitHub action 添加到 Status checks that are required


