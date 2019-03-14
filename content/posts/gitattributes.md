---
title: ".gitattributes"
date: 2019-03-10T20:54:45-08:00
draft: false
tags: ["git"]
---
Just like [.gitignore](https://git-scm.com/docs/gitignore), there is another file that you can use in your git repos: The [.gitattributes](https://git-scm.com/docs/gitattributes) file.

You typically place this in the root of your repo. It has lines of the form:

{{< highlight bash >}}
pattern attr1 attr2 ...
{{< / highlight >}}

It essentially means that for files that satisfy the regex `pattern`, apply the attribute `attr1`, `attr2` etc to them. Let's see some examples. Let's say our .gitattributes had the line below:

{{< highlight bash >}}
*.png binary
*.jpg binary
{{< / highlight >}}

What this is saying is that all *.png / *.jpg files shoule be treated as binary. i.e. git will not change them in any way (e.g. CRLF type normalization. It will also not print any diff for files markes as binary during `git diff`)

Another place this can prove useful is when you have generated files e.g. protobuf files. We want to tell git to ignore diff between these files as they are auto generated. We can then add the following like in our .gitattributes file:

{{< highlight bash >}}
**/generated.proto linguist-generated=true
{{< / highlight >}}

For reference, [here](https://github.com/kubernetes/kubernetes/blob/master/.gitattributes) is an example from the kubernetes repo.

You can also use custom programs for diffing files e.g. exif for image files. When images change, then the exif program is called which presents a diff between the metadata of the two versions (*NOTE*: you will have to install exif and configure git to call exif vis `git config`):

{{< highlight bash >}}
*.png diff=exif
{{< / highlight >}}

There are a whole lot more attributes you can use with .gitattributes. Refer to the [manual](https://git-scm.com/docs/gitattributes) for an exhaustive list.



