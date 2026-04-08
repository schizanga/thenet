+++
date = "{{ .Date }}"
title = "{{ replace .Name "-" " " | title }}"
draft = true

#
tags = [{{ range $plural, $terms := .Site.Taxonomies }}{{ range $term, $val := $terms }}"{{ printf "%s" $term }}",{{ end }}{{ end }}]

[params]
    author = ''
    description = ''
+++

This is a page about »{{ replace .Name "-" " " | title }}«.

Your intro paragraph here.

![Alt text](assets/my-image.jpg)

More content here.

![Another image](assets/another-image.jpg)
