修改自 [**ThuThesis v7.2.1**](https://github.com/tuna/thuthesis/tree/1971191972d5cbe826a4d208539465e33f01927d)，下面这份基本是原版 README，**用的时候把 thu 换成 bnu 就行**。2024 年以前的部分，按 2015 年的模板和规范改过本硕博中文非涉密部分；当前版本则按 2026 年 10 月 25 日[图书馆的模板和规范](libref)更新了中文硕博格式。我没有全盘照搬[图书馆模板和规范](libref)——那份规范和模板自己就矛盾重重（光是论文原创声明及使用授权书就有三个版本），有些地方是我自己拍的板。

## 2026 版中文研究生格式

中文硕士、博士论文的内页格式按 [2026 版编写规则](libref/北京师范大学研究生学位论文编写规则（2026版）.pdf) 更新，示例用的是学术博士配置。本科、博士后及以英文为主的论文维持原格式。正文和示例 PDF 都是 A4，A3 实体封皮交给装订环节。

示例保留了原有的题名页、校徽页和书脊插页。声明和授权书沿用已确认的版本，页边距和其他内页一致（上下 2.54 cm、左右 3.17 cm），使用授权书从页面中线开始。下面是我做的具体取舍，并不是对附件的照搬：

- 图、表在正文中分别连续编号，按规则后部的具体格式表执行；附录采用 A1、B1 这类编号。规则前文其实允许分章编号，跟格式表对不上。
- 规则里“索引”一栏误写成了“附录”，我按索引在附录之后来理解；要不要索引，看实际需要。
- Word 附件说明书脊不算论文内页，但示例还是留了书脊插页。英文摘要的留白没按“空几行”机械处理。
- 示例保留 `bnuthesis-author-year-bnu.bst` 的作者字顺排序和正文数字上标组合，这是我自己的定制，跟规则“统一选用顺序编码制或著者出版年制”并不一致；要完整按学校规则来，请换用相应的标准样式。
- 示例通过共享配置显式使用 Windows 的宋体、黑体、楷体、仿宋，以及 Times New Roman、Arial 和 Courier New；数学字体沿用 `newcm`，中文加粗沿用模板已有的仿粗配置。

中文文献按拼音排序靠 `.bib` 条目里的 `key` 字段（例如 `key = {zhang1 kun1}`），样式不会自动把汉字转成拼音。每条文献的作者仍按原文署名顺序填写，别为了整表排序去重排共同作者。

改过模板、题名或作者信息后，先重建书脊再编译，免得复用目录里旧的 `spine.pdf`：

```sh
xetex bnuthesis.ins
make spine
make thesis
```

示例里的摘要、成果、致谢和委员会都只是格式演示，实际论文请填真实内容；博士中文摘要一般约 2000 字、硕士约 1000 字。模板不会靠凑字数、编成果或者编译字数门槛来代替你自己的内容检查。

## 内容索引示例

`data/index.tex` 是一个单栏索引页示例，在附录之后、成果列表之前载入。标题是不编号的“索引”，会列入目录，页眉写“索引”，页码与正文连续；正文是宋体小四号、20 磅行距、段前后为零、首行缩进两字。规则格式表里“索引”栏误写的“附录”，按前文第 12 条的要求理解即可。

示例手工按拼音字顺组织主题条目并用字母分组，同时演示页码指引和同义词“见”参照；字母分组只是本例的选择。页码用 `\pageref` 指向正文中的实际标签，不需要跑 `makeindex`。新增条目照下面的写法：

```tex
% 正文中的实际内容位置：
待检索术语\label{idx:term}

% data/index.tex 中对应的索引条目：
待检索术语，\pageref{idx:term}\par
```

检索主题要按实际内容选，同义词和拼音排序（多音字尤其要留神）需要核对，还可以参照 [GB/T 41210—2021《学位论文内容索引编制规则》](https://openstd.samr.gov.cn/bzgk/std/newGbInfo?hcno=880E61B530D05F7F63340483FD2283CF) 完善条目。同一内容有多个指引页码时，先去重再升序排列；内容增删后记得回头核对同页合并和排序。索引是可选部分，不需要时把主文件里的 `\input{data/index}` 注释掉即可，正文里的标签留着无妨。`make thesis` 会自动完成交叉引用需要的后续编译。

## WSL 中的 Windows 字体

示例直接用 `/mnt/c/Windows/Fonts/` 里现成的字体，在用户字体目录建符号链接，不把字体文件复制进模板仓库。论文和书脊共同加载 `bnusetup.tex` 里的配置：

```tex
\bnusetup{
  font = times,
  cjk-font = windows,
}
```

确认下列文件都在后，首次配置执行：

```sh
mkdir -p "$HOME/.local/share/fonts/windows"
for file in simsun.ttc simhei.ttf simkai.ttf simfang.ttf \
            times.ttf timesbd.ttf timesi.ttf timesbi.ttf \
            arial.ttf arialbd.ttf ariali.ttf arialbi.ttf \
            cour.ttf courbd.ttf couri.ttf courbi.ttf; do
  ln -s "/mnt/c/Windows/Fonts/$file" "$HOME/.local/share/fonts/windows/$file"
done
fc-cache -f
```

`simsun.ttc` 同时提供宋体和新宋体，模板使用 `SimSun`；其余中文字体分别用 `SimHei`、`KaiTi` 和 `FangSong`。每组西文字体的四个文件对应常规、粗体、斜体、粗斜体。想看实际匹配到哪个字体，运行 `fc-match -f '%{family}: %{file}\n' SimSun` 和 `fc-match -f '%{family}: %{file}\n' 'Times New Roman'`；用 apt 装过微软字体的，保留即可。

编译期间 `/mnt/c/Windows/Fonts/` 必须可访问。其他环境需要先装好相应字体，或者显式改用模板提供的其他字体方案；Noto CJK 和 TeX Gyre 是替代选项，跟这里的 Windows 字体不是一回事。

[![Actions Status](https://github.com/tuna/thuthesis/workflows/Test/badge.svg)](https://github.com/tuna/thuthesis/actions)
[![GitHub downloads](https://img.shields.io/github/downloads/tuna/thuthesis/total)](https://github.com/tuna/thuthesis/releases)
[![GitHub commits](https://img.shields.io/github/commits-since/tuna/thuthesis/latest)](https://github.com/tuna/thuthesis/commits/master)
[![GitHub release](https://img.shields.io/github/v/release/tuna/thuthesis)](https://github.com/tuna/thuthesis/releases/latest)
[![CTAN](https://img.shields.io/ctan/v/thuthesis)](https://www.ctan.org/pkg/thuthesis)


# ThuThesis

Scroll down for the English version of README.

**ThuThesis** 是 **T**sing**h**ua **U**niversity **Thesis** LaTeX Template 的缩写。

此宏包旨在建立一个简单易用的清华大学学位论文 LaTeX 模板，包括本科综合论文训练、硕士论文、博士论文以及博士后出站报告。

**由于模板升级频繁，在开始使用和提问前，请确保您已经认真完整地阅读了使用说明文档和示例代码。**

**由于模板升级频繁，在开始使用和提问前，请确保您已经认真完整地阅读了使用说明文档和示例代码。**

**由于模板升级频繁，在开始使用和提问前，请确保您已经认真完整地阅读了使用说明文档和示例代码。**

## 下载

推荐下载**发布版**模板，里面包括具体使用说明以及示例文档：

* 模板使用说明（thuthesis.pdf）
* 示例文档（thuthesis-example.pdf）

开发版中不提供预生成的 `cls` 文件和文档，仅包含源码。其仅供开发者与需要尚未发布的功能的有经验的 TeX 用户使用，不提供任何保证。

下载途径：

* 发布版：
  * [CTAN](https://www.ctan.org/pkg/thuthesis)：可能滞后正式发布少许时间。
  * [GitHub Releases](https://github.com/tuna/thuthesis/releases)：最新版的及时发布途径。
  * [TUNA 镜像站](https://mirrors.tuna.tsinghua.edu.cn/github-release/tuna/thuthesis/)：GitHub Releases 的镜像。
  * [Overleaf](https://www.overleaf.com/latex/templates/thuthesis-tsinghua-university-thesis-latex-template/wddqnwbyhtnk)：Overleaf 的模板。
* 开发版：[GitHub](https://github.com/tuna/thuthesis)

## 更新日志

每个版本的详细更新日志，请见 [CHANGELOG.md](blob/master/CHANGELOG.md)。使用文档中也包含了这一内容。

## 升级
### 自动更新
通过 TeX 发行版工具（如 `tlmgr`）自动从 [CTAN](https://www.ctan.org/pkg/thuthesis) 更新。

### 手动更新

#### 发布版

下载发布版的的 zip 包，使用其中的 `thuthesis.cls` 等文件覆盖原有的即可，无须额外操作。

#### 开发版

从 GitHub clone 项目源码或者下载源码 zip 包，执行命令（Windows 用户在文件夹空白处按 `Shift + 鼠标右键`，点击“在此处打开命令行窗口”）：

```shell
xetex thuthesis.ins
```

即可得到 `thuthesis.cls` 等模板文件。

## 提问
按推荐顺序排序：

* 先到 [FAQ](https://github.com/tuna/thuthesis/wiki/FAQ) 看看常见问题
* [GitHub Issues](https://github.com/tuna/thuthesis/issues)

## Makefile的用法

```shell
make [{all|thesis|spine|doc|clean|cleanall|distclean}]
```

### 目标
* `make thesis`    生成论文 thuthesis-example.pdf；
* `make spine`     生成书脊 spine.pdf；
* `make doc`       生成模板使用说明书 thuthesis.pdf；
* `make all`       生成论文和书脊，相当于 `make thesis && make spine`；
* `make clean`     删除示例文件的中间文件（不含 thuthesis-example.pdf）；
* `make cleanall`  删除示例文件的中间文件和 thuthesis-example.pdf；
* `make distclean` 删除示例文件和模板的所有中间文件和 PDF。

---

# ThuThesis

**ThuThesis** is an abbreviation of **T**sing**h**ua **U**niversity **Thesis** LaTeX Template.

This package establishes a simple and easy-to-use LaTeX template for Tsinghua dissertations, including general undergraduate research papers, masters theses, doctoral dissertations, and postdoctoral reports. An English translation of this README follows the Chinese below.

**This template is subject to frequent changes. Please make sure you have read the usage documentation and example code completely and carefully before using and asking questions.**

## Downloads

**Published versions** are recommended. Specific usage documentation and examples can be found in the archive. At present, these documents are <b>only available in Chinese</b>:
* Template usage documentation (thuthesis.pdf)
* Template example (thuthesis-example.pdf)

Developer versions contain only source code but no pre-compiled `cls` file and documentations. They are only for the usage of developers and experienced TeX users in need of unpublished features. No warranties are provided.

* Published versions：
  * [CTAN](https://www.ctan.org/pkg/thuthesis)
  * [GitHub Releases](https://github.com/tuna/thuthesis/releases)
  * [TUNA Mirrors](https://mirrors.tuna.tsinghua.edu.cn/github-release/tuna/thuthesis/): mirror of GitHub Releases
  * [Overleaf Template](https://www.overleaf.com/latex/templates/thuthesis-tsinghua-university-thesis-latex-template/wddqnwbyhtnk)
* Developer versions: [GitHub](https://github.com/tuna/thuthesis)

## Changelog

See [CHANGELOG.md](blob/master/CHANGELOG.md) for detailed changes in each release. They are also included in the usage documentation.

## Updates
### Automatic
Get the most up-to-date published version with your TeX distribution from [CTAN](https://www.ctan.org/pkg/thuthesis).

### Manual

#### Published versions

Download the published zip files, extract `thuthesis.cls` and other files (if needed) and override the existing ones in your thesis.

#### Developer versions

Download the source code package and unzip to the root directory of your thesis (or clone this project), then execute the command (Windows users `Shift + right click` white area in the file window and click "Open command line window here" from the popup menu):

```shell
xetex thuthesis.ins
```

You'll get `thuthesis.cls` along with other template files.

## Reporting Issues
Please follow the procedure below:

* Check the [FAQ](https://github.com/tuna/thuthesis/wiki/FAQ)
* [GitHub Issues](https://github.com/tuna/thuthesis/issues)

## Makefile Usage

```shell
make [{all|thesis|spine|doc|clean|cleanall|distclean}]
```

### Targets
* `make thesis`    generate thesis thuthesis-example.pdf;
* `make spine`     generate book spine for printing spine.pdf;
* `make doc`       generate template documentation thuthesis.pdf;
* `make all`       generate thesis and spine, same as `make thesis && make spine`;
* `make clean`     delete all examples' files (excluding thuthesis-example.pdf);
* `make cleanall`  delete all examples' files and thuthesis-example.pdf;
* `make distclean` delete all examples' and templates' files and PDFs.
