# Google Go 编程规范

[《Google Go 编程规范》](https://github.com/gocn/styleguide)采用 [Hugo](https://gohugo.io) 发布。欢迎大家通过 [issue](https://github.com/gocn/styleguide/issues) 提供建议，也可以通过 [pull requests](https://github.com/gocn/styleguide/pulls) 来共同参与贡献。

贡献者（按昵称首字母排序）:

> [astaxie](https://github.com/astaxie) | [Fivezh](https://github.com/fivezh) | [刘思家](https://github.com/lsj1342) | [Sijing233](https://github.com/sijing233) | [小超人](https://github.com/focozz) | [Xiaomin Zheng](https://github.com/zxmfke) | [Yu Zhang](https://github.com/pseudoyu) | [朱亚光](https://github.com/zhuyaguang) | [784909593](https://github.com/784909593)

安装完 `hugo` 之后，需要先同步主题文件

```bash
git submodule update --init --recursive
```

同步完成后，可在根目录执行以下指令来测试网站：

```bash
hugo server
```

文档在 `content/zh/docs` 目录下，修改后可以通过 pull requests 提交。

## 目录

1. 概述
2. 风格指南
3. 风格决策
4. 最佳实践

## 授权

The articles in 《Google Go 编程规范》 are licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).

## 贡献者

<!-- readme: collaborators,contributors -start -->
<table>
<tr>
    <td align="center">
        <a href="https://github.com/fivezh">
            <img src="https://avatars.githubusercontent.com/u/1311319?v=4" width="100;" alt="fivezh"/>
            <br />
            <sub><b>Fivezh</b></sub>
        </a>
    </td>
    <td align="center">
        <a href="https://github.com/zhuyaguang">
            <img src="https://avatars.githubusercontent.com/u/8857976?v=4" width="100;" alt="zhuyaguang"/>
            <br />
            <sub><b>朱亚光</b></sub>
        </a>
    </td>
    <td align="center">
        <a href="https://github.com/zxmfke">
            <img src="https://avatars.githubusercontent.com/u/19350643?v=4" width="100;" alt="zxmfke"/>
            <br />
            <sub><b>Xiaomin Zheng</b></sub>
        </a>
    </td>
    <td align="center">
        <a href="https://github.com/784909593">
            <img src="https://avatars.githubusercontent.com/u/35739463?v=4" width="100;" alt="784909593"/>
            <br />
            <sub><b>784909593</b></sub>
        </a>
    </td>
    <td align="center">
        <a href="https://github.com/lsj1342">
            <img src="https://avatars.githubusercontent.com/u/43659912?v=4" width="100;" alt="lsj1342"/>
            <br />
            <sub><b>刘思家</b></sub>
        </a>
    </td>
    <td align="center">
        <a href="https://github.com/sijing233">
            <img src="https://avatars.githubusercontent.com/u/48935581?v=4" width="100;" alt="sijing233"/>
            <br />
            <sub><b>Sijing233</b></sub>
        </a>
    </td></tr>
<tr>
    <td align="center">
        <a href="https://github.com/xuxicheng00">
            <img src="https://avatars.githubusercontent.com/u/67250607?v=4" width="100;" alt="xuxicheng00"/>
            <br />
            <sub><b>小超人</b></sub>
        </a>
    </td>
    <td align="center">
        <a href="https://github.com/pseudoyu">
            <img src="https://avatars.githubusercontent.com/u/69753389?v=4" width="100;" alt="pseudoyu"/>
            <br />
            <sub><b>Yu ZHANG</b></sub>
        </a>
    </td>
    <td align="center">
        <a href="https://github.com/liuzengh">
            <img src="https://avatars.githubusercontent.com/u/53028871?v=4" width="100;" alt="liuzengh"/>
            <br />
            <sub><b>Goodliu</b></sub>
        </a>
    </td></tr>
</table>
<!-- readme: collaborators,contributors -end -->
